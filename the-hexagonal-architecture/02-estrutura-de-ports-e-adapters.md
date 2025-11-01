### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Nature of the Solution, Structure, Sample Code

#### 2. **Conceitos-Chave Identificados**
- **Porta**: define um protocolo de conversação com propósito semântico claro (ex: “receber comandos do usuário”, “persistir dados”). É uma interface estável.
- **Adaptador**: implementação concreta que conecta uma tecnologia externa (GUI, banco SQL, API HTTP, mock) a uma porta.
- A aplicação reside no **interior do hexágono** e comunica-se exclusivamente por meio de portas.
- O hexágono não é literalmente de seis lados; o formato serve para **romper com a mentalidade de arquitetura em camadas unidimensionais**.
- Existem tipicamente **várias portas** (não apenas duas), embora muitas aplicações comecem com duas: uma primária (controle) e uma secundária (dados).

#### 3. **Insights Relevantes**
> “The application has a semantically sound interaction with the adapters on all sides of it, without actually knowing the nature of the things on the other side of the adapters.”  
→ A aplicação interage com significado, não com tecnologia. Isso permite substituir qualquer periférico sem alterar o núcleo.

> “The hexagon is not a hexagon because the number six is important, but rather to allow the people doing the drawing to have room to insert ports and adapters as they need...”  
→ A forma visual é um antídoto contra a rigidez das arquiteturas em N camadas.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Podemos implementar múltiplos adaptadores para a mesma porta: por exemplo, um adaptador de UI web (FastAPI/Flask), um CLI e um adaptador para testes automatizados.
- Do lado de dados, podemos ter adaptadores para PostgreSQL (produção), SQLite (testes) e um dicionário em memória (mock para testes unitários rápidos).
- A evolução do desenvolvimento pode seguir a sequência sugerida: (1) testes + mock, (2) UI + mock, (3) testes + DB real, (4) UI + DB real.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Toda dependência externa (UI, DB, API, fila) deve ser acessada por meio de uma interface (porta)** definida dentro do domínio.
- **Os adaptadores residirão fora do núcleo da aplicação** e serão responsáveis por traduzir protocolos externos para chamadas de domínio.
- Usaremos **injeção de dependência** para conectar adaptadores às portas no momento da inicialização da aplicação.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como lidar com exceções e tratamento de erros que são específicos de um adaptador (ex: timeout de HTTP) sem contaminar o domínio?
- Qual a melhor forma de organizar o código-fonte (estrutura de pastas) para refletir claramente as portas e adaptadores?
- Como testar a integração entre múltiplos adaptadores sem perder os benefícios do isolamento?