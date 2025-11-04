### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Abstract Server" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- O padrão **Abstract Server** insere uma interface abstrata entre um cliente e um servidor concreto.
- Resolve violações do **Princípio da Inversão de Dependência (DIP)** ao evitar que clientes dependam diretamente de implementações concretas.
- A interface atua como um “ponto de articulação” (*hinge point*) que permite flexibilidade na substituição de implementações.

#### 3. **Insights Relevantes**
> “The abstract interface becomes a ‘hinge point’ upon which the design can flex.”  
→ Isso transforma um acoplamento rígido em um contrato estável, permitindo evolução independente de cliente e servidor.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em serviços internos (ex: notificação, pagamento, log), definiremos interfaces no domínio (`NotificationService`) e implementações em camadas de infraestrutura (`EmailNotificationService`, `SmsNotificationService`).
- Facilita testes: mocks implementam a mesma interface usada em produção.
- Permite troca de provedores (ex: AWS SNS → Twilio) sem alterar lógica de negócio.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Todo consumo de serviço externo ou de baixo nível passará por uma interface abstrata definida no módulo de mais alto nível.
- Nenhum módulo de domínio terá importações diretas de classes concretas de infraestrutura.
- Interfaces pertencem ao consumidor, não ao fornecedor.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como gerenciar versionamento de interfaces quando há múltiplos consumidores com ciclos de release distintos?
- Em sistemas serverless, onde o “cliente” é um gatilho (trigger), como aplicar esse padrão de forma eficaz?