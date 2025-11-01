### 📘 **Registro de Aprendizados – Capítulo 9: Going to Town on the Message Bus**

#### 1. **Referência da Leitura**  
- **Capítulo**: 9 – *Going to Town on the Message Bus*

#### 2. **Conceitos-Chave Identificados**  
- **Event Handler como Padrão Central**: todas as operações (API, eventos internos) são tratadas por handlers, transformando o sistema em um “processador de mensagens”.  
- **Eventos como Interface do Sistema**: eventos (`AllocationRequired`, `BatchCreated`) substituem parâmetros primitivos na assinatura dos handlers, tornando a entrada do sistema mais explícita e estruturada.  
- **Message Bus como Orquestrador**: o Message Bus é o único ponto de entrada para o sistema, coletando eventos do UoW e disparando os handlers correspondentes em sequência.  
- **Requisito Complexo Implementado com Simplicidade**: a mudança de quantidade de batch (`BatchQuantityChanged`) desencadeia dealocação e realocação via eventos, sem alterar a arquitetura — apenas adiciona novos eventos e handlers.  
- **SRP Aplicado à Arquitetura**: cada handler tem uma única responsabilidade, e eventos permitem encadear fluxos complexos sem acoplamento.

#### 3. **Insights Relevantes**  
> “Wouldn't it be easier if everything was an event handler?”  
→ Refatorar serviços em handlers simplifica o modelo: toda interação com o sistema é um evento, e todo evento tem um handler — não há diferença entre “uso externo” e “side effect interno”.

> “We're done with our refactoring phase. Let's see if we really have 'made the change easy.'”  
→ O princípio “Make the change easy; then make the easy change” se aplica perfeitamente: a refatoração prévia permitiu implementar um requisito complexo com poucas linhas de código.

> “Our API and our service layer currently want to know the allocated batch reference... This means we need to put in a temporary hack.”  
→ O uso de `results` no Message Bus é um hack necessário para lidar com leituras, mas revela uma falha na arquitetura — que será resolvida com CQRS no próximo capítulo.

> “Events are simple dataclasses that define the data structures for inputs and internal messages within our system.”  
→ Eventos são contratos imutáveis e expressivos — eles são a linguagem comum entre domínio, infraestrutura e stakeholders.

#### 4. **Aplicações Práticas no Nosso Contexto**  
*(Genéricas, independentes de negócio ou tecnologia)*  
- Modelar qualquer novo requisito como um **evento + handler**, mesmo que pareça simples (ex: `UserCreated`, `OrderCancelled`).  
- Usar **eventos como interface pública** do sistema — todos os pontos de entrada (API, CLI, fila) devem produzir eventos.  
- Manter os **handlers focados em uma única responsabilidade** — se um handler começa a fazer muitas coisas, ele deve emitir eventos para outros handlers.  
- Testar fluxos completos usando **edge-to-edge testing** com o Message Bus, e usar fakes apenas quando a cadeia de eventos for muito longa.

#### 5. **Decisões de Design ou Padrões a Adotar**  
- Priorizar **eventos como contrato de comunicação** — evite passar objetos de domínio ou primitivos diretamente para handlers.  
- Garantir que o **Message Bus seja o único ponto de entrada** — ele deve ser o “cérebro” do sistema, orquestrando todos os fluxos.  
- Usar **UoW para coletar eventos** — o UoW não publica eventos, ele apenas os disponibiliza para o Message Bus.  
- Evitar **side effects diretos em handlers** — qualquer efeito colateral (ex: enviar e-mail) deve ser feito por outro handler, não pelo handler principal.

#### 6. **Dúvidas ou Pontos a Aprofundar**  
- Como garantir que a ordem de execução dos handlers seja sempre correta?  
- Qual o impacto de usar múltiplos UoWs em um fluxo (ex: dealocar → realocar)?  
- Como lidar com eventos que falham e precisam ser reprocessados?