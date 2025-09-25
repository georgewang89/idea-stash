# Unified Project Structure

```
ideastash/
├── .github/                           # CI/CD workflows
│   └── workflows/
│       ├── ci.yml                    # Test and lint on PR
│       ├── deploy-staging.yml        # Deploy to staging
│       └── deploy-production.yml     # Deploy to production
├── .next/                            # Next.js build output (gitignored)
├── public/                           # Static assets
│   ├── icons/                       # PWA icons
│   ├── manifest.json               # PWA manifest
│   └── sw.js                       # Service Worker
├── src/
│   ├── components/                  # React components
│   │   ├── ui/                     # Base UI components
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Modal.tsx
│   │   │   └── index.ts
│   │   ├── ideas/                  # Idea-specific components
│   │   │   ├── QuickCapture.tsx
│   │   │   ├── IdeaCard.tsx
│   │   │   ├── DraftList.tsx
│   │   │   ├── IdeaPool.tsx
│   │   │   └── SearchResults.tsx
│   │   ├── auth/                   # Authentication components
│   │   │   ├── SignInForm.tsx
│   │   │   ├── SignUpForm.tsx
│   │   │   └── ProtectedRoute.tsx
│   │   └── layout/                 # Layout components
│   │       ├── Navigation.tsx
│   │       ├── Header.tsx
│   │       └── Layout.tsx
│   ├── hooks/                      # Custom React hooks
│   │   ├── useIdeas.ts
│   │   ├── useOfflineSync.ts
│   │   ├── useSearch.ts
│   │   └── useLocalStorage.ts
│   ├── lib/                        # Shared utilities
│   │   ├── api.ts                 # API client
│   │   ├── auth.ts                # Auth utilities
│   │   ├── constants.ts           # App constants
│   │   ├── utils.ts               # Helper functions
│   │   ├── validations.ts         # Schema validations
│   │   ├── supabase.ts           # Supabase client
│   │   ├── services/              # Business logic services
│   │   │   ├── ideaService.ts
│   │   │   ├── searchService.ts
│   │   │   └── syncService.ts
│   │   ├── repositories/          # Data access layer
│   │   │   ├── ideaRepository.ts
│   │   │   └── userRepository.ts
│   │   └── middleware/            # API middleware
│   │       ├── authMiddleware.ts
│   │       ├── errorHandler.ts
│   │       ├── rateLimit.ts
│   │       └── validation.ts
│   ├── stores/                    # Zustand stores
│   │   ├── useIdeaStore.ts
│   │   ├── useAuthStore.ts
│   │   └── useAppStore.ts
│   ├── styles/                    # Global styles
│   │   ├── globals.css
│   │   └── components.css
│   └── types/                     # TypeScript type definitions
│       ├── index.ts
│       ├── api.ts
│       ├── database.ts
│       └── supabase.ts
├── pages/                         # Next.js pages and API routes
│   ├── _app.tsx                  # App wrapper with providers
│   ├── _document.tsx             # Custom document
│   ├── index.tsx                 # Quick Capture (home page)
│   ├── drafts.tsx                # Draft Management View
│   ├── ideas.tsx                 # Idea Pool Dashboard
│   ├── search.tsx                # Search Results Page
│   ├── settings/
│   │   ├── profile.tsx           # User Profile Settings
│   │   └── preferences.tsx       # App Preferences
│   ├── auth/
│   │   ├── sign-in/
│   │   │   └── [[...index]].tsx  # Clerk sign-in pages
│   │   ├── sign-up/
│   │   │   └── [[...index]].tsx  # Clerk sign-up pages
│   │   └── sso-callback.tsx      # SSO callback handling
│   └── api/                      # API Routes
│       ├── ideas/
│       │   ├── index.ts          # GET /api/ideas, POST /api/ideas
│       │   ├── [id].ts           # GET/PATCH/DELETE /api/ideas/:id
│       │   └── search.ts         # GET /api/ideas/search
│       ├── sync.ts               # POST /api/sync
│       ├── health.ts             # GET /api/health
│       └── webhooks/
│           └── clerk.ts          # Clerk user events
├── __tests__/                    # Test files
│   ├── components/               # Component tests
│   │   ├── IdeaCard.test.tsx
│   │   └── QuickCapture.test.tsx
│   ├── hooks/                    # Hook tests
│   │   └── useIdeas.test.ts
│   ├── api/                      # API endpoint tests
│   │   └── ideas.test.ts
│   ├── e2e/                      # End-to-end tests
│   │   ├── idea-capture.spec.ts
│   │   ├── draft-commit.spec.ts
│   │   └── search.spec.ts
│   └── setup/                    # Test setup files
│       ├── jest.setup.ts
│       └── test-utils.tsx
├── docs/                         # Documentation
│   ├── prd.md                   # Product Requirements Document
│   ├── front-end-spec.md        # Frontend/UX Specification
│   ├── architecture.md          # This fullstack architecture doc
│   └── api/                     # API documentation
│       └── openapi.yaml         # OpenAPI specification
├── supabase/                    # Supabase configuration
│   ├── migrations/              # Database migrations
│   │   ├── 20241001000000_initial_schema.sql
│   │   └── 20241001000001_add_search_indexes.sql
│   ├── seed.sql                # Database seed data
│   └── config.toml             # Supabase config
├── .env.local.example          # Environment variable template
├── .env.local                  # Local environment variables (gitignored)
├── .gitignore                  # Git ignore rules
├── next.config.js              # Next.js configuration
├── tailwind.config.js          # Tailwind CSS configuration
├── tsconfig.json               # TypeScript configuration
├── jest.config.js              # Jest testing configuration
├── playwright.config.ts        # Playwright E2E configuration
├── package.json                # Dependencies and scripts
├── turbo.json                  # Turborepo configuration
└── README.md                   # Project documentation
```
