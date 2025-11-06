# 📘 Registro de Aprendizados

## 1. Referência da Leitura
- Capítulo 13 – "Overloading, Type Classes and Type Checking" (Craft of Functional Programming)
- Capítulo 14 – "Algebraic Types" (Craft of Functional Programming)
- Artigo: "Conception, Evolution, and Application of Functional Programming Languages" (Paul Hudak, 1989)

## 2. Conceitos-Chave Identificados
- **Sistema de tipos estático e forte**: Haskell verifica tipos em tempo de compilação, evitando erros de execução relacionados a tipos.
- **Inferência de tipos Hindley-Milner**: Sistema que permite deduzir automaticamente os tipos das expressões sem anotações explícitas.
- **Tipos algébricos**: Estruturas de dados definidas por construtores, incluindo:
  - *Tipos soma* (union types): múltiplos construtores (ex: `data Shape = Circle Float | Rectangle Float Float`)
  - *Tipos produto*: único construtor com múltiplos campos (ex: `data Person = Person String Int`)
  - *Tipos recursivos*: auto-referenciais (ex: `data Tree a = Leaf a | Node (Tree a) (Tree a)`)
- **Classes de tipos**: Mecanismo para definir interfaces polimórficas (ex: `class Eq a where (==) :: a -> a -> Bool`)
- **Polimorfismo paramétrico**: Funções que operam uniformemente sobre diferentes tipos (ex: `map :: (a -> b) -> [a] -> [b]`)
- **Polimorfismo ad-hoc (sobrecarga)**: Funções que se comportam diferentemente dependendo do tipo (ex: operador `+` para inteiros e floats)

## 3. Insights e Reflexões
> "O tipo de uma função é a peça mais importante de documentação para a definição, pois descreve precisamente como a função deve ser usada."

Esta afirmação do texto reforça como os tipos em Haskell servem como documentação viva, muito mais precisa que comentários que podem ficar desatualizados.

> "Polimorfismo e sobrecarga são mecanismos pelos quais o mesmo nome de função pode ser usado em diferentes tipos, mas com uma diferença importante: uma função polimórfica como fst tem a mesma definição em todos os tipos."

Esta distinção crucial entre polimorfismo paramétrico (mesma implementação) e sobrecarga (implementações diferentes) ajuda a entender quando devemos usar classes de tipos versus funções polimórficas.

> "Hindley e Milner independentemente descobriram um sistema de tipos polimórfico restrito que é quase tão rico quanto o cálculo lambda tipado completo e para o qual a inferência de tipos é decidível."

Isso revela a elegância do sistema de tipos de Haskell/ML – um equilíbrio entre expressividade e praticidade, permitindo inferência automática de tipos sem sacrificar muito poder expressivo.

> "Em programação funcional, concentramo-nos em valores e nas funções que operam sobre eles. Cada paradigma de programação nos dá ferramentas diferentes para modelar situações."

Essa perspectiva ajuda a entender por que sistemas de tipos ricos são fundamentais para a programação funcional – eles estruturam e validam os valores que transformamos através de funções.

## 4. Aplicações Práticas
- **Modelagem de domínio com tipos algébricos**: Podemos criar tipos que representam exatamente os estados válidos de nosso domínio, tornando estados inválidos impossíveis de representar. Por exemplo, um tipo para edit distance:
  ```haskell
  data Edit = Change Char | Copy | Delete | Insert Char | Kill
  ```
- **Classes de tipos para extensibilidade**: Podemos definir operações genéricas como:
  ```haskell
  class Movable a where
    move :: Vector -> a -> a
    reflectX :: a -> a
  ```
  E depois implementar para diferentes tipos (formas geométricas, listas de objetos, etc.), permitindo extensão sem modificar código existente.
- **Tipos como especificação formal**: Tipos complexos podem capturar invariantes do sistema, como em:
  ```haskell
  data Tree a = Nil | Node a (Tree a) (Tree a) 
  ```
  Que garante que uma árvore binária sempre terá a estrutura correta.
- **Combinação de técnicas**: Usar tipos algébricos com classes de tipos para criar sistemas modulares:
  ```haskell
  instance (Eq a) => Eq (Tree a) where
    -- implementação que depende da igualdade do tipo parâmetro
  ```

## 5. Decisões de Design ou Padrões Adotar
1. **Sempre declarar tipos para funções de nível superior**: Embora o compilador possa inferir tipos, declarações explícitas servem como documentação essencial e verificação de intenção.
2. **Modelar primeiro os tipos, depois as funções**: Antes de implementar funcionalidades, definir os tipos que representam o domínio do problema.
3. **Preferir polimorfismo paramétrico quando possível**: Funções que funcionam uniformemente em todos os tipos são mais fáceis de raciocinar e mais reutilizáveis.
4. **Usar classes de tipos para sobrecarga controlada**: Em vez de múltiplas funções com nomes diferentes para operações similares em tipos diferentes, usar classes de tipos para unificar interfaces.
5. **Explorar derivação de instâncias**: Para classes padrão como `Eq`, `Ord`, `Show`, usar o mecanismo de derivação quando a implementação padrão for adequada:
   ```haskell
   data Shape = Circle Float | Rectangle Float Float deriving (Eq, Show)
   ```

## 6. Questões em Aberto
1. **Trade-offs entre expressividade do sistema de tipos e complexidade**: Até que ponto devemos utilizar recursos avançados como tipos dependentes ou tipos de alta ordem antes que o custo cognitivo supere os benefícios?
2. **Integração com sistemas externos**: Como manter a pureza e as garantias do sistema de tipos quando interagimos com APIs externas, bancos de dados ou sistemas legados que não compartilham as mesmas garantias?
3. **Evolução de tipos em sistemas grandes**: Como gerenciar mudanças em tipos fundamentais em uma base de código extensa sem quebrar compatibilidade?
4. **Performance versus abstração**: Em que situações o uso de abstrações de alto nível (como classes de tipos) impacta significativamente a performance, e como mitigar esses efeitos?
5. **Tipos como ferramenta de design**: Como usar o sistema de tipos desde as fases iniciais de projeto para descobrir requisitos e invariantes que poderiam passar despercebidos em linguagens com sistemas de tipos mais fracos?