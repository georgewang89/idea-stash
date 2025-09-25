# Development Workflow

## Local Development Setup

### Prerequisites
```bash
# Install Node.js (v18+)
# Install pnpm (faster than npm)
npm install -g pnpm

# Install Supabase CLI
npm install -g supabase

# Install Vercel CLI (optional, for deployment)
npm install -g vercel
```

### Initial Setup
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

### Development Commands
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

## Environment Configuration

### Required Environment Variables
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
