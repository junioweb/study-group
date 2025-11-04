# The Hexagonal (Ports & Adapters) Architecture

**Autor:** Alistair Cockburn  
**Data:** 2005-09-04 (v 0.9)  
**Foco:** Arquitetura que permite desenvolver e testar aplicações isoladas de suas interfaces externas (UI, banco de dados, serviços)

A Arquitetura Hexagonal (também conhecida como Ports & Adapters) resolve o problema da infiltração da lógica de negócio em interfaces e do acoplamento rígido à infraestrutura externa, permitindo que aplicações sejam igualmente impulsionadas por usuários, programas, testes automatizados ou scripts em lote.

## Progresso dos Estudos

- ✅ Introdução, Intent e Motivation
- ✅ Nature of the Solution, Structure e Sample Code
- ✅ Application Notes – The Left-Right Asymmetry
- ✅ Application Notes – Use Cases And The Application Boundary

## Conceitos Técnicos Principais

### Fundamentos da Arquitetura Hexagonal

#### Intenção e Motivação (Intent, Motivation)
- **Problema Central**: Infiltração da lógica de negócio na interface do usuário e acoplamento rígido à infraestrutura externa (bancos de dados)
- **Consequências**:
  - Impossibilidade de testar com suítes automatizadas (lógica acoplada a detalhes visuais)
  - Dificuldade de migração de sistemas dirigidos por humanos para sistemas em lote
  - Impossibilidade de permitir que programas dirijam a aplicação quando necessário
- **Solução Proposta**: Criar aplicações que funcionem sem UI ou banco de dados, permitindo:
  - Testes de regressão automatizados
  - Funcionamento quando o banco de dados está indisponível
  - Integração de aplicações sem envolvimento do usuário
- **Princípio Fundamental**: A aplicação deve ser autossuficiente em sua lógica de domínio, independente de tecnologias periféricas

#### Estrutura de Ports e Adapters (Nature of the Solution, Structure)
- **Porta (Port)**: Define um protocolo de conversação com propósito semântico claro (ex: "receber comandos do usuário", "persistir dados"). É uma interface estável que identifica uma conversa com propósito
- **Adaptador (Adapter)**: Implementação concreta que converte sinais de uma tecnologia externa (GUI, banco SQL, API HTTP, mock) para chamadas de procedimento utilizáveis pela aplicação e vice-versa
- **Hexágono Interno**: A aplicação reside no interior do hexágono e comunica-se exclusivamente por meio de portas, sem conhecer a natureza das coisas do outro lado dos adaptadores
- **Simetria**: O problema não é entre esquerda e direita, mas entre **dentro e fora**. Código do interior não deve vazar para o exterior
- **Forma Hexagonal**: O hexágono não representa seis lados literais, mas serve para romper com a mentalidade de arquiteturas em camadas unidimensionais, permitindo inserir múltiplas portas conforme necessário

#### Portas Primárias e Secundárias (The Left-Right Asymmetry)
- **Portas Primárias (Driving)**: São acionadas por atores primários que **disparam** a aplicação, tirando-a do estado quiescente para realizar uma função anunciada. Exemplos: usuário, sistema externo, script de teste
  - Localizadas no "lado esquerdo" (ou superior) do hexágono
  - Adaptadores de teste naturais: **harnesses** (ex: FIT, frameworks de teste automatizado)
- **Portas Secundárias (Driven)**: São usadas pela aplicação para **interagir com atores secundários** que fornecem informações ou são notificados. Exemplos: banco de dados, serviço externo, sistema de notificação
  - Localizadas no "lado direito" (ou inferior) do hexágono
  - Adaptadores de teste naturais: **mocks** (substitutos em memória)
- **Distinção Baseada em Casos de Uso**: A diferenciação vem da ideia de atores primários (que dirigem) versus secundários (que são dirigidos) nos casos de uso
- **Observação**: Embora a implementação seja assimétrica, a arquitetura permanece simétrica em sua estrutura

#### Casos de Uso e Fronteira da Aplicação (Use Cases And The Application Boundary)
- **Fronteira da Aplicação**: Casos de uso devem ser escritos na fronteira da aplicação (hexágono interno), descrevendo funções e eventos suportados, **independentemente da tecnologia externa**
- **Erro Comum**: Escrever casos de uso com conhecimento íntimo da tecnologia externa, resultando em casos de uso longos, difíceis de ler, frágeis e caros de manter
- **Benefícios de Casos de Uso na Fronteira**:
  - Mais curtos e fáceis de ler
  - Menos caros de manter
  - Mais estáveis ao longo do tempo
