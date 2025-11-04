### 📘 **Registro de Aprendizados – Capítulo 13: Dependency Injection (and Bootstrapping)**

#### 1. **Referência da Leitura**
- **Capítulo**: 13 – *Dependency Injection (and Bootstrapping)*

#### 2. **Conceitos-Chave Identificados**
- **Dependency Injection (DI)**: Técnica de design que separa a criação de dependências da sua utilização, permitindo maior testabilidade e flexibilidade.
- **Composition Root (Raiz de Composição)**: Ponto único na aplicação onde todas as dependências são injetadas e os objetos são compostos — no contexto do livro, implementado como `bootstrap.py`.
- **Explicit vs Implicit Dependencies**: Preferência por dependências explícitas (passadas como parâmetros) em vez de implícitas (via import), para evitar acoplamento e facilitar testes.
- **Manual DI com Closures/Partials**: Uso de funções lambda ou `functools.partial` para criar versões de handlers com dependências já injetadas.
- **DI com Classes**: Alternativa à abordagem funcional, onde handlers são transformados em classes com `__call__`, e dependências são injetadas via `__init__`.
- **Bootstrap Script**: Componente responsável por inicializar o sistema, configurar mappers, injetar dependências e retornar uma instância pronta do Message Bus.
- **Adapter Pattern com DI**: Estruturação de adaptadores (ex: notificações) com interfaces abstratas (ABCs), permitindo substituição entre versões reais e fake para testes.
- **Teste de Adapters Reais**: Uso de ferramentas como MailHog para simular serviços externos (ex: SMTP) em ambientes de integração.

#### 3. **Insights Relevantes**
> “Explicit is better than implicit.” — The Zen of Python  
→ Reforça o valor de declarar dependências explicitamente, mesmo que pareça mais verboso, pois aumenta a clareza e a manutenibilidade.

> “We want our bootstrap script to do the following: Declare default dependencies but allow us to override them... Give us back the core object for our app, the message bus.”  
→ Define claramente o papel do bootstrap como um ponto central de composição, isolando lógica de inicialização e injeção de dependências dos entrypoints.

> “The trouble is that we've made it look easy because our toy example doesn't send real email... In real life, you'd end up having to call `mock.patch` for _every single test_.”  
→ Mostra que mockar dependências globalmente gera boilerplate e acoplamento indesejado — DI resolve isso de forma mais limpa.

> “A dependency injection framework can be useful if you find yourself needing to do DI at multiple levels—if you have chained dependencies of components that all need DI, for example.”  
→ Alerta que frameworks de DI devem ser considerados apenas quando há complexidade significativa de dependências encadeadas, não por default.

> “Define your API using an ABC. Implement the real thing. Build a fake and use it for unit tests. Find a less fake version you can put into your Docker environment. Test the less fake 'real' thing. Profit!”  
→ Síntese prática do ciclo de desenvolvimento de adaptadores: abstração → implementação → teste → integração.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Criar um módulo `bootstrap.py` como ponto único de inicialização e injeção de dependências, evitando duplicação nos entrypoints (Flask, Redis, etc.).
- Substituir imports implícitos por parâmetros explícitos em handlers, especialmente para dependências externas (email, pub/sub, HTTP clients).
- Para testes, usar o bootstrap com dependências fake (ex: `FakeUnitOfWork`, `lambda *args: None`) para isolar o domínio.
- Para integração, usar o bootstrap com dependências reais (ex: `EmailNotifications`, `RedisClient`) conectadas a ambientes simulados (MailHog, Redis local).
- Estruturar adaptadores com interfaces abstratas (ABCs) e múltiplas implementações (real, fake, mock), facilitando trocas e testes.
- Usar `functools.partial` ou closures para injetar dependências manualmente, sem depender de frameworks pesados, mantendo simplicidade e controle.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Todos os handlers devem receber suas dependências explicitamente** — nunca confiar em imports globais ou monkeypatching.
- **Criar um `bootstrap.py` como Composition Root** — ele será responsável por:
  - Inicializar mappers, logging e outros componentes de infraestrutura.
  - Injetar dependências nos handlers (via partials, closures ou classes).
  - Retornar uma instância do Message Bus pronta para uso.
- **Adotar o padrão de Adapter com ABCs para serviços externos** — ex: `AbstractNotifications`, `AbstractMessageBroker`, permitindo múltiplas implementações.
- **Usar fakes para testes unitários e mocks/simuladores para testes de integração** — ex: MailHog para email, Redis local para mensageria.
- **Evitar frameworks de DI a menos que haja cadeias complexas de dependências** — preferir injeção manual simples e explícita.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como lidar com injeção de dependências em cenários assíncronos (ex: coroutines, asyncio) sem comprometer a clareza e a testabilidade?
- Qual é o impacto de performance ao usar injeção manual via `inspect.signature()` comparado a soluções baseadas em classes ou partials?
- Como garantir que o bootstrap seja testável e extensível, especialmente quando novos adaptadores ou configurações precisam ser adicionados?
- Em sistemas grandes, como gerenciar diferentes "flavors" de bootstrap (ex: dev, staging, prod) sem duplicação de código?
- Quais estratégias podem ser usadas para validar que todos os handlers estão recebendo as dependências corretas, especialmente após refatorações?