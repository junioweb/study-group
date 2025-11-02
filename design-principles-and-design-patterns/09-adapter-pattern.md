### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Adapter" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- O **Adapter** encapsula um componente de terceiros (ou legado) para compatibilizá-lo com uma interface abstrata esperada pelo sistema.
- É usado quando não é possível modificar o servidor (ex: biblioteca fechada, SDK de parceiro).
- Cada método do adaptador delega chamadas para o componente real, traduzindo parâmetros/semanas conforme necessário.

#### 3. **Insights Relevantes**
> “When inserting an abstract interface is infeasible [...] an ADAPTER can be used to bind the abstract interface to the server.”  
→ O Adapter é uma “camada de tradução” que protege o núcleo do sistema contra instabilidades externas.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Ao integrar com APIs de parceiros (ex: gateway de pagamento, CRM), criaremos adaptadores que implementam nossa interface de domínio (`PaymentGateway`) e traduzem chamadas para o SDK do parceiro.
- Em migrações graduais de sistemas legados, usaremos adaptadores para expor a lógica antiga sob novos contratos.
- Reduz o risco de mudanças no SDK do parceiro quebrarem nosso domínio.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Toda integração com código de terceiros será mediada por um Adapter.
- O Adapter será o único ponto do sistema que conhece detalhes do SDK/biblioteca externa.
- Testes de integração validarão o comportamento do Adapter, enquanto testes unitários usarão mocks da interface.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como lidar com diferenças semânticas profundas entre nossa interface e a do parceiro (ex: conceitos de “transação” distintos)?
- Qual o limite entre Adapter e Facade? Quando usar um ou outro?