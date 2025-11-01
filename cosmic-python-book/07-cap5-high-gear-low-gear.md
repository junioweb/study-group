### 📘 **Registro de Aprendizados – Capítulo 5: TDD in High Gear and Low Gear**

#### 1. **Referência da Leitura**  
- **Capítulo**: 5 – *TDD in High Gear and Low Gear*

#### 2. **Conceitos-Chave Identificados**  
- **Test Pyramid saudável**: maioria dos testes são unitários (no nível da camada de serviço), poucos de integração e mínimos end-to-end.  
- **High Gear vs. Low Gear**:  
  - **High Gear**: testes no nível da camada de serviço — mais rápidos, menos acoplados, cobrem fluxos completos.  
  - **Low Gear**: testes diretos no modelo de domínio — mais granulares, com alto feedback de design, mas mais frágeis a refatorações.  
- **Desacoplamento total da camada de serviço**: usar **tipos primitivos** (ex: `str`, `int`) em vez de objetos de domínio na assinatura dos serviços.  
- **Serviços completos**: se os testes precisam manipular o domínio diretamente, provavelmente falta um serviço (ex: `add_batch`).  
- **Testes como “cola”**: cada linha de teste acopla o sistema a uma implementação; testes de baixo nível dificultam refatorações.

#### 3. **Insights Relevantes**  
> “Every line of code that we put in a test is like a blob of glue, holding the system in a particular shape.”  
→ Testes devem validar **comportamento observável**, não estrutura interna. Quanto mais baixo o nível do teste, mais difícil será evoluir o design.

> “Testing against this API reduces the amount of code that we need to change when we refactor our domain model.”  
→ A camada de serviço atua como uma **API estável** do sistema — testá-la isola os testes das mudanças internas do domínio.

> “We only get that feedback [...] when we're working closely with the target code.”  
→ Testes de domínio são valiosos **durante a exploração inicial** de um problema, mas devem ser substituídos ou complementados por testes de serviço à medida que o design amadurece.

> “If you find yourself needing to do domain-layer stuff directly in your service-layer tests, it may be an indication that your service layer is incomplete.”  
→ Os testes revelam lacunas na arquitetura — use-os como guia para evoluir a interface do sistema.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Estruturar a maioria dos testes no nível da **camada de serviço**, usando `FakeRepository` e tipos primitivos.  
- Manter um **conjunto pequeno de testes de domínio** apenas para documentar regras complexas ou durante a fase de modelagem inicial.  
- Evitar depender de objetos de domínio nos testes de serviço — criar **serviços auxiliares** (ex: `add_batch`) para preparar o estado.  
- Aplicar o mesmo princípio nos testes E2E: usar **endpoints da API** para configurar o estado, não SQL direto ou fixtures de baixo nível.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Assinar funções de serviço com **tipos primitivos**, não com objetos de domínio.  
- Garantir que a camada de serviço seja **completa**: todo estado necessário para os testes deve ser configurável via serviços.  
- Manter a **Test Pyramid equilibrada**:  
  - **E2E**: 1 por feature (happy + unhappy paths).  
  - **Serviço**: cobertura exaustiva de casos de uso e bordas.  
  - **Domínio**: apenas para lógica altamente complexa ou em fase de descoberta.  
- Eliminar testes de domínio redundantes quando a mesma lógica já for coberta por testes de serviço.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como lidar com parâmetros complexos (ex: listas de itens) sem recorrer a DTOs ou objetos de domínio?  
- Em que momento exato migrar um teste de “low gear” para “high gear”?  
- Qual o impacto de usar primitivos na assinatura do serviço para a legibilidade e manutenibilidade do código?