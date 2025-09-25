# IdeaStash Fullstack Architecture Document

This document outlines the complete fullstack architecture for IdeaStash, including backend systems, frontend implementation, and their integration. It serves as the single source of truth for AI-driven development, ensuring consistency across the entire technology stack.

This unified approach combines what would traditionally be separate backend and frontend architecture documents, streamlining the development process for modern fullstack applications where these concerns are increasingly intertwined.

## Starter Template or Existing Project
**N/A - Greenfield project**

Based on the PRD technical assumptions, this is a new project without existing templates. However, I recommend considering the **T3 Stack** (Next.js + TypeScript + tRPC + Prisma + NextAuth) as it aligns perfectly with our requirements:
- Vercel deployment ready
- Built-in authentication
- Type-safe APIs
- PostgreSQL support
- Excellent developer experience

## Change Log
| Date | Version | Description | Author |
|------|---------|-------------|---------|
| 2025-09-25 | 1.0 | Initial fullstack architecture creation | Winston (Architect) |

## High Level Architecture

### Technical Summary
IdeaStash follows a modern Jamstack architecture with a Next.js React frontend deployed on Vercel and a serverless backend API using PostgreSQL database hosted on Railway/Supabase. The application implements a Progressive Web App (PWA) pattern with offline-first capabilities, using Service Workers for caching and local storage for draft persistence. The core two-stage capture/commit workflow is supported by a RESTful API with real-time synchronization, full-text search via PostgreSQL, and seamless authentication through Clerk. This architecture maximizes development velocity while ensuring scalability, security, and the sub-200ms response times required for optimal idea capture.

### Platform and Infrastructure Choice
**Platform:** Vercel + Supabase
**Key Services:** Vercel (Frontend Hosting, Edge Functions), Supabase (Database, Auth, Storage), Clerk (Alternative Auth), Railway (Alternative Database)
**Deployment Host and Regions:** Vercel Edge Network (Global CDN), Supabase US-East-1 (primary database region)

### Repository Structure
**Structure:** Monorepo with Next.js app and API routes
**Monorepo Tool:** Turborepo for build optimization and caching
**Package Organization:** Single Next.js app with API routes, shared utilities in lib folder

### High Level Architecture Diagram
```mermaid
graph TD
    U[Users] --> CDN[Vercel Edge Network]
    CDN --> FE[Next.js Frontend]
    FE --> SW[Service Worker]
    SW --> LS[Local Storage]
    FE --> API[Next.js API Routes]
    API --> AUTH[Clerk Auth]
    API --> DB[(Supabase PostgreSQL)]
    API --> SEARCH[PostgreSQL Full-Text Search]

    subgraph "Vercel Platform"
        FE
        API
        EDGE[Edge Functions]
    end

    subgraph "Supabase Platform"
        DB
        SEARCH
        STORAGE[File Storage]
        REALTIME[Real-time subscriptions]
    end
```

### Architectural Patterns
- **Jamstack Architecture:** Next.js SSG/SSR with serverless API functions - _Rationale:_ Optimal performance, SEO, and developer experience
- **Progressive Web App:** Service Worker + offline capabilities - _Rationale:_ Ensures idea capture works without internet connection
- **API-First Design:** RESTful API with clear contracts - _Rationale:_ Enables future mobile apps and third-party integrations
- **Repository Pattern:** Abstract data access logic - _Rationale:_ Enables testing and potential database migration flexibility
- **Component-Based UI:** Reusable React components with TypeScript - _Rationale:_ Maintainability and type safety across codebase

## Tech Stack

### Technology Stack Table

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

## Data Models

### User Model
**Purpose:** Represents authenticated users with their account information and preferences

**Key Attributes:**
- id: string (UUID) - Unique user identifier
- email: string - Primary authentication email
- name: string - Display name for the user
- avatar_url: string (optional) - Profile picture URL
- created_at: timestamp - Account creation time
- updated_at: timestamp - Last profile update
- settings: JSON - User preferences and configuration

#### TypeScript Interface
```typescript
export interface User {
  id: string;
  email: string;
  name: string;
  avatar_url?: string;
  created_at: Date;
  updated_at: Date;
  settings: {
    theme: 'light' | 'dark' | 'system';
    notifications_enabled: boolean;
    auto_commit_enabled: boolean;
  };
}
```

#### Relationships
- One-to-many with Ideas (user can have multiple ideas)

### Idea Model
**Purpose:** Core entity representing both draft and committed ideas with full audit trail

