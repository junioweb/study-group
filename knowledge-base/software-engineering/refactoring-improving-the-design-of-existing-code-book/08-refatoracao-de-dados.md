### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: Cap. “Organizing Data” (especialmente seções: *Encapsulate Record*, *Encapsulate Collection*, *Replace Derived Variable with Query*, *Change Reference to Value*, *Replace Primitive with Object*)

#### 2. **Conceitos-Chave Identificados**
- **Dados bem modelados simplificam a lógica de negócio** e reduzem a complexidade acidental.
- **Encapsulate Record**: transformar estruturas de dados planas (ex: objetos literais, dicionários) em classes com acesso controlado.
- **Encapsulate Collection**: evitar exposição direta de coleções mutáveis; fornecer métodos específicos para adicionar/remover elementos.
- **Replace Derived Variable with Query**: substituir variáveis calculadas e armazenadas por funções que recalculam o valor sob demanda — eliminando risco de inconsistência.
- **Change Reference to Value** (e vice-versa): decidir entre compartilhar uma instância (referência) ou copiar seu valor, com base em mutabilidade e semântica de domínio.
- **Imutabilidade** é um facilitador poderoso: dados imutáveis podem ser copiados livremente, não exigem encapsulamento rígido e reduzem efeitos colaterais.

#### 3. **Insights Relevantes**
> “Data structures are the key to understanding what’s going on.”  
→ A clareza do domínio muitas vezes reside na modelagem dos dados, não apenas na lógica.

> “Duplicating data is a recipe for disaster with mutable data structures; removing such disasters is why immutable data is so popular.”  
→ Imutabilidade não é só uma moda funcional — é uma estratégia de segurança estrutural.

> “I can remove any variable that I could just as easily calculate.”  
→ Variáveis derivadas introduzem estado mutável desnecessário e risco de desatualização.

> “Encapsulating collections prevents clients from modifying the collection directly, which gives me control over how it changes.”  
→ Expor uma lista diretamente é abrir mão do controle sobre a integridade do objeto.

> “With immutable data, I can have all three values in my record… it’s easy to copy the field when renaming.”  
→ Imutabilidade simplifica refatorações como renomeação, extração e migração.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Substituir objetos literais (ex: `user = { name, email }`) por **classes com getters/setters** quando o escopo ultrapassa uma única função.
- Nunca expor diretamente arrays ou listas mutáveis; oferecer métodos como `addPermission()`, `removeTag()` em vez de `getTags().push(...)`.
- Eliminar variáveis derivadas (ex: `totalPrice` atualizada manualmente) em favor de funções como `calculateTotalPrice()` — especialmente em contextos com múltiplas fontes de mudança.
- Avaliar se entidades como `Currency`, `Email`, `Cpf` devem ser **valores imutáveis** (value objects) em vez de strings primitivas — aumentando expressividade e validação.
- Em sistemas com alto compartilhamento de estado (ex: cache, contexto de requisição), preferir **imutabilidade** ou **cópias defensivas** para evitar efeitos colaterais indesejados.

#### 5. **Decisões de Design ou Padrões a Adotar**
- **Toda estrutura de dados com escopo além de uma função deve ser encapsulada** (via classe ou módulo com interface controlada).
- **Coleções nunca devem ser retornadas diretamente** — sempre retornar cópias ou iteradores somente leitura.
- **Evitar variáveis derivadas mutáveis** — exceto em casos de performance comprovada (e mesmo assim, com testes de regressão rigorosos).
- **Preferir value objects imutáveis para conceitos de domínio** (ex: `Money`, `DateRange`, `PhoneNumber`) em vez de primitivos soltos.
- **Adotar imutabilidade por padrão** em novos módulos — especialmente em pipelines de dados, configurações e entidades de leitura.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como equilibrar imutabilidade com performance em sistemas com alto volume de alocação de objetos (ex: processamento de eventos em tempo real)?
- Em arquiteturas orientadas a eventos, quando é seguro usar referência compartilhada vs. cópia de valor na passagem de mensagens?
- Existe um limite de granularidade para value objects? Quando encapsular um campo simples como `email` se ele já é validado no banco de dados?
- Como aplicar `Replace Derived Variable with Query` em sistemas com requisitos de performance rigorosos (ex: jogos, HFT)?
- Ferramentas como Immer (JavaScript) ou Pyrsistent (Python) ajudam ou atrapalham a clareza do modelo de dados?