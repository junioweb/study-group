### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "The Principles of OOD" – Robert C. Martin (artigo complementar)

#### 2. **Conceitos-Chave Identificados**
- O **Single Responsibility Principle (SRP)** afirma que uma classe deve ter **uma, e somente uma, razão para mudar**.
- "Razão para mudar" está associada a **atores** ou **stakeholders** com interesses distintos no comportamento da classe.
- Violações do SRP levam a classes com múltiplas responsabilidades, dificultando manutenção, testes e reuso.

#### 3. **Insights Relevantes**
> “A class should have one, and only one, reason to change.”  
→ Isso não se refere a funcionalidades técnicas, mas a **motivações de mudança provenientes de requisitos de negócio**. Uma classe que lida com lógica de domínio e persistência, por exemplo, está sujeita a mudanças por dois atores distintos: o time de negócio e o time de infraestrutura.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em nossos serviços, evitaremos que classes de domínio também tratem de serialização, logging ou comunicação com APIs externas.
- Separação clara entre **entidades de domínio**, **repositórios**, **serviços** e **adaptadores** (arquitetura hexagonal/ports-and-adapters).
- Facilita testes unitários focados apenas na lógica de negócio, sem mocks complexos.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Toda classe será revisada quanto ao número de "razões para mudar". Se houver mais de uma, será refatorada.
- Adoção do **Repository Pattern** com interfaces definidas no domínio e implementações em camadas externas.
- Nomes de classes devem refletir sua única responsabilidade (ex: `OrderValidator`, não `OrderProcessor` se ele também salva no banco).

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como identificar claramente os "atores" em projetos com stakeholders mal definidos?
- Em microsserviços pequenos, há risco de fragmentação excessiva ao aplicar SRP rigorosamente?