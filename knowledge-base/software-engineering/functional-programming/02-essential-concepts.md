# 📘 Registro de Aprendizados

## 1. Referência da Leitura
Capítulo 2 – "Conceitos Essenciais do Paradigma Funcional"
- Craft of Functional Programming (Simon Thompson, 3rd Edition)
- "Conception, Evolution, and Application of Functional Programming Languages" (Paul Hudak)

## 2. Conceitos-Chave Identificados
- **Funções puras**: Funções cujo resultado depende exclusivamente de seus parâmetros de entrada e que não produzem efeitos colaterais observáveis.
- **Imutabilidade de dados**: Os dados, uma vez criados, não podem ser modificados; operações que parecem alterar dados na verdade criam novas estruturas.
- **Funções como cidadãs de primeira classe**: Funções podem ser atribuídas a variáveis, passadas como argumentos para outras funções e retornadas como resultados.
- **Expressões vs. comandos**: Programação funcional enfatiza expressões (que produzem valores) em vez de comandos (que alteram estado).
- **Avaliação de expressões**: O processo de reduzir uma expressão a um valor através de substituição e simplificação.
- **Transparência referencial**: A propriedade que permite substituir uma expressão por seu valor sem alterar o comportamento do programa.
- **Efeitos colaterais**: Operações que modificam o estado externo à função (como I/O, modificação de variáveis globais) que são minimizados ou estritamente controlados.

## 3. Insights Relevantes

> "A functional program consists of a number of definitions, such as val:: Integer val= 42"

→ As definições em programação funcional são imutáveis por natureza - uma vez definido, o valor não muda, criando uma base sólida para raciocínio sobre o código.

> "In functional programming, we'll focus on values – such as bar codes and bills – and the functions which work over them – giving the total of a bill, say."

→ Este insight fundamental muda nossa mentalidade: em vez de focar em como mudar o estado do programa (como em paradigmas imperativos), focamos em transformar valores através de funções.

> "Referential transparency is often used to describe this style of programming, in which 'equals can be replaced by equals'."

→ A transparência referencial não é apenas um conceito teórico, mas uma propriedade prática que permite otimizações do compilador, testes mais simples e raciocínio matemático sobre o código.

> "Consider a simple functional program like length[] = 0... This is an important example, and shows something that's really distinctive about functional programming, because it uses functions to model data in a completely natural way."

→ A elegância de definir funções recursivamente e raciocinar sobre elas como equações matemáticas demonstra o poder expressivo do paradigma funcional.

> "In the imperative program has an overall effect which is not obvious from the program itself."

→ Esta observação crítica destaca uma das principais limitações dos paradigmas imperativos: o comportamento do programa está disperso em mudanças de estado, dificultando a compreensão e manutenção.

## 4. Aplicações Práticas no Nosso Contexto
- **Transformação de dados imutáveis**: Ao processar dados de usuários ou transações financeiras, podemos usar funções puras para transformar cópias imutáveis dos dados originais, eliminando bugs relacionados a modificações inesperadas.
- **Composição de funções para pipelines de processamento**: Podemos substituir cadeias complexas de operações imperativas por pipelines declarativos usando `map`, `filter` e `fold`, melhorando a legibilidade e testabilidade.
- **Isolamento de efeitos colaterais**: Podemos estruturar nossa aplicação com uma "casca imperativa" (para I/O, banco de dados) envolvendo um "núcleo funcional" (lógica de negócio), seguindo o Hexagonal Architecture.
- **Testes baseados em propriedades**: Utilizando bibliotecas como QuickCheck, podemos testar funções puras verificando propriedades matemáticas em vez de cenários específicos, aumentando a cobertura de testes com menos código.
- **Memoização para performance**: Funções puras podem ter seus resultados armazenados em cache automaticamente, otimizando cálculos repetidos sem alterar a lógica do programa.

## 5. Decisões de Design ou Padrões a Adotar
- **Adotar imutabilidade por padrão**: Utilizar estruturas de dados imutáveis para todos os objetos de domínio e estado da aplicação, usando bibliotecas como Immutable.js ou técnicas nativas da linguagem.
- **Funções puras para lógica de negócio**: Implementar toda a lógica de negócio como funções puras, isolando efeitos colaterais em camadas específicas e bem definidas da aplicação.
- **Expressões em vez de comandos**: Priorizar expressões que retornam valores sobre comandos que modificam estado, especialmente em funções críticas e de alto nível.
- **Nomeação descritiva para transparência**: Nomear funções de forma que seus efeitos colaterais sejam explícitos (ex: `saveUserAndNotify` em vez de apenas `saveUser`).
- **Tipagem explícita**: Declarar tipos específicos para parâmetros e retornos de funções, aproveitando o sistema de tipos para documentação e verificação estática de propriedades.
- **Evitar variáveis temporárias**: Minimizar o uso de variáveis intermediárias mutáveis em favor de pipelines de transformação de dados através de funções compostas.

## 6. Dúvidas ou Pontos a Aprofundar
- Como equilibrar a imutabilidade de dados com requisitos de performance em sistemas de alta escala, onde a criação constante de novos objetos pode impactar o coletor de lixo?
- Qual a melhor abordagem para gerenciar estado global (como configurações ou autenticação) em uma arquitetura predominantemente funcional?
- Como integrar bibliotecas e frameworks existentes (que frequentemente dependem de mutabilidade e efeitos colaterais) em um sistema funcional sem comprometer os benefícios do paradigma?
- Quais são os trade-offs práticos entre avaliação preguiçosa (Haskell) e avaliação estrita (ML, Scheme) em aplicações comerciais de grande porte?
- Como estruturar código funcional para lidar com operações de I/O assíncronas (como chamadas a APIs externas) de forma elegante e testável?