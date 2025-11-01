### 📘 **Registro de Aprendizados – Capítulo 11: Event-Driven Architecture: Using Events to Integrate Microservices**

#### 1. **Referência da Leitura**
- **Capítulo**: 11 – *Event-Driven Architecture: Using Events to Integrate Microservices*

#### 2. **Conceitos-Chave Identificados**
- **Distributed Ball of Mud (Bola de Lama Distribuída)**: Antipadrão arquitetural que surge quando serviços são divididos por “nouns” (entidades) e acoplados temporalmente via chamadas síncronas, levando a dependências circulares e fragilidade.
- **Temporal Coupling (Acoplamento Temporal)**: Dependência entre componentes que exigem execução simultânea ou sequencial para funcionar — comum em sistemas baseados em RPC/HTTP síncrono.
- **Connascence**: Modelo mental para classificar tipos de acoplamento entre componentes. O objetivo é substituir *Connascence of Execution* e *Timing* por *Connascence of Name* (apenas nomes e estruturas de eventos precisam ser conhecidos).
- **Event-Driven Integration**: Uso de eventos assíncronos como meio de comunicação entre microserviços, promovendo desacoplamento temporal e permitindo eventual consistência.
- **Consistency Boundaries (Limites de Consistência)**: Microserviços devem atuar como fronteiras de consistência, onde dentro do serviço a consistência é forte, mas entre serviços aceita-se eventualidade.
- **Internal vs External Events**: Distinção entre eventos gerados internamente pelo domínio e eventos publicados externamente para integração com outros sistemas.
- **Thin Adapters**: Adaptadores leves (ex: Redis Event Consumer/Publisher) que traduzem mensagens externas para o modelo interno (comandos/eventos) e vice-versa, mantendo o núcleo do domínio isolado.

#### 3. **Insights Relevantes**
> “Our domain model is about modeling a business process. It's not a static data model about a thing; it's a model of a verb.”  
→ Reforça a ideia central de DDD: focar no comportamento e nos fluxos de negócio, não nas entidades estáticas.

> “We want our `BatchQuantityChanged` messages to come in as external messages from upstream systems, and we want our system to publish `Allocated` events for downstream systems to listen to.”  
→ Mostra como eventos podem ser usados tanto como entrada quanto saída, transformando o sistema em um processador de mensagens end-to-end.

> “The overall flows of information are harder to see.” — Martin Fowler  
→ Alerta sobre o custo de arquiteturas orientadas a eventos: a complexidade implícita dos fluxos pode dificultar depuração e manutenção.

> “Each service accepts commands from the outside world and raises events to record the result. Other services can listen to those events to trigger the next steps in the workflow.”  
→ Define um padrão claro de responsabilidade: cada serviço responde a comandos e emite eventos, sem assumir controle direto sobre os próximos passos.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Substituir chamadas HTTP síncronas entre serviços por eventos assíncronos via *message broker*, mesmo que leve (como Redis pub/sub), para evitar acoplamento temporal.
- Estruturar serviços ao redor de **verbos** (ex: “allocating”, “ordering”) em vez de **nouns** (“batches”, “orders”), alinhando-os com processos de negócio reais.
- Implementar adaptadores leves (thin adapters) para traduzir eventos externos (JSON, mensagens de fila) em comandos internos e eventos internos em mensagens externas, mantendo o núcleo do domínio livre de detalhes de infraestrutura.
- Usar testes end-to-end para validar fluxos assíncronos, incluindo retry loops para lidar com a natureza não determinística de mensagens.
- Separar claramente eventos internos (usados apenas dentro do domínio) de eventos externos (publicados para integração), evitando vazamento de detalhes de implementação.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Todos os serviços devem ser tratados como limites de consistência**: operações dentro de um serviço são transacionais; entre serviços, aceitar eventualidade.
- **Evitar acoplamento temporal**: nenhuma chamada síncrona entre serviços deve ser feita se puder ser substituída por evento assíncrono.
- **Padrão de mensagem única por canal**: cada tipo de evento externo deve ter seu próprio canal ou tópico, facilitando a subscrição e o rastreamento.
- **Validação obrigatória em eventos externos**: antes de converter uma mensagem externa em comando, validar sua estrutura e conteúdo.
- **Eventos internos não devem ser expostos diretamente**: sempre mapear eventos internos para formatos específicos de integração antes de publicá-los externamente.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como garantir que eventos externos sejam processados de forma idempotente, especialmente em cenários de retrial?
- Quais estratégias podemos adotar para rastrear e debugar fluxos de eventos distribuídos, considerando que o fluxo lógico não está explícito no código?
- Qual o impacto da escolha do *message broker* (Redis vs Kafka vs RabbitMQ) em termos de ordenação, confiabilidade e recuperação de falhas?
- Como modelar e testar cenários de compensação (saga) quando eventos falham ou causam inconsistências temporárias?
- Em que ponto a complexidade de eventos assíncronos supera os benefícios de desacoplamento? Quando manter um modelo síncrono ainda faz sentido?