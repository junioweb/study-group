# 🎨 PRP - Desenvolvimento Frontend

## 🏷️ Metadados do PRP Frontend
- **PRP ID**: {{prp_id}}
- **Tipo**: Frontend Development
- **Framework**: {{framework}} (ex: React, Vue, Angular)
- **Styling**: {{styling}} (ex: Tailwind CSS, Styled Components)
- **Complexidade**: {{complexity}}

## 🎯 Business Context Layer

### Frontend Business Objectives
```
{{frontend_objectives}}
# Exemplo:
# "Criar interface moderna e responsiva para gestão de usuários que 
# reduza tempo de interação em 30% e aumente satisfação do usuário"
```

### UX Requirements
- **Responsividade**: {{responsiveness}} (ex: mobile-first, desktop optimized)
- **Acessibilidade**: {{accessibility}} (ex: WCAG 2.1 AA compliant)
- **Performance**: {{performance}} (ex: FCP < 1s, LCP < 2.5s)
- **Cross-browser**: {{cross_browser}} (ex: Chrome, Firefox, Safari, Edge)

## 👥 Stakeholder Analysis

### Frontend Stakeholders
```
{{frontend_stakeholders}}
# - End Users: Precisam de interface intuitiva e responsiva
# - UI/UX Designers: Garantir fidelidade ao design system
# - Backend Developers: Integração com APIs
# - Product Owners: Métricas de engajamento e conversão
```

## 📋 Frontend Requirement Extraction

### Component Specifications
```
{{component_specs}}
# UserProfileForm:
# - Campos: nome, email, telefone, avatar upload
# - Validação: email válido, nome obrigatório
# - Estados: loading, success, error
# - Responsivo: mobile, tablet, desktop
```

### Page Structure & Routing
```
{{routing_structure}}
# /users - Lista de usuários com busca e paginação
# /users/{id} - Detalhes do usuário com edição inline
# /users/new - Formulário de criação de usuário
# /profile - Perfil do usuário logado
```

### State Management Requirements
```
{{state_management}}
# - Global: User authentication state
# - Local: Form states, UI toggle states
# - Server: Data fetching and caching
# - URL: Query parameters and filters
```

## 🔧 Frontend Technical Translation

### Architecture Pattern
```
{{frontend_architecture}}
# React + TypeScript + Tailwind CSS:
# - Components: Functional components with hooks
# - State: React Context + useReducer or Zustand
# - Styling: Tailwind CSS with custom design tokens
# - Routing: React Router with lazy loading
# - API: React Query for data fetching
```

### Technology Stack Specifics
- **Framework**: {{framework}} (ex: React 18+, Vue 3, Angular)
- **Language**: {{language}} (ex: TypeScript, JavaScript)
- **Styling**: {{styling}} (ex: Tailwind, CSS Modules, Styled Components)
- **Build Tool**: {{build_tool}} (ex: Vite, Webpack, Next.js)

### Component Design Specifications
```
{{component_design}}
# - Atomic design methodology
# - Reusable component library
# - Prop interfaces with TypeScript
# - Storybook documentation
# - Accessibility attributes
```

### Performance Optimizations
```
{{performance_optimizations}}
# - Code splitting and lazy loading
# - Image optimization and lazy loading
# - Bundle size analysis and optimization
# - Memoization of expensive components
# - Virtualization for large lists
```

## 📝 Frontend Specification Output

### Expected Frontend Deliverables
```
{{frontend_deliverables}}
# 1. Complete React component implementation
# 2. TypeScript interfaces and types
# 3. Responsive styling with Tailwind CSS
# 4. State management setup
# 5. API integration with error handling
# 6. Form validation with user feedback
# 7. Loading states and skeleton screens
# 8. Error boundaries and fallback UI
```

### Project Structure
```
{{project_structure}}
# src/
# ├── components/
# │   ├── ui/           # Basic UI components
# │   ├── forms/        # Form components
# │   └── layout/       # Layout components
# ├── pages/           # Page components
# ├── hooks/           # Custom React hooks
# ├── services/        # API services
# ├── types/           # TypeScript definitions
# ├── utils/           # Utility functions
# └── styles/          # Global styles
```

### Development Configuration
```
{{dev_configuration}}
# package.json scripts:
# - dev: vite dev
# - build: vite build
# - preview: vite preview
# - lint: eslint .
# - type-check: tsc --noEmit
```

## ✅ Frontend Validation Framework

### Frontend Testing Strategy
```
{{frontend_testing}}
# - Unit tests: Jest + React Testing Library
# - Component tests: Storybook + play function
# - E2E tests: Playwright or Cypress
# - Accessibility tests: axe-core
# - Visual regression tests: Chromatic
```

### Frontend Quality Gates
```
{{frontend_quality_gates}}
# - TypeScript strict mode enabled
# - ESLint with no errors
# - All components have basic tests
# - Accessibility score > 90%
# - Bundle size under threshold
# - Lighthouse score > 90
```

### Browser Compatibility
```
{{browser_compatibility}}
# - Chrome 90+
# - Firefox 88+
# - Safari 14+
# - Edge 90+
# - Mobile Safari 14+
# - Chrome Mobile 90+
```

### Performance Metrics
```
{{performance_metrics}}
# - First Contentful Paint: < 1s
# - Largest Contentful Paint: < 2.5s
# - Cumulative Layout Shift: < 0.1
# - Time to Interactive: < 3.5s
# - Bundle size: < 500KB gzipped
```

## ⚠️ Frontend Known Gotchas

### Common Frontend Pitfalls
```
{{frontend_pitfalls}}
# - Prop drilling in deep component trees
# - Unnecessary re-renders causing performance issues
# - Memory leaks from event listeners
# - Accessibility violations
# - Responsive design breakpoints
# - Cross-browser compatibility issues
```

### State Management Challenges
```
{{state_management_challenges}}
# - Race conditions in async operations
# - Stale state in closures
# - Complex state synchronization
# - Optimistic UI updates
# - Error state handling
```

## 🔄 Frontend Execution Context

### Frontend Dependencies
```
{{frontend_dependencies}}
# - Backend API endpoints available
# - Design system tokens and assets
# - Icon library (Lucide, Heroicons)
# - Authentication service
```

### Development Setup
```
{{frontend_dev_setup}}
# npm install
# npm run dev
# Open http://localhost:5173
```

### Build & Deployment
```
{{build_deployment}}
# - Environment variables for API URLs
# - CDN for static assets
# - Cache headers configuration
# - CI/CD pipeline for testing and deployment
```

## 📊 Frontend Metrics

### Frontend Success Metrics
```
{{frontend_metrics}}
# - Core Web Vitals within thresholds
# - User engagement metrics (time on page, conversions)
# - Error rates and user-reported issues
# - Performance benchmark scores
# - Accessibility compliance score
```

### User Experience Monitoring
```
{{ux_monitoring}}
# - Real User Monitoring (RUM) data
# - Session replay for issue debugging
# - User feedback collection
# - A/B testing instrumentation
```

---
*PRP Frontend Template - Especializado em desenvolvimento de interfaces modernas e responsivas*