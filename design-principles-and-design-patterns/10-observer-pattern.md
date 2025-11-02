### 📘 **Registro de Aprendizados**

#### 1. **Referência da Leitura**
- **Capítulo / Seção**: "Observer" – *Design Principles and Design Patterns*, Robert C. Martin

#### 2. **Conceitos-Chave Identificados**
- O padrão **Observer** permite que objetos (**observadores**) sejam notificados automaticamente quando outro objeto (**sujeito**) muda de estado.
- Desacopla o produtor de eventos dos consumidores: o sujeito não precisa conhecer os observadores.
- Baseado em duas abstrações: `Subject` (com lista de observadores e método `Notify`) e `Observer` (com método `Update`).

#### 3. **Insights Relevantes**
> “We don’t want the detector to know about the actor.”  
→ Essa separação de responsabilidades é essencial para sistemas extensíveis e com baixo acoplamento.

#### 4. **Aplicações Práticas no Nosso Contexto**
- Em sistemas com múltiplas reações a um evento (ex: criação de pedido → enviar email, atualizar estoque, registrar métrica), usaremos Observer para desacoplar o gatilho das ações.
- Pode ser implementado via eventos em memória (para processamento síncrono) ou com message broker (para assíncrono).
- Útil em UIs reativas, mas também em backend para pipelines de processamento.

#### 5. **Decisões de Design ou Padrões a Adotar**
- Eventos de domínio serão modelados como `DomainEvent`, e handlers como `DomainEventHandler`.
- Evitaremos acoplamento direto entre serviços; preferiremos publicar eventos e reagir a eles.
- Em contextos síncronos, usaremos injeção de lista de observadores registrados.

#### 6. **Dúvidas ou Pontos a Aprofundar**
- Como garantir ordenação ou atomicidade em cenários com múltiplos observadores?
- Em ambientes distribuídos, Observer em memória é insuficiente — devemos migrar para pub/sub com garantias de entrega?