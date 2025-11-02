### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Principles of Package Architecture" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- Três princípios de coesão de pacotes:
  - **REP (Release-Reuse Equivalency Principle)**: “A granularidade de reúso é a granularidade de release.”
  - **CCP (Common Closure Principle)**: “Classes que mudam juntas devem estar no mesmo pacote.”
  - **CRP (Common Reuse Principle)**: “Classes que não são reusadas juntas não devem estar no mesmo pacote.”

#### 3. **Insights Relevantes**
> “The CCP makes life easier for maintainers; REP and CRP make life easier for reusers.”  
→ Há **tensão natural** entre manutenibilidade e reusabilidade. A estrutura de pacotes deve evoluir conforme o projeto amadurece.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em fase inicial (prototipagem), priorizaremos **CCP**: agrupar por motivo de mudança (ex: tudo relacionado a "faturamento" em um pacote).
- Em fase madura (bibliotecas internas, microsserviços reutilizáveis), priorizaremos **REP/CRP**: extrair pacotes coesos e reutilizáveis (ex: `common-validation`, `payment-domain`).
- Evitaremos pacotes "genéricos" como `utils` ou `helpers` que violam CRP.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Pacotes serão versionados e publicados internamente (ex: via Nexus, GitHub Packages) quando seguirem REP.
- Revisões periódicas da estrutura de pacotes para alinhar com o estágio do projeto.
- Nenhum pacote conterá classes com motivos de mudança independentes.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como medir objetivamente "motivos de mudança" em equipes com visões distintas de domínio?
- Em monorepos, como aplicar REP sem sobrecarregar o processo de CI/CD?