### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Liskov Substitution Principle (LSP)" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- **LSP**: "Subclasses devem ser substituíveis por suas classes base."
- Baseado em **Design by Contract**: pré-condições não devem ser fortalecidas, pós-condições não devem ser enfraquecidas.
- Violações do LSP quebram a confiança no polimorfismo e levam a verificações de tipo (`if (obj is Circle)`), o que viola o OCP.

#### 3. **Insights Relevantes**
> “Clients Ruin Everything.”  
→ Mesmo que uma hierarquia pareça logicamente correta (ex: `Circle extends Ellipse`), o **contrato implícito da interface base** pode ser violado, quebrando clientes existentes.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Evitar herança apenas por reuso de código; preferir composição.
- Ao modelar entidades (ex: `User`, `AdminUser`), garantir que todos os métodos da base tenham o mesmo contrato semântico.
- Testes devem validar que subclasses respeitam o comportamento esperado da interface base (testes de contrato).

#### 5. **Decisões de Design ou Padrões a Adotar**
- Proibido usar `instanceof` ou `type checking` em lógica de negócio.
- Herança só será usada quando houver **substituição real**, não apenas similaridade conceitual.
- Interfaces devem ser explícitas sobre seus contratos (via documentação ou testes de contrato).

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como testar contratos de forma automatizada em linguagens sem suporte nativo a Design by Contract?
- Em sistemas com herança profunda (ex: frameworks), como garantir conformidade com LSP?