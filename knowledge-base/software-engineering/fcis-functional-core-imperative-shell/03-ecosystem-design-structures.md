📘 Modelo de Registro de Aprendizados

**1. Referência da Leitura**  
Síntese dos artigos: *"The Functional Core Imperative Shell Paradigm"* (frontend/Nuxt) e *"Mastering Functional Core/Imperative Shell in Go"* + análise de frameworks opinativos (Contexto: Estruturas de projeto para diferentes ecossistemas tecnológicos)  

---

**2. Conceitos-Chave Identificados**  
- **Separação física vs. lógica**:  
  - Em projetos frontend, a separação é frequentemente lógica (pastas/modulos), enquanto em backends Go a separação pode ser física (pacotes distintos).  
- **Framework boundaries**:  
  - Frameworks opinativos (Nuxt, NestJS) têm estruturas pré-definidas que devem ser adaptadas para acomodar o FCIS sem lutar contra as convenções do framework.  
- **Domain-first organization**:  
  - Estruturas orientadas por domínio (não por técnica) mantêm o core funcional coeso, mesmo em frameworks que incentivam separação por tipo de arquivo.  
- **Adaptadores especializados**:  
  - Cada ponto de entrada no sistema (HTTP, CLI, eventos) requer adaptadores específicos que conectam o shell ao core.  
- **Barreiras de compilação**:  
  - Em linguagens com sistemas de módulos fortes (Go), usar limites de pacotes para reforçar a direção das dependências.  

---

**3. Insights Relevantes**  
> *"At the 'top' context of our component based frontend application, we have the page level components, which are where things related to navigation happen."*  
→ Em Nuxt, os componentes de página são naturalmente a camada shell, enquanto componentes atômicos e utilitários formam o core funcional.  

> *"Our imperative shell will interact with the core and the dependencies."*  
→ A camada do shell deve ser a única responsável por conhecer tanto o core quanto as dependências externas, agindo como tradutor entre os dois mundos.  

> *"Core stays pure, services orchestrate, adapters talk to the outside world."*  
→ Esta máxima define claramente as responsabilidades de cada camada na estrutura do projeto, independente do ecossistema.  

> *"Functions are usually enough."*  
→ Em Go, evitar over-engineering com structs e métodos onde funções simples e pacotes bem organizados são suficientes para manter o core puro.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Estrutura para Nuxt/Vue**:  
  ```bash
  /app
    ├── domain/              # Functional Core - lógica pura e tipos
    │   ├── cart/            # Módulos por contexto de domínio
    │   │   ├── types.ts     # Tipos imutáveis
    │   │   ├── logic.ts     # Funções puras (calculateTotal, validateItem)
    │   │   └── events.ts    # Eventos de domínio
    ├── application/         # Imperative Shell - orquestradores
    │   ├── services/        # Services com dependências injetadas
    │   └── use-cases/       # Casos de uso compostos
    ├── infrastructure/      # Adapters específicos
    │   ├── http/            # API routes e handlers
    │   ├── persistence/     # Repositórios e acesso a dados
    │   └── external/        # Comunicações com serviços externos
    └── presentation/        # UI components e páginas
        ├── components/      # Componentes reutilizáveis (quase puros)
        └── pages/           # Páginas Nuxt como shell final
  ```
  
- **Estrutura para Go**:  
  ```bash
  /internal
    ├── core/                # Functional Core - 100% puro, sem dependências externas
    │   ├── cart/
    │   └── user/
    ├── services/            # Imperative Shell - orquestradores e services
    │   ├── cart_service.go  # Coordenação de workflows
    │   └── checkout_orchestrator.go
    ├── adapters/            # Pontos de entrada específicos
    │   ├── http/            # Handlers HTTP
    │   ├── cli/             # Comandos CLI
    │   └── grpc/            # Serviços gRPC
    └── repositories/        # Implementações de persistência
        ├── postgres/        # Implementação PostgreSQL
        └── mocks/           # Mocks para testes
  /cmd
    ├── http/                # Ponto de entrada HTTP
    └── cli/                 # Ponto de entrada CLI
  ```

- **Adaptação para frameworks opinativos**:  
  - **Nuxt**: Usar o diretório `/composables` para funções puras (core) e `/server` para lógica imperativa (shell).  
  - **Next.js**: Separar `/app/api` (shell) de `/lib/domain` (core), usando Server Components como adaptadores.  
  - **NestJS**: Mapear modules do Nest para camadas FCIS (controllers/adapters = shell, services = core orquestrado).  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra do namespace**:  
  - Em TypeScript: Todos os arquivos do core devem ter o sufixo `.domain.ts` ou residir em `/domain`.  
  - Em Go: Pacotes do core devem ser importáveis sem dependências de `cmd/` ou `adapters/`.  
- **Regra de importação**:  
  - Nenhum arquivo em `/domain` ou `/core` pode importar diretórios como `/infrastructure`, `/adapters`, `/pages` ou `/cmd`.  
- **Padrão de interface**:  
  - Definir interfaces de repositórios no core, mas implementá-las no shell (dependency inversion).  
  - Exemplo em Go: `type UserRepository interface { ... }` em `/core/user`, implementação em `/adapters/postgres`.  
- **Padrão de contexto**:  
  - Em Go, passar `context.Context` apenas no shell e serviços, nunca no core puro.  
  - Em frontend, usar providers/context API apenas na camada de apresentação, mantendo o core livre de React/Vue specifics.  
- **Mecanismos de enforcement**:  
  - Configurar ESLint com `no-restricted-imports` para bloquear imports cruzando fronteiras do FCIS.  
  - Em Go, usar `go vet` customizado ou `golangci-lint` com regras para detectar violações de dependência.  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como lidar com frameworks que exigem decorators/metadata (ex.: NestJS) sem contaminar o core funcional?  
- Qual a melhor estratégia para gerenciar estado compartilhado (ex.: autenticação) que precisa estar acessível tanto no core quanto no shell?  
- Como manter a estrutura de projeto consistente em monorepos com múltiplas tecnologias (frontend/backend) seguindo FCIS?  
- Existem ferramentas de análise estática específicas para validar a conformidade FCIS em diferentes ecossistemas?  
- Como adaptar esta estrutura para serverless (AWS Lambda, Vercel) onde cada função pode ser vista como um shell independente?