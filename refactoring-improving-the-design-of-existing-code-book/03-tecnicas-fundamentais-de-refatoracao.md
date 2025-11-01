### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Cap. 6–12 – “A First Set of Refactorings”, “Moving Features”, “Organizing Data”, “Conditional Logic”, “Refactoring APIs” e “Dealing with Generalization”

#### 2. **Conceitos-Chave Identificados**
- **Extract Function / Extract Variable**: extrair lógica ou expressões repetidas para funções ou variáveis com nomes que expressem intenção.
- **Inline Function / Inline Variable**: remover abstrações que não agregam valor ou que obscurecem o fluxo.
- **Rename** (funções, variáveis, parâmetros, campos): priorizar clareza e intenção sobre brevidade.
- **Move Function / Move Field**: realocar comportamentos e dados para o contexto mais apropriado (classe, módulo ou namespace).
- **Replace Conditional with Polymorphism**: substituir lógica condicional por estruturas orientadas a objetos (ex: strategy, factory).
- **Introduce Parameter Object**: agrupar parâmetros relacionados em um objeto coeso, reduzindo acoplamento e melhorando legibilidade.
- **Split Phase**: dividir uma lógica complexa em fases distintas quando cada fase opera com conjuntos diferentes de dados ou responsabilidades.

#### 3. **Insights Relevantes**
> “Extract Function is one of the most common refactorings I do.”  
→ Dar nome a um trecho de código é uma das formas mais poderosas de comunicar intenção.

> “Any code that’s hard to understand should be extracted into a function with a clear name.”  
→ A extração não é apenas para reuso — é para **clareza cognitiva**.

> “Polymorphism is the antidote to conditional complexity.”  
→ Condições repetidas ou profundas são sinais de que o sistema está pedindo por abstrações orientadas a objetos.

> “Good names are a form of documentation that never gets out of date.”  
→ Renomear não é cosmético; é **documentação viva**.

> “Split Phase makes it obvious where one concern ends and another begins.”  
→ Separação de fases é uma forma eficaz de aplicar o **princípio da responsabilidade única** em nível de fluxo.

#### 4. **Aplicações Práticas no Nosso Contexto**
- **Extrair lógica de validação, cálculo ou formatação** em funções nomeadas, mesmo que usadas apenas uma vez.
- **Substituir blocos de `if/else` com lógica de negócio variável** por estratégias polimórficas (ex: cálculo de frete, regras fiscais, pipelines de processamento).
- **Agrupar parâmetros de contexto** (ex: `userId`, `tenantId`, `requestId`) em um *Parameter Object* para facilitar evolução e testes.
- **Mover funções que acessam dados de outra classe** para dentro dessa classe — reduzindo *Feature Envy* e aumentando coesão.
- **Renomear agressivamente** durante revisões de código ou leitura de legado — nomes ruins são dívida cognitiva.
- **Aplicar Split Phase** em rotinas que misturam parsing, cálculo e formatação (ex: geradores de relatórios, pipelines de importação).

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Toda função deve expressar uma única intenção**, mesmo que tenha apenas 2–3 linhas.
- **Evitar parâmetros posicionais acima de 3** — agrupar em objetos ou usar *named parameters* quando possível.
- **Condicional com mais de dois ramos merece avaliação para polimorfismo** — especialmente se os ramos representam conceitos de domínio.
- **Sempre extrair antes de mover**: use `Extract Function` → `Move Function` como sequência segura.
- **Renomear é parte obrigatória da refatoração** — nunca deixar nomes genéricos (`data`, `handler`, `process`) em código consolidado.
- **Split Phase é preferível a comentários como “// Fase 1: validação”** — a estrutura do código deve refletir as fases naturalmente.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como aplicar `Replace Conditional with Polymorphism` em linguagens funcionais ou em contextos com baixa orientação a objetos?
- Qual o impacto de `Introduce Parameter Object` em APIs públicas ou contratos entre microsserviços?
- Existem casos em que `Inline Function` pode prejudicar a legibilidade ao “desfazer” uma abstração útil?
- Como lidar com dependências cíclicas ao mover funções entre módulos?
- Em sistemas com alto volume de eventos (event-driven), como aplicar `Split Phase` sem introduzir latência ou complexidade de orquestração?