**Key Attributes:**
- id: string (UUID) - Unique idea identifier
- user_id: string (UUID, FK) - Owner reference
- content: text - The actual idea content
- status: enum - 'draft' or 'committed'
- created_at: timestamp - Initial capture time
- committed_at: timestamp (optional) - When idea was committed
- updated_at: timestamp - Last modification time
- search_vector: tsvector - PostgreSQL full-text search vector

#### TypeScript Interface
```typescript
export interface Idea {
  id: string;
  user_id: string;
  content: string;
  status: 'draft' | 'committed';
  created_at: Date;
  committed_at?: Date;
  updated_at: Date;
}

// API Response types
export interface IdeaWithUser extends Idea {
  user: Pick<User, 'name' | 'avatar_url'>;
}

export interface IdeaSearchResult extends Idea {
  headline?: string; // Search result highlight
  rank?: number; // Search relevance score
}
```

#### Relationships
- Many-to-one with User (each idea belongs to one user)

## API Specification

Using REST API with tRPC for type safety:

### REST API Specification
```yaml
openapi: 3.0.0
info:
  title: IdeaStash API
  version: 1.0.0
  description: RESTful API for IdeaStash idea management platform
servers:
  - url: https://ideastash.vercel.app/api
    description: Production API
  - url: http://localhost:3000/api
    description: Development API

paths:
  /ideas:
    get:
      summary: List user's ideas
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [draft, committed, all]
        - name: limit
          in: query
          schema:
            type: integer
            default: 50
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: List of ideas
          content:
            application/json:
              schema:
                type: object
                properties:
                  ideas:
                    type: array
                    items:
                      $ref: '#/components/schemas/Idea'
                  nextCursor:
                    type: string
    post:
      summary: Create new idea
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                  maxLength: 10000
                status:
                  type: string
                  enum: [draft, committed]
                  default: draft
      responses:
        '201':
          description: Idea created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Idea'

  /ideas/{id}:
    get:
      summary: Get specific idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Idea details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Idea'
    patch:
      summary: Update idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                status:
                  type: string
                  enum: [draft, committed]
      responses:
        '200':
          description: Idea updated
    delete:
      summary: Delete idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '204':
          description: Idea deleted

  /search:
    get:
      summary: Search committed ideas
      parameters:
        - name: q
          in: query
          required: true
          schema:
            type: string
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: Search results
          content:
            application/json:
              schema:
                type: object
                properties:
                  results:
                    type: array
                    items:
                      $ref: '#/components/schemas/IdeaSearchResult'
                  total:
                    type: integer

  /sync:
    post:
      summary: Sync offline ideas
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                ideas:
                  type: array
                  items:
                    $ref: '#/components/schemas/OfflineIdea'
      responses:
        '200':
          description: Sync results
          content:
            application/json:
              schema:
                type: object
                properties:
                  synced:
                    type: array
                    items:
                      $ref: '#/components/schemas/Idea'
                  conflicts:
                    type: array
                    items:
                      $ref: '#/components/schemas/SyncConflict'

components:
  schemas:
    Idea:
      type: object
      properties:
        id:
          type: string
        user_id:
          type: string
        content:
          type: string
        status:
          type: string
          enum: [draft, committed]
        created_at:
          type: string
          format: date-time
        committed_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time
    IdeaSearchResult:
      allOf:
        - $ref: '#/components/schemas/Idea'
        - type: object
          properties:
            headline:
              type: string
            rank:
              type: number
    OfflineIdea:
      type: object
      properties:
        local_id:
          type: string
        content:
          type: string
        created_at:
          type: string
          format: date-time
    SyncConflict:
      type: object
      properties:
        local_id:
          type: string
        server_idea:
          $ref: '#/components/schemas/Idea'
        conflict_type:
          type: string
          enum: [content_mismatch, timestamp_conflict]
```

## Components

### API Layer Component
**Responsibility:** Handles all backend API endpoints, authentication, and data validation

**Key Interfaces:**
- REST endpoints for CRUD operations on ideas
- Authentication middleware integration
- PostgreSQL database connection and queries
- Full-text search implementation

**Dependencies:** Supabase client, Clerk authentication, PostgreSQL

**Technology Stack:** Next.js API Routes, TypeScript, Supabase-js, Clerk SDK

### Idea Management Component
**Responsibility:** Core business logic for the two-stage capture/commit workflow

**Key Interfaces:**
- Draft creation and editing services
- Commit workflow processing
- Idea retrieval and filtering
- Search functionality with ranking

**Dependencies:** API Layer, Database Component

**Technology Stack:** TypeScript business logic, PostgreSQL stored procedures

### Frontend App Component
**Responsibility:** React-based user interface implementing the UX specification

