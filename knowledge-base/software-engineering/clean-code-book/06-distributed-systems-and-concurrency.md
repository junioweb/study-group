# 📘 Registro de Aprendizado

## 1. Referência da Leitura
Capítulo 13 – "Concurrency" e Apêndice A – "Concurrency II" do livro *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin)

## 2. Conceitos-Chave Identificados

- **Concorrência como estratégia de desacoplamento**: Concorrência desacopla "o quê" do "quando", permitindo que sistemas sejam estruturados como múltiplos computadores colaborativos em vez de um grande loop principal.
- **Princípio da Responsabilidade Única aplicado à concorrência**: O código relacionado à concorrência deve ser mantido separado do código de aplicação, pois tem seu próprio ciclo de vida, desafios e necessidades de mudança.
- **Defesas contra problemas de concorrência**:
  - Limitar o escopo de dados compartilhados
  - Usar cópias de dados quando possível
  - Manter threads tão independentes quanto possível
  - Manter seções sincronizadas o menor possível
  - Evitar dependências entre métodos sincronizados
- **Padrões de execução**:
  - Producer-Consumer: um ou mais produtores geram dados, um ou mais consumidores processam
  - Readers-Writers: múltiplos leitores podem acessar simultaneamente, mas escritores exigem acesso exclusivo
  - Dining Philosophers: problema clássico de deadlock e starvation
- **Bibliotecas de concorrência**: Conhecimento profundo das bibliotecas disponíveis (ex: `java.util.concurrent`, `java.util.concurrent.atomic`, `java.util.concurrent.locks` em Java)
- **Testes para concorrência**:
  - Instrumentação para expor problemas concorrentes
  - Testes focados em throughput e desempenho
  - Testes que validam cenários de shutdown adequados
- **Separação entre construção e uso**: Separar o processo de inicialização (construção de objetos e injeção de dependências) da lógica de runtime.
- **Deadlocks e Livelocks**: Compreensão dos padrões que levam a esses problemas e estratégias para evitá-los.
- **Bound Resources**: Gerenciamento adequado de recursos de tamanho fixo ou número limitado em ambientes concorrentes (ex: conexões de banco de dados).

## 3. Frases ou Ideias que Trouxeram Clareza

> "Concurrency is a decoupling strategy. It helps us decouple what gets done from when it gets done."

> "Concurrency-related code has its own life cycle of development, change, and tuning. Concurrency-related code has its own challenges, which are different from and often more difficult than nonconcurrency-related code."

> "The number of ways in which miswritten concurrency-based code can fail makes it challenging enough without the added burden of surrounding application code."

> "Keep your synchronized sections as small as possible."

> "Writing clean concurrent programs is hard—very hard. It is much easier to write code that executes in a single thread. It is also easy to write multithreaded code that looks fine on the surface but is broken at a deeper level."

> "Testes para concorrência devem não apenas verificar comportamento, mas validar throughput, shutdown adequado e resiliência a condições de corrida."

→ Essas ideias reforçam que a programação concorrente não é apenas uma técnica adicional, mas requer uma abordagem fundamentalmente diferente de design. A separação clara entre código concorrente e sequencial é essencial para construir sistemas robustos e mantíveis.

## 4. Relação com Nossos Sistemas

- **Mistura de responsabilidades**: Identificamos em nosso serviço de processamento de pagamentos código concorrente misturado com lógica de negócios, tornando difícil identificar e resolver problemas de concorrência.
- **Seções sincronizadas excessivamente grandes**: Em nosso cache distribuído, temos blocos synchronized que abrangem operações complexas, criando gargalos de desempenho.
- **Falta de conhecimento das bibliotecas de concorrência**: Muitas vezes reimplementamos funcionalidades já disponíveis em bibliotecas padrão (ex: reinventando locks simples quando poderíamos usar ReentrantLock).
- **Testes insuficientes para cenários concorrentes**: Nossa suíte de testes foca principalmente em cenários sequenciais, com pouca cobertura para condições de corrida e deadlocks.
- **Problemas de shutdown**: Em nosso sistema de mensageria, shutdowns frequentemente resultam em perda de mensagens devido a lógica inadequada de encerramento.
- **Recursos compartilhados não gerenciados**: Conexões de banco de dados são frequentemente compartilhadas sem mecanismos adequados de pooling ou sincronização.

## 5. Decisões de Design ou Padrões Adotar

- **Separação rigorosa de código concorrente**:
  - Criar um pacote/componente específico para código relacionado à concorrência
  - Nenhum código de concorrência deve estar misturado com lógica de negócios
  - Implementar o padrão Separation of Main para isolar a construção do sistema

- **Padrões para gerenciamento de dados compartilhados**:
  - Usar estruturas de dados thread-safe nativas (ex: ConcurrentHashMap em vez de HashMap)
  - Limitar o escopo de dados compartilhados ao mínimo possível
  - Utilizar objetos imutáveis sempre que possível
  - Implementar cópias de segurança para operações de leitura quando a consistência eventual é aceitável

- **Padrões para sincronização**:
  - Manter seções sincronizadas com no máximo 5-10 linhas de código
  - Usar locks com ordem predefinida para evitar deadlocks
  - Implementar timeouts em operações de bloqueio
  - Utilizar o padrão tryLock para evitar deadlocks

- **Padrões para testes concorrentes**:
  - Implementar testes de estresse com múltiplas threads (ex: 100+ threads simultâneas)
  - Usar frameworks de teste concorrente (ex: ConTest, JCStress)
  - Medir e estabelecer metas de throughput mínimo
  - Testar cenários de shutdown abrupto e gradual
  - Instrumentar código para detectar condições de corrida

- **Padrões para recursos limitados**:
  - Implementar pooling para recursos escassos (conexões de banco, sockets)
  - Estabelecer timeouts claros para aquisição de recursos
  - Implementar estratégias de fallback para quando recursos estiverem indisponíveis
  - Monitorar métricas de uso de recursos concorrentes

- **Padrões de shutdown**:
  - Implementar graceful shutdown com tempo limite configurável
  - Garantir que todas as mensagens/processos em andamento sejam concluídos
  - Criar mecanismos para salvar estado intermediário antes do shutdown
  - Testar cenários de shutdown em todos os componentes concorrentes

## 6. Questões para Estudo Futuro

- Como balancear a complexidade da programação concorrente com a necessidade de manter o código simples e compreensível?
- Qual é a melhor abordagem para migrar sistemas legados sequenciais para arquiteturas concorrentes sem introduzir riscos significativos?
- Como medir objetivamente a qualidade e robustez do código concorrente além de testes básicos?
- Quais são as melhores práticas para monitorar e diagnosticar problemas de concorrência em produção?
- Como aplicar princípios de concorrência em arquiteturas baseadas em microsserviços e sistemas distribuídos?
- Qual é a relação entre programação funcional (imutabilidade, pureza) e a programação concorrente?
- Como treinar equipes para identificar e resolver problemas de concorrência de forma proativa?
- Como lidar com a complexidade crescente de sistemas concorrentes em ambientes de alta escala (ex: sistemas com milhares de threads)?