### 📘 **Registro de Aprendizados – Parte 1: Visão Geral**

#### 1. **Referência da Leitura**  
- **Seção**: Introdução da Parte 1 – *Building an Architecture to Support Domain Modeling*

#### 2. **Conceitos-Chave Identificados**  
- **Modelo de domínio ≠ modelo de dados**: o foco deve estar no **comportamento** do sistema, não na estrutura de armazenamento.  
- **Lógica de negócio dispersa** é um sintoma comum de arquiteturas fracassadas; a solução começa com um **modelo orientado a comportamento**, construído por TDD.  
- A Parte 1 introduz quatro padrões essenciais para isolar e proteger o modelo de domínio:  
  1. **Repository** – abstração de persistência  
  2. **Service Layer** – definição clara dos casos de uso  
  3. **Unit of Work** – operações atômicas e gerenciamento de transações  
  4. **Aggregate** – garantia de integridade de dados dentro de limites bem definidos  
- O objetivo arquitetural é criar código **ignorante de persistência** (*persistence-ignorant*) e APIs estáveis que permitam refatoração agressiva sem impacto externo.

#### 3. **Insights Relevantes**  
> “Most developers have never seen a domain model, only a data model.”  
→ Reforça a confusão comum entre **estrutura de dados** (tabelas, campos) e **modelo rico de comportamento** (regras, invariantes, ações).

> “Behavior should come first and drive our storage requirements.”  
→ A persistência é uma **consequência** do domínio, não seu ponto de partida. Isso inverte a prática comum de projetar o banco antes da lógica.

> “Our customers don’t care about the data model. They care about what the system _does_.”  
→ Alinha o desenvolvimento ao **valor do negócio**, não à conveniência técnica imediata.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas e independentes de tecnologia ou domínio)*  
- Iniciar o design de qualquer sistema pela **definição de comportamentos esperados**, usando testes unitários como especificação.  
- Tratar o banco de dados como um **detalhe de implementação**, não como o centro do sistema.  
- Estruturar o código de forma que seja possível **substituir toda a infraestrutura** (ex: trocar ORM por CSVs) sem alterar a lógica de domínio — validando o desacoplamento real.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Adotar os quatro padrões da Parte 1 como base mínima para sistemas com lógica de negócio significativa.  
- Garantir que o **modelo de domínio seja testável sem I/O** (banco, rede, disco).  
- Usar o **Service Layer** como único ponto de entrada para casos de uso, evitando que lógica vaze para controladores ou scripts.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como identificar os limites corretos de um **Aggregate** sem cair em agregados muito grandes ou muito granulares?  
- Qual o papel exato do **Unit of Work** em relação ao Repository? Há sobreposição ou complementaridade clara?  
- Como manter o Service Layer enxuto e evitar que ele se torne um “God Object”?