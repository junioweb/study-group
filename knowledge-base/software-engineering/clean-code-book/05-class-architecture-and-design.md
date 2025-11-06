# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 10 – "Classes" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **Princípio da Responsabilidade Única (SRP)**: Uma classe deve ter uma, e apenas uma, razão para mudar. Isso define claramente o que é uma responsabilidade - uma razão para mudar.
- **Classes pequenas são melhores**: Assim como funções, classes devem ser pequenas, com foco em uma única responsabilidade.
- **Coesão**: Classes altamente coesas têm métodos que dependem uns dos outros e compartilham um propósito comum. A coesão está diretamente relacionada ao SRP.
- **Acoplamento mínimo**: Classes devem depender o mínimo possível de outras classes, especialmente de implementações específicas.
- **Separação de preocupações**: Dividir o sistema em camadas ou módulos que abordam preocupações específicas (domínio, infraestrutura, UI).
- **Organização para facilitar mudanças**: Estruturar o código de forma que mudanças em uma parte do sistema não afetem desnecessariamente outras partes.
- **Isolamento de mudanças**: Criar abstrações que permitam que partes do sistema mudem sem afetar outras.
- **Princípio Aberto-Fechado (OCP)**: Classes devem estar abertas para extensão, mas fechadas para modificação.
- **Encapsulamento efetivo**: Esconder detalhes de implementação e expor apenas a interface necessária.
- **Classes como substantivos de uma linguagem**: Boas classes representam conceitos do domínio, não apenas estruturas técnicas.

## 3. Frases ou Ideias que Trouxeram Clareza

> "The Single Responsibility Principle (SRP) states that a class or module should have one, and only one, reason to change. This principle gives us both a definition of responsibility, and a guideline for class size. Classes should have one responsibility—one reason to change."

> "To restate the former points for emphasis: We want our systems to be composed of many small classes, not a few large ones. Each small class encapsulates a single responsibility, has a single reason to change, and collaborates with a few others to achieve the desired system behaviors."

> "Our restructured Sql logic represents the best of all worlds. It supports the SRP. It also supports another key OO class design principle known as the Open-Closed Principle, or OCP: Classes should be open for extension but closed for modification."

> "We code the logic to build update statements in a new subclass of Sql named UpdateSql. No other code in the system will break because of this change."

> "Trying to identify responsibilities (reasons to change) often helps us recognize and create better abstractions in our code."

→ Essas ideias reforçam que o design de classes não é apenas uma questão estética, mas fundamental para a capacidade do sistema evoluir com segurança. Classes bem projetadas são a base para sistemas flexíveis e sustentáveis a longo prazo.

## 4. Relação com Nossos Sistemas

- **Classes com múltiplas responsabilidades**: Identificamos em nosso serviço de pedidos uma classe `OrderProcessor` que lida com validação, persistência, notificação e integração com gateways de pagamento.
- **Falta de coesão**: Em nosso módulo de autenticação, a classe `AuthManager` contém métodos não relacionados como `validateToken()`, `sendPasswordResetEmail()` e `calculateSessionTimeout()`.
- **Acoplamento excessivo**: Nossa camada de domínio depende diretamente de implementações específicas de repositórios, dificultando testes e substituição de tecnologias.
- **Classes grandes e complexas**: Temos classes com mais de 500 linhas que violam claramente o princípio de que "classes devem ser pequenas".
- **Falta de abstração para isolamento de mudanças**: Alterações em integrações externas frequentemente exigem mudanças em múltiplas partes do sistema devido à falta de interfaces bem definidas.
- **Violação do OCP**: Para adicionar novos tipos de pagamento, precisamos modificar a classe `PaymentProcessor` existente em vez de estendê-la.

## 5. Decisões de Design ou Padrões Adotar

- **Princípio da Responsabilidade Única como regra fundamental**:
  - Cada classe deve ter no máximo uma razão para mudar
  - Critério de validação: Perguntar "quais são as razões pelas quais esta classe precisaria ser modificada?"
  - Classes com mais de 200 linhas requerem revisão explícita e justificativa

- **Padrões para coesão e tamanho de classes**:
  - Tamanho ideal: 50-200 linhas (dependendo da complexidade do domínio)
  - Número máximo de métodos por classe: 10-15
  - Usar a métrica de coesão: métodos devem compartilhar variáveis de instância
  - Classes com métodos que não usam variáveis de instância podem ser candidatas a métodos estáticos ou classes utilitárias

- **Estratégias para separação de preocupações**:
  - Adotar arquitetura em camadas claramente definidas (domínio, aplicação, infraestrutura)
  - Criar interfaces para dependências externas (repositórios, serviços externos)
  - Implementar padrão de inversão de dependência (Dependency Injection)
  - Separar construção do sistema do uso do sistema (Separation of Main)

- **Padrões para organização visando mudanças**:
  - Implementar OCP através de herança ou composição
  - Criar classes abstratas para conceitos de alto nível
  - Usar o padrão Strategy para algoritmos que podem variar
  - Implementar o padrão Adapter para integrações com sistemas externos
  - Utilizar o padrão Decorator para adicionar responsabilidades dinamicamente

- **Padrões de nomenclatura para classes**:
  - Usar substantivos descritivos relacionados ao domínio (ex: `OrderValidator`, não `OrderProcessor`)
  - Evitar termos genéricos como "Manager", "Handler", "Service" sem contexto adicional
  - Classes técnicas devem ter nomes que revelem seu propósito específico (ex: `JwtTokenValidator`, não `SecurityUtil`)

## 6. Questões para Estudo Futuro

- Como determinar objetivamente quando uma classe está violando o SRP em casos borderline?
- Qual é o equilíbrio ideal entre criar muitas classes pequenas versus poucas classes maiores?
- Como aplicar esses princípios em sistemas legados com classes extremamente grandes e acopladas?
- Como medir quantitativamente a coesão e o acoplamento para monitorar a saúde da arquitetura?
- Qual é a melhor abordagem para refatorar sistemas para melhorar a separação de preocupações sem introduzir riscos?
- Como treinar equipes para reconhecer e aplicar o SRP consistentemente em diferentes contextos?
- Como aplicar esses princípios em linguagens que não suportam herança ou interfaces (ex: JavaScript)?
- Qual é a relação entre microserviços e o SRP em nível de sistema?