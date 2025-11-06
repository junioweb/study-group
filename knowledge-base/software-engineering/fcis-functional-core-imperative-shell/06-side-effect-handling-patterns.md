📘 Modelo de Registro de Aprendizados

**1. Referência da Leitura**  
Síntese dos artigos: *"The Functional Core Imperative Shell Paradigm"*, *"Mastering Functional Core/Imperative Shell in Go"* e *"A Look at the Functional Core and Imperative Shell Pattern"* (Contexto: Técnicas para isolamento de efeitos colaterais em arquiteturas híbridas)  

---

**2. Conceitos-Chave Identificados**  
- **Isolamento estrito de efeitos colaterais**:  
  - Todo I/O (arquivos, rede, banco de dados), logging, chamadas a APIs externas e manipulação de tempo devem residir exclusivamente no Imperative Shell.  
- **Dependency Rejection**:  
  - Técnica de "rejeitar dependências" ao projetar o Functional Core, garantindo que funções puras não aceitem callbacks ou callbacks para operações externas.  
- **Padrões de assimetria assíncrona**:  
  - O shell lida com assincronia (promises, callbacks, goroutines), enquanto o core opera com dados síncronos e determinísticos.  
- **Barreira de tipos imutáveis**:  
  - Conversão de dados mutáveis/externos para estruturas imutáveis ao atravessar a fronteira para o core.  
- **Efeitos como dados**:  
  - Representação de operações com efeitos colaterais como estruturas de dados no core, que são posteriormente executadas pelo shell.  

---

**3. Insights Relevantes**  
> *"The world is impure. Unfortunately, most of our applications need to handle the outside world, which means having dependencies where state changes are part of the equation."*  
→ Reconhecimento pragmático de que sistemas reais necessitam de efeitos colaterais, mas sua localização deve ser controlada e explícita.  

> *"The functional core is a way to separate the functionality of the user interface into clean boundaries."*  
→ Fronteiras explícitas não apenas isolam efeitos colaterais, mas criam limites claros de responsabilidade e testabilidade.  

> *"No need to have data transfer objects (DTO) as the returned values from the core are read-only."*  
→ A imutabilidade nativa do core elimina a necessidade de padrões como DTOs, simplificando a comunicação entre camadas.  

> *"Think of the functional core as an isolated and perfect world, whereas the imperative shell is the layer to deal with the real imperfect world."*  
→ Esta metáfora ajuda a visualizar a responsabilidade do shell em "traduzir" a imperfeição do mundo real para o core idealizado.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Estratégia para logging**:  
  - Criar um `Logger` no shell que aceita mensagens estruturadas do core, mas mantém a formatação e output no shell.  
  - Exemplo em Go: `core.ProcessOrder(order, func(msg string) { logger.Info(msg) })` onde o callback é injetado pelo shell.  
- **Manipulação de dados assíncronos**:  
  - No Vue/Nuxt, usar composables para orquestrar chamadas assíncronas no shell, passando apenas dados já resolvidos para o core funcional.  
  - Em Go, implementar pattern de "async boundary" onde goroutines são confinadas aos adaptadores e serviços do shell.  
- **Técnica de dependency rejection**:  
  - Funções do core recebem apenas dados processáveis, não callbacks ou interfaces para operações externas.  
  - Exemplo: `func CalculateShipping(address core.Address, items []core.Item) (core.ShippingOption, error)` nunca recebe um `ShippingRepository`.  
- **Representação de efeitos como dados**:  
  - Modelar operações externas como estruturas no core (ex.: `type Effect struct { Type string; Payload map[string]interface{} }`), que o shell interpreta e executa.  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra do "nunca async no core"**:  
  - Nenhuma função no Functional Core pode retornar promises, channels ou callbacks. Todas as operações devem ser síncronas e determinísticas.  
- **Padrão de injeção de efeitos**:  
  - Quando necessário passar capacidade de efeitos para o core (ex.: gerador de IDs únicos), injetar como função pura (ex.: `func() string` em vez de `UUIDService`).  
- **Mecanismo de validação de fronteira**:  
  - Configurar ESLint/Rules customizadas para flaggar qualquer uso de `console.log`, `fetch`, `setTimeout` ou bibliotecas de I/O em arquivos do core.  
  - Em Go, usar `go vet` customizado para detectar imports de pacotes como `net/http`, `os`, `log` em `/core`.  
- **Padrão de tratamento de erros**:  
  - O core retorna apenas erros de domínio (ex.: `InvalidEmailError`), nunca erros técnicos (ex.: `TimeoutError`, `DBConnectionError`).  
  - O shell converte erros técnicos em erros de domínio compreensíveis antes de passar para o core ou usuário final.  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como lidar com operações que exigem feedback contínuo do usuário (ex.: progress bars, streaming) sem contaminar o core com callbacks complexos?  
- Qual a melhor abordagem para sistemas em tempo real onde latência é crítica e a tradicional separação core/shell pode introduzir overhead?  
- Como representar transações distribuídas que envolvem múltiplos efeitos colaterais enquanto mantém a consistência no modelo funcional?  
- Existe um padrão elegante para manipular timeouts e retries no shell sem espalhar essa lógica por múltiplos adaptadores?  
- Como balancear a pureza funcional com necessidades de performance em cenários de alto throughput, onde alocação de novos objetos para cada operação pode ser custosa?  