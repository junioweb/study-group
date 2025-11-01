### 📘 **Registro de Aprendizados – Capítulo 8: Events and the Message Bus**

#### 1. **Referência da Leitura**  
- **Capítulo**: 8 – *Events and the Message Bus* 

#### 2. **Conceitos-Chave Identificados**  
- **Domain Event (Evento de Domínio)**: fato que ocorreu no sistema, representado como um objeto de dados imutável (dataclass), sem comportamento. Ex: `OutOfStock(sku="SMALL-FORK")`.  
- **Message Bus (Barramento de Mensagens)**: mecanismo centralizado que mapeia eventos para handlers, permitindo a execução de ações secundárias (ex: enviar e-mail) de forma desacoplada.  
- **Single Responsibility Principle (SRP)**: o evento é uma extensão do SRP — separa a lógica principal (alocar) da lógica secundária (notificar).  
- **UoW como Publisher de Eventos**: o Unit of Work coleta eventos gerados por agregados durante a transação e os publica no Message Bus após o commit, tornando o serviço livre de responsabilidades secundárias.  
- **Event Handling Síncrono**: os handlers são executados imediatamente após o commit, mantendo a consistência, mas podendo impactar performance em endpoints web.

#### 3. **Insights Relevantes**  
> “The magic words ‘When X, then Y’ often tell us about an event that we can make concrete in our system.”  
→ Requisitos expressos em termos de causa e efeito (“quando X, então Y”) são candidatos perfeitos para serem modelados como eventos de domínio.

> “We don't want our model to have any dependencies on infrastructure concerns like `email.send_mail`.”  
→ O modelo de domínio deve permanecer puro — qualquer side effect (ex: enviar e-mail) deve ser tratado fora dele, via eventos.

> “The UoW already has a `try/finally`, and it knows about all the aggregates currently in play... So it's a good place to spot events and pass them to the message bus.”  
→ O UoW é o lugar natural para publicar eventos, pois ele já está envolvido no ciclo de vida dos objetos e pode rastrear quais agregados foram modificados.

> “Using exceptions for control flow is a code smell.”  
→ Substituir exceções por eventos é uma melhoria de design — eventos são fatos, não erros. Isso simplifica o fluxo de código e evita confusão entre tratamento de erro e notificação.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar qualquer side effect (ex: notificação, log, auditoria) como um **evento de domínio**, não como uma chamada direta a infraestrutura.  
- Usar o **Message Bus** para conectar eventos a handlers, permitindo substituir facilmente a implementação (ex: trocar e-mail por SMS).  
- Implementar o **UoW como publisher automático de eventos**, eliminando a necessidade de o serviço lidar com eventos manualmente.  
- Manter os testes unitários focados no domínio — os handlers podem ser testados separadamente, garantindo que o core do sistema não dependa de infraestrutura.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **eventos como contratos imutáveis** — eles devem ser simples, descritivos e expressos na linguagem do domínio.  
- Usar o **Message Bus como abstração para side effects**, evitando acoplamento direto entre domínio e infraestrutura.  
- Delegar a publicação de eventos ao **UoW**, usando um atributo `.seen` nos repositórios para rastrear agregados modificados.  
- Evitar **eventos síncronos em endpoints web** quando possível — se o desempenho for crítico, considerar processamento assíncrono (ex: filas).

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como evitar loops infinitos entre handlers de eventos?  
- Qual o impacto de usar eventos para comunicação entre agregados em vez de transações atômicas?  
- Como testar cenários onde múltiplos handlers reagem ao mesmo evento?