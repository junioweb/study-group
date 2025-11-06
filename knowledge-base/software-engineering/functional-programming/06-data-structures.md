# 📘 Registro de Aprendizados

## 1. Referência da Leitura
- Capítulo 5 – "Data Types, Tuples and Lists" (Craft of Functional Programming)
- Capítulo 14 – "Algebraic Types" (Craft of Functional Programming)
- Seções 16.3-16.8 – "Queues", "Sets", "Relations and Graphs" (Craft of Functional Programming)
- Artigo: "Conception, Evolution, and Application of Functional Programming Languages" (Paul Hudak, 1989) - Seções 2.3 (Data Abstraction)

## 2. Conceitos-Chave Identificados
- **Listas e tuplas**: Estruturas fundamentais em Haskell onde as listas são homogêneas (`[a]`) e as tuplas heterogêneas (`(a,b,c)`).
- **Tipos algébricos**: Combinação de *product types* (múltiplos campos em um único construtor) e *sum types* (múltiplos construtores alternativos).
  ```haskell
  data Shape = Circle Float | Rectangle Float Float  -- Sum type
  data Person = Person String Int                    -- Product type
  ```
- **Estruturas de dados recursivas**: Definições que se referem a si mesmas, como árvores binárias:
  ```haskell
  data Tree a = Nil | Node a (Tree a) (Tree a)
  ```
- **Estruturas de dados persistentes**: Imutabilidade como princípio fundamental, onde operações como inserção e remoção criam novas estruturas compartilhando partes das anteriores.
- **Tipos polimórficos**: Estruturas de dados que funcionam com múltiplos tipos:
  ```haskell
  data List a = Nil | Cons a (List a)
  ```
- **Abstração de dados**: Ocultamento da representação interna através de interfaces bem definidas (abstract data types).
- **Transformações de dados**: Uso de funções como `map`, `filter`, e `fold` para transformar estruturas imutáveis sem efeitos colaterais.

## 3. Insights e Reflexões
> "A principal vantagem das estruturas de dados avaliadas preguiçosamente vem de sua capacidade de separar dados de controle. A ideia é que um programador deve poder descrever uma estrutura de dados específica sem se preocupar com como ela será avaliada."

Esta perspectiva revela como a imutabilidade e a avaliação preguiçosa permitem modelar problemas focando na estrutura dos dados, não no processo de mutação. Isso resulta em código mais declarativo e menos propenso a erros.

> "Em um programa tradicional, pode ser muito eficiente fazer isso na prática, pois poderíamos ter que computar uma série de estruturas de dados complexas que são 'internas' ao programa. Em uma linguagem preguiçosa, estas são construídas apenas conforme necessário, e na prática podem nunca ser construídas completamente."

Este trecho ilustra como a imutabilidade combinada com avaliação sob demanda pode ser mais eficiente que a mutação em certos cenários, construindo estruturas incrementais que são coletadas quando não mais necessárias.

> "Tipos algébricos combinam recursão e polimorfismo, e esta mistura poderosa fornece tipos que podem ser reutilizados em muitas situações diferentes."

A capacidade de criar tipos que modelam precisamente o domínio do problema (como `data Edit = Change Char | Copy | Delete | Insert Char | Kill`) transforma o processo de desenvolvimento, tornando estados inválidos impossíveis de representar e documentando a intenção do design através do próprio sistema de tipos.

> "O tipo de uma função é a peça mais importante de documentação para a definição, pois descreve precisamente como a função deve ser usada."

Em contextos de estruturas imutáveis, os tipos se tornam ainda mais importantes como especificações de comportamento, já que a ausência de efeitos colaterais significa que funções são puramente transformações de dados.

## 4. Aplicações Práticas
- **Árvores de busca balanceadas**:
  ```haskell
  data SearchTree a = Empty | Node (SearchTree a) a (SearchTree a)
                      deriving (Eq, Show)
  
  insert :: Ord a => a -> SearchTree a -> SearchTree a
  insert x Empty = Node Empty x Empty
  insert x (Node l y r)
    | x < y     = Node (insert x l) y r
    | x > y     = Node l y (insert x r)
    | otherwise = Node l y r
  ```
  Cada inserção cria uma nova árvore compartilhando estrutura com a original, preservando o acesso à versão anterior.

