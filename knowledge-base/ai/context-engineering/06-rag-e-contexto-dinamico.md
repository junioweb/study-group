## 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Artigo**: *Context Engineering (2/2)—Product Requirements Prompts* por A B Vijay Kumar

#### 2. **Conceitos-Chave Identificados**
- **RAG (Retrieval-Augmented Generation)** e **Context Engineering** são parceiros naturais: RAG fornece conhecimento dinâmico, enquanto Context Engineering garante que esse conhecimento seja processado e apresentado corretamente.
- RAG age como um “assistente de pesquisa” da IA, trazendo informações atualizadas sem necessidade de re-treinamento.
- O fluxo típico de RAG + Context Engineering:
  1. Consulta do usuário
  2. Camada de engenharia de contexto
  3. Sistema de recuperação RAG
  4. Recuperação de contexto relevante (base de conhecimento + fontes externas)
  5. Augmentação do contexto
  6. Prompt aprimorado → LLM → Resposta
- RAG permite trabalhar com **contexto dinâmico**, **específico de domínio** e **personalizado por usuário**.

#### 3. **Insights Relevantes**
> “Think of RAG as the AI’s research assistant, while context engineering serves as its communication coach.”
→ Essa analogia é poderosa: RAG encontra os fatos, Context Engineering ensina a IA como usá-los.

> “When new information becomes available, rather than having to retrain the model, all that’s needed is to augment the model’s external knowledge base with the updated information.”
→ Isso transforma a IA em um sistema vivo, adaptável às mudanças do mundo real.

> “Grounding and Verification: RAG ensures that the model has access to the most current, reliable facts and that users have access to the model’s sources.”
→ Transparência e confiabilidade aumentam drasticamente com RAG.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Criar um **diretório `ai_docs/`** no projeto para armazenar documentação interna e externa (ex: guias de framework, especificações de API, políticas de segurança).
- Usar RAG para buscar automaticamente documentação relevante durante a execução de um PRP (ex: se o PRP menciona “JWT”, buscar `jwt.io/introduction` e `fastapi.tiangolo.com/tutorial/security/oauth2-jwt/`).
- Implementar **padrões de RAG** em nossos prompts, como:
  - **Expert Knowledge Synthesis**: buscar papers recentes e melhores práticas antes de dar recomendações técnicas.
  - **Compliance-Aware Analysis**: buscar regulamentações atuais antes de aprovar decisões de arquitetura.
  - **Competitive Intelligence**: buscar benchmarks e soluções de mercado para validar escolhas de tecnologia.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Integrar RAG ao nosso processo de **validação de contexto** — antes de executar um PRP, verificar se há documentação atualizada disponível.
- Usar **links diretos e versões específicas** de documentação no PRP para garantir reprodutibilidade (ex: `https://fastapi.tiangolo.com/v0.95.0/...`).
- Definir uma **política de atualização de documentos** no `ai_docs/` — revisar mensalmente ou após cada release significativa.
- Incluir no PRP um campo opcional **“RAG Sources”** para listar os documentos e URLs que devem ser recuperados durante a execução.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Quais ferramentas de RAG são mais adequadas para nosso stack (Python, FastAPI, etc.)?
- Como evitar que a IA dependa excessivamente de RAG e perca sua capacidade de raciocínio geral?
- Como lidar com conflitos entre informação recuperada via RAG e o contexto estático fornecido no PRP?