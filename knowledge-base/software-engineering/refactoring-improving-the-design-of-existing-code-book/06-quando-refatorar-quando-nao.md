### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: “When Should I Refactor?” (Cap. introdutório e seções esparsas ao longo do livro, especialmente no Cap. 1 e nas conclusões)

#### 2. **Conceitos-Chave Identificados**
- Refatoração deve ser **oportuna e contextual**, não uma tarefa isolada ou programada em grandes blocos.
- **Três momentos ideais para refatorar**:
  - Ao **adicionar nova funcionalidade** (refatoração preparatória).
  - Ao **corrigir um bug** (refatoração para compreensão).
  - Ao **ler código legado** (refatoração de limpeza leve — “litter-pickup refactoring”).
- **Não refatore código que não será tocado** — se ele funciona como uma API estável e não será modificado, deixe como está.
- **Refatoração ≠ reescrita**: a primeira preserva o comportamento observável; a segunda pode mudar contratos e introduzir riscos.
- **Mudanças incrementais são mais seguras** do que refatorações em massa, especialmente em sistemas sem testes robustos.

#### 3. **Insights Relevantes**
> “If I run across code that is a mess, but I don’t need to modify it, then I don’t need to refactor it.”  
→ Refatorar por perfeccionismo é desperdício. Refatore apenas quando há **valor prático imediato**.

> “The fastest way to add a feature is often to refactor first.”  
→ Um design adequado reduz o tempo total de implementação — mesmo que pareça contraintuitivo sob pressão.

> “If it’s easier to rewrite than to refactor, then rewrite—but that’s rare.”  
→ A decisão entre refatorar e reescrever exige julgamento técnico maduro, não impulso.

> “Refactoring doesn’t break the code with each small step—so sometimes it takes months to complete the job, but the code is never broken.”  
→ A segurança da refatoração está na **granularidade dos passos**, não na velocidade aparente.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Durante o desenvolvimento de uma nova feature, **dedicar 10–20% do tempo à refatoração preparatória** da área afetada.
- Ao investigar um bug, **extrair funções, renomear variáveis e remover lógica morta** para tornar a causa raiz evidente.
- Em sistemas legados com baixa cobertura, **evitar refatorações amplas**; em vez disso, aplicar melhorias locais sempre que o código for visitado (“leave the campsite cleaner”).
- Tratar bibliotecas internas estáveis como **APIs congeladas**: não refatore apenas para “modernizar” — só o faça se houver necessidade de mudança.
- Usar a **Regra dos Três (Rule of Three)** como gatilho: na terceira repetição de lógica similar, refatore para eliminar duplicação.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Nunca refatore código que não será modificado** — a menos que esteja causando problemas de desempenho, segurança ou manutenção indireta.
- **Sempre prefira refatoração incremental a reescrita**, a menos que o código seja tão caótico que não permita extração segura de comportamento.
- **Refatoração em massa só é aceitável com suíte de testes abrangente** — caso contrário, adote estratégia gradual.
- **Distinguir claramente entre “refatorar” e “reestruturar”**: só chamamos de refatoração quando o comportamento externo é preservado.
- **Documentar decisões de não refatorar** em comentários ou tickets — ex: “Módulo X é estável e não será alterado; mantido como-is por decisão técnica”.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como avaliar objetivamente se um código legado é “refatorável” ou se exige reescrita? Existem métricas além da intuição?
- Em contextos regulatórios (ex: saúde, finanças), como justificar refatorações incrementais sem gerar retrabalho em validações?
- Qual o impacto de não refatorar um módulo “estável” que, no futuro, acaba precisando de mudança — e se torna um gargalo técnico?
- Como alinhar times multidisciplinares (produto, QA, compliance) sobre o valor de refatorar no momento certo, e não depois?
- Existem ferramentas que ajudam a identificar “código que não será modificado” com base em histórico de commits ou uso em produção?