- **Grafos como estruturas imutáveis**:
  ```haskell
  type Relation a = Set (a, a)
  
  addEdge :: Ord a => a -> a -> Relation a -> Relation a
  addEdge x y rel = union rel (makeSet [(x, y)])
  
  -- Busca de caminhos sem modificar a estrutura original
  paths :: Ord a => Relation a -> a -> a -> [[a]]
  ```

- **Transformações de dados em pipeline**:
  ```haskell
  processDatabase :: Database -> Report
  processDatabase = generateReport
                  . filter overdueBooks
                  . map calculateFees
                  . sortBy borrowerName
  ```
  Cada etapa do pipeline produz uma nova estrutura imutável, permitindo composição clara e teste unitário fácil.

- **Estruturas de dados com compartilhamento estrutural**:
  ```haskell
  -- Implementação eficiente de fila com duas listas
  data Queue a = Queue [a] [a]
  
  enqueue :: a -> Queue a -> Queue a
  enqueue x (Queue ins outs) = Queue (x:ins) outs
  
  dequeue :: Queue a -> (Maybe a, Queue a)
  dequeue (Queue [] []) = (Nothing, Queue [] [])
  dequeue (Queue ins []) = dequeue (Queue [] (reverse ins))
  dequeue (Queue ins (o:outs)) = (Just o, Queue ins outs)
  ```
  Esta implementação aproveita o compartilhamento estrutural para operações O(1) amortizadas, demonstrando como estruturas imutáveis podem ser eficientes.

## 5. Decisões de Design ou Padrões Adotar
1. **Modelar domínio com precisão**:
   - Usar tipos algébricos para representar exatamente os estados válidos do problema
   - Tornar estados inválidos impossíveis de representar no tipo

2. **Preferir transformações puras**:
   - Sempre que possível, transformar estruturas inteiras em vez de modificar partes
   - Usar funções como `map` e `zipWith` para transformações estruturadas

3. **Explorar compartilhamento estrutural**:
   - Projetar estruturas de dados que maximizem o reuso de partes não modificadas
   - Entender o trade-off entre espaço e tempo em operações persistentes

4. **Esconder representações internas**:
   - Usar abstract data types com módulos para expor apenas operações seguras
   ```haskell
   module Queue (Queue, empty, enqueue, dequeue) where
   ```

5. **Aproveitar a preguiça estratégica**:
   - Usar avaliação preguiçosa para construir estruturas potencialmente infinitas ou custosas
   - Balancear com pontos de estrita quando necessário para controle de espaço

6. **Composição sobre mutação**:
   - Projetar pipelines de transformação de dados em vez de loops com atualizações
   - Usar folds e unfolds como padrões fundamentais para construção e destruição de estruturas

## 6. Questões em Aberto
1. **Performance em larga escala**: Como estruturas de dados puramente funcionais se comparam com suas contrapartes mutáveis em sistemas de alta performance e grande volume de dados?

2. **Cache e localidade**: Como mitigar os problemas de localidade de cache inerentes às estruturas de dados persistentes que compartilham nodos dispersos na memória?

3. **Tipos dependentes para invariantes complexos**: Até que ponto podemos usar sistemas de tipos avançados para garantir invariantes estruturais (como balanceamento de árvores) em tempo de compilação?

4. **Serialização eficiente**: Como serializar e desserializar estruturas de dados funcionais complexas preservando referências compartilhadas e minimizando espaço?

5. **Integração com sistemas imperativos**: Qual a melhor estratégia para integrar estruturas de dados funcionais imutáveis com bibliotecas e sistemas externos que esperam mutabilidade?

6. **Paralelismo e concorrência**: Como aproveitar a imutabilidade natural das estruturas funcionais para paralelizar operações complexas em árvores e grafos, evitando problemas clássicos de concorrência?