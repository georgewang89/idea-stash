# Tech Stack

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| Frontend Language | TypeScript | 5.3+ | Type-safe JavaScript development | Prevents runtime errors, better DX, aligns with team preferences |
| Frontend Framework | Next.js | 14.0+ | React framework with SSR/SSG | Excellent performance, SEO, API routes, Vercel integration |
| UI Component Library | Tailwind CSS + Headless UI | 3.3+ / 1.7+ | Utility-first styling + accessible components | Rapid development, consistent design, accessibility built-in |
| State Management | Zustand | 4.4+ | Lightweight state management | Simpler than Redux, TypeScript-first, perfect for MVP scope |
| Backend Language | TypeScript | 5.3+ | Shared language across stack | Code reuse, consistent patterns, single hiring profile |
| Backend Framework | Next.js API Routes | 14.0+ | Serverless API endpoints | Unified codebase, automatic deployments, edge optimization |
| API Style | REST + tRPC | tRPC 10.0+ | Type-safe APIs with REST fallback | End-to-end type safety, excellent DX, gradual adoption |
| Database | PostgreSQL | 15+ | Relational database with full-text search | Proven reliability, excellent search, JSON support for flexibility |
| Cache | Redis | 7.0+ | Session storage and API caching | Sub-200ms response times, session persistence |
| File Storage | Supabase Storage | Latest | File uploads (future feature) | Integrated with database, CDN included |
| Authentication | Clerk | Latest | Complete auth solution | Easy setup, social logins, user management UI |
| Frontend Testing | Vitest + React Testing Library | 1.0+ / 13.0+ | Fast unit and component testing | Faster than Jest, excellent React integration |
| Backend Testing | Vitest + Supertest | 1.0+ / 6.3+ | API endpoint testing | Consistent tooling, fast execution |
| E2E Testing | Playwright | 1.40+ | End-to-end user flows | Reliable, fast, multi-browser support |
| Build Tool | Turborepo | 1.10+ | Monorepo build optimization | Incremental builds, remote caching, perfect for Next.js |
| Bundler | Next.js Built-in | 14.0+ | Webpack-based bundling | Optimized for React, automatic splitting |
| IaC Tool | Vercel CLI + Supabase CLI | Latest | Infrastructure as code | Version-controlled deployments, environment parity |
| CI/CD | GitHub Actions | Latest | Automated testing and deployment | Free for open source, Vercel integration |
| Monitoring | Vercel Analytics + Sentry | Latest | Performance and error tracking | Real user metrics, comprehensive error reporting |
| Logging | Pino | 8.0+ | Structured JSON logging | High performance, structured data for analysis |
| CSS Framework | Tailwind CSS | 3.3+ | Utility-first CSS framework | Rapid prototyping, consistent design system |
