### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Package Coupling Principles" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- Três princípios de acoplamento entre pacotes:
  - **ADP (Acyclic Dependencies Principle)**: “As dependências entre pacotes não devem formar ciclos.”
  - **SDP (Stable Dependencies Principle)**: “Dependa na direção da estabilidade.”
  - **SAP (Stable Abstractions Principle)**: “Pacotes estáveis devem ser abstratos.”

#### 3. **Insights Relevantes**
> “Cycles can be broken by creating a new package or by applying DIP/ISP.”  
→ Ciclos de dependência tornam impossível testar ou liberar pacotes de forma independente.
> “Stable packages should be abstract so they can be extended even if they can’t be changed.”  
→ Estabilidade + abstração = flexibilidade sustentável.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Usaremos ferramentas (ex: ArchUnit, SonarQube) para detectar ciclos de dependência entre módulos.
- Pacotes de núcleo de domínio serão **estáveis e abstratos** (interfaces, entidades, regras).
- Pacotes de infraestrutura serão **instáveis e concretos**, dependendo do núcleo — nunca o contrário.
- Ao detectar ciclo, aplicaremos DIP: mover interface para o pacote que consome.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Proibido criar dependência cíclica entre módulos/pacotes.
- Todo pacote com alta estabilidade (muitas dependências entrantes) deverá ter alto grau de abstração.
- Métricas de estabilidade (I) e abstração (A) serão monitoradas em pipelines de qualidade.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como calcular I (Instability) e A (Abstractness) de forma automatizada em projetos Python/Go/JS?
- Em arquiteturas orientadas a eventos, como os princípios de pacote se aplicam quando não há dependência direta?