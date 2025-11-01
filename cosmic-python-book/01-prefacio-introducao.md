### 📘 **Registro de Aprendizados – Prefácio + Introdução**

#### 1. **Referência da Leitura**  
- **Seções**: Prefácio e Introdução

#### 2. **Conceitos-Chave Identificados**  
- **Tendência natural dos sistemas ao caos** (Big Ball of Mud): código inicialmente ordenado degenera em acoplamento excessivo, lógica espalhada e violação de camadas.  
- **Encapsulamento e abstração** como mecanismos para combater a complexidade: encapsular comportamento (não apenas dados) e elevar o nível de expressividade do código.  
- **Arquitetura em camadas tradicional** (apresentação → lógica de negócio → banco de dados) é comum, mas propensa a colapso se não houver disciplina de dependências.  
- **Princípio da Inversão de Dependência (DIP)**:  
  - Módulos de alto nível (domínio) não devem depender de módulos de baixo nível (infraestrutura).  
  - Ambos devem depender de abstrações.  
  - Detalhes (implementações) devem depender de abstrações, nunca o contrário.  
- **Modelo de Domínio** como núcleo isolado da aplicação, onde reside toda a lógica de negócio, livre de preocupações técnicas.

#### 3. **Insights Relevantes**  
> “A big ball of mud is the natural state of software [...] It takes energy and direction to prevent the collapse.”  
→ Arquitetura não é um estado estático, mas um esforço contínuo de **intenção de design**. Sem disciplina, o sistema regride à entropia.

> “High-level modules should not depend on low-level modules. Both should depend on abstractions.”  
→ O DIP não é apenas sobre testabilidade, mas sobre **capacidade de evoluir domínio e infraestrutura de forma independente**.

> “We’re going to be systematically turning this [three-layered architecture] inside out.”  
→ A proposta do livro é **inverter a direção das dependências**: em vez de camadas superiores chamarem inferiores, a infraestrutura “envolve” o domínio, que permanece puro e central.

> “Encapsulating behavior by using abstractions is a powerful tool for making code more expressive, more testable, and easier to maintain.”  
→ Abstração bem escolhida **revela intenção**, não apenas esconde complexidade.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou stack)*  
- Estruturar o sistema de forma que **toda a lógica de negócio resida em um modelo de domínio isolado**, sem importações de frameworks, I/O ou serialização.  
- Tratar frameworks web, bancos de dados, filas etc. como **adaptadores periféricos**, conectados ao núcleo por meio de interfaces (abstrações).  
- Usar **abstrações baseadas em comportamento** (ex: `send_email()`, `allocate_stock()`) em vez de estruturas de dados ou tecnologias específicas.  
- Aplicar o DIP desde o início, mesmo em projetos pequenos, para evitar o “colapso da arquitetura”.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- **Domínio puro**: nenhuma dependência externa no núcleo da aplicação.  
- **Direção das dependências invertida**: infraestrutura depende do domínio, nunca o contrário.  
- **Abstrações orientadas a responsabilidades**, não a tecnologias.  
- **Testes unitários rápidos** como indicador de bom desacoplamento: se o teste precisa de banco, rede ou framework, há vazamento de infraestrutura no domínio.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como definir os limites de um “módulo de alto nível” sem cair em abstrações prematuras?  
- Qual o trade-off entre uso de ABCs (Abstract Base Classes) e duck typing na definição de abstrações em Python?  
- Quando uma abstração se torna um indireção desnecessária?