**Key Interfaces:**
- Quick capture interface
- Draft management views
- Idea pool dashboard
- Search and discovery UI

**Dependencies:** API Layer, Authentication Component, Offline Component

**Technology Stack:** Next.js, React 18, TypeScript, Tailwind CSS, Headless UI

### Authentication Component
**Responsibility:** User authentication, session management, and authorization

**Key Interfaces:**
- Login/logout flows
- Protected route middleware
- User profile management
- Session persistence

**Dependencies:** Clerk service, API Layer

**Technology Stack:** Clerk React SDK, Next.js middleware

### Offline/PWA Component
**Responsibility:** Service Worker implementation for offline functionality

**Key Interfaces:**
- Draft persistence in local storage
- Background sync when online
- Conflict resolution UI
- Cache management for committed ideas

**Dependencies:** Browser APIs, API Layer

**Technology Stack:** Service Worker API, IndexedDB, React Context

### Search Component
**Responsibility:** Full-text search implementation with ranking and highlighting

**Key Interfaces:**
- PostgreSQL full-text search queries
- Result ranking and relevance scoring
- Search result highlighting
- Search analytics

**Dependencies:** Database Component, API Layer

**Technology Stack:** PostgreSQL GIN indexes, tsvector, ts_rank functions

### Component Diagrams
```mermaid
graph TD
    subgraph "Frontend (Next.js)"
        FE[Frontend App Component]
        AUTH[Authentication Component]
        OFFLINE[Offline/PWA Component]
        SEARCH_UI[Search UI Component]
    end

    subgraph "Backend (API Routes)"
        API[API Layer Component]
        IDEAS[Idea Management Component]
        SEARCH_BE[Search Component]
    end

    subgraph "External Services"
        CLERK[Clerk Auth]
        SUPABASE[(Supabase PostgreSQL)]
        STORAGE[Supabase Storage]
    end

    FE --> AUTH
    FE --> OFFLINE
    FE --> API
    AUTH --> CLERK
    API --> IDEAS
    API --> SEARCH_BE
    IDEAS --> SUPABASE
    SEARCH_BE --> SUPABASE
    OFFLINE --> API
    SEARCH_UI --> SEARCH_BE
```

## Core Workflows

```mermaid
sequenceDiagram
    participant U as User
    participant FE as Frontend
    participant SW as Service Worker
    participant API as API Routes
    participant DB as PostgreSQL
    participant CLERK as Clerk Auth

    Note over U,CLERK: Quick Idea Capture Flow
    U->>FE: Open app
    FE->>CLERK: Check auth status
    CLERK->>FE: User authenticated
    FE->>U: Show Quick Capture screen
    U->>FE: Start typing idea
    FE->>SW: Auto-save to local storage
    U->>FE: Click "Save Draft"
    FE->>API: POST /api/ideas (draft)
    API->>CLERK: Validate JWT token
    CLERK->>API: Token valid + user info
    API->>DB: INSERT into ideas table
    DB->>API: Draft created with ID
    API->>FE: Success response with idea
    FE->>U: Show success feedback
    FE->>U: Update draft list

    Note over U,CLERK: Draft to Commit Flow
    U->>FE: Click "Commit" on draft
    FE->>U: Show commit confirmation
    U->>FE: Confirm commit
    FE->>API: PATCH /api/ideas/:id (set status=committed)
    API->>DB: UPDATE idea SET status='committed', committed_at=NOW()
    DB->>API: Update successful
    API->>FE: Committed idea response
    FE->>U: Show success animation
    FE->>U: Move to Idea Pool view

    Note over U,CLERK: Search Flow
    U->>FE: Enter search query
    FE->>API: GET /api/search?q=query
    API->>DB: SELECT with to_tsquery() and ts_rank()
    DB->>API: Ranked results with highlights
    API->>FE: Search results JSON
    FE->>U: Display highlighted results
```

## Database Schema

