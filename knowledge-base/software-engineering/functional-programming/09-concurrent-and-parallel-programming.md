📘 Registro de Aprendizados

1. **Referência da Leitura**  
   Capítulo/Seção:  
   - Capítulo 18 – "Programming with monads" e seções sobre concorrência do livro "The Craft of Functional Programming" (Simon Thompson, 3rd Edition)  
   - Seção 3.5 "Parallel Functional Programming" do artigo "Conception, Evolution, and Application of Functional Programming Languages" (Paul Hudak, 1989)  
   - Seções sobre lazy evaluation e processamento concorrente de dados no Capítulo 17 do livro de Thompson

2. **Conceitos-Chave Identificados**  
   - **Paralelismo implícito**: Em linguagens funcionais, o paralelismo é manifesto através de dependências de dados e semântica de operadores primitivos, não através de construções explícitas
   - **Transparência referencial**: Propriedade fundamental que permite substituição de "iguais por iguais", essencial para execução determinística em ambientes concorrentes
   - **Threads leves e MVars**: Abstrações funcionais para concorrência que encapsulam estado mutável de forma controlada
   - **Processamento por streams**: Modelo de computação onde dados são processados como fluxos contínuos, permitindo pipelines concorrentes
   - **Estruturas de dados imutáveis**: Fundamentais para sincronização sem locks tradicionais, eliminando condições de corrida
   - **Processamento determinístico**: Mesmo em sistemas paralelos, a pureza funcional garante resultados consistentes
   - **Memoization**: Técnica de caching de resultados para evitar recálculo em ambientes concorrentes
   - **Avaliação preguiçosa**: Permite construção de pipelines de processamento eficientes com demanda sob demanda

3. **Insights Relevantes**  
   > "An often-heralded advantage of functional languages is that parallelism in a functional program is implicit; it is manifested solely through data dependencies and the semantics of primitive operators." (Hudak)
   
   → Isso significa que o programador não precisa se preocupar com detalhes de como distribuir tarefas, apenas com definir as dependências lógicas entre dados.
   
   > "The emphasis on a pure declarative style of programming is perhaps the hallmark of the functional programming paradigm. The term referentially transparent is often used to describe this style of programming, in which 'equals can be replaced by equals'."
   
   → A transparência referencial não é apenas uma propriedade teórica, mas uma ferramenta prática que permite raciocínio local sobre código concorrente.
   
   > "In many functional languages, parallelism is detected by the system and allocated to processors automatically."
   
   → Isso contrasta com linguagens imperativas onde o programador precisa gerenciar explicitamente threads, locks e sincronização.

4. **Aplicações Práticas no Nosso Contexto**  
   - Implementar pipelines de processamento de dados usando abstrações de streams (como `zipWith`, `map`, `filter` em paralelo)
   - Substituir estruturas de dados mutáveis por versões imutáveis em serviços concorrentes (usando bibliotecas como Immutable.js ou Immer)
   - Utilizar técnicas de memoization para cálculos intensivos compartilhados entre múltiplos usuários
   - Construir DSLs específicas para orquestração de tarefas concorrentes, inspiradas no modelo de Orc (Launchbury e Elliott, 2010)
   - Processar requisições HTTP de forma concorrente mantendo estado imutável entre requisições
   - Implementar workers stateless que processam mensagens de filas usando funções puras
   - Utilizar avaliação preguiçosa para carregar dados sob demanda em aplicações com alto volume de dados

5. **Decisões de Design ou Padrões a Adotar**  
   - Priorizar transparência referencial em componentes que precisam escalar horizontalmente
   - Utilizar estruturas de dados imutáveis como padrão para qualquer compartilhamento de estado entre threads
   - Adotar a abordagem de "concorrência por composição" usando funções de ordem superior em vez de gerenciamento explícito de threads
   - Implementar sistemas de mensageria usando tipos algébricos para representar eventos e estados possíveis
   - Utilizar técnicas de streaming para processamento concorrente de grandes volumes de dados
   - Restringir efeitos colaterais (como I/O) a módulos específicos e bem definidos, isolando-os do núcleo funcional
   - Adotar o modelo de atores leves para paralelismo em sistemas distribuídos, mantendo a imutabilidade das mensagens

6. **Dúvidas ou Pontos a Aprofundar**  
   - Como equilibrar a granularidade de tarefas paralelas para evitar overhead de criação de threads versus subutilização de recursos?
   - Quais são os trade-offs entre determinismo e performance em sistemas funcionais concorrentes em produção?
   - Como lidar eficientemente com recursos compartilhados externos (banco de dados, APIs externas) mantendo o modelo funcional?
   - Qual o impacto do garbage collector em sistemas concorrentes funcionais de alta performance e como mitigá-lo?
   - Quais técnicas de profiling são eficazes para identificar gargalos em aplicações concorrentes funcionais?
   - Como implementar estratégias de backpressure em pipelines funcionais de processamento de streams?
   - Como conciliar a programação funcional com frameworks existentes de concorrência no ecossistema JavaScript/TypeScript?