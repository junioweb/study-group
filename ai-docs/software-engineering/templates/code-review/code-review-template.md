# 📋 Template de Code Review - Context Engineering

## 🏷️ Metadados da Review
- **PRP ID**: {{prp_id}}
- **Contexto**: {{context_name}}
- **Autor**: {{author}}
- **Reviewer**: {{reviewer}}
- **Data**: {{review_date}}
- **Status**: {{status}}

## 🎯 Critérios de Review Baseados em SOLID
- [ ] **Single Responsibility**: Cada classe/função tem uma única responsabilidade?
- [ ] **Open/Closed**: Código aberto para extensão, fechado para modificação?
- [ ] **Liskov Substitution**: Subtipos substituem seus tipos base?
- [ ] **Interface Segregation**: Interfaces específicas ao invés de gerais?
- [ ] **Dependency Inversion**: Depende de abstrações, não implementações?

## 🏗️ Arquitetura (Hexagonal/Clean)
- [ ] **Separação de Concerns**: Camadas bem definidas?
- [ ] **Ports & Adapters**: Interfaces claras para integrações?
- [ ] **Dependency Direction**: Dependências apontam para o centro?
- [ ] **Testabilidade**: Fácil de mockar e testar?

## 🧪 Testing & Quality
- [ ] **Cobertura**: >90% coverage para código crítico?
- [ ] **Testes Unitários**: Business logic testado isoladamente?
- [ ] **Testes de Integração**: Integrações testadas adequadamente?
- [ ] **Edge Cases**: Casos de borda cobertos?
- [ ] **Performance**: Testes de carga para endpoints críticos?

## 🔒 Security
- [ ] **Input Validation**: Todas as entradas validadas?
- [ ] **Authentication**: JWT validation implementado?
- [ ] **Authorization**: Controle de acesso adequado?
- [ ] **Data Sanitization**: Dados sanitizados antes do processamento?

## 📊 Performance
- [ ] **Response Time**: Dentro dos limites estabelecidos?
- [ ] **Database Queries**: Queries otimizadas e indexadas?
- [ ] **Caching**: Cache implementado onde apropriado?
- [ ] **Concurrency**: Tratamento adequado de concorrência?

## 📝 Documentation
- [ ] **OpenAPI Spec**: Documentação completa da API?
- [ ] **Code Comments**: Comentários claros e úteis?
- [ ] **README**: Guia de setup e uso?
- [ ] **Examples**: Exemplos de uso incluídos?

## 💡 Sugestões de Melhoria
**Pontos Fortes:**
{{strengths}}

**Áreas de Melhoria:**
{{improvements}}

**Ações Recomendadas:**
{{actions}}

## ✅ Resultado Final
- [ ] **✅ APPROVED** - Pode ser mergeado
- [ ] **✅ APPROVED WITH COMMENTS** - Merge após pequenos ajustes
- [ ] **🔄 NEEDS WORK** - Revisão significativa necessária
- [ ] **❌ REJECTED** - Não atende aos critérios mínimos

**Comentários Finais:**
{{final_comments}}