- **Especificação Funcional**: Deve ser feita contra a interface do hexágono interno, não contra qualquer tecnologia externa específica

### Implementação Prática

#### Estrutura de Portas
- **Quantidade de Portas**: Não há regra rígida. Aplicações simples podem ter duas portas (primária e secundária), mas podem ter três, quatro ou mais conforme necessário
  - Exemplo de quatro portas: sistema de alertas meteorológicos (feed de dados, administrador, notificações, banco de assinantes)
  - Não há dano significativo em escolher o número "errado" de portas
- **Definição de Porta**: Uma porta identifica uma conversa com propósito. Pode ser ampla (ex: "comunicação com usuário") ou específica (ex: "gerenciamento de pedidos")
- **Múltiplos Adaptadores por Porta**: Uma porta pode ter vários adaptadores para diferentes tecnologias (ex: GUI, CLI, API HTTP, test harness)

#### Ordem de Desenvolvimento Sugerida
1. **Testes + Mock**: Desenvolver com harness de teste (ex: FIT) dirigindo a aplicação e usando mock (banco em memória)
2. **UI + Mock**: Adicionar interface de usuário, ainda usando mock de banco de dados
3. **Testes + DB Real**: Em testes de integração, usar scripts automatizados contra banco de dados real com dados de teste
4. **UI + DB Real**: Em uso real, pessoa usando a aplicação para acessar banco de dados de produção

#### Código de Exemplo (Simplificado)
```java
// Interface da Porta Secundária
public interface RateRepository {
   double getRate(double amount);
}

// Aplicação (Hexágono Interno)
public class Discounter {
   private RateRepository rateRepository;
   
   public Discounter(RateRepository r) {
      rateRepository = r;
   }
   
   public double discount(double amount) {
      double rate = rateRepository.getRate(amount);
      return amount * rate;
   }
}

// Adaptador Mock
public class MockRateRepository implements RateRepository {
   public double getRate(double amount) {
      if(amount <= 100) return 0.01;
      if(amount <= 1000) return 0.02;
      return 0.05;
   }
}
```

## Padrões e Decisões de Design

### Princípios Arquiteturais

- **Isolamento da Aplicação**: A aplicação deve poder funcionar sem UI ou banco de dados
- **Simetria Dentro-Fora**: A assimetria relevante é entre dentro e fora, não entre esquerda e direita
- **Zero Vazamento de Lógica**: Código do interior não deve vazar para o exterior
- **Portas por Propósito**: Portas identificam conversas com propósito, não tecnologias
- **Substituibilidade**: Adaptadores devem ser substituíveis sem alterar a aplicação

### Estrutura de Código

- **Toda Dependência Externa é uma Porta**: UI, DB, API, fila, sistema de notificação — todos devem ser acessados via interface definida no domínio
- **Adaptadores Fora do Núcleo**: Adaptadores residem fora do núcleo da aplicação e traduzem protocolos externos para chamadas de domínio
- **Injeção de Dependência**: Adaptadores devem ser injetados na aplicação no momento da inicialização
- **Nomes Expressivos**: Interfaces de porta devem ter nomes claros indicando propósito: `UserCommandPort` (primária), `PaymentGatewayPort` (secundária)

### Modelagem de Portas

- **Definir Portas por Propósito Semântico**: Portas devem representar conversas com significado, não implementações técnicas
- **Múltiplos Adaptadores são Esperados**: Uma porta pode e deve ter vários adaptadores (produção, teste, mock)
- **Portas Primárias**: Descrevem como a aplicação é **acionada** do mundo externo
- **Portas Secundárias**: Descrevem como a aplicação **obtém dados ou notifica** sistemas externos

### Integração com Processo de Desenvolvimento

