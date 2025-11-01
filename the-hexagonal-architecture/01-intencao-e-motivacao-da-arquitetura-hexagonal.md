### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Introdução, Intent, Motivation

#### 2. **Conceitos-Chave Identificados**
- A arquitetura hexagonal permite que uma aplicação seja impulsionada indistintamente por usuários, programas, testes automatizados ou scripts em lote.
- O objetivo é desenvolver e testar a aplicação de forma isolada dos dispositivos e bancos de dados de execução.
- O principal problema resolvido é a **infiltração da lógica de negócio na interface do usuário** e o **acoplamento rígido à infraestrutura externa** (como bancos de dados).
- A solução proposta é baseada na simetria entre os lados “interno” e “externo” da aplicação, e não na assimetria esquerda-direita.

#### 3. **Insights Relevantes**
> “Create your application to work without either a UI or a database so you can run automated regression-tests against the application, work when the database becomes unavailable, and link applications together without any user involvement.”  
→ Isso estabelece o princípio fundamental: a aplicação deve ser autossuficiente em sua lógica de domínio, independente de tecnologias periféricas.

> “The attempted solution, repeated in many organizations, is to create a new layer in the architecture, with the promise that this time, really and truly, no business logic will be put into the new layer. However, having no mechanism to detect when a violation of that promise occurs...”  
→ A arquitetura hexagonal não é apenas uma camada adicional, mas um mecanismo estrutural que **permite detectar e prevenir vazamentos de lógica** por meio de testes automatizados contra a API central.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Podemos desenvolver funcionalidades completas com testes automatizados (ex: usando Pytest ou frameworks similares) antes mesmo de definir a UI ou escolher o banco de dados.
- Em situações de falha de infraestrutura (ex: banco de dados fora do ar), a aplicação pode continuar sendo testada ou até executada em modo “headless”.
- Facilita a integração entre microsserviços ou sistemas legados via APIs bem definidas, sem dependência de interfaces humanas.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Toda nova funcionalidade deve ser implementada primeiro contra uma interface de domínio (porta)**, com testes automatizados usando adaptadores em memória (mocks).
- **Proibido acoplar lógica de negócio diretamente a frameworks de UI, ORM ou clientes HTTP**. Essas dependências devem ser injetadas via adaptadores.
- A especificação funcional (ex: casos de uso) deve ser escrita contra a **fronteira da aplicação (hexágono interno)**, não contra tecnologias específicas.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como definir o escopo de uma “porta”? Quando criar uma nova porta versus reutilizar uma existente?
- Qual o impacto dessa arquitetura em aplicações com alta interação síncrona e UI complexa (ex: SPAs)?
- Como gerenciar transações distribuídas quando múltiplos adaptadores secundários estão envolvidos?