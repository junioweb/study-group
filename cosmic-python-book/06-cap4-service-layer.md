### 📘 **Registro de Aprendizados – Capítulo 4: Service Layer Pattern**

#### 1. **Referência da Leitura**  
- **Capítulo**: 4 – *Our First Use Case: Flask API and Service Layer* 

#### 2. **Conceitos-Chave Identificados**  
- **Service Layer (Camada de Serviço)**: camada intermediária que orquestra o fluxo de uso do sistema, separando a lógica de negócio (domínio) da infraestrutura (API, persistência).  
- **Orquestração vs. Lógica de Negócio**: a camada de serviço trata validações, transações e chamadas ao domínio — não contém regras de negócio.  
- **Padrão Ports & Adapters aplicado**: o Service Layer depende de abstrações (`AbstractRepository`), permitindo testes com `FakeRepository` e execução com `SqlAlchemyRepository`.  
- **Testes em “alta marcha”**: testes unitários no nível da camada de serviço são mais ricos que os do domínio puro, mas ainda rápidos, pois usam fakes.  
- **Estrutura de pastas sugerida**: `domain/`, `service_layer/`, `adapters/`, `entrypoints/` — organiza o código por responsabilidade e tipo de componente.

#### 3. **Insights Relevantes**  
> “Our Flask API endpoints become very thin and easy to write: their only responsibility is doing ‘web stuff’.”  
→ A API deve ser um adaptador leve, delegando toda complexidade para a camada de serviço.

> “We’ve defined a clear API for our domain, a set of use cases or entrypoints that can be used by any adapter without needing to know anything about our domain model classes.”  
→ O Service Layer é a interface estável do sistema — ele define *o que* pode ser feito, não *como* é feito.

> “Putting too much logic into the service layer can lead to the Anemic Domain antipattern.”  
→ Cuidado! A camada de serviço não deve assumir regras de negócio. Se isso acontecer, o domínio fica anêmico — o foco deve permanecer no modelo de domínio.

> “The service layer enables more productive TDD.”  
→ Testar no nível do Service Layer permite cobrir mais cenários sem depender de I/O, mantendo a velocidade dos testes unitários.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Estruturar qualquer sistema com uma camada de serviço como **único ponto de entrada para casos de uso**, isolando o domínio de detalhes de infraestrutura.  
- Usar `FakeRepository` para testar fluxos completos (ex: alocação + persistência) sem depender de banco.  
- Definir exceções específicas de domínio (ex: `InvalidSku`) na camada de serviço, não no domínio, pois são validações de contexto externo.  
- Separar responsabilidades:  
  - **Entry Point (Flask)**: parsing, status HTTP, serialização.  
  - **Service Layer**: validação, orquestração, transação.  
  - **Domain Model**: regras de negócio puras.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **testes unitários na camada de serviço** para validar fluxos completos, usando `FakeRepository` e `FakeSession`.  
- Manter o **domínio puro** — nenhuma validação de SKU inexistente ou controle de transação deve estar nele.  
- Definir a camada de serviço como **interface de uso do sistema**, acessível por APIs, CLIs ou scripts.  
- Evitar o **Anemic Domain Model** — se a camada de serviço começar a conter lógica de negócio, refatore para mover essa lógica para o domínio.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como definir o escopo ideal de um serviço? Quando um caso de uso justifica uma função própria na camada de serviço?  
- Qual o impacto de usar tipos primitivos (ex: `str`, `int`) em vez de objetos de domínio (ex: `OrderLine`) na assinatura da função de serviço?  
- Como lidar com validações que exigem consulta ao estado atual do sistema (ex: “estoque disponível”) sem acoplar a camada de serviço ao domínio?