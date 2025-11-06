# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 7 – "Error Handling" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **Exceções versus códigos de retorno**: Preferir exceções a códigos de retorno para comunicação de erros, pois permitem separar claramente a lógica de negócios do tratamento de erros.
- **Escrever blocos try-catch primeiro**: Ao implementar funcionalidades que envolvem recursos externos, começar com a estrutura try-catch para definir claramente os limites do código transacional.
- **Exceções não verificadas (unchecked)**: Em aplicações gerais, exceções não verificadas são preferíveis às verificadas (checked), pois não criam acoplamento excessivo entre módulos.
- **Contexto nas mensagens de erro**: Cada exceção deve conter informações suficientes para identificar a origem e contexto do erro, não apenas o fato de que ocorreu.
- **Classes de exceção específicas**: Definir classes de exceção em termos das necessidades do chamador, não da implementação, criando uma hierarquia lógica.
- **Padrão como exceção (Define the Normal Flow)**: Usar o padrão SPECIAL CASE para evitar checagens repetitivas de null ou condições especiais.
- **Nunca retornar null**: Retornar objetos especiais (Special Case Pattern) ou Optional ao invés de null para evitar NullPointerExceptions.
- **Nunca passar null**: Validar parâmetros de entrada e rejeitar explicitamente valores null.
- **Separação de responsabilidades**: Tratamento de erros é uma única responsabilidade que deve ser isolada da lógica de negócios.
- **Encapsular blocos try/catch**: Extrair o corpo dos blocos try e catch para funções dedicadas, mantendo o foco na intenção.
- **Fluxo normal definido claramente**: A leitura do código deve seguir o caminho feliz primeiro, com tratamento de erros isolado.

## 3. Frases ou Ideias que Trouxeram Clareza

> "Don't mix error processing with normal processing. So it is better to extract the bodies of the try and catch blocks out into functions of their own."

> "When you use exceptions rather than error codes, then new exceptions are derivatives of the exception class. They can be added without forcing any recompilation or redeployment."

> "Each exception that you throw should provide enough context to determine the source and location of an error. In Java, you can get a stack trace from any exception; however, a stack trace can't tell you the intent of the operation that failed."

> "Don't return null. If you are tempted to return null from a method, consider throwing an exception or returning a special case object instead."

> "Don't pass null. If you are tempted to pass a null argument to a method, consider not doing it."

→ Essas ideias reforçam que o tratamento de erros não deve ser uma preocupação secundária, mas parte integrante do design do sistema. Um bom tratamento de erros torna o código mais robusto, legível e mantível, permitindo que a lógica de negócios permaneça clara e focada.

## 4. Relação com Nossos Sistemas

- **Mistura de responsabilidades**: Identificamos em nosso serviço de processamento de pedidos código que mistura validação de estoque com tratamento de exceções de banco de dados.
- **Uso excessivo de null**: Vários serviços retornam null em casos de entidade não encontrada, levando a NullPointerExceptions difíceis de diagnosticar.
- **Códigos de retorno em APIs REST**: Nossa API de autenticação usa códigos de status HTTP genéricos sem contexto suficiente para o cliente.
- **Blocos try/catch complexos**: Em nosso módulo de integração com gateways de pagamento, vemos blocos try/catch com lógica complexa misturada com tratamento de erros.
- **Falta de hierarquia de exceções**: Usamos genericamente RuntimeException em vários pontos, dificultando o tratamento específico de diferentes tipos de erro.
- **Parâmetros null não validados**: Muitas funções não verificam parâmetros de entrada, assumindo que serão válidos, levando a falhas inesperadas.

## 5. Decisões de Design ou Padrões Adotar

- **Hierarquia de exceções específica do domínio**:
  - Criar uma árvore de exceções alinhada com conceitos de negócio (ex: `OrderProcessingException`, `PaymentValidationException`)
  - Evitar usar exceções genéricas do framework em camadas de domínio
  - Padronizar códigos de erro específicos para cada exceção de domínio

- **Política rigorosa para valores null**:
  - Nunca retornar null de métodos públicos (usar Optional ou Special Case Pattern)
  - Validar todos os parâmetros de entrada e lançar IllegalArgumentException para nulls
  - Usar anotações @NonNull em IDEs que suportam (ex: IntelliJ, Eclipse)
  - Implementar um helper `requireNonNull` com mensagens de erro descritivas

- **Estrutura de tratamento de erros**:
  - Sempre usar exceções em vez de códigos de retorno para comunicação de erros
  - Isolar blocos try/catch em funções dedicadas (ex: `executeWithRetry()`, `processWithFallback()`)
  - Criar um handler global para conversão de exceções em respostas de API consistentes
  - Separar claramente o fluxo normal do fluxo de exceção na estrutura do código

- **Contexto rico em mensagens de erro**:
  - Incluir IDs de correlação em todas as mensagens de erro para rastreamento
  - Fornecer informações suficientes para diagnóstico sem expor detalhes de implementação
  - Padronizar formato das mensagens de erro (ex: "Erro [CÓDIGO]: [DESCRIÇÃO] - Contexto: [VARIÁVEIS]")
  - Usar objetos de erro estruturados em APIs REST com campos específicos

- **Padrão SPECIAL CASE para casos especiais**:
  - Implementar objetos EmptyList ao invés de retornar null para coleções
  - Criar objetos GuestUser ao invés de retornar null para usuários não autenticados
  - Usar Optional<T> consistentemente em Java 8+

- **Testes para cenários de erro**:
  - Garantir que todos os caminhos de exceção tenham testes específicos
  - Testar a mensagem de erro e contexto fornecido pelas exceções
  - Validar que o sistema recupera adequadamente de erros esperados

## 6. Questões para Estudo Futuro

- Como balancear a granularidade das exceções sem criar complexidade excessiva na hierarquia?
- Qual é a melhor abordagem para tratamento de erros em sistemas distribuídos com múltiplas camadas?
- Como lidar com exceções em APIs públicas sem expor detalhes sensíveis de implementação?
- Como monitorar, analisar e responder proativamente a padrões de exceção em produção?
- Como treinar equipes para escreverem mensagens de erro realmente úteis e diagnosticáveis?
- Qual é a melhor prática para lidar com exceções em sistemas legados que usam extensivamente códigos de retorno?
- Como integrar o tratamento de erros com sistemas de logging e monitoramento de forma consistente?
- Como aplicar esses princípios em linguagens que não têm suporte nativo a exceções (ex: Go)?