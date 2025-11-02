## 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Artigo**: *Context Engineering (1/2)—Getting the best out of Agentic AI Systems* por A B Vijay Kumar

#### 2. **Conceitos-Chave Identificados**
- A **Context Stack** é composta por cinco camadas sequenciais:
  1. **System Context Layer**: define a “personalidade” e limites da IA (capacidades, comportamento, segurança).
  2. **Domain Context Layer**: fornece conhecimento especializado do domínio (terminologia, padrões, metodologias).
  3. **Task Context Layer**: especifica exatamente o que fazer, critérios de sucesso e expectativas de desempenho.
  4. **Interaction Context Layer**: governa o fluxo da conversa (estilo, feedback, tratamento de erros).
  5. **Response Context Layer**: determina como a saída deve ser estruturada e formatada.
- Os **Context Components** são os elementos práticos que alimentam cada camada:
  - Role Definition → System Layer
  - Knowledge Base → Domain Layer
  - Constraints → Task Layer
  - Examples → Interaction Layer
  - Output Format → Response Layer

#### 3. **Insights Relevantes**
> “Without this layer [Domain Context], the AI would give generic advice instead of expert-level guidance.”
→ Contexto genérico gera resultados genéricos. Para soluções de negócio, precisamos de especialização.

> “This consistency made the documentation predictable and user-friendly.”
→ Formato de saída previsível é tão importante quanto o conteúdo — facilita integração, leitura e automação.

> “Think of it like creating an outline for a presentation—everything has its place and purpose.”
→ A estruturação do contexto é tão crítica quanto seu conteúdo. Um bom layout reduz ruído e aumenta clareza.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Criar um **template de Context Stack** para cada tipo de projeto (ex: backend, frontend, infra, teste) com campos obrigatórios para cada camada.
- Alimentar a camada de **Knowledge Base** com documentação interna, arquitetura, glossário de termos e referências externas (ex: FastAPI docs, JWT specs).
- Definir **Constraints** claros em todos os prompts: “NÃO use bibliotecas não testadas”, “SIGA o padrão de logging do projeto”, etc.
- Incluir **Examples** de código ou documentos anteriores como referência — isso ajuda a IA a entender o “tom” e o “estilo” esperado.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Cada PRP (Product Requirement Prompt) deve incluir explicitamente as cinco camadas da Context Stack.
- Padronizar o uso de **Output Format** em todos os prompts: “Responda em Markdown, com cabeçalhos, listas numeradas e blocos de código.”
- Documentar e versionar os **Role Definitions** usados (ex: “Você é um engenheiro sênior de backend com foco em escalabilidade e segurança”).
- Separar os componentes de contexto em arquivos distintos (ex: `context/domain.md`, `context/constraints.md`) para facilitar reutilização e manutenção.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como evitar que o contexto fique excessivamente longo? Quais técnicas de compressão são aplicáveis sem perda de qualidade?
- É possível automatizar parte da construção das camadas de contexto com ferramentas de RAG ou scraping de código?
- Como validar se um componente de contexto (ex: exemplo de código) está sendo interpretado corretamente pela IA?