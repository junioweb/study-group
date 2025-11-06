# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 2 – "Meaningful Names" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **Nomes que revelam intenção**: Um bom nome deve responder às grandes questões: por que a entidade existe, o que ela faz e como é usada.
- **Evitar disinformação**: Nomes como `klass` (em vez de `class`) ou `hp` (em vez de `homePage`) criam confusão e má prática.
- **Nomes pronunciáveis**: Facilitam a comunicação entre desenvolvedores (ex: `generationTimestamp` em vez de `genymdhms`).
- **Nomes pesquisáveis**: Variáveis com nomes significativos (ex: `elapsedTimeInDays`) são mais fáceis de encontrar do que nomes genéricos como `d`.
- **Evitar codificações**: Prefixos como `m_` para membros de classe ou notação húngara são desnecessários em ambientes modernos.
- **Nomes de classes**: Devem ser substantivos descritivos (ex: `Customer`, `Account`), nunca termos genéricos como `Data` ou `Info`.
- **Nomes de métodos**: Devem ser verbos ou frases verbais (ex: `postPayment()`, `deletePage()`).
- **Um conceito, uma palavra**: Manter consistência no vocabulário (ex: usar `fetch` ou `retrieve`, mas não ambos para a mesma operação).
- **Contexto significativo**: Encapsular nomes em classes, funções ou namespaces bem nomeados em vez de adicionar prefixos desnecessários.
- **Níveis de abstração**: Nomes devem corresponder ao nível de abstração do código (não misturar detalhes de implementação com conceitos de domínio).

## 3. Frases ou Ideias que Trouxeram Clareza

> "O nome de uma variável, função ou classe deve responder a todas as grandes questões. Deve dizer por que ela existe, o que faz e como é usada. Se um nome requer um comentário, então o nome não revela sua intenção."

> "Nomes curtos são geralmente melhores que nomes longos, desde que sejam claros. Não adicione mais contexto a um nome do que é necessário."

> "Separação de conceitos de domínio da solução é parte do trabalho de um bom programador e designer. O código que tem mais a ver com conceitos do domínio do problema deve ter nomes retirados do próprio domínio do problema."

> "O leitor não deve precisar adivinhar a diferença entre conceitos com nomes similares. Se você tem `getActiveAccount()` e `getActiveAccounts()`, o leitor deve saber imediatamente a diferença."

→ Essas ideias reforçam que a nomenclatura não é apenas uma convenção estética, mas um componente fundamental da documentação do código. Nomes bem escolhidos reduzem drasticamente a necessidade de comentários explicativos.

## 4. Relação com Nossos Sistemas

- **Contextualização de variáveis**: Em nossos serviços REST, podemos melhorar nomes como `response` para `customerCreationResponse` quando necessário, mas evitar excessos como `customerServiceCreateCustomerPostEndpointResponse`.
- **Padronização de verbos**: Estabelecer convenções consistentes para operações (ex: sempre usar `fetch` para obter dados de APIs externas, `retrieve` para acessar cache, `get` para métodos locais).
- **Domínio versus solução**: Em nossos microsserviços, garantir que classes como `OrderProcessor` reflitam conceitos do negócio, enquanto classes técnicas como `KafkaMessageProducer` mantenham claramente seu propósito técnico.
- **Níveis de abstração**: Em nosso módulo de autenticação, substituir nomes como `tokenHandler` por `jwtTokenValidator` para revelar claramente a implementação específica sem perder o foco no propósito.

## 5. Decisões de Design ou Padrões Adotar

- **Nomenclatura de classes**:
  - Sempre usar substantivos descritivos relacionados ao domínio
  - Evitar termos genéricos como "Manager", "Processor" ou "Service" sem contexto adicional
  - Exemplo: `PaymentValidator` em vez de `PaymentService`

- **Nomenclatura de métodos**:
  - Usar verbos claros que revelem intenção
  - Métodos booleanos devem começar com `is`, `has`, `can` (ex: `isValid()`, `hasPermission()`)
  - Métodos que retornam coleções devem indicar pluralidade (ex: `getActiveUsers()`)

- **Nomenclatura de variáveis**:
  - Escopo curto → nomes curtos (ex: `i` em loops)
  - Escopo longo → nomes descritivos (ex: `maximumConcurrentConnections`)
  - Evitar abreviações não óbvias (ex: usar `account` em vez de `acct`)

- **Padrão de nomenclatura para testes**:
  - Estrutura: `[Método]_[Cenário]_[ResultadoEsperado]`
  - Exemplo: `calculateTotal_withDiscountApplied_returnsCorrectAmount`

## 6. Questões para Estudo Futuro

- Como equilibrar nomes descritivos com concisão em linguagens com restrições de tamanho de identificador?
- Qual é o limite ideal para o comprimento de nomes em diferentes contextos (ex: nomes de métodos em interfaces públicas versus código interno)?
- Como aplicar princípios de nomenclatura em projetos com múltiplos idiomas (ex: nomes em inglês para código, mas conceitos do domínio em outro idioma)?
- Como garantir consistência na nomenclatura em equipes grandes com diferentes níveis de experiência?
- Como documentar e fazer cumprir padrões de nomenclatura sem tornar o processo burocrático?