- **Desenvolvimento em Isolamento**: Aplicações ou componentes podem ser testados em modo standalone usando FIT e mocks
- **Testes Automatizados Antes da UI**: Equipe de QA pode criar testes automatizados antes da implementação da UI, alinhando desenvolvimento e validação
- **Especificação Contra Fronteira**: Todo refinamento de backlog deve resultar em casos de uso escritos contra a fronteira da aplicação
- **Proibido Detalhes Tecnológicos em Casos de Uso**: Não mencionar tecnologias específicas (ex: "botão", "tabela SQL") em casos de uso de alto nível
- **Testes de Aceitação como Expressão Executável**: Testes de aceitação automatizados são a expressão executável dos casos de uso

### Testes e Qualidade

- **Testes Detectam Vazamento**: Testes automatizados de regressão detectam qualquer violação da promessa de manter lógica de negócio fora da camada de apresentação
- **Testes de Integração Focados**: Focam na composição de adaptadores reais com a aplicação, mas ainda isolados de infraestrutura externa quando possível
- **Mocks para Portas Secundárias**: Substitutos em memória para bancos de dados e serviços externos
- **Harnesses para Portas Primárias**: Frameworks de teste (ex: FIT) para dirigir a aplicação como um ator primário

### Performance e Escalabilidade

- **Modo Headless**: Aplicação pode ser implantada em modo headless, disponibilizando apenas a API
- **Integração Entre Aplicações**: Facilita integração entre microsserviços ou sistemas legados via APIs bem definidas, sem dependência de interfaces humanas
- **Substituição de Infraestrutura**: Permite trocar banco de dados, serviços externos ou interfaces sem alterar a aplicação

## Princípios Fundamentais

1. **A aplicação é isolada**: Funciona sem UI ou banco de dados para permitir testes automatizados
2. **Portas definem protocolos**: Identificam conversas com propósito, não tecnologias
3. **Adaptadores são substituíveis**: Múltiplos adaptadores podem implementar a mesma porta
4. **Simetria dentro-fora**: A assimetria relevante é entre dentro e fora, não entre lados
5. **Lógica não vaza**: Código do interior não deve vazar para o exterior
6. **Casos de uso na fronteira**: Especificações funcionais são feitas contra o hexágono interno
7. **Testes detectam violações**: Testes automatizados detectam vazamento de lógica
8. **Primárias dirigem, secundárias são dirigidas**: Portas primárias são acionadas; secundárias são usadas pela aplicação
9. **Desenvolvimento incremental**: Desenvolver com testes e mocks, evoluir para UI e DB real
10. **APIs visíveis**: Portas primárias devem ter APIs visíveis para permitir integração entre aplicações

## Padrões Relacionados

### Adapter Pattern
A Arquitetura Hexagonal é uma aplicação particular do padrão Adapter do Design Patterns, onde adaptadores convertem interfaces de classes em interfaces esperadas pelos clientes.

### Dependency Inversion (Dependency Injection)
O Princípio de Inversão de Dependência de Bob Martin (também chamado Dependency Injection por Martin Fowler) é fundamental: "Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações."

### Model-View-Controller (MVC)
MVC implementa a ideia de ports-and-adapters nas portas primárias, mas não nas secundárias.

### Mock Objects
Mocks são substitutos em memória para atores secundários (ex: bancos de dados), permitindo testes isolados.

### FIT (Framework for Integrated Testing)
FIT é um framework natural para criar adaptadores de teste para portas primárias, permitindo que testes automatizados dirijam a aplicação.

## Referências dos Registros de Estudo

Todos os conceitos possuem registros detalhados em arquivos Markdown seguindo o padrão de numeração sequencial:

- `01-intencao-e-motivacao-da-arquitetura-hexagonal.md` - Intent, Motivation
- `02-estrutura-de-ports-e-adapters.md` - Nature of the Solution, Structure, Sample Code
- `03-portas-primarias-e-secundarias.md` - Application Notes – The Left-Right Asymmetry
- `04-casos-de-uso-e-fronteira-da-aplicacao.md` - Application Notes – Use Cases And The Application Boundary

Consulte os arquivos individuais para insights específicos, citações relevantes e aplicações práticas de cada conceito.

## Referências Externas

- Artigo Original: "The Hexagonal (Ports & Adapters) Architecture" - Alistair Cockburn (2005)
- FIT: Framework for Integrated Testing - http://fit.c2.com
- Dependency Inversion Principle - Robert C. Martin
- Dependency Injection - Martin Fowler - http://martinfowler.com/articles/injection.html
- Mock Objects - http://MockObjects.com

