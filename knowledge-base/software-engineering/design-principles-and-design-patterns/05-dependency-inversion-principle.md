### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Dependency Inversion Principle (DIP)" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- **DIP**: "Dependa de abstrações, não de concretudes."
- Módulos de alto nível (políticas de negócio) não devem depender de módulos de baixo nível (detalhes técnicos); ambos devem depender de abstrações.
- Inverte a estrutura de dependência típica de arquiteturas procedurais.

#### 3. **Insights Relevantes**
> “Depend upon Abstractions. Do not depend upon concretions.”  
→ Abstrações são "pontos de articulação" que permitem estender o sistema sem modificar código existente (ligação direta com OCP).
> “The DIP states the primary mechanism [for achieving OCP].”  
→ DIP é o alicerce prático para tornar o OCP viável.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Camada de domínio definirá interfaces para repositórios, notificadores, gateways externos.
- Implementações (ex: `PostgresUserRepository`, `SendGridEmailService`) residirão em camadas externas (infraestrutura/adaptadores).
- Facilita testes: mocks substituem implementações reais sem alterar lógica de domínio.
- Permite troca de tecnologias (ex: banco de dados, provedor de email) sem impacto no núcleo do sistema.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Nenhum módulo de domínio dependerá diretamente de classes concretas de infraestrutura.
- Uso de injeção de dependência (DI) para resolver abstrações em tempo de execução.
- Interfaces pertencem ao módulo que as **usa**, não ao que as **implementa** (ex: `UserRepository` está no domínio, não na infra).

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como gerenciar a complexidade de configuração de DI em sistemas com dezenas de abstrações?
- Em funções serverless ou scripts curtos, o custo de DIP supera o benefício?