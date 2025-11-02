### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Abstract Factory" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- O **Abstract Factory** resolve o conflito entre DIP e a necessidade de criar instâncias concretas.
- Fornece uma interface para criar famílias de objetos relacionados sem especificar suas classes concretas.
- Centraliza a criação de objetos em um único ponto controlado (geralmente inicializado na camada de composição).

#### 3. **Insights Relevantes**
> “No module in the system knows about the concrete modem classes except for ModemFactory_I.”  
→ Isso isola a volatilidade da criação de objetos, preservando o DIP em todo o resto do sistema.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em sistemas com múltiplas estratégias configuráveis (ex: modos de frete, motores de cálculo), usaremos fábricas abstratas para instanciar a variante correta.
- Em testes, substituiremos a fábrica concreta por uma que retorna mocks.
- Em aplicações com plugins, cada plugin pode registrar sua própria fábrica.

#### 5. **Decisões de Design ou Padrões a Adotar**
- A criação de qualquer objeto cuja implementação varia será mediada por uma Abstract Factory ou por injeção de dependência.
- O módulo `main` (ou equivalente de inicialização) será o único responsável por instanciar fábricas concretas.
- Evitaremos chamadas diretas a `new ConcreteClass()` fora da camada de composição.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Em linguagens com DI containers robustos (ex: Spring, Dagger), Abstract Factory ainda é relevante ou é absorvido pela infraestrutura?
- Como lidar com parâmetros dinâmicos na criação de objetos (ex: criar `TaxCalculator` com base em país do usuário)?