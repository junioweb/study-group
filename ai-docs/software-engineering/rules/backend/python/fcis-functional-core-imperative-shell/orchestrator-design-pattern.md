## **Rule: Orchestrator Design Pattern**  
**ID:** `PY-FCIS-ORCHESTRATOR-003`  
**Scope:** Workflow coordination modules (`src/shell/orchestrators/`).  
**Quality Criterion:** Separation of coordination logic from business rules.  

### **Action:**  
- **Orchestrators contain no business logic**: Only sequence operations and handle errors  
- **Maximum 4 dependencies per orchestrator**: Enforce single responsibility principle  
- **Core provides pure workflow definitions**: Orchestrators execute workflows defined in Core  
- **Explicit error handling strategies**: Define retry, fallback, and compensation patterns at orchestration level  
- **Stateless orchestrators**: No internal state beyond current workflow execution  

### **Violation Example:**  
```python
# ❌ Bad - "God Orchestrator" with mixed concerns
# src/shell/checkout_orchestrator.py
class CheckoutOrchestrator:
    def __init__(self, db, payment_gateway, email_service, inventory_service, logger):
        self.db = db
        self.payment_gateway = payment_gateway
        self.email_service = email_service
        self.inventory_service = inventory_service
        self.logger = logger
    
    def process_checkout(self, user_id, cart_items):
        """Bloated orchestrator with business logic and coordination mixed"""
        # Business logic hidden in orchestrator
        if len(cart_items) > 10:
            raise ValueError("Cart limit exceeded")
        
        # Hidden validation logic
        for item in cart_items:
            if item.price < 0:
                raise ValueError("Invalid item price")
        
        # Database operations
        user = self.db.get_user(user_id)
        if not user.is_verified:
            self.logger.warning(f"Unverified user {user_id} attempting checkout")
            raise PermissionError("User not verified")
        
        # Inventory check with business rules
        for item in cart_items:
            stock = self.inventory_service.get_stock(item.id)
            if stock < item.quantity:
                # Business rule: allow backorders for premium users
                if user.tier != "premium":
                    raise ValueError(f"Insufficient stock for {item.name}")
        
        # Payment processing with error handling
        try:
            total = sum(item.price * item.quantity for item in cart_items)
            payment_result = self.payment_gateway.charge(user.card, total)
            
            if not payment_result.success:
                # Business rule: retry failed payments for premium users
                if user.tier == "premium" and "declined" in payment_result.reason:
                    payment_result = self.payment_gateway.charge(user.card, total, force=True)
            
            # Order creation
            order = self.db.create_order(user_id, cart_items, payment_result.id)
            
            # Notification with business rules
            if total > 1000:
                self.email_service.send_premium_confirmation(user.email, order)
            else:
                self.email_service.send_standard_confirmation(user.email, order)
            
            return order
            
        except Exception as e:
            self.logger.error(f"Checkout failed: {str(e)}")
            # Compensation logic mixed with error handling
            for item in cart_items:
                self.inventory_service.release_reserved_stock(item.id, item.quantity)
            raise
```

