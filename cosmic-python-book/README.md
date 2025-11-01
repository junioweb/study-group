# Architecture Patterns with Python (Cosmic Python)

**Autores:** Harry Percival & Bob Gregory  
**Site:** [cosmicpython.com](https://www.cosmicpython.com/)  
**Foco:** Test-Driven Development, Domain-Driven Design, and Event-Driven Microservices

Este livro explora como construir arquiteturas robustas usando Python, aplicando princípios como TDD, DDD, Event-Driven Architecture e Clean Architecture com inversão de dependências.

## Progresso dos Estudos

### Parte 1: Building a Test-Driven Web App

- ✅ Prefácio e Introdução
- ✅ Parte 1: Introdução
- ✅ Capítulo 1: Domain Modeling
- ✅ Capítulo 2: Repository Pattern
- ✅ Capítulo 3: Abstractions
- ✅ Capítulo 4: Service Layer
- ✅ Capítulo 5: High Gear / Low Gear
- ✅ Capítulo 6: Unit of Work
- ✅ Capítulo 7: Aggregate

### Parte 2: Event-Driven Architecture

- ✅ Parte 2: Introdução
- ✅ Capítulo 8: Events and Message Bus
- ✅ Capítulo 9: All Messagebus
- ✅ Capítulo 10: Commands
- ✅ Capítulo 11: External Events
- ✅ Capítulo 12: CQRS
- ✅ Capítulo 13: Dependency Injection

## Conceitos Técnicos Principais

### Parte 1: Arquitetura Limpa e Padrões Fundamentais

#### Domain Modeling (Cap. 1)
- **Entidade vs. Objeto de Valor**: Entidades possuem identidade persistente; objetos de valor são imutáveis e comparáveis por valor
- **Serviço de Domínio**: Funções que encapsulam lógica de negócio que não pertence naturalmente a uma entidade
- **Linguagem Ubíqua**: Uso de termos do domínio nos nomes de classes, métodos e testes
- **Testes como Especificação**: Testes unitários expressam regras de negócio em linguagem do domínio

#### Repository Pattern (Cap. 2)
- **Inversão de Dependência**: O domínio não depende de infraestrutura; a persistência depende do domínio
- **Mapeamento Imperativo**: Definição da tabela separada da classe de domínio, invertendo a dependência
- **Portas e Adaptadores**: Interface (port) vs. implementação concreta (adapter)
- **Duck Typing e Protocolos**: Abstração definida por comportamento em vez de herança explícita

#### Abstractions (Cap. 3)
- **Functional Core, Imperative Shell (FCIS)**: Separar núcleo funcional (puro) da camada imperativa (I/O)
- **Fakes vs. Mocks**: Preferência por fakes (implementações reais simplificadas) para testes
- **Dependency Injection**: Injeção explícita de dependências permite substituir implementações

#### Service Layer (Cap. 4)
- **Orquestração vs. Lógica de Negócio**: Service Layer trata validações e transações, não contém regras de negócio
- **Padrão Ports & Adapters**: Service Layer depende de abstrações, permitindo testes com fakes
- **Testes em "Alta Marcha"**: Testes unitários na camada de serviço são ricos e rápidos

#### High Gear / Low Gear (Cap. 5)
- **Testes de Unidade vs. Integração**: Equilíbrio entre velocidade (unidade) e confiança (integração)
- **Pirâmide de Testes**: Base larga (unitários) e topo estreito (integração/E2E)

#### Unit of Work (Cap. 6)
- **Operações Atômicas**: Abstração que agrupa operações em bloco atômico (commit/rollback)
- **Context Manager**: Uso de `with` para definir escopo de unidade de trabalho
- **Abstração sobre Atomicidade**: UoW encapsula transações, isolando serviço da infraestrutura

#### Aggregate (Cap. 7)
- **Consistency Boundary**: Fronteira definida por um agregado para manter invariantes
- **Optimistic Concurrency Control**: Uso de números de versão para detectar conflitos
- **Repositório de Aggregate**: Apenas repositórios que retornam aggregates são permitidos

### Parte 2: Arquitetura Orientada a Eventos

#### Events and Message Bus (Cap. 8)
- **Domain Event**: Fato que ocorreu no sistema, representado como objeto imutável
- **Message Bus**: Mecanismo centralizado que mapeia eventos para handlers
- **UoW como Publisher**: UoW coleta eventos e os publica após commit

#### All Messagebus (Cap. 9)
- **Handlers de Eventos**: Múltiplos handlers podem reagir ao mesmo evento
- **Desacoplamento**: Sistema pode crescer sem modificar código existente

#### Commands (Cap. 10)
- **Comandos vs. Eventos**: Comandos expressam intenção (imperativo); eventos expressam fatos (passado)
- **Tratamento de Erros Distinto**: Comandos falham ruidosamente; eventos falham silenciosamente
- **Consistência por Agregado**: Comandos modificam um único agregado atomicamente

#### External Events (Cap. 11)
- **Integração entre Sistemas**: Eventos como contrato de comunicação entre sistemas
- **Eventos Externos**: Publicação de eventos para sistemas externos

#### CQRS (Cap. 12)
- **Command-Query Responsibility Segregation**: Separação entre escrita (comandos) e leitura (consultas)
- **Read Side vs. Write Side**: Lado de escrita garante consistência; lado de leitura prioriza performance
- **Event-Driven Read Models**: Atualização de modelos de leitura via eventos do domínio
- **Denormalized Read Models**: Estruturas otimizadas para leitura, frequentemente desnormalizadas

#### Dependency Injection (Cap. 13)
- **Composition Root**: Ponto único onde todas as dependências são injetadas (ex: `bootstrap.py`)
- **Explicit vs. Implicit Dependencies**: Preferência por dependências explícitas (parâmetros)
- **Manual DI**: Uso de closures/partials ou classes com `__call__` para injeção manual
- **Adapter Pattern com DI**: Estruturação de adaptadores com interfaces abstratas (ABCs)

## Padrões e Decisões de Design

### Arquitetura

- **Clean Architecture**: Domínio no centro, infraestrutura nas bordas
- **Inversão de Dependências**: Domínio não conhece infraestrutura
- **Ports & Adapters**: Interfaces abstratas (ports) e implementações concretas (adapters)
- **Service Layer**: Único ponto de entrada para casos de uso

### Domínio

- **Modelo de Domínio Puro**: Sem dependências de I/O, frameworks ou infraestrutura
- **Entidades e Objetos de Valor**: Distinção clara entre identidade e igualdade por valor
- **Aggregates**: Unidade de consistência e transação
- **Domain Events**: Fatos do domínio como objetos imutáveis

### Persistência

- **Repository Pattern**: Abstração sobre armazenamento persistente
- **Unit of Work**: Gerenciamento de transações e publicação de eventos
- **Mapeamento Imperativo**: Separação entre modelo de domínio e schema de banco
- **Optimistic Locking**: Controle de concorrência via números de versão

### Comunicação

- **Message Bus**: Centralização do roteamento de eventos e comandos
- **Commands**: Intenções que modificam estado (um handler por comando)
- **Events**: Fatos que ocorreram (múltiplos handlers por evento)
- **CQRS**: Separação entre escrita e leitura para otimização

### Testes

- **Fakes over Mocks**: Implementações simplificadas para componentes próprios
- **Functional Core, Imperative Shell**: Testes focados no núcleo funcional
- **Testes em Alta Marcha**: Testes unitários na camada de serviço com fakes
- **Testes de Integração**: Validação contra infraestrutura real quando necessário

### Dependências

- **Dependency Injection Manual**: Preferência por injeção explícita via parâmetros
- **Composition Root**: Bootstrap como ponto único de inicialização
- **Abstrações Explícitas**: Interfaces (ABCs ou Protocols) para serviços externos

## Estrutura de Arquivos Sugerida

```
project/
├── domain/              # Modelo de domínio puro
│   ├── model.py        # Entidades e objetos de valor
│   ├── events.py       # Eventos de domínio
│   └── exceptions.py   # Exceções de domínio
├── service_layer/      # Casos de uso e orquestração
│   ├── services.py     # Funções de serviço
│   └── unit_of_work.py # Unit of Work
├── adapters/           # Implementações concretas
│   ├── repository.py   # Repositórios (SQLAlchemy, etc.)
│   └── notifications.py # Adaptadores de notificação
├── entrypoints/        # Pontos de entrada
│   ├── api.py         # Flask/FastAPI endpoints
│   └── cli.py         # Command line interface
└── bootstrap.py        # Composition Root (DI)
```

## Princípios Fundamentais

1. **O domínio é o centro**: Tudo gira em torno do modelo de domínio
2. **Infraestrutura é detalhe**: O domínio não conhece detalhes de implementação
3. **Testes guiam o design**: Testabilidade revela boa arquitetura
4. **Abstrações simples**: Se é difícil criar um fake, a abstração está complicada
5. **Eventos como fatos**: Eventos representam coisas que aconteceram, não intenções
6. **Consistência eventual**: Leitura pode ser eventualmente consistente; escrita deve ser transacional
7. **Explicit is better than implicit**: Dependências explícitas são preferíveis

## Referências dos Registros de Estudo

Todos os capítulos possuem registros detalhados em arquivos Markdown seguindo o padrão de numeração sequencial. Consulte os arquivos individuais para insights específicos, citações relevantes e aplicações práticas de cada conceito.

