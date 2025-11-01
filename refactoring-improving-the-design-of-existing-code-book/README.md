# Refactoring: Improving the Design of Existing Code

**Autores:** Martin Fowler  
**Site:** [refactoring.com](https://refactoring.com/)  
**Foco:** Técnicas práticas para melhorar o design de código existente sem alterar seu comportamento observável

Este livro apresenta um catálogo sistemático de refatorações, code smells e princípios que guiam a melhoria contínua da estrutura do código, integrando refatoração como prática essencial no desenvolvimento de software.

## Progresso dos Estudos

- ✅ Cap. 1 – "Refactoring: A First Example" e Cap. 2 – "Principles in Refactoring"
- ✅ Cap. 3 – "Bad Smells in Code"
- ✅ Cap. 6–12 – "A First Set of Refactorings", "Moving Features", "Organizing Data", "Conditional Logic", "Refactoring APIs" e "Dealing with Generalization"
- ✅ Cap. 4 – "Building Tests" e seções sobre "Self-Testing Code" e "Refactoring and the Wider Software Development Process"
- ✅ "Refactoring and the Wider Software Development Process" (seções sobre XP e práticas ágeis)
- ✅ "When Should I Refactor?" (Cap. introdutório e seções esparsas ao longo do livro, especialmente no Cap. 1 e nas conclusões)
- ✅ "Refactoring Tools" (Cap. introdutório e seções esparsas sobre automação de refatoração)
- ✅ Cap. "Organizing Data" (especialmente seções: *Encapsulate Record*, *Encapsulate Collection*, *Replace Derived Variable with Query*, *Change Reference to Value*, *Replace Primitive with Object*)
- ✅ "Refactoring in a Code Review" (seção esparsa no capítulo introdutório e ao longo do livro)
- ✅ "Refactoring and Performance" (Capítulo introdutório e seções esparsas ao longo do livro)

## Conceitos Técnicos Principais

### Fundamentos de Refatoração

#### O que é Refatoração (Cap. 1 e Cap. 2)
- **Definição**: Reestruturação do código **sem alterar seu comportamento observável**
- **Objetivo**: Melhorar legibilidade e reduzir custo de modificação futura
- **Distinções Importantes**: Refatoração não é reescrita, não corrige bugs e não adiciona funcionalidades
- **Abordagem**: Pequenos passos seguros com testes automatizados como rede de segurança
- **Integração**: Parte integrante do fluxo diário de desenvolvimento, não tarefa isolada
- **Regra dos Dois Hats (Kent Beck)**: Separar mentalmente adicionar funcionalidade e refatorar

#### Maus Cheiros no Código (Cap. 3)
- **Code Smells**: Sinais de deterioração do design, não bugs, mas indicadores estruturais
- **Código Duplicado**: Lógica repetida em múltiplos lugares — prioridade absoluta para refatoração
- **Métodos Longos**: Dificultam leitura, teste e reutilização
- **Classes Grandes (God Classes)**: Concentram muitas responsabilidades
- **Switch Statements Complexos**: Indicam ausência de polimorfismo
- **Feature Envy**: Método usa mais dados de outra classe do que da própria
- **Data Clumps**: Grupos de dados que sempre aparecem juntos (sinal de nova abstração)
- **Comentários Excessivos**: Frequentemente usados como "desodorante" para código confuso
- **Regra dos 5**: Métodos com mais de 5 linhas merecem avaliação; mais de 10 precisam refatoração

#### Técnicas Fundamentais de Refatoração (Cap. 6–12)
- **Extract Function / Extract Variable**: Extrair lógica para funções/variáveis com nomes expressivos
- **Inline Function / Inline Variable**: Remover abstrações que não agregam valor
- **Rename**: Priorizar clareza e intenção sobre brevidade — documentação viva
- **Move Function / Move Field**: Realocar comportamentos para o contexto mais apropriado
- **Replace Conditional with Polymorphism**: Substituir lógica condicional por estruturas OO (strategy, factory)
- **Introduce Parameter Object**: Agrupar parâmetros relacionados em objeto coeso
- **Split Phase**: Dividir lógica complexa em fases distintas quando cada fase opera com dados/responsabilidades diferentes

### Base para Refatoração Segura

#### Testes Automatizados (Cap. 4)
- **Código Auto-testável**: Condição necessária para refatoração segura
- **Testes Autoverificáveis**: Resultado validado automaticamente pelo framework
- **Testes como Rede de Segurança**: Detectam regressões rapidamente
- **Ciclo Fundamental**: Testar → refatorar → testar novamente
- **Testes Rápidos e Focados**: Priorizar áreas complexas ou propensas a erros
- **Testes de Caracterização**: Testes em código legado para capturar comportamento atual antes de refatorar
- **Princípio**: Sem testes, não há refatoração — apenas reestruturação arriscada

#### Refatoração e Processo Ágil (Refactoring and the Wider Software Development Process)
- **Pilar do XP**: Refatoração é central no Extreme Programming, junto com integração contínua e código auto-testável
- **Design Evolutivo**: Só é possível quando refatoração é praticada continuamente
- **Metáfora do Acampamento**: "Leave the campsite cleaner than you found it" — melhoria incremental
- **YAGNI + Refatoração**: Construir o mínimo necessário hoje, evoluindo o design amanhã sem dor
- **Ágil Técnico vs. Ágil de Fachada**: Adotar práticas técnicas (testes, refatoração, CI) além de cerimônias
- **Refatoração Contínua**: Distribuir melhoria ao longo de todas as iterações, não em sprints técnicos isolados

#### Quando Refatorar, Quando Não (When Should I Refactor?)
- **Três Momentos Ideais**:
  - Ao adicionar nova funcionalidade (refatoração preparatória)
  - Ao corrigir bug (refatoração para compreensão)
  - Ao ler código legado (refatoração de limpeza leve)
- **Não Refatorar Código Estável**: Se funciona como API estável e não será modificado, deixe como está
- **Refatoração ≠ Reescrever**: Primeira preserva comportamento; segunda pode mudar contratos
- **Mudanças Incrementais**: Mais seguras que refatorações em massa, especialmente sem testes robustos
- **Rule of Three**: Na terceira repetição de lógica similar, refatore para eliminar duplicação

### Ferramentas e Contextos

#### Ferramentas e IDEs (Refactoring Tools)
- **AST-based Refactoring**: Ferramentas modernas operam sobre árvore sintática abstrata, não texto bruto
- **IDEs e LSP**: IntelliJ IDEA, Eclipse, Visual Studio (ReSharper), editores com Language Server Protocol
- **Refatorações Comuns**: Rename, Extract Function/Variable, Move, Inline, Introduce Parameter Object
- **Busca/Substituição Textual**: Abordagem frágil — útil apenas como auxílio inicial, nunca estratégia principal
- **Testes Continuam Essenciais**: Especialmente em linguagens dinâmicas ou com uso intenso de reflexão

#### Refatoração de Dados (Organizing Data)
- **Encapsulate Record**: Transformar estruturas planas em classes com acesso controlado
- **Encapsulate Collection**: Evitar exposição direta de coleções mutáveis; fornecer métodos específicos
- **Replace Derived Variable with Query**: Substituir variáveis calculadas por funções que recalculam sob demanda
- **Change Reference to Value**: Decidir entre compartilhar instância ou copiar valor, baseado em mutabilidade
- **Imutabilidade**: Facilitador poderoso — dados imutáveis podem ser copiados livremente, reduzem efeitos colaterais
- **Value Objects**: Preferir objetos imutáveis para conceitos de domínio (ex: Money, DateRange, PhoneNumber)

#### Refatoração em Revisões (Refactoring in a Code Review)
- **Refatoração Colaborativa**: Transformar sugestões abstratas em melhorias concretas durante revisões
- **Revisões Síncronas**: Pair programming ou sessões permitem experimentar e validar melhorias em tempo real
- **Compartilhamento de Conhecimento**: Refatorar durante revisão é ato de aprendizado e alinhamento de padrões
- **Refatoração de Compreensão**: Ao revisar código pouco claro, aplicar mudanças que tornem intenção explícita
- **Evitar Comentários Vagos**: Substituir por ações concretas ("vamos extrair essa lógica para função X?")

#### Performance e Refatoração (Refactoring and Performance)
- **Regra dos 90/10**: ~90% do tempo de execução concentrado em ~10% do código
- **Medir, Não Adivinhar**: Otimizações baseadas em intuição são frequentemente ineficazes
- **Estratégia Recomendada**: Refatore primeiro para clareza; só então, se necessário, otimize com base em profiling
- **Código Bem Estruturado é Mais Fácil de Otimizar**: Permite identificar e isolar hotspots com precisão
- **Refatoração e Performance são Complementares**: Não são inimigos, mas fases do mesmo processo de melhoria

## Padrões e Decisões de Design

### Princípios de Refatoração

- **Pequenos Passos Seguros**: Cada refatoração deve manter o sistema funcional
- **Testes Antes de Refatorar**: Nenhum código crítico será refatorado sem testes prévios
- **Nomes Expressivos**: Variáveis, funções e classes devem revelar intenção — renomear é poderosa
- **Zero Tolerância com Duplicação**: Aplicar Rule of Three — na terceira ocorrência, refatorar
- **Comentários só para "Porquê"**: Se código não é autoexplicativo, renomeie ou extraia

### Estrutura de Código

- **Toda Função Expressa Uma Intenção**: Mesmo que tenha apenas 2-3 linhas
- **Evitar Parâmetros Posicionais Acima de 3**: Agrupar em objetos ou usar named parameters
- **Condicional com Mais de Dois Ramos**: Avaliar para polimorfismo, especialmente se representam conceitos de domínio
- **Regra dos 5**: Métodos com mais de 5 linhas merecem avaliação crítica
- **Classes com Máximo de 3 Responsabilidades**: Usar Single Responsibility como guia

### Modelagem de Dados

- **Encapsular Estruturas com Escopo Além de Uma Função**: Via classe ou módulo com interface controlada
- **Coleções Nunca Retornadas Diretamente**: Sempre retornar cópias ou iteradores somente leitura
- **Evitar Variáveis Derivadas Mutáveis**: Exceto em casos de performance comprovada
- **Preferir Value Objects Imutáveis**: Para conceitos de domínio em vez de primitivos soltos
- **Imutabilidade por Padrão**: Em novos módulos, especialmente em pipelines de dados

### Integração com Processo

- **Nenhuma História "Pronta" se Deixar Código Pior**: Refatoração é responsabilidade coletiva
- **YAGNI + Refatoração = Estratégia de Design**: Não antecipe flexibilidade; evolua conforme necessidade real
- **Integração Contínua Deve Falhar se Testes Falharem**: Testes são parte viva do sistema
- **Pequenas Melhorias Contínuas > Grandes Reestruturações**: Priorizar consistência sobre heroísmo técnico
- **Refatoração Oportuna**: Apenas quando há valor prático imediato, não por perfeccionismo

### Performance

- **Nunca Sacrificar Clareza por Performance sem Medição**: Medir antes de otimizar
- **Refatorar Antes de Otimizar**: Design limpo revela onde otimização realmente importa
- **Manter 95% do Código Focado em Legibilidade**: Apenas hotspots validados por profiling merecem otimizações
- **Documentar Decisões de Performance**: Incluir métrica base, ganho obtido e trade-off de legibilidade
- **Evitar Otimizações Baseadas em Versões Antigas**: O que era lento pode ser otimizado por JIT moderno

## Princípios Fundamentais

1. **Refatoração não altera comportamento**: O código deve funcionar exatamente igual após refatoração
2. **Testes são pré-requisito**: Sem rede de segurança, refatoração é arriscada
3. **Pequenos passos são seguros**: Cada passo mantém o sistema funcional
4. **Refatoração é contínua**: Parte do fluxo diário, não tarefa isolada ou sprint técnico
5. **Clareza antes de performance**: Refatore primeiro, otimize depois (se necessário e com dados)
6. **Nomes são documentação viva**: Renomear é uma das refatorações mais poderosas
7. **Duplicação é sinal de alerta**: Rule of Three — na terceira repetição, refatore
8. **Refatore apenas quando necessário**: Não refatore código estável que não será modificado
9. **Refatoração é colaborativa**: Code reviews são oportunidades de melhoria compartilhada
10. **YAGNI + Refatoração**: Comece simples, evolua o design quando a necessidade surgir

## Referências dos Registros de Estudo

Todos os capítulos possuem registros detalhados em arquivos Markdown seguindo o padrão de numeração sequencial. Consulte os arquivos individuais para insights específicos, citações relevantes e aplicações práticas de cada conceito.