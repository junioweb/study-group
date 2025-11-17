## Purpose
Establish standard toolchain and development workflow for Node.js/TypeScript projects with consistent linting, formatting, testing, and build processes.

## Guidelines
Required tooling stack:
- TypeScript as primary language with `strict: true` in tsconfig.json
- ESLint with TypeScript plugin and custom architecture rules
- Prettier for consistent code formatting
- Vitest or Jest for testing framework
- Husky for Git hooks automation
- Dependency-cruiser for architectural boundary validation

## TypeScript configuration:
```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "node",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "paths": {
      "@domain/*": ["src/domain/*"],
      "@application/*": ["src/application/*"],
      "@infrastructure/*": ["src/infrastructure/*"],
      "@interfaces/*": ["src/interfaces/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## ESLint configuration with architecture rules:
```javascript
// .eslintrc.js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'import'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier'
  ],
  rules: {
    // Architecture boundary rules
    'no-restricted-imports': [
      'error',
      {
        patterns: [
          {
            group: ['@domain/**'],
            message: 'Domain layer must not import from other layers'
          },
          {
            group: ['@application/**'],
            message: 'Application layer must not import from infrastructure/interfaces'
          }
        ]
      }
    ],
    // Code quality rules
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/explicit-function-return-type': 'error',
    'import/order': ['error', { 'groups': ['builtin', 'external', 'parent', 'sibling', 'index'] }]
  },
}
```

## Git hooks with Husky:
```bash
# Install Husky
npm install -D husky lint-staged
npx husky init
```

```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
npx dependency-cruiser --validate .dependency-cruiser.json src
```

```json
// lint-staged.config.js
module.exports = {
  '*.{ts,tsx}': [
    'eslint --fix',
    'prettier --write'
  ],
  '*.{md,json,yml,yaml}': [
    'prettier --write'
  ]
};
```

## Testing setup with Vitest:
```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    setupFiles: ['./tests/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      thresholds: {
        statements: 80,
        branches: 70,
        functions: 80,
        lines: 80
      }
    }
  }
});
```

## Build and deployment pipeline:
```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v3
        with:
          node-version: '18.x'
      - run: npm ci
      - run: npm run lint
      - run: npm run test:coverage
      - run: npm run build
      - name: Check architecture boundaries
        run: npx dependency-cruiser --validate .dependency-cruiser.json src
      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
```

## Script standards in package.json:
```json
{
  "scripts": {
    "build": "tsc",
    "dev": "ts-node-dev --respawn --transpile-only src/interfaces/http/server.ts",
    "start": "node dist/interfaces/http/server.js",
    "lint": "eslint . --ext .ts,.tsx",
    "lint:fix": "eslint . --ext .ts,.tsx --fix",
    "format": "prettier --write \"**/*.{ts,tsx,md,json,yml,yaml}\"",
    "test": "vitest",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "vitest run -c ./tests/e2e/vitest.config.ts",
    "arch:validate": "dependency-cruiser --validate .dependency-cruiser.json src",
    "prepare": "husky install"
  }
}
```

## Anti-Patterns
❌ Mixed JavaScript/TypeScript in the same project
❌ Different formatting rules across team members
❌ Tests that require database setup to run (unit tests should be isolated)
❌ Build processes that don't validate architectural boundaries
❌ Manual code reviews for formatting issues (automate with Prettier + ESLint)
❌ Different TypeScript configurations across environments
❌ Skipping type checking in CI pipeline
❌ Using different testing frameworks for unit vs integration tests