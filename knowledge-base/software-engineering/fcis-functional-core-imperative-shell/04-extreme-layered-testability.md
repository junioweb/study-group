📘 Modelo de Registro de Aprendizados

**1. Referência da Leitura**  
Síntese dos artigos: *"The Functional Core Imperative Shell Paradigm"* e *"Mastering Functional Core/Imperative Shell in Go"* + práticas de TDD em sistemas funcionais (Contexto: Estratégias de testabilidade para arquiteturas híbridas)  

---

**2. Conceitos-Chave Identificados**  
- **Testabilidade como indicador arquitetural**:  
  - A facilidade de testar componentes reflete diretamente a qualidade da separação entre core e shell.  
- **Pureza testável**:  
  - Functional Core deve ser 100% testável sem mocks, stubs ou configurações complexas de ambiente.  
- **Testes como especificação**:  
  - No core, testes definem o comportamento esperado antes da implementação (TDD como ferramenta de design).  
- **Shell como caixa-preta**:  
  - Imperative Shell é testado como sistema completo via E2E, verificando entradas e saídas sem expor implementação.  
- **Métricas de pureza**:  
  - Cobertura de testes no core deve ser medida sem dependências externas, enquanto no shell prioriza-se fluxos críticos de usuário.  

---

**3. Insights Relevantes**  
> *"The harder a component is to test, the more we are straying from a functional core."*  
→ Dificuldade nos testes é um sintoma direto de violação das fronteiras arquiteturais, não um problema de ferramentas.  

> *"Pure functions are trivial to test."*  
→ A simplicidade extrema dos testes no core funcional elimina a necessidade de frameworks complexos de mocking e setup.  

> *"TDD is used as a way to design components, not just validate them."*  
→ Testes primeiro definem interfaces e contratos explícitos entre camadas, forçando uma arquitetura mais limpa desde o início.  

> *"There is an extremely high level of confidence that we are not introducing breaking changes."*  
→ A combinação de core testável e shell validado por E2E gera confiança para refatorações e evolução do sistema.  

---

**4. Aplicações Práticas no Nosso Contexto**  
- **Estratégia de TDD para o Functional Core**:  
  - Começar escrevendo testes para funções puras antes da implementação (ex.: `calculateTotal()` deve retornar 0 para carrinho vazio).  
  - Usar exemplos tabulares para cobrir todas as combinações de entrada/resultado (ex.: diferentes cenários de desconto).  
  - Testar invariantes de domínio independentemente de frameworks (ex.: `validateCreditCard()` em isolamento completo).  
- **Abordagem para o Imperative Shell**:  
  - Testes E2E com ferramentas como Cypress (frontend) ou Testcontainers (backend) para validar fluxos completos.  
  - Simular falhas de infraestrutura no shell para verificar resiliência (ex.: API indisponível, timeout de banco de dados).  
  - Validar integração entre adaptadores e core em cenários realistas (ex.: requisição HTTP → processamento no core → resposta formatada).  
- **Métricas de qualidade**:  
  - Cobertura de linha > 95% para o core, medida sem mocks ou simulações.  
  - Cobertura de decisão > 85% para lógica condicional complexa no core.  
  - Número de testes unitários vs. E2E na proporção 5:1 (refletindo a divisão 80/20 entre core/shell).  

---

**5. Decisões de Design ou Padrões a Adotar**  
- **Regra do "teste sem setup"**:  
  - Todo teste do Functional Core deve executar em < 10ms e sem configuração de ambiente (bancos de dados, redes, etc.).  
- **Padrão de organização de testes**:  
  - Testes do core em `/tests/unit/` com sufixo `.spec.domain.ts` ou `_test.go`.  
  - Testes do shell em `/tests/e2e/` com sufixo `.spec.shell.ts` ou `_e2e_test.go`.  
- **Métricas obrigatórias em CI**:  
  - Pipeline falha se cobertura do core < 90% ou se qualquer teste do core usar mocks.  
  - Relatório de pureza do core: percentual de funções testáveis sem dependências externas.  
- **Padrão de nomenclatura para testes**:  
  - Testes do core seguem o formato `Dado_X_quando_Y_então_Z` (ex.: `Dado_carrinho_com_itens_quando_calcular_total_então_retorna_valor_correto`).  
  - Testes do shell descrevem cenários de usuário (ex.: `Usuário_finaliza_compra_com_cartão_válido_recebe_confirmacao`).  

---

**6. Dúvidas ou Pontos a Aprofundar**  
- Como testar funções do core que dependem de dados gerados aleatoriamente (ex.: IDs únicos) sem comprometer a determinística?  
- Qual a estratégia ideal para testar orquestradores complexos que coordenam múltiplos serviços sem cair no "mock hell"?  
- Como medir o impacto do TDD no core versus o tempo de desenvolvimento em projetos com prazos apertados?  
- Existe um ponto de retorno decrescente na cobertura de testes para o core (ex.: 95% vs 100%)?  
- Como integrar testes de performance no modelo FCIS (core otimizado para simplicidade, shell para throughput)?  