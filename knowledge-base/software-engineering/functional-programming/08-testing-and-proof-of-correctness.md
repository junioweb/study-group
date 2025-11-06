# 📘 Registro de Aprendizados

## 1. Referência da Leitura
- Capítulo 9 – "Reasoning about programs" (Craft of Functional Programming)
- Seções 4.8, 19.6 – "Program testing" e "DSLs for computation: generating data in QuickCheck"
- Seção 1.14 – "Tests, properties and proofs" (Craft of Functional Programming)
- Artigo: "Conception, Evolution, and Application of Functional Programming Languages" (Paul Hudak, 1989), Seção 4 "Dispelling Myths About Functional Programming"

## 2. Conceitos-Chave Identificados
- **Property-based testing**: Abordagem que verifica se propriedades mantêm-se verdadeiras para um grande número de casos gerados aleatoriamente.
- **QuickCheck**: Framework para property-based testing em Haskell que gera automaticamente dados de teste com base nos tipos e permite "encolher" contra-exemplos para suas formas mínimas.
- **Testes unitários (HUnit)**: Verificação de comportamento em casos específicos pré-definidos.
- **Prova por indução estrutural**: Técnica de demonstração matemática que segue a estrutura recursiva de tipos de dados (especialmente listas), com casos base e indutivo.
- **Equational reasoning**: Capacidade de tratar definições como equações matemáticas e substituir expressões equivalentes, possível graças à transparência referencial.
- **Invariantes de estruturas de dados**: Propriedades que devem permanecer verdadeiras após operações (ex: listas ordenadas permanecem ordenadas após inserção).
- **Definibilidade e terminação**: Conceitos fundamentais para raciocínio sobre programas, distinguindo entre valores totalmente definidos e indefinidos (bottom).
- **White box testing**: Estratégia de teste que considera a implementação interna para escolher dados representativos de diferentes caminhos de execução.

## 3. Insights e Reflexões
> "Property-based testing – as given by QuickCheck – provides much better coverage, but it is still possible that the places where an error occurs could be missed by randomly generating data; proof avoids this, and gives an error 'nowhere to hide'."

Esta citação revela um trade-off fundamental: testes dão certeza local com grande cobertura, enquanto provas oferecem certeza global mas com maior custo cognitivo. A combinação estratégica das duas abordagens é ideal para sistemas críticos.

> "If definitions are equations, then it's possible to think of them as expressing properties of programs, and we can use these to write proofs of other properties our programs have, and so validate what they do."

Este insight é transformador: em linguagens funcionais puras, as definições não são apenas instruções executáveis, mas verdadeiras igualdades matemáticas que podem ser raciocinadas formalmente. Isso muda completamente como pensamos sobre programas.

> "The type of a function is the piece most important documentation for the definition, because it describes precisely how the function should be used."

Os tipos em Haskell não são apenas verificações de segurança, mas especificações formais que podem ser incorporadas em propriedades de teste e teoremas a serem provados, unificando documentação, teste e prova.

> "One way of looking at the IO types is that they provide a small imperative programming language for writing I/O programs on top of Haskell, without compromising the functional model of Haskell itself."

Esta perspectiva sobre mônadas revela como Haskell mantém a pureza funcional enquanto permite efeitos colaterais controlados, facilitando o raciocínio formal sobre programas que interagem com o mundo externo.

> "If we're able to generate random terms of type Expr or Command then we can write some elegant QuickCheck tests by 'round tripping' from expression to string – via a show function – and then back to an expression by parsing the string."

Esta técnica de teste de ida e volta (round-tripping) demonstra como o sistema de tipos orienta a criação de propriedades robustas que validam invariantes estruturais complexos.

## 4. Aplicações Práticas
- **Testes de propriedades para funções de lista**:
  ```haskell
  prop_reverseLength :: [Int] -> Bool
  prop_reverseLength xs = length (reverse xs) == length xs
  
  prop_mapFilter :: (Int -> Bool) -> (Int -> Int) -> [Int] -> Bool
  prop_mapFilter p f xs = filter p (map f xs) == map f (filter (p . f) xs)
  ```
  QuickCheck verifica automaticamente centenas de casos, gerando contra-exemplos quando a propriedade falha.

