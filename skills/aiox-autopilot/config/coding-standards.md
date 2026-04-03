# Coding Standards — AIOX Autopilot

## General

- **Language**: TypeScript-first (frontend e backend)
- **Style**: Clean code, legível > clever
- **Naming**: camelCase para variáveis/funções, PascalCase para types/classes, kebab-case para arquivos
- **Imports**: Organizados (external → internal → types), sem unused imports
- **Error Handling**: Try/catch em async, error boundaries em React, typed errors

## TypeScript

- `strict: true` no tsconfig
- Sem `any` desnecessário — usar `unknown` + type guards
- Interfaces para contratos públicos, types para unions/intersections
- Enums → const objects ou string unions

## React / Next.js

- Componentes funcionais com hooks
- Server Components por padrão, Client Components apenas quando necessário
- Props tipadas com interface nomeada `{ComponentName}Props`
- Colocação: componente + teste + styles no mesmo diretório

## Testes

- Vitest ou Jest para unit tests
- Testing Library para componentes React
- Testes cobrindo acceptance criteria da story
- Naming: `describe('Feature') > it('should do X when Y')`

## Git

- Commits atômicos: 1 commit = 1 mudança lógica
- Conventional commits: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`
- Branch por story: `feature/story-{id}-{slug}`
