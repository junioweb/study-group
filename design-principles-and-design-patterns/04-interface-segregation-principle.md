### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Interface Segregation Principle (ISP)" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- **ISP**: "Muitas interfaces específicas para clientes são melhores do que uma interface genérica."
- Interfaces grandes ("fat interfaces") forçam clientes a depender de métodos que não usam.
- Violações do ISP aumentam o acoplamento e propagam mudanças desnecessárias (recompilação/redeploy de módulos não afetados).

#### 3. **Insights Relevantes**
> “Many client specific interfaces are better than one general purpose interface.”  
→ Interfaces devem ser vistas como **contratos sob demanda**, não como "tudo ou nada". Um cliente não deve ser penalizado por funcionalidades que não utiliza.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em serviços com múltiplos consumidores (ex: frontend, integrações externas, batch jobs), definiremos interfaces distintas por tipo de cliente.
- Evitaremos interfaces monolíticas como `UserService` com dezenas de métodos; em vez disso, teremos `UserQueryService`, `UserCommandService`, `UserProfileExporter`, etc.
- Facilita versionamento: novas funcionalidades podem ser expostas via novas interfaces sem quebrar clientes existentes.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Toda interface será avaliada quanto ao número de clientes e à coesão de seus métodos.
- Proibido adicionar métodos a uma interface existente apenas para atender um novo cliente; em vez disso, criar nova interface ou usar composição.
- Em sistemas com plugins ou extensões, cada extensão define sua própria interface de contrato.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como equilibrar ISP com a proliferação excessiva de interfaces em domínios pequenos?
- Em APIs REST, como aplicar ISP sem fragmentar endpoints demais?