- **Prova por indução para concatenação de listas**:
  Desejamos provar que `length (xs ++ ys) == length xs + length ys`:
  - **Caso base**: `xs = []`
    `length ([] ++ ys) = length ys = 0 + length ys = length [] + length ys`
  - **Passo indutivo**: Assumindo verdadeiro para `xs`, provar para `(x:xs)`
    `length ((x:xs) ++ ys) = length (x:(xs++ys)) = 1 + length (xs++ys)`
    `= 1 + (length xs + length ys) = (1 + length xs) + length ys = length (x:xs) + length ys`

- **Equational reasoning para refatoração**:
  Transformamos `(filter p . map f) xs` em `(map f . filter (p . f)) xs` com base nas propriedades de map e filter, garantindo equivalência através de raciocínio matemático.

- **Testes de invariantes para estruturas de dados**:
  ```haskell
  prop_setInsert :: Int -> Set Int -> Bool
  prop_setInsert x s = member x (insert x s) &&
                      size (insert x s) <= size s + 1
  ```
  Estas propriedades verificam que operações mantêm os invariantes fundamentais de conjuntos.

- **Teste de ida e volta para codificação/decodificação**:
  ```haskell
  prop_huffmanRoundTrip :: String -> Bool
  prop_huffmanRoundTrip s = decode (code s) == s
  ```
  Propriedade fundamental para sistemas de compressão que deve manter a integridade dos dados.

## 5. Decisões de Design ou Padrões Adotar
1. **Hierarquia de validação**: Priorizar tipos expressivos > propriedades QuickCheck > provas formais > testes unitários específicos.
   
2. **Propriedades como documentação**: Escrever propriedades QuickCheck para todas as funções de nível superior, servindo como especificação executável.
   ```haskell
   -- Documentação através de propriedades
   -- map preserva o comprimento da lista
   prop_mapLength :: (a -> b) -> [a] -> Bool
   prop_mapLength f xs = length (map f xs) == length xs
   ```

3. **Abordagem de teste guiada por tipos**: Utilizar os tipos para gerar dados de teste relevantes, especialmente para tipos algébricos complexos.

4. **Estruturar código para facilitar provas**:
   - Manter funções pequenas com responsabilidades únicas
   - Usar definições equacionais sempre que possível
   - Evitar efeitos colaterais no núcleo do sistema

5. **Processo de debug matemático**: Quando uma propriedade falha no QuickCheck:
   - Analisar o contra-exemplo mínimo gerado
   - Tentar provar formalmente a propriedade para identificar a falha
   - Corrigir o código ou reformular a propriedade

6. **Integração contínua de validação**: Executar testes QuickCheck automaticamente em cada commit, com cobertura mínima de propriedades para módulos críticos.

7. **Padrão de propriedades complementares**:
   - Propriedades de igualdade: `f x == g x`
   - Propriedades de invariante: `p (f x) == True`
   - Propriedades relacionais: `g (f x) == x` (quando aplicável)

## 6. Questões em Aberto
1. **Escalabilidade de provas formais**: Como escalar técnicas de prova para sistemas complexos com múltiplos componentes interagindo? Existem abordagens modulares para composição de provas?

2. **Testagem de propriedades de ordem superior**: Como testar efetivamente propriedades que envolvem funções como entradas, considerando o espaço infinito de possíveis funções?

3. **Integração com sistemas imperativos**: Como garantir corretude em sistemas híbridos que combinam código funcional puro com bibliotecas imperativas externas?

4. **Automação de provas**: Quais ferramentas e técnicas podem automatizar partes do processo de prova, especialmente para propriedades comuns sobre estruturas de dados padrão?

5. **Balanceamento de custos**: Qual o ponto ideal entre investimento em prova formal completa versus cobertura extensiva de propriedades QuickCheck para diferentes classes de sistemas (críticos vs não-críticos)?

6. **Propriedades de performance**: Como especificar e verificar propriedades de complexidade temporal e espacial usando técnicas similares às de corretude lógica?

7. **Educação em raciocínio formal**: Como tornar técnicas de prova e raciocínio equacional acessíveis para equipes de desenvolvimento sem formação matemática extensa?