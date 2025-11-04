### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Cap. 4 – “Building Tests” e seções sobre “Self-Testing Code” e “Refactoring and the Wider Software Development Process”

#### 2. **Conceitos-Chave Identificados**
- **Código auto-testável** é condição necessária para refatoração segura.
- **Testes devem ser autoverificáveis**: o resultado deve ser validado automaticamente pelo framework, sem intervenção manual.
- **Testes são uma rede de segurança**, não uma prova de ausência de bugs — seu valor está em detectar regressões rapidamente.
- O ciclo **testar → refatorar → testar novamente** é fundamental para manter a integridade comportamental do sistema.
- Testes devem ser **rápidos** e **focados em risco**: priorize áreas complexas ou propensas a erros, não cobertura cega.
- Mesmo testes incompletos são melhores que nenhum teste — comece pequeno e evolua iterativamente.

#### 3. **Insights Relevantes**
> “The first step in refactoring is always the same: I need to ensure I have a solid set of tests.”  
→ Sem testes, não há refatoração — apenas reestruturação arriscada.

> “It is better to write and run incomplete tests than not to run complete tests.”  
→ Perfeição não é pré-requisito; ação contínua sim.

> “By writing what I want twice—in the code and in the test—I have to make the same mistake consistently in both places to fool the detector.”  
→ Testes funcionam como um detector de inconsistências humanas.

> “Self-testing code is not just about testing—it’s about enabling change.”  
→ A verdadeira métrica de sucesso de um suite de testes é **quão confiante você se sente para alterar o código**.

> “When you get a bug report, start by writing a unit test that exposes the bug.”  
→ Cada bug corrigido deve deixar um legado de cobertura que impede sua reincidência.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Adotar o hábito de **escrever testes antes de refatorar**, mesmo em código legado — começar com testes de caracterização (*characterization tests*).
- Usar o padrão **“escrever com placeholder → substituir pelo valor real → injetar falha → reverter”** para construir testes confiáveis em código existente.
- Priorizar **testes de unidade rápidos** como base da suíte; testes lentos ou de integração devem ser complementares.
- Manter **fixtures frescas por teste** (ex: `beforeEach`) para garantir isolamento e evitar efeitos colaterais não determinísticos.
- Tratar a suíte de testes como **código de produção**: refatorá-la para clareza, eliminar duplicação e garantir legibilidade.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Nenhum código crítico será refatorado sem testes prévios** — essa é uma regra não negociável do grupo.
- **Testes devem falhar de forma clara**: mensagens de erro devem indicar exatamente o que foi esperado vs. o que foi obtido.
- **Cobertura de código é um indicador de lacunas, não de qualidade** — foco em testar comportamentos, não em atingir 100% de cobertura.
- **Evitar testes que dependem de estado global ou mutável** — preferir imutabilidade e fixtures controladas.
- **Executar testes localmente a cada poucos minutos** durante refatoração; na pipeline, executar a suíte completa a cada commit.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como construir testes eficazes em sistemas legados com alto acoplamento e baixa coesão, onde criar “seams” para injeção de dependência é difícil?
- Qual o trade-off entre mockagem pesada e testes de integração em arquiteturas orientadas a eventos?
- Como garantir que testes de caracterização realmente representam o comportamento legítimo (e não um bug aceito)?
- Existem métricas qualitativas (além da confiança subjetiva) para avaliar a eficácia de uma suíte de testes?
- Como equilibrar velocidade de feedback (testes rápidos) com fidelidade ao ambiente real (testes integrados)?