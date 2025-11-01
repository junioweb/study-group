### 📘 **Registro de Aprendizados – Capítulo 12: Command-Query Responsibility Segregation (CQRS)**

#### 1. **Referência da Leitura**
- **Capítulo**: 12 – *Command-Query Responsibility Segregation (CQRS)*

#### 2. **Conceitos-Chave Identificados**
- **CQRS (Command-Query Responsibility Segregation)**: Princípio arquitetural que separa claramente operações de escrita (comandos) de operações de leitura (consultas), permitindo otimizações específicas para cada tipo.
- **Read Side vs Write Side**: O lado de escrita é responsável por aplicar regras de negócio e garantir consistência transacional; o lado de leitura prioriza performance, cacheabilidade e eventual consistência.
- **Event-Driven Read Models**: Atualização de modelos de leitura (read models) via eventos do domínio, permitindo desacoplamento entre a lógica de escrita e a estrutura de consulta.
- **Denormalized Read Models**: Estruturas de dados otimizadas para leitura, frequentemente desnormalizadas, que podem ser mantidas atualizadas por meio de handlers de eventos.
- **Post/Redirect/Get como CQS em APIs**: Aplicação prática do princípio de separação de comandos e consultas no contexto web, evitando retornar dados em respostas de escrita.
- **N+1 Query Problem**: Problema de performance comum em ORMs quando múltiplas consultas são feitas para cada item retornado, podendo ser mitigado com queries agrupadas ou denormalização.
- **Trade-offs de Implementação**: Diferentes abordagens para views (repositórios, ORM, SQL puro, tabelas denormalizadas, stores externos) têm custos e benefícios distintos, exigindo avaliação contextual.

#### 3. **Insights Relevantes**
> “The domain is the same—but the access pattern is very different. For example, our customers won't notice if the query is a few seconds out of date, but if our allocate service is inconsistent, we'll make a mess of their orders.”  
→ Justifica a adoção de consistência eventual nas leituras: a natureza distribuída dos sistemas já implica inconsistência, então otimizar para performance nas consultas é racional.

> “Your domain model is not optimized for read operations... Most of this stuff is totally irrelevant for read-only operations.”  
→ Reforça que o modelo de domínio, projetado para regras de negócio e transações, não deve ser forçado a servir também como camada de consulta — isso gera complexidade desnecessária.

> “We can think of these requirements as forming two halves of a system: the read side and the write side.”  
→ Define claramente a divisão de responsabilidades, permitindo escolhas tecnológicas e de design independentes para cada lado.

> “Rebuilding a view model is easy... since we're using a service layer to update our view model, we can write a tool that does the following: queries the current state of the write side... and calls the handler for each allocated item.”  
→ Mostra que a manutenção de read models event-driven é resiliente e recuperável, mesmo após falhas.

> “Harry will be forever suspicious of your tastes and motives.”  
→ Humor que alerta: CQRS completo pode parecer excessivo, mas sua adoção deve ser guiada por necessidades reais de desempenho e escalabilidade, não por modismo.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Separar endpoints de escrita (POST/PUT) de leitura (GET) no API, seguindo o padrão Post/Redirect/Get ou retornando apenas status 202 Accepted + Location header.
- Criar módulos ou pacotes específicos para *views* (leitura), isolando-os da lógica de domínio e de persistência.
- Para consultas simples, usar SQL puro ou consultas diretas ao banco, evitando overhead de ORM quando não necessário.
- Para consultas complexas ou de alto volume, considerar a criação de *read models* denormalizados, atualizados via eventos do domínio.
- Avaliar a possibilidade de usar armazenamentos alternativos (ex: Redis) para read models, aproveitando sua velocidade e escalabilidade horizontal.
- Manter testes de integração que validem o fluxo completo (escrita → evento → atualização do read model → leitura), garantindo que a separação não comprometa a consistência lógica.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Todos os endpoints de escrita devem retornar apenas confirmação de aceitação (202) ou criação (201), sem dados** — a consulta deve ser feita via endpoint GET separado.
- **Consultas devem ser implementadas em módulos `views`**, independentes do domínio e dos repositórios de escrita.
- **Para consultas de alta performance ou alta frequência, preferir SQL puro ou denormalização sobre ORM**, especialmente se houver risco de N+1 ou joins complexos.
- **Read models devem ser atualizados via handlers de eventos**, mantendo-se desacoplados da lógica de escrita e permitindo troca de tecnologia (ex: de SQL para Redis).
- **Sempre validar trade-offs antes de adotar CQRS completo**: se o modelo de leitura é simples e alinhado com o domínio, manter uma abordagem mais leve (ex: ORM ou repositório) pode ser suficiente.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como definir métricas claras para decidir quando migrar de uma abordagem simples (ORM/repositories) para um read model denormalizado?
- Quais estratégias de cache (ex: Redis, Varnish) podem ser combinadas com read models para maximizar performance?
- Como garantir que handlers de eventos para atualização de read models sejam idempotentes e resilientes a falhas?
- Em cenários de migração incremental, como sincronizar read models existentes com o estado atual do sistema de escrita?
- Qual o impacto na manutenção e teste quando diferentes read models (SQL, Redis, etc.) coexistem para diferentes tipos de consulta?