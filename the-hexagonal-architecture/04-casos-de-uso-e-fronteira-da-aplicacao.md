### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Application Notes – Use Cases And The Application Boundary

#### 2. **Conceitos-Chave Identificados**
- Casos de uso **devem ser escritos na fronteira da aplicação (hexágono interno)**, descrevendo funções e eventos suportados, **independentemente da tecnologia externa**.
- Casos de uso que mencionam detalhes de UI ou infraestrutura são **longos, frágeis e caros de manter**.
- A arquitetura hexagonal **reforça a escrita de casos de uso estáveis e orientados a metas de negócio**.

#### 3. **Insights Relevantes**
> “Understanding the ports and adapters architecture, we can see that the use cases should generally be written at the application boundary... These use cases are shorter, easier to read, less expensive to maintain, and more stable over time.”  
→ A arquitetura não é apenas técnica; ela guia a **especificação do comportamento esperado** de forma limpa.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Ao refinar histórias de usuário, focaremos nas **intenções de domínio** (ex: “Registrar pedido”) e não nos detalhes de implementação (ex: “Preencher formulário X”).
- A especificação de aceitação será baseada em **cenários que interagem com as portas primárias**, usando exemplos concretos de entrada/saída.
- Isso permite que a equipe de QA crie testes automatizados **antes** da implementação da UI, alinhando desenvolvimento e validação.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Todo refinamento de backlog deve resultar em casos de uso escritos contra a fronteira da aplicação**.
- **Proibido mencionar tecnologias específicas (ex: “botão”, “tabela SQL”) em casos de uso de alto nível**.
- Os testes de aceitação automatizados serão a **expressão executável** desses casos de uso.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como lidar com requisitos não-funcionais (ex: performance, segurança) que muitas vezes estão ligados à infraestrutura?
- Qual o nível ideal de abstração para os casos de uso? Muito alto pode ser vago; muito baixo pode vazar detalhes técnicos.
- Como integrar essa abordagem com práticas de BDD (Behavior-Driven Development)?