### 📘 **Registro de Aprendizados – Parte 2: Event-Driven Architecture**

#### 1. **Referência da Leitura**  
- **Seção**: Introdução da Parte 2 – *Event-Driven Architecture*

#### 2. **Conceitos-Chave Identificados**  
- **Mensagens como núcleo**: a ideia central não é o objeto, mas a **comunicação entre módulos** — mensagens são a chave para sistemas escaláveis e evolutivos.  
- **Desafio de múltiplos domínios**: ao escalar para vários modelos de domínio (ex: Compras, E-commerce, Armazém), a comunicação entre eles deve ser **desacoplada**, evitando o “Big Ball of Mud” distribuído.  
- **Event-Driven Architecture (EDA)**: padrão que usa eventos assíncronos para integrar sistemas, permitindo que cada componente opere dentro de seu próprio limite de consistência (aggregate).  
- **CQRS (Command Query Responsibility Segregation)**: separação entre operações de escrita (comandos) e leitura (consultas), otimizando desempenho e flexibilidade em arquiteturas orientadas a eventos.  
- **Message Bus**: mecanismo centralizado para rotear eventos e comandos, tornando o sistema mais resiliente e testável.

#### 3. **Insights Relevantes**  
> “The big idea is 'messaging.'... The key in making great and growable systems is much more to design how its modules communicate than what their internal properties and behaviors should be.” — Alan Kay  
→ O foco deve estar na **interação entre componentes**, não apenas no estado interno deles. Isso é fundamental para sistemas distribuídos.

> “You may remember our context diagram... But exactly how will all these systems talk to each other?”  
→ A pergunta-chave: como garantir que sistemas independentes (ex: Compras, E-commerce, Armazém) se comuniquem sem acoplamento? A resposta é: eventos.

> “Many teams reach for microservices integrated via HTTP APIs. But if they're not careful, they'll end up producing the most chaotic mess of all: the distributed big ball of mud.”  
→ Microserviços com APIs síncronas podem criar um caos maior que monólitos — eventos assíncronos são a alternativa para manter a coesão sem sacrificar a independência.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar qualquer sistema distribuído com **eventos como contrato de comunicação** entre componentes.  
- Usar **Message Bus** para centralizar o roteamento de eventos e comandos, facilitando testes e monitoramento.  
- Aplicar **CQRS** para evitar compromissos de performance em consultas complexas — separar leitura e escrita permite otimizações específicas.  
- Manter cada **aggregate como uma unidade autônoma**, comunicando-se apenas por eventos, nunca por chamadas diretas.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **mensagens assíncronas** (eventos) sobre chamadas síncronas (HTTP) para integração entre sistemas.  
- Implementar **Message Bus** como camada de abstração para disparo e consumo de eventos, isolando os serviços do transporte.  
- Separar claramente **comandos** (escrita) de **consultas** (leitura) usando CQRS, especialmente em cenários de alta concorrência.  
- Tratar eventos como **contratos imutáveis** — eles devem expressar fatos que ocorreram, não intenções futuras.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como modelar eventos que dependem de estados de outros agregados sem criar acoplamento?  
- Qual o impacto de eventual consistência em cenários críticos (ex: pagamentos)?  
- Como lidar com falhas e reprocessamento em um sistema orientado a eventos?