📘 Modelo de Registro de Aprendizados

**1. Referência da Leitura**  
Artigo: *"Mastering Functional Core/Imperative Shell in Go: A Pragmatic Guide to Clean Architecture"* + discussões sobre *God Orchestrators* em sistemas complexos (Contexto: Aplicações reais em Go e Vue/Nuxt com foco em composição de serviços)  

---

**2. Conceitos-Chave Identificados**  
- **Orquestradores como intermediários especializados**:  
  - Camada que coordena a comunicação entre *Functional Core* e *Imperative Shell*, sem conter lógica de negócio.  
  - Função primária: sequenciamento de operações, não processamento de regras de domínio.  
- **Princípio da especialização**:  
  - Serviços focados em uma única responsabilidade (ex.: `UserService`, `InventoryService`) compõem funcionalidades complexas.  
  - Orquestradores de alto nível (ex.: `CheckoutOrchestrator`) combinam serviços especializados em workflows.  
- **Regra de acoplamento mínimo**:  
  - Orquestradores dependem apenas de interfaces, não de implementações concretas dos serviços.  
  - O core permanece totalmente isolado das decisões de orquestração.  
- **Direção de dados explícita**:  
  - Os fluxos de dados através de orquestradores seguem um caminho unidirecional e previsível.  
  - Dados são transformados explicitamente entre cada etapa do processo.  
- **Separation of Concerns por nível**:  
  - Core: lógica pura e determinística.  
  - Serviços especializados: lógica de domínio contextualizada.  
  - Orquestradores: sequenciamento e coordenação.  
  - Shell: efeitos colaterais e I/O.  

---

**3. Insights Relevantes**  
> *"That way lies madness: bloated services, fragile tests, painful maintenance."*  
→ O artigo de Go alerta que monolitos de orquestração ("God Orchestrators") são uma armadilha comum que destrói os benefícios do FCIS.  

> *"Break things down: UserService → authentication, InventoryService → stock validation, CartService → cart totals, CheckoutOrchestrator → sequences them."*  
→ A composição de serviços especializados é a resposta para sistemas complexos sem sacrificar a testabilidade.  

> *"Functions are usually enough."*  
→ Em Go, e em muitas linguagens, funções simples são preferíveis a structs com métodos para implementar orquestradores, mantendo a simplicidade.  

> *"The shell will interact with the core and the dependencies."*  
→ Os orquestradores, como parte do shell, são a ponte responsável por conectar dependências externas com a lógica pura do core.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Estrutura de orquestradores em Go**:  
  ```go
  type CheckoutOrchestrator struct {
      userSvc      UserService
      inventorySvc InventoryService
      cartSvc      CartService
      logger       Logger
  }
  
  func (o *CheckoutOrchestrator) Execute(ctx context.Context, userID string, cart core.Cart) (float64, error) {
      // Sequenciamento explícito sem lógica de negócio
      if _, err := o.userSvc.Authenticate(ctx, userID); err != nil {
          o.logger.Warn("authentication failed", "user", userID)
          return 0, err
      }
      
      if err := o.inventorySvc.ValidateInventory(ctx, cart.Items); err != nil {
          o.logger.Warn("inventory validation failed")
          return 0, err
      }
      
      return o.cartSvc.ProcessCart(ctx, cart)
  }
  ```
- **Orquestração no frontend (Vue/Nuxt)**:  
  - Criar composables (`useCheckoutProcess`) que orquestram chamadas aos módulos de core functional, mantendo os componentes de página limpos.  
  - Usar async/await para sequenciar operações sem aninhar callbacks (evitando "callback hell").  
- **Estratégia de fallback**:  
  - Implementar padrões de circuit breaker nos orquestradores para lidar com falhas em serviços dependentes.  
  - Separar lógica de fallback da lógica de negócio (ex.: `handleInventoryFailure()` como função dedicada).  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra do limite de responsabilidade**:  
  - Orquestradores não devem ter mais de 3-4 dependências. Acima disso, decompor em sub-orquestradores.  
  - Funções de orquestração não devem exceder 25 linhas de código lógico (sem contar tratamento de erros).  
- **Padrão de injeção**:  
  - Todos os orquestradores devem receber dependências via construtor, nunca instanciar diretamente.  
  - Usar interfaces mínimas (Interface Segregation Principle) para reduzir acoplamento.  
- **Padrão de erro explícito**:  
  - Orquestradores devem transformar erros técnicos em erros de domínio compreensíveis antes de propagar ao shell.  
  - Nunca permitir que erros de infraestrutura vazem para o core.  
- **Testagem**:  
  - Testes de unidade para orquestradores devem usar mocks leves apenas para verificar a sequência de chamadas.  
  - Testes de integração devem validar o fluxo completo com implementações reais de serviços.  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como implementar transações distribuídas que envolvem múltiplos serviços orquestrados sem violar o princípio de responsabilidade única?  
- Qual a melhor estratégia para versionamento de orquestradores quando os contratos de serviços mudam?  
- Como monitorar e rastrear a execução de workflows compostos por múltiplos orquestradores em produção?  
- Em quais cenários um orquestrador dedicado é preferível a um evento assíncrono para coordenação entre serviços?  
- Como lidar com compensações (rollback) quando uma etapa falha em um workflow longo e complexo?  