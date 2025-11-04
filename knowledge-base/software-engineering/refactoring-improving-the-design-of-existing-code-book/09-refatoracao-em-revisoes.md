### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: “Refactoring in a Code Review” (seção esparsa no capítulo introdutório e ao longo do livro)

#### 2. **Conceitos-Chave Identificados**
- **Refatoração durante revisões de código** permite transformar sugestões abstratas em melhorias concretas e visíveis.
- O modelo tradicional de **pull request assíncrono** (revisor sem o autor presente) limita a eficácia da refatoração colaborativa.
- **Revisões com o autor presente** (ex: pair programming, sessões síncronas) permitem experimentar e validar melhorias em tempo real.
- Refatorar durante a revisão **não é apenas corrigir código** — é um ato de **compartilhamento de conhecimento** e **alinhamento de padrões**.
- A prática fortalece a **cultura de melhoria contínua** e reduz o ciclo de feedback entre sugestão e implementação.

#### 3. **Insights Relevantes**
> “I’ve found that refactoring helps me review someone else’s code. Before I started using refactoring, I could read the code, understand it to some degree, and make suggestions. Now, when I come up with ideas, I consider whether they can be easily implemented then and there with refactoring.”  
→ Refatorar durante a revisão transforma opiniões em ações tangíveis.

> “You end up with much more of a sense of accomplishment from the exercise.”  
→ Revisões com refatoração imediata geram maior engajamento e senso de progresso coletivo.

> “The common pull request model, where a reviewer looks at code without the original author, doesn’t work too well.”  
→ A ausência do autor limita a profundidade da discussão e impede experimentação segura.

> “By refactoring during review, I can see more clearly what the code looks like with the suggestions in place. I don’t have to imagine it—I can see it.”  
→ Visualizar o resultado da sugestão elimina ambiguidades e revela novas oportunidades de melhoria.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Durante revisões síncronas (ex: pair programming, mob programming), **aplicar pequenas refatorações imediatamente** quando sugeridas (ex: renomear variável, extrair função).
- Em revisões assíncronas (pull requests), **incluir snippets refatorados como parte do comentário** ou **fazer commits adicionais com melhorias sugeridas** (quando autorizado).
- Usar a revisão como momento para **alinhamento de vocabulário de domínio** — garantir que nomes de funções, classes e variáveis reflitam o modelo mental compartilhado.
- Incentivar a prática de **“refatoração de compreensão”**: ao revisar código pouco claro, o revisor pode propor ou aplicar mudanças que tornem a intenção explícita.
- Documentar decisões de design emergentes durante a revisão — especialmente quando a refatoração revela novos insights sobre o domínio.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Toda sugestão de refatoração clara e segura deve ser implementada imediatamente** durante a revisão, quando possível.
- **Preferir revisões síncronas para mudanças complexas** ou com alto impacto estrutural — especialmente em código crítico ou pouco familiar à equipe.
- **Evitar comentários vagos como “isso está confuso”** — substituir por ações concretas: “vamos extrair essa lógica para uma função chamada X?”.
- **Manter testes verdes durante toda a sessão de revisão com refatoração** — garantir que cada passo seja validado.
- **Tratar a revisão como um espaço de aprendizado mútuo**, não como auditoria — o foco é na evolução do código e da equipe, não na correção de erros.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como equilibrar autonomia do autor com sugestões de refatoração direta em pull requests assíncronos?
- Existem ferramentas que permitem “refatoração colaborativa em tempo real” (ex: compartilhamento de AST editável) para revisões remotas?
- Como evitar que a prática de refatorar durante revisões se torne um fator de desaceleração em equipes com alta pressão de entrega?
- Qual o papel do revisor sênior ao encontrar *code smells* em código funcional, mas não ótimo? Deve insistir na refatoração?
- Como medir o impacto da refatoração em revisões na qualidade do código e na velocidade de onboarding de novos membros?