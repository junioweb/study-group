### 📘 **Registro de Aprendizados – Capítulo 1: Domain Modeling**

#### 1. **Referência da Leitura**  
- **Capítulo**: 1 – *Domain Modeling*

#### 2. **Conceitos-Chave Identificados**  
- **Modelo de Domínio ≠ Modelo de Dados**: o foco deve estar no **comportamento e regras de negócio**, não na estrutura de armazenamento.  
- **Entidade vs. Objeto de Valor**:  
  - **Entidade**: possui identidade persistente (ex: `Batch` com `reference`).  
  - **Objeto de Valor**: é definido por seus atributos; imutável e comparável por valor (ex: `OrderLine`).  
- **Serviço de Domínio**: função que encapsula lógica de negócio que não pertence naturalmente a uma entidade ou objeto de valor (ex: função `allocate()`).  
- **Linguagem Ubíqua**: usar termos do domínio nos nomes de classes, métodos e testes para alinhar entendimento entre técnicos e não-técnicos.  
- **Testes como Especificação**: os testes unitários devem expressar claramente as regras de negócio em linguagem do domínio.

#### 3. **Insights Relevantes**  
> “The domain is a fancy way of saying _the problem you're trying to solve._”  
→ O domínio não é um conceito abstrato — é o problema real que o software existe para resolver.

> “We’re not going to show you how TDD works in this book, but we want to show you how we would construct a model from this business conversation.”  
→ O TDD aqui é ferramenta para **descobrir e refinar o modelo**, não apenas validar código.

> “Sometimes, it just isn't a thing.” — Eric Evans  
→ Nem toda lógica precisa ser encapsulada em uma classe. Funções podem ser mais adequadas para operações de domínio.

> “Entities have identity equality. We can change their values, and they are still recognizably the same thing.”  
→ Entidades são sobre **identidade contínua**, objetos de valor são sobre **igualdade por conteúdo**.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar qualquer sistema começando pela **definição de comportamentos esperados**, usando testes como especificação inicial.  
- Usar **dataclasses imutáveis** (`@dataclass(frozen=True)`) para representar objetos de valor.  
- Implementar **métodos mágicos** (`__eq__`, `__hash__`, `__gt__`) para expressar semântica de domínio de forma idiomática em Python.  
- Criar **exceções de domínio** (`OutOfStock`) para capturar falhas específicas do negócio, não apenas erros técnicos.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **objetos de valor imutáveis** para dados sem identidade (ex: `OrderLine`, `Money`).  
- Definir **entidades com identidade explícita** (ex: `Batch.reference`) e implementar `__eq__` e `__hash__` corretamente.  
- Usar **funções** para serviços de domínio quando não houver estado compartilhado ou identidade natural.  
- Garantir que todos os nomes (classes, métodos, variáveis) reflitam a **linguagem do domínio**.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como decidir se uma entidade deve ter um identificador gerado internamente ou externamente?  
- Qual o impacto de usar `NewType` para wrappear tipos primitivos em modelos de domínio?  
- Como lidar com validações cruzadas entre objetos de valor e entidades sem violar o encapsulamento?