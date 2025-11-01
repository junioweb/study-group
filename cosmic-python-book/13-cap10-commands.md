### 📘 **Registro de Aprendizados – Capítulo 10: Commands and Command Handler**

#### 1. **Referência da Leitura**  
- **Capítulo**: 10 – *Commands and Command Handler*

#### 2. **Conceitos-Chave Identificados**  
- **Comandos vs. Eventos**:  
  - **Comandos** expressam *intenção* (imperativo: “alocar estoque”) e têm um destinatário específico.  
  - **Eventos** expressam *fatos* (passado: “estoque alocado”) e são transmitidos a todos os interessados.  
- **Tratamento de Erros Distinto**:  
  - Comandos falham de forma ruidosa (`raise`), pois representam operações críticas.  
  - Eventos falham silenciosamente (`continue`), pois são secundários à operação principal.  
- **Message Bus com Dispatching Diferenciado**: o Message Bus agora roteia comandos e eventos para handlers distintos, com regras específicas de execução e tratamento de exceções.  
- **Consistência por Agregado**: comandos devem modificar um único agregado de forma atômica; eventos lidam com efeitos colaterais (notificações, atualizações em outros agregados).  
- **Resiliência com Retries**: handlers de eventos podem usar estratégias de retry (ex: `tenacity`) para lidar com falhas transitórias.

#### 3. **Insights Relevantes**  
> “Commands capture _intent_. Events capture _facts_ about things that happened in the past.”  
→ A distinção entre intenção e fato é fundamental para modelar corretamente o fluxo do sistema.

> “The only part of this code that _has_ to complete is the command handler that creates an order.”  
→ Nem tudo precisa ser atômico. Separar comandos (críticos) de eventos (não críticos) melhora a resiliência do sistema.

> “By separating out these concerns, we have made it possible for things to fail in isolation.”  
→ Falhas localizadas são preferíveis a falhas globais — isso é engenharia de confiabilidade.

> “Manual replay works well [...] but our systems will always experience some background level of transient failure.”  
→ Logs estruturados e mensagens imutáveis permitem reprocessamento fácil, essencial para sistemas orientados a eventos.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar toda interação externa (API, CLI) como um **comando**, não como um evento.  
- Usar **eventos apenas para efeitos colaterais** (notificações, atualizações assíncronas, auditoria).  
- Implementar **retry com backoff exponencial** em handlers de eventos para lidar com falhas transitórias.  
- Garantir que **comandos sejam idempotentes** sempre que possível, facilitando reprocessamento.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **comandos imperativos** (`Allocate`, `CreateBatch`) como entrada do sistema.  
- Manter **um único handler por comando** e **múltiplos handlers por evento**.  
- Usar **logs estruturados** para facilitar o diagnóstico e reprocessamento de falhas em eventos.  
- Aplicar **retries automáticos** em handlers de eventos, com limite máximo de tentativas.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como lidar com eventos que falham persistentemente (não transitórios)?  
- Qual o impacto de usar filas externas (ex: RabbitMQ) para eventos, em vez de processamento síncrono?  
- Como garantir a ordem de processamento de eventos relacionados?