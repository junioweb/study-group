📘 Registro de Aprendizados

1. **Referência da Leitura**  
   Artigo: "Domain-driven design in functional programming" por Naveen Negi  
   Data de publicação: 21 de fevereiro de 2022  
   Fonte: https://www.thoughtworks.com/en-br/insights/blog/architecture/domain-driven-design-in-functional-programming

2. **Conceitos-Chave Identificados**  
   - **Strategic vs Tactical Patterns**: DDD é dividida em padrões estratégicos (contexto delimitado, linguagem ubíqua, mapeamento de contexto) e táticos (entidades, agregados, repositórios)
   - **Aggregates como fronteiras de consistência**: Em FP, agregados são fronteiras de transação onde invariantes do domínio são aplicados
   - **Distinção entre Value Types e Entities**: Baseada no ciclo de vida, não na mutabilidade - em FP, a distinção permanece válida mesmo com imutabilidade padrão
   - **Três regras para agregados**:  
     1. Cada agregado como fronteira de transação única  
     2. Mensagens endereçadas aos agregados por identidade única  
     3. Agregados representam conjuntos disjuntos de dados  
   - **Padrões funcionais para DDD**:  
     - Lentes para atualização de estruturas aninhadas  
     - Monóides para representar objetos de valor  
     - Testes baseados em propriedades para verificar invariantes  
     - Padrão "Imperative shell and functional core" para isolar efeitos colaterais  
     - Uso do Reader monad para injeção de dependências

3. **Insights Relevantes**  
   > "The problem is not the mutability of the state, it is the ownership of it. Who is responsible for keeping the state internally consistent?"
   
   → Essa é uma das observações mais importantes: o desafio não está na mutabilidade, mas na responsabilidade pelo estado, algo que DDD aborda com clareza mesmo em linguagens funcionais.
   
   > "Strategic patterns are easy to map to any language... However, the tactical pattern relies on programming language constructs and paradigms."
   
   → Os padrões estratégicos de DDD são universais, enquanto os táticos exigem adaptação para cada paradigma, mas não perdem sua relevância.
   
   > "In functional programming, everything is immutable by default, which leads us to wrongly believe that we don't need a distinction between value types and entities."
   
   → A distinção entre tipos de valor e entidades baseia-se no ciclo de vida do modelo de domínio, não na mutabilidade, sendo igualmente importante em FP.

4. **Aplicações Práticas no Nosso Contexto**  
   - Implementar lentes para atualizar estruturas de dados aninhadas sem comprometer a imutabilidade
   - Usar monóides para modelar objetos de valor com operações de combinação natural (ex: agregação de valores financeiros)
   - Aplicar testes baseados em propriedades (QuickCheck/Property-based testing) para verificar invariantes do domínio
   - Estruturar sistemas com núcleo funcional puro e camada imperativa nas bordas para lidar com I/O
   - Modelar mensagens de domínio como tipos algébricos para garantir que apenas operações válidas sejam executadas
   - Criar identidades únicas para agregados e usar mensagens para comunicação entre eles, mesmo em sistemas monolíticos
   - Desenvolver bibliotecas de validação de domínio que podem ser reutilizadas em diferentes contextos delimitados

5. **Decisões de Design ou Padrões a Adotar**  
   - **Fronteiras de transação**: Garantir que cada agregado seja uma unidade transacional atômica, evitando transações distribuídas entre agregados
   - **Mensagens endereçadas**: Implementar comunicação entre agregados via mensagens direcionadas às identidades dos agregados, não assumindo localização
   - **Isolamento de dados**: Manter agregados como conjuntos disjuntos de dados, sem compartilhamento de modelos entre contextos delimitados
   - **Lentes para atualização**: Utilizar bibliotecas de lentes (ex: Ramda, Monocle) para atualizar estruturas de dados imutáveis profundamente aninhadas
   - **Testes baseados em propriedades**: Implementar testes que verifiquem invariantes do domínio para todas as entradas válidas
   - **Injeção de dependências funcional**: Usar Reader monad ou similar para injetar dependências de forma pura
   - **Camada imperativa nas bordas**: Separar claramente entre lógica de domínio pura e operações com efeitos colaterais
   - **Linguagem ubíqua tipada**: Representar conceitos do domínio com tipos específicos em vez de primitivos genéricos

6. **Dúvidas ou Pontos a Aprofundar**  
   - Como implementar eventos de domínio em FP de forma que preservem a pureza funcional?
   - Qual é o trade-off entre a complexidade de lentes para estruturas profundamente aninhadas e a legibilidade do código?
   - Como modelar relacionamentos entre agregados em sistemas distribuídos sem comprometer a consistência?
   - Quais estratégias existem para versionar esquemas de agregados em sistemas com persistência imutável?
   - Como conciliar a abordagem orientada a eventos com a programação funcional pura em contextos de alto throughput?
   - Quais são os padrões mais eficazes para implementar repositórios em FP que mantêm a pureza do domínio?
   - Como gerenciar transições de estado complexas em entidades funcionais sem cair em "cascata de condicionais"?
   - Qual a melhor abordagem para persistência de agregados funcionais em bancos de dados tradicionais (SQL/NoSQL)?
   - Como lidar com a performance de agregados grandes em linguagens funcionais, considerando a cópia completa em cada atualização?