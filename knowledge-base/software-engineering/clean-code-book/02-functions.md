# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 3 – "Functions" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **Funções devem ser pequenas**: O tamanho ideal é de até 20 linhas, com foco em serem ainda menores. Funções curtas facilitam a leitura e compreensão.
- **"Do One Thing"**: Uma função deve fazer uma única coisa e fazê-la bem, sem seções ou divisões internas que indiquem múltiplas responsabilidades.
- **Níveis de abstração consistentes**: Cada função deve operar em um único nível de abstração, evitando misturar detalhes de implementação com conceitos de alto nível.
- **Regra do Stepdown**: O código deve ser lido como um conjunto de parágrafos em ordem top-down, onde cada função chama funções que estão logo abaixo dela no arquivo.
- **Parâmetros ideais**: 
  - Zero argumentos é o ideal
  - Um argumento é aceitável
  - Dois argumentos requer justificativa
  - Três ou mais argumentos devem ser evitados
- **Tipos problemáticos de argumentos**:
  - Argumentos de flag (ex: `boolean isDebugEnabled`)
  - Argumentos de saída (alteram estado externo)
  - Argumentos que são listas ou arrays sem contexto claro
- **Tratamento de erros**:
  - Preferir exceções a códigos de retorno
  - Separar lógica de negócios da lógica de tratamento de erros
  - Extrair blocos try/catch para funções dedicadas
- **Command Query Separation**: Funções devem fazer uma coisa OU outra: alterar estado (command) OU retornar informação (query), nunca ambas.
- **Evitar efeitos colaterais**: Funções devem fazer apenas o que seu nome sugere, sem alterar estado de forma não óbvia.

## 3. Frases ou Ideias que Trouxeram Clareza

> "Functions should be small. After that, they should be smaller. The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that."

> "They [functions] should do one thing. They should do it well. They should do it only."

> "If you have a function that seems to be doing more than 'one thing', try extracting functions from it until you can't extract any more, until all of the functions left are either one of the steps of the stated algorithm or one level of abstraction deeper."

> "The stepdown rule: We want the code to read like a top-down narrative. We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions."

> "Output arguments are counterintuitive. Readers expect arguments to be inputs, not outputs. If your function must change the state of something, have it change the state of the object it is called on."

> "Returning error codes from command functions is a violation of command query separation. Either the function does what it says it does, or it returns an error code. It can't do both."

→ Essas ideias reforçam que a estrutura de funções não é apenas uma questão de estilo, mas fundamental para a manutenibilidade e legibilidade do código. Funções bem estruturadas são a base para sistemas limpos e sustentáveis.

## 4. Relação com Nossos Sistemas

- **Funções excessivamente longas**: Identificamos em nosso módulo de processamento de pagamentos funções com mais de 100 linhas que misturam validação, cálculo e persistência.
- **Parâmetros problemáticos**: Em nosso serviço de notificação, temos funções com 4+ parâmetros, incluindo flags como `isUrgent` e `shouldLog`.
- **Tratamento inconsistente de erros**: Alguns serviços usam códigos de retorno, outros lançam exceções, criando inconsistência na forma como os erros são tratados.
- **Efeitos colaterais não óbvios**: Funções de atualização de perfil que também disparam eventos sem que isso seja evidente pelo nome.
- **Níveis de abstração misturados**: Em nosso controller de API, vemos operações de parsing HTTP ao lado de lógica de domínio complexa.

## 5. Decisões de Design ou Padrões Adotar

- **Tamanho máximo de funções**:
  - 15 linhas para funções de domínio
  - 25 linhas para funções técnicas complexas (com justificativa documentada)
  - Nenhuma função deve ter mais de 30 linhas sem revisão explícita

- **Padrões para parâmetros**:
  - Evitar flags como parâmetros (substituir por estratégias ou objetos de configuração)
  - Para mais de 2 parâmetros, usar objetos de argumento (ex: `PaymentRequestOptions`)
  - Nunca usar argumentos de saída (substituir por objetos de retorno ou alterar estado do objeto receptor)

- **Padrões para tratamento de erros**:
  - Sempre usar exceções para cenários de erro, nunca códigos de retorno
  - Criar uma hierarquia clara de exceções específicas do domínio
  - Isolar blocos try/catch em funções dedicadas (ex: `executeWithRetry()`)
  - Implementar um handler global para conversão de exceções em respostas de API

- **Padrão de estrutura para funções**:
  - Nome da função deve começar com verbo claro (ex: `calculateTotal()`, não `total()`)
  - Funções de comando não devem retornar valores (void) ou retornar o próprio objeto para encadeamento
  - Funções de query devem ser puras (sem efeitos colaterais)
  - Usar a regra Stepdown na organização do código fonte

- **Padrão para testes de funções**:
  - Cada função deve ter testes que verifiquem seu comportamento único
  - Testes devem seguir a estrutura Given-When-Then
  - Testes para caminhos de exceção devem ser explícitos

## 6. Questões para Estudo Futuro

- Como balancear a necessidade de funções pequenas com a performance em sistemas críticos de tempo real?
- Qual é a melhor abordagem para funções que naturalmente requerem vários parâmetros (ex: funções matemáticas complexas)?
- Como aplicar esses princípios em linguagens funcionais onde a composição de funções é mais comum?
- Como medir objetivamente a qualidade da estrutura de funções em um código base existente?
- Como lidar com frameworks que impõem estruturas específicas (como controllers em Spring) que naturalmente tendem a ser maiores?
- Como treinar equipes para reconhecer e refatorar funções que violam esses princípios sem introduzir bugs?