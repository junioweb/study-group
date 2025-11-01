### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Cap. 3 – “Bad Smells in Code” (por Kent Beck e Martin Fowler)

#### 2. **Conceitos-Chave Identificados**
- **Code Smells** são sinais de que o design do código está deteriorado e precisa de refatoração.
- Não são bugs, mas indicadores de problemas estruturais que dificultam manutenção, evolução e compreensão.
- Os principais “maus cheiros” abordados incluem:
  - **Código duplicado** – lógica repetida em múltiplos lugares.
  - **Métodos longos** – dificultam leitura, teste e reutilização.
  - **Classes grandes (God Classes)** – concentram muitas responsabilidades.
  - **Switch statements ou condicionais complexos** – indicam ausência de polimorfismo.
  - **Feature Envy** – método que usa mais dados de outra classe do que da própria.
  - **Data Clumps** – grupos de dados que sempre aparecem juntos (sinal de nova abstração).
  - **Comentários excessivos** – frequentemente usados como “desodorante” para código confuso.

#### 3. **Insights Relevantes**
> “If it stinks, change it.” — Grandma Beck  
→ Um princípio simples, mas poderoso: não ignore sinais de deterioração no código.

> “Comments are often used as a deodorant.”  
→ Comentários não corrigem más decisões de design; eles apenas mascaram o odor. Refatore para eliminar a necessidade deles.

> “Duplicated code is the strongest smell.”  
→ Duplicação aumenta o custo de mudança e o risco de inconsistências. É prioridade absoluta para refatoração.

> “Long functions are hard to understand because you can’t hold the whole thing in your head.”  
→ Funções devem expressar intenção clara em poucas linhas. Tamanho ideal: o que cabe em uma tela sem rolar.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Implementar revisões contínuas de código com foco em identificar *code smells* antes que se tornem dívida técnica crônica.
- Usar **métricas simples** (ex: complexidade ciclomática, número de parâmetros, tamanho de métodos) como gatilhos para refatoração.
- Tratar **comentários explicativos** como oportunidades para extrair funções com nomes expressivos (`Extract Function`).
- Agrupar **Data Clumps** em objetos valor (`Introduce Parameter Object`) para melhorar coesão e reduzir acoplamento.
- Substituir cadeias de `if/else` ou `switch` por **estratégias polimórficas** (`Replace Conditional with Polymorphism`), especialmente em lógica de negócio variável (ex: cálculo de preços, regras fiscais).

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Regra dos 5**: métodos com mais de 5 linhas merecem avaliação crítica; mais de 10, provavelmente precisam ser refatorados.
- **Zero tolerância com duplicação lógica**: aplicar o **Rule of Three** — na terceira ocorrência similar, refatorar.
- **Comentários só para “porquê”, nunca para “o quê”**: se o código não é autoexplicativo, renomeie ou extraia.
- **Evitar classes com mais de 3 responsabilidades claras**: use o princípio **Single Responsibility** como guia.
- **Feature Envy é um sinal para mover o comportamento**: se um método acessa mais dados de outra classe, talvez ele pertença lá.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como detectar *code smells* de forma automatizada em pipelines de CI/CD sem gerar ruído excessivo?
- Qual o equilíbrio entre eliminar um *smell* e não introduzir abstrações prematuras (ex: criar uma hierarquia de classes para apenas dois tipos)?
- Em sistemas legados sem testes, como priorizar quais *smells* atacar primeiro para maximizar impacto com mínimo risco?
- Existem *code smells* específicos em arquiteturas orientadas a eventos ou serverless que o livro não cobre?