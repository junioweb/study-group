# 📊 Template de Métricas e Monitoramento

## 🏷️ Metadados do Dashboard
- **Dashboard ID**: {{dashboard_id}}
- **Contexto Monitorado**: {{monitored_context}} (ex: PRP específico, processo de engenharia)
- **Período de Monitoramento**: {{monitoring_period}} (ex: sprint atual, projeto completo)
- **Stakeholders Principais**: {{key_stakeholders}} (ex: product owners, tech leads, equipe)

## 🎯 Objetivos de Monitoramento

### Metas de Performance
```
{{performance_goals}}
# Exemplo:
# "Monitorar eficácia do processo de Context Engineering através de métricas quantitativas,
# identificando oportunidades de melhoria e garantindo ROI positivo"
```

### Key Results Esperados
```
{{expected_key_results}}
# - Redução de 50% no retrabalho causado por contextos incompletos
# - Aumento de 40% na velocidade de desenvolvimento
# - Melhoria de 30% na qualidade do código entregue
# - Redução de 60% nos bugs relacionados a requisitos ambíguos
```

## 📈 Categoria 1: Métricas de Eficiência

### Velocidade de Desenvolvimento
```
{{development_velocity}}
# - Cycle Time: {{cycle_time}} horas (ideal: < 24h)
# - Lead Time: {{lead_time}} dias (ideal: < 3 dias)
# - Deployment Frequency: {{deployment_freq}}/dia (ideal: > 1)
# - Throughput: {{throughput}} tasks/sprint (baseline: {{baseline}})
```

### Eficiência de Context Engineering
```
{{context_efficiency}}
# - Time to First PRP: {{time_to_first_prp}} horas
# - PRP Completion Rate: {{prp_completion}}%
# - Context Validation Time: {{validation_time}} minutos
# - Iteration Cycle Time: {{iteration_cycle}} horas
```

## 🎯 Categoria 2: Métricas de Qualidade

### Qualidade do Contexto
```
{{context_quality}}
# - Context Completeness: {{completeness}}% (meta: > 95%)
# - Ambiguity Score: {{ambiguity_score}} (escala 1-10, ideal: < 3)
# - Stakeholder Confidence: {{stakeholder_confidence}}% (meta: > 90%)
# - Requirement Stability: {{requirement_stability}}% (meta: > 85%)
```

### Qualidade do Código
```
{{code_quality}}
# - Bug Rate: {{bug_rate}}/1000 linhas (meta: < 1)
# - Test Coverage: {{test_coverage}}% (meta: > 80%)
# - Code Complexity: {{code_complexity}} (cyclomatic, ideal: < 15)
# - Technical Debt Ratio: {{tech_debt_ratio}}% (meta: < 5%)
```

## 💰 Categoria 3: Métricas de Negócio

### ROI e Impacto Financeiro
```
{{financial_impact}}
# - Development Cost Savings: {{cost_savings}} $
# - Rework Reduction: {{rework_reduction}} horas
# - Time to Market Improvement: {{ttm_improvement}} dias
# - Business Value Delivered: {{business_value}} $
```

### Satisfação do Cliente
```
{{customer_satisfaction}}
# - Customer Satisfaction Score: {{csat_score}} (escala 1-10)
# - Net Promoter Score: {{nps_score}}
# - Feature Adoption Rate: {{adoption_rate}}%
# - Customer Reported Issues: {{customer_issues}}/mês
```

## 👥 Categoria 4: Métricas de Equipe

### Engajamento e Produtividade
```
{{team_engagement}}
# - Team Velocity: {{team_velocity}} pontos/sprint
# - Developer Satisfaction: {{dev_satisfaction}}% (pesquisa)
# - Burnout Risk: {{burnout_risk}}% (meta: < 20%)
# - Knowledge Sharing Index: {{knowledge_index}} (escala 1-10)
```

### Collaboration Metrics
```
{{collaboration_metrics}}
# - Cross-team Collaboration: {{cross_team_collab}} eventos/semana
# - PR Review Time: {{pr_review_time}} horas (meta: < 4h)
# - Feedback Loop: {{feedback_loop}} horas (meta: < 8h)
# - Decision Making Speed: {{decision_speed}} horas (meta: < 24h)
```

## 🔄 Categoria 5: Métricas de Processo

### Eficácia do Context Engineering
```
{{process_effectiveness}}
# - First-Time Success Rate: {{first_time_success}}% (meta: > 70%)
# - Rework Due to Context Issues: {{rework_context}}%
# - Context Utilization Rate: {{context_utilization}}%
# - Automated Validation Coverage: {{auto_validation}}%
```

### Melhoria Contínua
```
{{continuous_improvement}}
# - Process Improvement Ideas: {{improvement_ideas}}/mês
# - Implementation Rate: {{implementation_rate}}%
# - Learning Cycle Time: {{learning_cycle}} dias
# - Best Practices Adoption: {{best_practices}}%
```

## 📊 Dashboard Visualização

### Layout do Dashboard
```
{{dashboard_layout}}
# Seção 1: Resumo Executivo (KPIs principais)
# Seção 2: Métricas de Eficiência (gráficos de tendência)
# Seção 3: Métricas de Qualidade (scorecards)
# Seção 4: Impacto de Negócio (gráficos financeiros)
# Seção 5: Métricas de Equipe (heatmaps)
# Seção 6: Tendências e Insights (análise temporal)
```

