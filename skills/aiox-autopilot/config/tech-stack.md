# Tech Stack — AIOX Autopilot

> Stack padrão para projetos gerados pelo Autopilot. O aap-analyst pode recomendar alternativas baseado em pesquisa web.

## Stack Padrão (Greenfield Full-Stack)

| Layer | Default | Alternativas |
|---|---|---|
| **Frontend** | Next.js 15 (App Router) | Remix, Astro, SvelteKit |
| **UI Library** | React 19 | Vue, Svelte |
| **Styling** | Tailwind CSS 4 + shadcn/ui | Styled Components, CSS Modules |
| **State** | Zustand (client) + React Query (server) | Jotai, Redux Toolkit |
| **Backend** | Next.js API Routes / Route Handlers | Express, Fastify, Hono |
| **Database** | PostgreSQL (via Supabase) | MySQL, MongoDB, SQLite |
| **ORM** | Prisma / Drizzle | Kysely, TypeORM |
| **Auth** | Supabase Auth / NextAuth.js | Clerk, Auth0 |
| **Hosting** | Vercel (frontend) + Supabase (DB) | Railway, Fly.io, AWS |
| **CI/CD** | GitHub Actions | GitLab CI |
| **Testing** | Vitest + Testing Library + Playwright | Jest, Cypress |
| **Linting** | ESLint + Prettier + Biome | oxlint |

## Critérios de Seleção

O aap-analyst/product-architect avaliam stack com base em:

1. **Maturidade**: estável em produção, não beta experimental
2. **Ecossistema**: plugins, docs, comunidade ativa
3. **DX**: developer experience, facilidade de setup
4. **Performance**: benchmarks recentes
5. **Custo**: free tier generoso para MVP
6. **Fit**: adequado ao tipo de projeto específico

## Pesquisa Web

O aap-analyst SEMPRE pesquisa:
- "best [framework] for [project type] 2025 2026"
- "[framework A] vs [framework B] comparison"
- "[framework] production ready review"
