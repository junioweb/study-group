📘 Modelo de Registro de Aprendizados

**1. Referência da Leitura**  
Capítulo / Seção: *Fronteiras Explícitas e Regras de Dependência no Functional Core & Imperative Shell* (Síntese dos artigos analisados sobre FCIS em Go, Vue/Nuxt e contextos DDD)  

---

**2. Conceitos-Chave Identificados**  
- **Direção de dependências unidirecional**:  
  - O *Imperative Shell* pode depender do *Functional Core*, mas o inverso é estritamente proibido (regra fundamental para preservar a pureza do core).  
- **Fronteira como contrato**:  
  - A interface entre core e shell funciona como um contrato explícito, definindo quais dados entram e saem do core sem expor implementações.  
- **Isolamento de efeitos colaterais**:  
  - Qualquer operação com I/O, estado global, ou não determinística (data/hora, aleatoriedade) pertence exclusivamente ao shell.  
- **Imutabilidade como fronteira**:  
  - Dados que atravessam a fronteira devem ser imutáveis (ex.: tipos `readonly` em TypeScript, structs copiadas em Go) para evitar vazamento de efeitos colaterais.  
- **Validação de fronteira**:  
  - O shell deve validar e sanitizar todos os dados antes de passá-los ao core, garantindo que o core opere apenas com dados válidos e consistentes.  

---

**3. Insights Relevantes**  
> *"The shell can call the core, but the reverse is not allowed."*  
→ Esta regra simples é o cerne da arquitetura: violações criam dependências ocultas que comprometem a testabilidade e manutenibilidade.  

> *"The harder a component is to test, the more we are straying from a functional core."*  
→ Complexidade nos testes é um indicador direto de vazamento de responsabilidades do shell para o core.  

> *"No need to have data transfer objects (DTO) as the returned values from the core are read-only."*  
→ A imutabilidade nativa dos dados do core elimina a necessidade de DTOs, simplificando a comunicação entre camadas.  

> *"Think of the functional core as an isolated and perfect world, whereas the imperative shell is the layer to deal with the real imperfect world."*  
→ Esta metáfora ajuda a visualizar a separação conceitual: o core modela o domínio ideal, enquanto o shell lida com as impurezas da realidade.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Frontend (Vue/Nuxt)**:  
  - Componentes de rota (páginas Nuxt) como shell: responsáveis por *data fetching* e tratamento de erros.  
  - Componentes "dumb" e helpers de template como core: lógica de renderização pura, recebendo apenas props válidas.  
- **Backend (Go)**:  
  - Funções em `/core` devem aceitar apenas tipos primitivos ou structs imutáveis, nunca interfaces de repositório.  
  - Handlers HTTP em `/adapters` validam requisições antes de chamar o core, traduzindo erros específicos para respostas HTTP apropriadas.  
- **Estratégias de validação**:  
  - Usar *middleware* para sanitizar entradas antes de chegarem ao core (ex.: remover campos proibidos de payloads JSON).  
  - Implementar *type guards* no shell para garantir que dados inválidos nunca atinjam o core (ex.: `isValidCreditCard()` no shell antes de chamar `calculateDiscount()` no core).  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra do "arquivo proibido"**:  
  - Nenhum arquivo em `/core` pode importar módulos de `/shell`, `/adapters` ou bibliotecas de infraestrutura (ex.: `axios`, `database/sql`).  
- **Padrão de interface explícita**:  
  - Todas as funções do core devem declarar tipos de entrada/saída completos, sem `any` ou `interface{}` genéricos.  
- **Mecanismos de enforcement**:  
  - Configurar ESLint com regras customizadas (`no-restricted-imports`) para bloquear imports do shell no core.  
  - Usar `depcheck` ou `madge` em CI para detectar dependências violando a direção permitida.  
- **Padrão de nomenclatura**:  
  - Prefixar arquivos do shell com `adapter_` (ex.: `adapter_http_cart.go`, `adapter_db_user.ts`) para facilitar identificação visual.  
  - Funções do core devem ter nomes verbais puros (ex.: `calculateTotal()`, `validateOrder()`), enquanto funções do shell usam nomes imperativos (ex.: `fetchUserData()`, `saveToDatabase()`).  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como lidar com casos onde o core precisa de dados que só existem no shell (ex.: configurações de ambiente, feature flags)?  
- Qual a melhor estratégia para testar a fronteira em si (nem core nem shell, mas a interface entre ambos)?  
- Como aplicar esta separação em bibliotecas de terceiros que não seguem o paradigma (ex.: frameworks de autenticação que injetam estado global)?  
- Existe um tamanho ideal para o shell? Em sistemas complexos, o shell tende a inchar – como mitigar isso sem violar as fronteiras?  
- Como documentar visualmente as fronteiras em diagramas de arquitetura para facilitar o entendimento da equipe?