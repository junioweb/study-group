### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Open Closed Principle (OCP)" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- **OCP**: "Um módulo deve estar aberto para extensão, mas fechado para modificação."
- Atingido por meio de **abstrações** (interfaces, classes abstratas) e **polimorfismo** (dinâmico ou estático).
- Evita propagação de mudanças: novas funcionalidades são adicionadas sem alterar código existente e testado.

#### 3. **Insights Relevantes**
> “We should write our modules so that they can be extended, without requiring them to be modified.”  
→ Isso transforma o design em algo **evolutivo**, não frágil. A abstração atua como um “ponto de articulação” (hinge point) que permite flexibilidade sem risco.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em estratégias de cálculo de frete, impostos ou regras de negócio variáveis, usaremos interfaces como `TaxCalculator` e implementações concretas (`IcmsTaxCalculator`, `IpiTaxCalculator`).
- Controladores (controllers) dependerão apenas de interfaces de serviço, permitindo troca de implementações sem recompilação.
- Facilita experimentação de novas regras de negócio via feature flags sem modificar lógica central.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Sempre que houver lógica condicional baseada em tipos (ex: `if (type == "X")`), considerar refatoração para OCP com polimorfismo.
- Interfaces devem ser definidas no módulo de mais alto nível (domínio), não nas implementações.
- Uso de **Abstract Factory** ou **Injeção de Dependência** para instanciar implementações concretas.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Qual o trade-off entre antecipar extensibilidade (e criar abstrações prematuras) versus refatorar sob demanda?
- Como aplicar OCP em contextos serverless ou funções puras (ex: AWS Lambda)?