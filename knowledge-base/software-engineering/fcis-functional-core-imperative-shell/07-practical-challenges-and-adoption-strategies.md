📘 Modelo de Registro de Aprendizados
 
**1. Referência da Leitura**  
Síntese dos artigos: *"The Functional Core Imperative Shell Paradigm"* e *"Mastering Functional Core/Imperative Shell in Go: A Pragmatic Guide to Clean Architecture"* + relatos de adoção em ambientes corporativos (Contexto: Estratégias práticas para implementação do padrão FCIS em times e sistemas existentes)  

---

**2. Conceitos-Chave Identificados**  
- **Adoção incremental**:  
  - Estratégia de "cirurgia de precisão" para isolar módulos candidatos à refatoração para FCIS em sistemas monolíticos.  
- **Zonas de conforto paradigmáticas**:  
  - Resistência natural de equipes com background OOP forte à inversão de mentalidade funcional, mesmo em sistemas híbridos.  
- **Pragmatismo arquitetural**:  
  - Regra dos 80/20: 80% do código como core funcional, 20% como shell imperativo, aceitando exceções quando o custo de pureza excede benefícios.  
- **Métricas de valor tangível**:  
  - Definição de indicadores objetivos para justificar a adoção (redução de bugs críticos, tempo de onboarding, velocidade de refatoração).  
- **Ponto de inflexão de adoção**:  
  - Momento em que a curva de aprendizado inicial é superada e os benefícios começam a superar os custos de transição.  

---

**3. Insights Relevantes**  
> *"Like I mentioned before, the tendency is to want to fetch data close to where it will be consumed and displayed, or manipulate it close to where it will be fetched, and this paradigm requires holding ourselves to a higher agreed standard."*  
→ A adoção do FCIS exige disciplina coletiva e convenções explícitas para evitar o "caminho fácil" que contamina as fronteiras arquiteturais.  

> *"This is an experiment for a greenfield project I am currently working on, and it has been successful for the first 6 months of life. However, we are still working with the initial dev team of 4 engineers, so communication is clear."*  
→ O sucesso inicial em projetos novos com times pequenos não garante escalabilidade da abordagem para equipes maiores e sistemas legados.  

> *"If you've never tried FC/IS, start small: refactor one service into a pure core with an orchestrator shell. You'll feel the difference."*  
→ A adoção deve ser progressiva e experiencial, permitindo que o time "sinta" os benefícios antes de comprometer grandes mudanças.  

> *"There is an extremely high level of confidence that we are not introducing breaking changes, for the most part."*  
→ A confiança na estabilidade do sistema é um benefício imediato e mensurável que justifica o investimento inicial em reestruturação.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Estratégia de migração para monolitos**:  
  - Identificar módulos com alta complexidade de negócio e baixa dependência de I/O como candidatos prioritários para refatoração para FCIS.  
  - Criar "ilhas funcionais" dentro do monolito, gradualmente expandindo as fronteiras do core funcional.  
- **Programa de capacitação em fases**:  
  - Fase 1: Workshops práticos com exemplos concretos de como o FCIS resolve problemas do dia a dia (bugs difíceis de reproduzir, refatorações arriscadas).  
  - Fase 2: Pair programming em módulos não críticos para aplicar o padrão com mentoria.  
  - Fase 3: Revisão de código focada em validação das fronteiras core/shell.  
- **Trade-offs aceitáveis**:  
  - Permitir que módulos de UI extremamente dinâmicos tenham shell mais espesso, desde que a lógica de negócio crítica permaneça no core.  
  - Usar wrappers funcionais temporários para bibliotecas imperativas essenciais (ex.: autenticação), isolando sua complexidade.  
- **Medição de ROI**:  
  - Comparar métricas antes/depois da adoção em módulos selecionados:  
    - Tempo para corrigir bugs em produção (alvo: redução de 40%)  
    - Cobertura de testes sem mocks (alvo: >85% no core)  
    - Tempo médio de onboarding de novos desenvolvedores (alvo: redução de 30%)  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra dos 3 passos**:  
  - 1. Identificar lógica de negócio pura em arquivos existentes.  
  - 2. Extrair para pastas `/core` mantendo testes existentes.  
  - 3. Refatorar testes para remover mocks e validar apenas comportamento.  
- **Padrão de exceções controladas**:  
  - Documentar explicitamente onde o FCIS não é aplicado e por quê (ex.: performance crítica, bibliotecas incompatíveis).  
  - Revisar estas exceções trimestralmente para identificar oportunidades de refatoração.  
- **Mecanismo de enforcement gradual**:  
  - Iniciar com revisão manual de PRs focada nas fronteiras.  
  - Após 3 meses, implementar regras estáticas (ESLint, linters customizados).  
  - Após 6 meses, bloquear merges com violações críticas de fronteira no CI.  
- **Critérios de adoção por módulo**:  
  - Priorizar módulos com: alta frequência de bugs, alto impacto no negócio, e alta rotatividade de desenvolvedores.  
  - Adiar módulos estáveis com baixa complexidade de negócio e alto acoplamento com infraestrutura.  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como lidar com pressão de prazos apertados que frequentemente levam à quebra das fronteiras para "entregar rápido"?  
- Qual a estratégia para times com conhecimento técnico heterogêneo (alguns com background funcional, outros apenas OOP)?  
- Como demonstrar ROI para stakeholders não técnicos que priorizam features sobre arquitetura?  
- Existe um ponto de saturação onde o custo de manter a pureza funcional supera os benefícios em sistemas muito grandes?  
- Como integrar FCIS em ambientes com compliance rigoroso onde toda mudança de arquitetura requer aprovação formal?  