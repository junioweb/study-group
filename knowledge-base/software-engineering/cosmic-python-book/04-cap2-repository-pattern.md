### 📘 **Registro de Aprendizados – Capítulo 2: Repository Pattern**

#### 1. **Referência da Leitura**  
- **Capítulo**: 2 – *Repository Pattern*

#### 2. **Conceitos-Chave Identificados**  
- **Inversão de Dependência (DIP) aplicada à persistência**: o modelo de domínio deve ser **ignorante de infraestrutura**; a camada de persistência (ORM, banco de dados) depende do domínio, não o contrário.  
- **Padrão Repository**: abstração sobre armazenamento persistente que simula um “repositório em memória”, permitindo operações como `add()` e `get()` sem expor detalhes de I/O.  
- **Mapeamento imperativo (classical mapping)**: alternativa ao mapeamento declarativo no SQLAlchemy, onde a definição da tabela é separada da classe de domínio, invertendo a dependência.  
- **Portas e Adaptadores (Ports & Adapters)**: o *port* é a interface (ex: `AbstractRepository`), e o *adapter* é a implementação concreta (ex: `SqlAlchemyRepository`, `FakeRepository`).  
- **Duck typing e Protocolos (PEP 544)**: em Python, a abstração pode ser definida por comportamento (“tem os métodos `add` e `get`”) em vez de herança explícita via ABCs.

#### 3. **Insights Relevantes**  
> “Our domain model should be free of infrastructure concerns, so your ORM should import your model, and not the other way around.”  
→ O domínio é o centro — tudo gira em torno dele. A persistência é uma extensão, não o núcleo.

> “The repository gives you the illusion of a collection of in-memory objects.”  
→ Abstrair a persistência permite tratar dados como se estivessem sempre na memória, simplificando testes e manutenção.

> “Building fakes for your abstractions is an excellent way to get design feedback: if it's hard to fake, the abstraction is probably too complicated.”  
→ Se criar um *fake* é difícil, sua abstração está mal projetada — use isso como métrica de qualidade.

> “If your app is just a simple CRUD wrapper around a database, then you don't need a domain model or a repository.”  
→ Padrões arquiteturais são ferramentas para complexidade. Não os aplique quando não há necessidade real.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Estruturar qualquer sistema com **domínio puro** — sem referências a bancos, frameworks ou I/O.  
- Usar **mapeamento imperativo** (SQLAlchemy) ou até mesmo **SQL bruto** como adaptador, mantendo o domínio isolado.  
- Implementar **fakes simples** (ex: `set()`) para testes unitários, garantindo que o domínio seja testável sem depender de infraestrutura.  
- Definir interfaces via **duck typing** ou **Protocolos (PEP 544)** para evitar rigidez das ABCs em produção.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **dependência invertida** entre domínio e persistência — nunca o contrário.  
- Limitar o repositório aos métodos essenciais: `add()`, `get()`, e opcionalmente `list()`.  
- Usar **métodos de teste de integração** para validar o repositório contra o banco, mas manter testes unitários focados no domínio.  
- Considerar **Protocolos (PEP 544)** como alternativa moderna às ABCs para definir portas, especialmente em código novo.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como equilibrar a simplicidade do repositório com a necessidade de operações mais complexas (ex: consultas customizadas)?  
- Qual o impacto de usar `@runtime_checkable` em Protocolos para validações em tempo de execução?  
- Em que cenários vale a pena implementar um repositório sem ORM, usando apenas SQL?