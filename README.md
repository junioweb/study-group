# 📚 Grupo de Estudos - Padrões de Arquitetura

Este repositório foi criado para simular um grupo de estudos focado em **Padrões de Arquitetura de Software**, com ênfase em desenvolvimento orientado a testes, design orientado a domínio e microsserviços orientados a eventos.

## 🎯 Objetivo

O objetivo principal é criar um espaço estruturado para estudo colaborativo de livros técnicos sobre arquitetura de software. A cada parte ou capítulo lido, extraímos pontos relevantes que norteiam nossos padrões de desenvolvimento, estruturação de software e refinamento das tarefas para implementação das necessidades do negócio.

## 📖 Metodologia de Estudo

Cada livro estudado possui um diretório próprio com registros estruturados de aprendizados. Os registros seguem um template padronizado que inclui:

1. **Referência da Leitura** - Identificação da seção/capítulo estudado
2. **Conceitos-Chave Identificados** - Principais conceitos abordados
3. **Insights Relevantes** - Citações e reflexões importantes
4. **Aplicações Práticas no Nosso Contexto** - Como aplicar na prática
5. **Decisões de Design ou Padrões a Adotar** - Regras e convenções
6. **Dúvidas ou Pontos a Aprofundar** - Questões para discussão

## 📂 Estrutura do Repositório

```
study-group/
├── README.md                    # Este arquivo
├── template.md                  # Template para registros de aprendizado
└── cosmic-python-book/          # Estudos do livro "Architecture Patterns with Python"
    ├── 01-prefacio-introducao.md
    ├── 02-parte1-introducao.md
    ├── 03-cap1-domain-modeling.md
    └── ...
```

## 📘 Livros em Estudo

### Architecture Patterns with Python (Cosmic Python)

**Autor:** Harry Percival & Bob Gregory  
**Foco:** Test-Driven Development, Domain-Driven Design, and Event-Driven Microservices

Este livro explora como construir arquiteturas robustas usando Python, aplicando princípios como:
- **TDD (Test-Driven Development)** - Desenvolvimento guiado por testes
- **DDD (Domain-Driven Design)** - Design orientado a domínio
- **Event-Driven Architecture** - Arquitetura orientada a eventos
- **Clean Architecture** - Arquitetura limpa com inversão de dependências

> **Nota:** Embora o livro original esteja em inglês, os registros de estudo estão em português para facilitar a compreensão e discussão no grupo.

#### Progresso dos Estudos

- ✅ Prefácio e Introdução
- ✅ Parte 1: Introdução
- ✅ Capítulo 1: Domain Modeling
- ✅ Capítulo 2: Repository Pattern
- ✅ Capítulo 3: Abstractions
- ✅ Capítulo 4: Service Layer
- ✅ Capítulo 5: High Gear / Low Gear
- ✅ Capítulo 6: Unit of Work
- ✅ Capítulo 7: Aggregate
- ✅ Parte 2: Introdução
- ✅ Capítulo 8: Events and Message Bus
- ✅ Capítulo 9: All Messagebus
- ✅ Capítulo 10: Commands
- ✅ Capítulo 11: External Events
- ✅ Capítulo 12: CQRS
- ✅ Capítulo 13: Dependency Injection

## 🛠️ Como Contribuir

1. Leia o `template.md` para entender o formato dos registros
2. Crie um novo arquivo seguindo a numeração sequencial
3. Preencha todas as seções do template com seus aprendizados
4. Commit suas contribuições seguindo o padrão de mensagens estabelecido

## 📝 Padrões e Convenções

### Nomenclatura de Arquivos

Os arquivos seguem o padrão: `##-descricao.md`, onde:
- `##` é um número sequencial de dois dígitos
- `descricao` é uma descrição curta do conteúdo (em kebab-case)

### Estrutura de Commits

Os commits devem seguir o padrão semântico:
- `feat:` para novos capítulos ou seções
- `docs:` para atualizações em documentação
- `refactor:` para melhorias nos registros existentes
- `fix:` para correções

## 🎓 Conceitos Centrais Estudados

Ao longo dos estudos, estamos focando em:

- **Clean Architecture** e inversão de dependências
- **Domain Model** como núcleo isolado da aplicação
- **Repository Pattern** para abstração de persistência
- **Service Layer** para definição de casos de uso
- **Unit of Work** para operações atômicas
- **Aggregate Pattern** para integridade de dados
- **Event-Driven Architecture** e Message Bus
- **CQRS (Command Query Responsibility Segregation)**
- **Dependency Injection** e inversão de controle

## 📚 Recursos Adicionais

- [Template de Registro](./template.md) - Formato padrão para documentar aprendizados
- [Cosmic Python Book](https://www.cosmicpython.com/) - Site oficial do livro

---

**Última atualização:** 2024

