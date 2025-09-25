# Testing Strategy

## Critical Fullstack Rules
- **Type Sharing:** Always define shared types in `src/types/` and import consistently across frontend and backend
- **API Error Handling:** All API routes must use the standard error format with code, message, timestamp, and requestId
- **Authentication Check:** Every protected API route must validate user authentication via Clerk middleware
- **Database Transactions:** Use Supabase transactions for multi-step operations to ensure data consistency
- **Environment Variables:** Access only through config objects in `src/lib/config.ts`, never `process.env` directly
- **State Mutations:** Never mutate Zustand state directly - use proper actions and immer patterns
- **SQL Injection Prevention:** Always use parameterized queries via Supabase client, never string concatenation
- **Component Props:** Define explicit interfaces for all component props, avoid `any` or generic object types
- **Error Boundaries:** Wrap all major UI sections with error boundaries for graceful failure handling

## Naming Conventions

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
