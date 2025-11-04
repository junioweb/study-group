### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Bridge" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- O **Bridge** separa uma abstração de sua implementação, permitindo que ambas variem independentemente.
- Resolve o problema de hierarquias rígidas criadas por herança, onde métodos concretos ficam presos à estrutura da classe base.
- Usa composição em vez de herança para ligar abstração e implementação.

#### 3. **Insights Relevantes**
> “The two functions have been decoupled. EmitVoice can be called without bringing along all the MusicSynthesizer baggage.”  
→ Herança cria acoplamento implícito; Bridge o torna explícito e controlável.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em sistemas com múltiplas dimensões de variação (ex: formatos de relatório × canais de saída), usaremos Bridge para evitar explosão combinatória de subclasses.
- Útil em drivers de dispositivos, renderizadores gráficos, ou estratégias de persistência com múltiplas representações.
- Permite reutilizar lógica de implementação em contextos diferentes da abstração original.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Sempre que identificarmos duas razões independentes para especialização, consideraremos Bridge.
- Evitaremos herança profunda quando houver mais de um eixo de variação.
- A implementação será injetada na abstração via construtor ou setter.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como diferenciar Bridge de Strategy? Ambos usam composição — a diferença está na intenção (abstração vs. algoritmo)?
- Em linguagens com múltipla herança (ou traits), Bridge ainda é necessário?