### 📘 **Registro de Aprendizados – Capítulo 7: Aggregates and Consistency Boundaries**

#### 1. **Referência da Leitura**  
- **Capítulo**: 7 – *Aggregates and Consistency Boundaries*

#### 2. **Conceitos-Chave Identificados**  
- **Aggregate (Agregado)**: objeto de domínio que encapsula outros objetos relacionados, atuando como uma unidade única para operações de dados e manutenção de invariantes.  
- **Consistency Boundary (Limite de Consistência)**: fronteira definida por um agregado, dentro da qual todas as operações devem preservar os invariantes do sistema.  
- **Invariante vs. Restrição**:  
  - *Restrição*: regra que limita estados possíveis (ex: “não pode alocar mais do que o disponível”).  
  - *Invariante*: condição que deve ser sempre verdadeira após uma operação (ex: “uma linha de pedido está alocada para zero ou uma batch, nunca mais”).  
- **Optimistic Concurrency Control**: técnica que assume que conflitos são raros, usando números de versão (`version_number`) para detectar e resolver concorrência em nível de banco de dados.  
- **Repositório de Aggregate**: apenas repositórios que retornam aggregates são permitidos — eles são os únicos pontos de entrada válidos para o modelo de domínio.

#### 3. **Insights Relevantes**  
> “An AGGREGATE is a cluster of associated objects that we treat as a unit for the purpose of data changes.” — Eric Evans  
→ O agregado é o “ponto único de entrada” para modificar um grupo de objetos relacionados, garantindo que todas as regras de negócio sejam aplicadas atomicamente.

> “We want to load the entire basket as a single blob from our data store. We don't want two requests to modify the basket at the same time...”  
→ A ideia de carregar o agregado completo (“single blob”) é fundamental para evitar erros de concorrência e manter a consistência.

> “Version numbers are just one way to implement optimistic locking... The number isn’t important. What’s important is that the `Product` database row is modified whenever we make a change to the `Product` aggregate.”  
→ O número de versão é um mecanismo técnico; seu propósito real é marcar que o estado do agregado foi alterado, forçando o banco de dados a verificar a concorrência.

> “Choosing the right aggregate is key, and it's a decision you may revisit over time.”  
→ O agregado não é uma escolha definitiva — ele deve ser revisado conforme o domínio evolui e novos requisitos surgem.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar qualquer entidade complexa (ex: `Order`, `Cart`, `Inventory`) como um **aggregate**, com métodos que encapsulam toda lógica de manipulação interna.  
- Usar **números de versão** em agregados para lidar com concorrência, em vez de bloqueios pessimistas (`SELECT FOR UPDATE`).  
- Criar **repositórios que retornam apenas aggregates** — isso força o uso correto do modelo e evita acessos diretos a entidades filhas.  
- Estruturar testes de integração para validar **comportamento sob concorrência**, simulando múltiplas threads modificando o mesmo aggregate.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **aggregates pequenos e focados** — quanto menor o limite de consistência, melhor o desempenho e a manutenibilidade.  
- Manter o **domínio puro** — o número de versão (`version_number`) é parte do domínio, pois é necessário para garantir invariância.  
- Usar **context managers** (como o UoW) para garantir que operações em um aggregate sejam atômicas e consistentes.  
- Evitar **acessos diretos a entidades filhas** fora do aggregate — elas são “privadas” e só podem ser modificadas pelo aggregate raiz.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como decidir quando usar lazy-loading para coleções dentro de um aggregate?  
- Em sistemas com muitos agregados, como gerenciar a eventual consistência entre eles?  
- Qual o impacto de usar `SERIALIZABLE` vs. números de versão em termos de desempenho e complexidade?