```sql
-- Enable extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";

-- Users table (managed by Clerk)
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    clerk_id VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    avatar_url TEXT,
    settings JSONB DEFAULT '{"theme": "system", "notifications_enabled": true, "auto_commit_enabled": false}'::jsonb,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Ideas table
CREATE TABLE ideas (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    content TEXT NOT NULL CHECK (length(content) > 0 AND length(content) <= 10000),
    status VARCHAR(20) NOT NULL DEFAULT 'draft' CHECK (status IN ('draft', 'committed')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    committed_at TIMESTAMP WITH TIME ZONE,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    search_vector TSVECTOR
);

-- Indexes for performance
CREATE INDEX idx_ideas_user_id ON ideas(user_id);
CREATE INDEX idx_ideas_status ON ideas(status);
CREATE INDEX idx_ideas_created_at ON ideas(created_at DESC);
CREATE INDEX idx_ideas_committed_at ON ideas(committed_at DESC) WHERE status = 'committed';

-- Full-text search index
CREATE INDEX idx_ideas_search ON ideas USING GIN(search_vector);

-- Trigger to update search vector
CREATE OR REPLACE FUNCTION update_idea_search_vector()
RETURNS TRIGGER AS $$
BEGIN
    NEW.search_vector := to_tsvector('english', NEW.content);
    NEW.updated_at := NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_ideas_search_vector
    BEFORE INSERT OR UPDATE ON ideas
    FOR EACH ROW EXECUTE FUNCTION update_idea_search_vector();

-- Row Level Security (RLS) for multi-tenant data isolation
ALTER TABLE ideas ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their own ideas
CREATE POLICY ideas_user_isolation ON ideas
    FOR ALL
    USING (user_id = auth.uid())
    WITH CHECK (user_id = auth.uid());

-- Optimized search function
CREATE OR REPLACE FUNCTION search_ideas(
    user_uuid UUID,
    search_query TEXT,
    result_limit INTEGER DEFAULT 20
)
RETURNS TABLE (
    id UUID,
    content TEXT,
    status VARCHAR(20),
    created_at TIMESTAMP WITH TIME ZONE,
    committed_at TIMESTAMP WITH TIME ZONE,
    headline TEXT,
    rank REAL
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        i.id,
        i.content,
        i.status,
        i.created_at,
        i.committed_at,
        ts_headline('english', i.content, plainto_tsquery('english', search_query)) as headline,
        ts_rank(i.search_vector, plainto_tsquery('english', search_query)) as rank
    FROM ideas i
    WHERE i.user_id = user_uuid
        AND i.status = 'committed'
        AND i.search_vector @@ plainto_tsquery('english', search_query)
    ORDER BY rank DESC, i.created_at DESC
    LIMIT result_limit;
END;
$$ LANGUAGE plpgsql;
```

## Unified Project Structure

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

## Development Workflow

### Local Development Setup

#### Prerequisites
```bash
# Install Node.js (v18+)
# Install pnpm (faster than npm)
npm install -g pnpm

# Install Supabase CLI
npm install -g supabase

# Install Vercel CLI (optional, for deployment)
npm install -g vercel
```

#### Initial Setup
```bash
# Clone repository
git clone https://github.com/your-org/ideastash.git
cd ideastash

# Install dependencies
pnpm install

# Copy environment variables
cp .env.local.example .env.local
# Edit .env.local with your API keys

# Start Supabase local development
supabase start

# Run database migrations
supabase db reset

# Generate TypeScript types from Supabase
pnpm run types:generate
```

#### Development Commands
```bash
# Start all services (frontend + Supabase)
pnpm run dev

# Start frontend only
pnpm run dev:next

# Start Supabase only
pnpm run dev:supabase

# Run tests
pnpm run test              # Unit tests
pnpm run test:watch        # Watch mode
pnpm run test:e2e          # End-to-end tests
pnpm run test:coverage     # Coverage report

# Database operations
pnpm run db:reset          # Reset local database
pnpm run db:seed           # Seed with test data
pnpm run db:migrate        # Run pending migrations
pnpm run types:generate    # Generate TS types from DB

# Code quality
pnpm run lint              # ESLint
pnpm run type-check        # TypeScript check
pnpm run format            # Prettier formatting

# Build
pnpm run build             # Production build
pnpm run start             # Start production server
```

### Environment Configuration

#### Required Environment Variables
```bash
# Frontend (.env.local)
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/auth/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/auth/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/
NEXT_PUBLIC_SUPABASE_URL=http://localhost:54321
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# Backend (.env.local)
CLERK_SECRET_KEY=sk_test_...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
DATABASE_URL=postgresql://postgres:postgres@localhost:54322/postgres

# Shared
NODE_ENV=development
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

## Deployment Architecture

### Deployment Strategy
**Frontend Deployment:**
- **Platform:** Vercel Edge Network with automatic deployments
- **Build Command:** `pnpm run build`
- **Output Directory:** `.next` (handled by Vercel automatically)
- **CDN/Edge:** Global CDN with edge caching and ISR support

**Backend Deployment:**
- **Platform:** Supabase for database, Vercel for API routes
- **Build Command:** Built with frontend (API routes)
- **Deployment Method:** Git-based automatic deployments

### Environments

| Environment | Frontend URL | Backend URL | Purpose |
|-------------|-------------|-------------|---------|
| Development | http://localhost:3000 | http://localhost:3000/api | Local development |
| Staging | https://staging.ideastash.com | https://staging.ideastash.com/api | Pre-production testing |
| Production | https://ideastash.com | https://ideastash.com/api | Live environment |

## Security and Performance

### Security Requirements
**Frontend Security:**
- CSP Headers: `default-src 'self'; script-src 'self' 'unsafe-inline' https://clerk.dev; style-src 'self' 'unsafe-inline'`
- XSS Prevention: Input sanitization, Content Security Policy, HttpOnly cookies
- Secure Storage: JWT tokens in httpOnly cookies, sensitive data encrypted