### **Correct Example:**  
```python
# ✅ Good - Clean orchestrator pattern with separation of concerns
# src/core/checkout.py (Functional Core)
from dataclasses import dataclass
from typing import NewType, List
from enum import Enum

UserId = NewType("UserId", str)
OrderId = NewType("OrderId", str)

class UserTier(str, Enum):
    STANDARD = "standard"
    PREMIUM = "premium"

@dataclass(frozen=True)
class User:
    id: UserId
    tier: UserTier
    is_verified: bool

@dataclass(frozen=True)
class CartItem:
    id: str
    name: str
    price: float
    quantity: int

@dataclass(frozen=True)
class Order:
    id: OrderId
    user_id: UserId
    items: List[CartItem]
    total_amount: float
    payment_id: str

class CheckoutValidationError(Exception):
    pass

def validate_checkout(user: User, items: List[CartItem]) -> None:
    """Pure validation logic with no side effects"""
    if not user.is_verified:
        raise CheckoutValidationError("User not verified")
    
    if len(items) > 10:
        raise CheckoutValidationError("Cart limit exceeded")
    
    for item in items:
        if item.price <= 0:
            raise CheckoutValidationError(f"Invalid price for {item.name}")
        if item.quantity <= 0:
            raise CheckoutValidationError(f"Invalid quantity for {item.name}")

def calculate_total(items: List[CartItem]) -> float:
    """Pure calculation function"""
    return sum(item.price * item.quantity for item in items)

def determine_confirmation_type(total_amount: float, user_tier: UserTier) -> str:
    """Pure business rule function"""
    if total_amount > 1000 or user_tier == UserTier.PREMIUM:
        return "premium"
    return "standard"

# src/shell/orchestrators/checkout_orchestrator.py (Imperative Shell)
from src.core.checkout import (
    User, CartItem, Order, UserTier, 
    validate_checkout, calculate_total, determine_confirmation_type,
    CheckoutValidationError
)
from src.shell.adapters import (
    UserRepository, InventoryService, PaymentGateway, EmailService
)

class CheckoutOrchestrator:
    """Orchestrator with single responsibility: coordinate workflow"""
    
    def __init__(
        self,
        user_repo: UserRepository,
        inventory_svc: InventoryService,
        payment_gateway: PaymentGateway,
        email_service: EmailService,
        order_repository
    ):
        # Maximum 5 dependencies enforced by constructor
        self.user_repo = user_repo
        self.inventory_svc = inventory_svc
        self.payment_gateway = payment_gateway
        self.email_service = email_service
        self.order_repository = order_repository
    
    def process_checkout(self, user_id: str, cart_items: list) -> dict:
        """Orchestrates workflow without containing business logic"""
        try:
            # 1. Get data from external sources
            user_data = self.user_repo.get_by_id(user_id)
            inventory_status = self.inventory_svc.check_availability(cart_items)
            
            # 2. Convert to Core types
            user = User(
                id=UserId(user_data["id"]),
                tier=UserTier(user_data["tier"]),
                is_verified=user_data["is_verified"]
            )
            
            items = [
                CartItem(
                    id=item["id"],
                    name=item["name"],
                    price=float(item["price"]),
                    quantity=int(item["quantity"])
                ) for item in cart_items
            ]
            
            # 3. Execute pure Core functions
            validate_checkout(user, items)
            total = calculate_total(items)
            
            # 4. Check inventory availability with fallback strategy
            if not inventory_status["available"]:
                if user.tier != UserTier.PREMIUM:
                    raise CheckoutValidationError("Insufficient inventory")
                # Premium users can proceed with backorders
                self.inventory_svc.reserve_backorder(items)
            
            # 5. Process payment with retry strategy
            payment_result = self._process_payment_with_retry(
                user, total, max_retries=1 if user.tier == UserTier.PREMIUM else 0
            )
            
            # 6. Create order
            order = self.order_repository.create(
                user_id=user.id,
                items=items,
                total_amount=total,
                payment_id=payment_result["id"]
            )
            
            # 7. Send notification based on business rules
            confirmation_type = determine_confirmation_type(total, user.tier)
            self.email_service.send_confirmation(
                user.email, 
                order, 
                confirmation_type
            )
            
            return {
                "order_id": order.id,
                "total": total,
                "status": "completed"
            }
            
        except CheckoutValidationError as e:
            self._handle_validation_error(e, cart_items)
            raise
        except Exception as e:
            self._handle_system_error(e, cart_items)
            raise
    
    def _process_payment_with_retry(self, user, amount, max_retries):
        """Focused helper method for payment retry strategy"""
        retries = 0
        while retries <= max_retries:
            try:
                return self.payment_gateway.charge(
                    card_id=user.payment_method,
                    amount=amount
                )
            except PaymentDeclinedError as e:
                if retries >= max_retries or "fraud" in str(e).lower():
                    raise
                retries += 1
                # Log retry attempt
                logger.info(f"Payment retry {retries}/{max_retries} for user {user.id}")
    
    def _handle_validation_error(self, error, items):
        """Focused error handling strategy"""
        logger.warning(f"Checkout validation failed: {str(error)}")
        # Release any reserved inventory
        self.inventory_svc.release_reserved_stock(items)
    
    def _handle_system_error(self, error, items):
        """Focused system error handling"""
        logger.error(f"System error during checkout: {str(error)}", exc_info=True)
        # Compensation: release any reserved resources
        self.inventory_svc.release_all_reservations(items)
        # Alert operations team
        alert_service.notify_critical_failure("checkout", str(error))
```

### **Verification:**  
```python
# tests/unit/shell/orchestrators/test_checkout_orchestrator.py
def test_checkout_orchestrator_dependencies():
    """Verify orchestrator has limited dependencies"""
    import inspect
    from src.shell.orchestrators.checkout_orchestrator import CheckoutOrchestrator
    
    init_params = inspect.signature(CheckoutOrchestrator.__init__).parameters
    # Should have self + 5 dependencies max
    assert len(init_params) <= 6

def test_checkout_workflow_success(mocker):
    """Test successful checkout workflow"""
    # Mock all shell dependencies
    mocks = {
        "user_repo": mocker.Mock(),
        "inventory_svc": mocker.Mock(),
        "payment_gateway": mocker.Mock(),
        "email_service": mocker.Mock(),
        "order_repository": mocker.Mock()
    }
    
    # Setup test data
    user_data = {"id": "user1", "tier": "premium", "is_verified": True, "payment_method": "card123"}
    mocks["user_repo"].get_by_id.return_value = user_data
    
    mocks["inventory_svc"].check_availability.return_value = {"available": True}
    
    payment_result = {"id": "pay123", "success": True}
    mocks["payment_gateway"].charge.return_value = payment_result
    
    mocks["order_repository"].create.return_value = Order(
        id="order123", user_id="user1", items=[], total_amount=100.0, payment_id="pay123"
    )
    
    # Create orchestrator with mocks
    orchestrator = CheckoutOrchestrator(**mocks)
    
    # Execute workflow
    cart_items = [{"id": "item1", "name": "Test", "price": "50.0", "quantity": "2"}]
    result = orchestrator.process_checkout("user1", cart_items)
    
    # Verify interactions
    assert result["order_id"] == "order123"
    assert result["total"] == 100.0
    mocks["payment_gateway"].charge.assert_called_once_with(card_id="card123", amount=100.0)
    mocks["email_service"].send_confirmation.assert_called_once()

# tests/unit/core/test_checkout_business_rules.py
def test_confirmation_type_rules():
    """Test pure business rules in isolation"""
    assert determine_confirmation_type(500.0, UserTier.STANDARD) == "standard"
    assert determine_confirmation_type(1500.0, UserTier.STANDARD) == "premium"
    assert determine_confirmation_type(500.0, UserTier.PREMIUM) == "premium"
```