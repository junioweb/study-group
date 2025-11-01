### 📘 **Registro de Aprendizados – Capítulo 3: A Brief Interlude on Coupling and Abstractions**

#### 1. **Referência da Leitura**  
- **Capítulo**: 3 – *A Brief Interlude: On Coupling and Abstractions*

#### 2. **Conceitos-Chave Identificados**  
- **Acoplamento vs. Coesão**: Acoplamento excessivo entre componentes leva ao “Big Ball of Mud”; coesão alta localmente é boa, mas acoplamento global é um risco.  
- **Abstração como ferramenta de desacoplamento**: inserir uma abstração entre dois sistemas reduz o número de dependências diretas e isola a complexidade.  
- **Testabilidade e isolamento**: código acoplado a I/O (filesystem, rede) dificulta testes unitários; a solução é extrair a lógica de negócio em um “core funcional” e isolar as operações de estado.  
- **Functional Core, Imperative Shell (FCIS)**: separar o núcleo funcional (puro, sem efeitos colaterais) da camada imperativa (I/O, persistência).  
- **Fakes vs. Mocks**: preferimos fakes (implementações reais simplificadas) para testes, pois eles permitem verificar estado final, não apenas interações.  
- **Dependency Injection (DI)**: injeção explícita de dependências (ex: `FileSystem`) permite substituir implementações reais por fakes, mantendo a mesma interface.

#### 3. **Insights Relevantes**  
> “We can reduce the degree of coupling within a system by abstracting away the details.”  
→ Abstrações são barreiras que protegem o domínio da complexidade externa — quanto mais simples a interface, menos frágil o sistema.

> “Our goal is to isolate the clever part of our system, and to be able to test it thoroughly without needing to set up a real filesystem.”  
→ O foco dos testes deve ser a lógica de negócio, não a infraestrutura. Isso exige uma divisão clara entre “o que fazer” e “como fazer”.

> “Tests that use too many mocks get overwhelmed with setup code that hides the story we care about.”  
→ Testes com muitos mocks se tornam difíceis de entender e manter. Fakes, por outro lado, revelam intenção e comportamento.

> “Designing for testability really means designing for extensibility.”  
→ Um sistema fácil de testar é, por definição, flexível o suficiente para suportar novos cenários (ex: dry-run, cloud storage).

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Estruturar qualquer módulo com **core funcional puro** (sem I/O) e **shell imperativo** (com I/O), seguindo o padrão FCIS.  
- Usar **dicionários, tuplas ou listas** como abstrações de estado para representar dados externos (ex: hashes de arquivos, registros do banco).  
- Substituir chamadas diretas a bibliotecas (`shutil`, `os`) por interfaces injetáveis (`FileSystem`), permitindo testes com fakes.  
- Priorizar **testes de estado** (o que mudou?) sobre **testes de comportamento** (quem foi chamado?), especialmente em código de domínio.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Aplicar o **Functional Core, Imperative Shell** em todos os módulos que lidam com I/O ou estado externo.  
- Evitar `mock.patch` em produção — prefira DI com interfaces explícitas (mesmo que via duck typing).  
- Usar **fakes** (não mocks) para testes de integração leve, pois eles preservam a semântica do sistema e são mais fáceis de depurar.  
- Definir **seams** (pontos de corte) onde a abstração será inserida, sempre entre domínio e infraestrutura.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como escolher a granularidade ideal das abstrações? Quando uma abstração se torna muito genérica e perde expressividade?  
- Em que cenários o uso de mocks seria justificado, mesmo sendo considerado “code smell”?  
- Como aplicar o FCIS em sistemas orientados a eventos, onde o “core funcional” pode ser distribuído?