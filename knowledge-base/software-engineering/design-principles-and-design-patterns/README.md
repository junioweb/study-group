# Design Principles and Design Patterns

**Autor:** Robert C. Martin (Uncle Bob)  
**Foco:** Princípios fundamentais de design orientado a objetos e padrões de design que promovem código limpo, manutenível e extensível

Esta coleção documenta o estudo sistemático dos princípios SOLID, princípios de arquitetura de pacotes e padrões de design clássicos, com aplicações práticas no contexto de desenvolvimento de software moderno.

## Progresso dos Estudos

### ✅ Princípios SOLID
- **SRP** - Single Responsibility Principle
- **OCP** - Open Closed Principle  
- **LSP** - Liskov Substitution Principle
- **ISP** - Interface Segregation Principle
- **DIP** - Dependency Inversion Principle

### ✅ Princípios de Arquitetura de Pacotes
- **REP** - Release-Reuse Equivalency Principle
- **CCP** - Common Closure Principle
- **CRP** - Common Reuse Principle
- **ADP** - Acyclic Dependencies Principle
- **SDP** - Stable Dependencies Principle
- **SAP** - Stable Abstractions Principle

### ✅ Padrões de Design
- **Abstract Server** - Interface de abstração entre cliente e servidor
- **Adapter** - Adaptação de interfaces incompatíveis
- **Observer** - Padrão de notificação de eventos
- **Bridge** - Separação de abstração e implementação
- **Abstract Factory** - Criação de famílias de objetos

## Conceitos Técnicos Principais

### Princípios SOLID

#### Single Responsibility Principle (SRP)
- **Definição**: Uma classe deve ter uma, e somente uma, razão para mudar
- **Foco**: Separação de preocupações baseada em atores/stakeholders
- **Aplicação**: Classes coesas com responsabilidades bem definidas, evitando "God Classes"

#### Open Closed Principle (OCP)
- **Definição**: Módulos devem estar abertos para extensão, mas fechados para modificação
- **Foco**: Extensibilidade através de abstrações e polimorfismo
- **Aplicação**: Design evolutivo que permite adicionar funcionalidades sem alterar código existente

#### Liskov Substitution Principle (LSP)
- **Definição**: Subclasses devem ser substituíveis por suas classes base
- **Foco**: Design por contrato e consistência semântica em hierarquias
- **Aplicação**: Herança significativa que respeita contratos implícitos

#### Interface Segregation Principle (ISP)
- **Definição**: Muitas interfaces específicas são melhores que uma interface genérica
- **Foco**: Minimização de dependências desnecessárias
- **Aplicação**: Interfaces coesas e client-specific, evitando "fat interfaces"

#### Dependency Inversion Principle (DIP)
- **Definição**: Dependa de abstrações, não de implementações concretas
- **Foco**: Inversão do fluxo de dependência tradicional
- **Aplicação**: Módulos de alto nível independentes de detalhes de baixo nível

### Princípios de Arquitetura de Pacotes

#### Princípios de Coesão (REP, CCP, CRP)
- **REP**: A granularidade de reúso é a granularidade de release
- **CCP**: Classes que mudam juntas devem estar juntas
- **CRP**: Classes que não são reusadas juntas não devem estar juntas
- **Tensão**: Equilíbrio entre manutenibilidade (CCP) e reusabilidade (REP/CRP)

#### Princípios de Acoplamento (ADP, SDP, SAP)
- **ADP**: Dependências entre pacotes não devem formar ciclos
- **SDP**: Dependa na direção da estabilidade
- **SAP**: Pacotes estáveis devem ser abstratos
- **Métrica**: Instability (I) e Abstractness (A) para análise quantitativa

### Padrões de Design

#### Abstract Server Pattern
- **Propósito**: Inserir interface abstrata entre cliente e servidor concreto
- **Benefício**: Resolve violações do DIP e permite substituição flexível
- **Aplicação**: Serviços internos como notificação, pagamento e logging

#### Adapter Pattern
- **Propósito**: Adaptar interfaces incompatíveis para trabalhar em conjunto
- **Benefício**: Integração com sistemas legados ou bibliotecas de terceiros
- **Aplicação**: SDKs de parceiros, APIs externas, migrações graduais

#### Observer Pattern
- **Propósito**: Notificação de eventos para múltiplos observadores
- **Benefício**: Acoplamento fraco entre publicadores e assinantes
- **Aplicação**: Sistemas de eventos, notificações em tempo real

#### Bridge Pattern
- **Propósito**: Separar abstração de implementação
- **Benefício**: Independência evolutiva entre interface e implementação
- **Aplicação**: Drivers, plugins, implementações multiplataforma

#### Abstract Factory Pattern
- **Propósito**: Criar famílias de objetos relacionados
- **Benefício**: Resolve conflito entre DIP e necessidade de instanciação
- **Aplicação**: Estratégias configuráveis, sistemas com plugins

## Aplicações Práticas no Nosso Contexto