### Alertas e Notificações
```
{{alerts_config}}
# - Alertas de Performance: Limiar {{performance_threshold}}%
# - Alertas de Qualidade: Limiar {{quality_threshold}}
# - Alertas de Negócio: Limiar {{business_threshold}}$
# - Alertas de Equipe: Limiar {{team_threshold}}%
# - Canal de Notificação: {{notification_channel}}
```

## 🔍 Categoria 6: Análise de Causa Raiz

### Análise de Problemas
```
{{root_cause_analysis}}
# - Top 5 Issues: {{top_issues}}
# - Root Causes: {{root_causes}}
# - Impact Assessment: {{impact_assessment}}
# - Countermeasures: {{countermeasures}}
```

### Tendências e Patterns
```
{{trends_patterns}}
# - Seasonal Patterns: {{seasonal_patterns}}
# - Correlation Analysis: {{correlations}}
# - Predictive Trends: {{predictive_trends}}
# - Anomaly Detection: {{anomalies_detected}}
```

## 📋 Categoria 7: Relatórios e Insights

### Relatório Semanal
```
{{weekly_report}}
# Período: {{week_period}}
# Highlights: {{weekly_highlights}}
# Lowlights: {{weekly_lowlights}}
# KPIs Performance: {{weekly_kpis}}
# Action Items: {{weekly_actions}}
```

### Insights Estratégicos
```
{{strategic_insights}}
# - Opportunity Areas: {{opportunity_areas}}
# - Risk Factors: {{risk_factors}}
# - Investment Recommendations: {{investment_recs}}
# - Strategic Decisions: {{strategic_decisions}}
```

## 🛠️ Categoria 8: Configuração Técnica

### Ferramentas de Coleta
```
{{collection_tools}}
# - Monitoring: {{monitoring_tools}} (ex: Prometheus, Datadog)
# - Logging: {{logging_tools}} (ex: ELK, Splunk)
# - APM: {{apm_tools}} (ex: New Relic, AppDynamics)
# - CI/CD: {{ci_cd_tools}} (ex: Jenkins, GitHub Actions)
```

### Integrações
```
{{integrations}}
# - Project Management: {{pm_tools}} (ex: JIRA, Asana)
# - Communication: {{comms_tools}} (ex: Slack, Teams)
# - Documentation: {{docs_tools}} (ex: Confluence, Notion)
# - Code Quality: {{code_tools}} (ex: SonarQube, CodeClimate)
```

### Configuração de Data Pipeline
```
{{data_pipeline}}
# - Data Sources: {{data_sources}}
# - ETL Process: {{etl_process}}
# - Storage: {{storage_solution}}
# - Visualization: {{viz_tools}} (ex: Grafana, Tableau)
```

## 📈 Categoria 9: Benchmarking

### Comparativos Internos
```
{{internal_benchmarking}}
# - Team vs Team: {{team_comparison}}
# - Project vs Project: {{project_comparison}}
# - Sprint over Sprint: {{sprint_comparison}}
# - Year over Year: {{yoy_comparison}}
```

### Benchmarking Externo
```
{{external_benchmarking}}
# - Industry Standards: {{industry_standards}}
# - Competitor Analysis: {{competitor_analysis}}
# - Best Practices: {{best_practices_benchmark}}
# - Market Trends: {{market_trends}}
```

## 🚨 Categoria 10: Gestão de Riscos

### Indicadores de Risco
```
{{risk_indicators}}
# - Project Health: {{project_health}}% (meta: > 80%)
# - Risk Exposure: {{risk_exposure}} $
# - Issue Backlog: {{issue_backlog}} items
# - Dependency Risks: {{dependency_risks}}
```

### Early Warning Signals
```
{{warning_signals}}
# - Velocity Drop: {{velocity_drop}}% (alerta: > 20%)
# - Quality Decline: {{quality_decline}}% (alerta: > 15%)
# - Cost Overrun: {{cost_overrun}}% (alerta: > 10%)
# - Timeline Slippage: {{timeline_slippage}}% (alerta: > 15%)
```

## 📋 Template de Relatório Executivo

### Resumo para Leadership
```
{{executive_summary}}
# Período: {{reporting_period}}
# Overall Health: {{overall_health}}/100
# Key Achievements: {{key_achievements}}
# Major Challenges: {{major_challenges}}
# Strategic Recommendations: {{strategic_recommendations}}
# Investment Needs: {{investment_needs}}
```

### Action Plan
```
{{action_plan}}
# Priority 1: {{priority_1_action}} (Owner: {{owner_1}}, ETA: {{eta_1}})
# Priority 2: {{priority_2_action}} (Owner: {{owner_2}}, ETA: {{eta_2}})
# Priority 3: {{priority_3_action}} (Owner: {{owner_3}}, ETA: {{eta_3}})
# Monitoring Plan: {{monitoring_plan}}
```

---
*Template de Métricas e Monitoramento - Para tracking abrangente do impacto do Context Engineering através de dados quantitativos e qualitativos*