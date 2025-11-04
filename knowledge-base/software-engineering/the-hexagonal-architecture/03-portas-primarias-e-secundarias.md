### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Application Notes – The Left-Right Asymmetry

#### 2. **Conceitos-Chave Identificados**
- **Portas primárias (driving)**: são acionadas por atores primários (ex: usuário, sistema externo) que **disparam** a aplicação. Estão do “lado esquerdo” do hexágono.
- **Portas secundárias (driven)**: são usadas pela aplicação para **interagir com atores secundários** (ex: banco de dados, serviço externo). Estão do “lado direito”.
- A distinção vem dos **casos de uso**: atores primários iniciam o fluxo; atores secundários respondem ou são notificados.
- Adaptadores de teste para portas primárias são **harnesses** (ex: FIT); para portas secundárias, são **mocks**.

#### 3. **Insights Relevantes**
> “A primary actor is an actor that drives the application (takes it out of quiescent state to perform one of its advertised functions). A secondary actor is one that the application drives...”  
→ Essa distinção ajuda a modelar corretamente o fluxo de controle e a escolher os tipos apropriados de testes.

> “The natural test adapter to substitute for a primary actor is FIT... The natural test adapter to substitute for a secondary actor such as a database is a mock...”  
→ Reforça que a estratégia de teste deve ser assimétrica, mas a arquitetura permanece simétrica em sua estrutura.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Ao escrever testes de aceitação, simularemos **atores primários** (ex: chamadas HTTP simulando um usuário).
- Ao escrever testes unitários de serviços de domínio, injetaremos **mocks de portas secundárias** (ex: repositórios em memória).
- A documentação da API (OpenAPI, por exemplo) deve descrever as **portas primárias**, pois são o contrato com o mundo externo.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Nomear interfaces de porta de forma clara**: `UserCommandPort` (primária), `PaymentGatewayPort` (secundária).
- **Testes de integração focarão na composição de adaptadores reais com a aplicação**, mas ainda isolados de infraestrutura externa quando possível.
- **Casos de uso devem ser escritos contra as portas primárias**, descrevendo o que o sistema faz, não como é acessado.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como classificar um sistema que é acionado por uma fila de mensagens (ex: Kafka)? É um ator primário ou secundário?
- Em arquiteturas orientadas a eventos, como encaixar “event handlers” nesse modelo de portas primárias/secundárias?
- Existe risco de criar uma porta primária para cada endpoint HTTP? Como evitar essa granularidade excessiva?