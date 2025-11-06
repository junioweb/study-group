# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 9 – "Unit Tests" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **TDD como disciplina fundamental**: Test-Driven Development não é apenas uma técnica, mas uma disciplina essencial para profissionais de software.
- **Clean Tests**: Testes devem ser tão bem escritos e mantidos quanto o código de produção, com ênfase em legibilidade acima de tudo.
- **Princípios F.I.R.S.T. para testes**:
  - **F**ast: Testes devem executar rapidamente
  - **I**ndependent: Testes não devem depender uns dos outros
  - **R**epeatable: Devem produzir o mesmo resultado em qualquer ambiente
  - **S**elf-validating: Devem ter um resultado booleano (passa/falha)
  - **T**imely: Devem ser escritos antes do código de produção (no TDD)
- **Testes como documentação**: Testes bem escritos documentam a intenção do código e servem como exemplos de uso.
- **Testes habilitam as "habilidades" do código**: Flexibilidade, manutenibilidade e reusabilidade dependem de um bom conjunto de testes.
- **Métricas importantes**:
  - Cobertura de código (mas não como meta final)
  - Tempo de execução dos testes
  - Taxa de falso positivo/negativo
  - Proporção de testes por tipo (unitários, de integração, E2E)
- **Refatoração contínua de testes**: Assim como o código de produção, os testes precisam ser constantemente refatorados para manter a clareza.
- **Padrões para testes limpos**:
  - Formato Given-When-Then (ou Arrange-Act-Assert)
  - Um único conceito por teste
  - Nomes descritivos que revelam a intenção
  - Uso de DSLs (Domain-Specific Languages) para testes

## 3. Frases ou Ideias que Trouxeram Clareza

> "If you don't keep your tests clean, you will lose them. And without them, you lose the very thing that keeps your production code flexible. Yes, you read that correctly. It is unit tests that keep our code flexible, maintainable, and reusable. The reason is simple. If you have tests, you do not fear making changes to the code!"

> "Tests enable all the-ilities, because tests enable change. Without tests every change is a possible bug. No matter how flexible your architecture is, no matter how nicely partitioned your design, without tests you will be reluctant to make changes because of the fear that you will introduce undetected bugs."

> "Readability is perhaps even more important in unit tests than it is in production code."

> "Three laws of TDD: 1. You are not allowed to write any production code unless it is to make a failing unit test pass. 2. You are not allowed to write any more of a unit test than is sufficient to fail (and not compiling is failing). 3. You are not allowed to write any more production code than is sufficient to pass the one failing unit test."

> "Well-written unit tests are also expressive. A primary goal of tests is to act as documentation by example. Someone reading our tests should be able to get a quick understanding of what a class is all about."

→ Essas ideias reforçam que testes não são apenas uma verificação técnica, mas um componente fundamental da saúde do código e da capacidade da equipe de evoluir o sistema com confiança.

## 4. Relação com Nossos Sistemas

- **Testes pouco mantidos**: Identificamos que aproximadamente 30% dos testes em nosso código legado são comentados ou desabilitados, reduzindo nossa capacidade de refatorar com segurança.
- **Falta de clareza nos testes**: Muitos testes usam nomes genéricos como `testMethod()` e contêm lógica complexa de setup, dificultando a compreensão do comportamento esperado.
- **Dependência de ambiente**: Alguns testes de integração dependem de configurações específicas de ambiente, tornando-os não-repeatable e frágeis.
- **Testes lentos**: Nossa suíte de testes de integração leva mais de 45 minutos para executar, violando o princípio de que "testes devem ser rápidos".
- **Falta de cobertura estratégica**: Temos alta cobertura em código simples, mas baixa cobertura em áreas complexas de negócio, onde os testes seriam mais valiosos.
- **Testes como validação única**: Muitas vezes escrevemos testes apenas para verificar comportamento, não para documentar intenção ou especificar o domínio.

## 5. Decisões de Design ou Padrões Adotar

- **Princípios F.I.R.S.T. como padrão obrigatório**:
  - Testes unitários devem executar em < 100ms
  - Nenhum teste deve depender de outros ou de estado global
  - Testes devem ser executáveis em qualquer ambiente (CI, máquina local)
  - Todos os testes devem ter resultado booleano claro (passa/falha)
  - Testes devem ser escritos antes do código de produção no TDD

- **Padrão de escrita de testes**:
  - Formato Given-When-Then explícito nos comentários ou estrutura
  - Um único conceito por teste (máximo de 1 assert por teste, ou múltiplos apenas se testando uma única propriedade)
  - Nomes descritivos no formato `methodName_scenario_expectedResult()` (ex: `calculateTotal_withDiscountApplied_returnsCorrectAmount`)
  - Uso de Builders ou Factories para setup complexo de objetos de teste

- **Métricas de qualidade de testes**:
  - Taxa mínima de 80% de cobertura em novos módulos (foco em caminhos de negócio críticos)
  - Tempo máximo de 10 minutos para execução completa da suíte de testes unitários
  - 70% de testes unitários, 20% de testes de integração, 10% de testes E2E como proporção ideal
  - Taxa de falhas intermitentes (flaky tests) deve ser < 1%

- **Processo de manutenção de testes**:
  - Revisão de testes em cada pull request com mesmo rigor que código de produção
  - Refatoração de testes sempre que a legibilidade for comprometida
  - Política de "nenhum teste comentado ou ignorado sem justificativa documentada"
  - "Teste limpo" como critério de aceitação para conclusão de tarefas

- **DSL para testes de domínio**:
  - Desenvolver APIs específicas para testes que expressem conceitos de domínio
  - Exemplo: `given().aCustomerWithDiscount().when().makesAPurchase().then().receivesCorrectTotal()`

## 6. Questões para Estudo Futuro

- Como equilibrar a necessidade de testes completos com a necessidade de velocidade na entrega contínua?
- Qual é a abordagem ideal para testar código legado sem testes, sem cair na armadilha de escrever testes ruins apenas para aumentar a cobertura?
- Como medir objetivamente a qualidade dos testes além da simples cobertura de código?
- Como aplicar princípios de TDD em sistemas com forte dependência de UI ou em ambientes altamente regulados onde a documentação formal é prioritária?
- Qual é o limite entre testes de unidade e testes de integração, e como definir essa fronteira de forma consistente na equipe?
- Como treinar equipes para valorizar a qualidade dos testes com o mesmo rigor que valorizam o código de produção?