**Backend Security:**
- Input Validation: Zod schemas for all API inputs, SQL injection prevention
- Rate Limiting: 100 requests per 15 minutes per IP, slower responses after 50 requests
- CORS Policy: `https://ideastash.com, https://*.ideastash.com` origins only

**Authentication Security:**
- Token Storage: HttpOnly cookies with secure flag, SameSite=strict
- Session Management: Clerk-managed with automatic token refresh
- Password Policy: Minimum 8 characters, complexity requirements via Clerk

### Performance Optimization
**Frontend Performance:**
- Bundle Size Target: <250KB initial bundle, <100KB route chunks
- Loading Strategy: React.lazy() for route-based code splitting, Suspense boundaries
- Caching Strategy: SWR for API calls, Service Worker for offline assets, CDN for static content

**Backend Performance:**
- Response Time Target: <200ms for API endpoints, <100ms for cached responses
- Database Optimization: Proper indexing, connection pooling, query optimization
- Caching Strategy: Redis for session data, PostgreSQL query caching, API response caching

## Testing Strategy

### Critical Fullstack Rules
- **Type Sharing:** Always define shared types in `src/types/` and import consistently across frontend and backend
- **API Error Handling:** All API routes must use the standard error format with code, message, timestamp, and requestId
- **Authentication Check:** Every protected API route must validate user authentication via Clerk middleware
- **Database Transactions:** Use Supabase transactions for multi-step operations to ensure data consistency
- **Environment Variables:** Access only through config objects in `src/lib/config.ts`, never `process.env` directly
- **State Mutations:** Never mutate Zustand state directly - use proper actions and immer patterns
- **SQL Injection Prevention:** Always use parameterized queries via Supabase client, never string concatenation
- **Component Props:** Define explicit interfaces for all component props, avoid `any` or generic object types
- **Error Boundaries:** Wrap all major UI sections with error boundaries for graceful failure handling

### Naming Conventions

| Element | Frontend | Backend | Example |
|---------|----------|---------|---------|
| Components | PascalCase | - | `IdeaCard.tsx`, `QuickCapture.tsx` |
| Hooks | camelCase with 'use' | - | `useIdeas.ts`, `useOfflineSync.ts` |
| API Routes | - | kebab-case paths | `/api/ideas/search`, `/api/sync` |
| Database Tables | - | snake_case | `users`, `ideas`, `user_sessions` |
| Store Actions | camelCase | - | `addDraft`, `commitIdea` |
| Service Methods | camelCase | camelCase | `createIdea`, `searchIdeas` |
| Environment Variables | SCREAMING_SNAKE_CASE | SCREAMING_SNAKE_CASE | `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` |
| Type Interfaces | PascalCase | PascalCase | `Idea`, `User`, `ApiError` |

## Monitoring and Observability

### Monitoring Stack
- **Frontend Monitoring:** Vercel Analytics for Core Web Vitals and page performance
- **Backend Monitoring:** Vercel Functions Insights for API performance and errors
- **Error Tracking:** Sentry for comprehensive error reporting and performance monitoring
- **Performance Monitoring:** Real User Monitoring (RUM) via Sentry, synthetic monitoring via Vercel

### Key Metrics
**Frontend Metrics:**
- Core Web Vitals (LCP, FID, CLS, TTFB)
- JavaScript errors and unhandled promises
- API response times from client perspective
- User interactions and conversion funnel
- Bundle size and loading performance
- Service Worker cache hit rates

**Backend Metrics:**
- Request rate and response time percentiles
- Error rate by endpoint and error type
- Database query performance and slow queries
- Authentication success/failure rates
- Search query performance and result relevance
- Background sync success rates

**Business Metrics:**
- Idea capture rate (drafts created per session)
- Commit rate (percentage of drafts committed)
- User retention (daily, weekly, monthly)
- Search usage and success rates
- Offline usage patterns and sync performance