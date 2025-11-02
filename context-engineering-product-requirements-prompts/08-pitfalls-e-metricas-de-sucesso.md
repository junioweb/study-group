## 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Artigo**: *Context Engineering (2/2)—Product Requirements Prompts* por A B Vijay Kumar

#### 2. **Conceitos-Chave Identificados**
- **Principais armadilhas (pitfalls)** na engenharia de contexto:
  - **Context Overload**: fornecer demasiado contexto, sobrecarregando a IA.
  - **Ambiguous Instructions**: instruções vagas ou contraditórias levam a saídas inconsistentes.
  - **Insufficient Validation**: falta de critérios de validação torna difícil avaliar a qualidade da saída.
  - **Context Drift**: o significado do contexto muda ao longo do tempo ou entre diferentes usos.
- **Métricas para medir o sucesso da engenharia de contexto**:
  - **Accuracy**: % de saídas que atendem aos critérios de qualidade.
  - **Efficiency**: tempo e recursos necessários para processar o contexto.
  - **Consistency**: variação nas saídas para entradas similares.
  - **Scalability**: degradação de desempenho com aumento de complexidade.
  - **Maintainability**: esforço necessário para atualizar e modificar o contexto.

#### 3. **Insights Relevantes**
> “Providing too much context can overwhelm the AI and reduce performance.”
→ Mais não é melhor. O contexto deve ser **preciso e relevante**, não exaustivo.

> “Use PRP templates to ensure consistency and clarity.”
→ Templates são a defesa contra ambiguidade — eles impõem estrutura onde há caos.

> “Establish version control and regular context audits.”
→ Contexto é código. Deve ser versionado, auditado e mantido como qualquer outro ativo de software.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Implementar um **checklist de verificação de contexto** para evitar os pitfalls:
  - [ ] Contexto é conciso e focado?
  - [ ] Instruções são claras e não contraditórias?
  - [ ] Há critérios de validação definidos?
  - [ ] O contexto está versionado e documentado?
- Criar um **dashboard de métricas de contexto** para monitorar:
  - Taxa de sucesso dos PRPs (ex: % de features entregues sem retrabalho)
  - Tempo médio de refinamento de contexto
  - Consistência das saídas (ex: variação entre respostas de modelos diferentes)
- Realizar **auditorias mensais de contexto** para identificar drift e obsolescência.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Estabelecer uma **política de tamanho máximo de contexto** (ex: 5k tokens) e usar compressão ou RAG para manter a eficiência.
- Toda alteração em um contexto deve ser acompanhada de uma **justificativa e impacto esperado**.
- Incluir métricas de contexto no nosso **relatório de sprint** — elas são tão importantes quanto métricas de código.
- Criar um **role de “Context Owner”** para cada projeto ou feature, responsável por manter e auditar o contexto associado.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Quais ferramentas我们可以 usar para medir automaticamente a consistência e precisão do contexto?
- Como definir metas quantitativas para as métricas de sucesso (ex: “Accuracy > 90%”)?
- Como integrar as métricas de contexto com nossos KPIs de entrega de software?