### Estratégia de Implementação
1. **Domínio First**: Interfaces definidas no domínio, implementações na infraestrutura
2. **Dependency Injection**: Injeção de dependências para resolver abstrações
3. **Test-Driven**: Mocks implementando interfaces de domínio para testes
4. **Evolutionary Design**: Estrutura de pacotes que evolui com o projeto

### Decisões de Design Adotadas
- ✅ Nenhum módulo de domínio depende diretamente de implementações concretas
- ✅ Interfaces pertencem ao consumidor, não ao fornecedor
- ✅ Proibido ciclos de dependência entre módulos/pacotes
- ✅ Pacotes estáveis são abstratos, instáveis são concretos
- ✅ Cada padrão aplicado conforme necessidade específica do problema

### Ferramentas e Métricas
- **Detecção de Ciclos**: ArchUnit, SonarQube para análise de dependências
- **Métricas de Qualidade**: Instability (I) e Abstractness (A) monitoradas
- **Testes Automatizados**: Testes de contrato para validar LSP
- **Versionamento**: Pacotes seguindo REP são versionados e publicados

## Relacionamentos entre Princípios e Padrões

### SOLID como Fundamento
- **SRP** → Base para todos os outros princípios
- **OCP** → Habilitado pelo **DIP** através de abstrações
- **LSP** → Garante que **OCP** funcione corretamente com herança
- **ISP** → Complementa **DIP** ao minimizar dependências

### Padrões como Implementação
- **Abstract Server** → Implementação prática do **DIP**
- **Adapter** → Permite **DIP** com sistemas legados/terceiros
- **Abstract Factory** → Resolve tensão **DIP** × instanciação
- **Bridge** → Facilita **OCP** para dimensões de variação

### Princípios de Pacote como Arquitetura
- **ADP** → Previne dependências circulares que quebram **DIP**
- **SDP** + **SAP** → Garante que **DIP** seja sustentável em larga escala
- **REP/CCP/CRP** → Organiza código para facilitar **SOLID**

## Princípios Fundamentais

1. **Dependência de Abstrações**: DIP como princípio central
2. **Coesão Forte**: SRP aplicado em múltiplos níveis (classe, pacote)
3. **Extensibilidade Controlada**: OCP através de pontos de articulação
4. **Substituição Segura**: LSP garantindo confiabilidade polimórfica
5. **Interfaces Específicas**: ISP prevenindo poluição de dependências
6. **Estrutura sem Ciclos**: ADP mantendo dependências saneadas
7. **Estabilidade Direcionada**: SDP + SAP para arquitetura sustentável
8. **Empacotamento Inteligente**: REP/CCP/CRP para organização ótima
9. **Padrões como Soluções**: Uso contextual de padrões de design
10. **Evolução Contínua**: Design que evolui com necessidades do projeto

## Referências dos Registros de Estudo

Todos os conceitos possuem registros detalhados em arquivos Markdown seguindo o padrão de numeração sequencial:

### Princípios SOLID
- `01-single-responsibility-principle.md` - Single Responsibility Principle
- `02-open-closed-principle.md` - Open Closed Principle  
- `03-liskov-substitution-principle.md` - Liskov Substitution Principle
- `04-interface-segregation-principle.md` - Interface Segregation Principle
- `05-dependency-inversion-principle.md` - Dependency Inversion Principle

### Princípios de Pacote
- `06-package-cohesion-principles.md` - REP, CCP, CRP
- `07-package-coupling-principles.md` - ADP, SDP, SAP

### Padrões de Design
- `08-abstract-server-pattern.md` - Abstract Server Pattern
- `09-adapter-pattern.md` - Adapter Pattern
- `10-observer-pattern.md` - Observer Pattern
- `11-bridge-pattern.md` - Bridge Pattern
- `12-abstract-factory-pattern.md` - Abstract Factory Pattern

Consulte os arquivos individuais para insights específicos, citações relevantes, aplicações práticas e dúvidas de cada conceito.

## Referências Externas

- **Livro Principal**: "Design Principles and Design Patterns" - Robert C. Martin
- **SOLID Principles**: Robert C. Martin - https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html
- **Package Principles**: Robert C. Martin - http://www.cvc.uab.es/shared/teach/a21291/temes/object_oriented_design/materials_adicionals/principles_and_patterns.pdf
- **Design Patterns**: Gang of Four - "Design Patterns: Elements of Reusable Object-Oriented Software"
- **Clean Architecture**: Robert C. Martin - https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html

## Próximos Passos

1. **Aplicação Prática**: Implementar esses princípios em projetos reais
2. **Refinamento Contínuo**: Revisitar conceitos com experiência prática
3. **Expansão**: Estudar padrões adicionais (Strategy, Decorator, Template Method)
4. **Integração**: Combinar com Arquitetura Hexagonal e outros estilos arquiteturais
5. **Mentoria**: Compartilhar conhecimento com outros membros da equipe