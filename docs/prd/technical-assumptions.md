# Technical Assumptions

## Repository Structure: Monorepo
Single repository with separate client and server directories for simplified development workflow and easier dependency management during MVP phase.

## Service Architecture
**Monolithic REST API with PWA frontend** - Simple Express.js backend serving a React frontend. This provides the right balance of simplicity for MVP development while supporting the offline functionality and performance requirements.

## Testing Requirements
**Unit + Integration testing** focused on core business logic (idea persistence, search functionality) and critical user flows (capture, commit, search). Manual testing convenience methods for rapid iteration during development.

## Additional Technical Assumptions and Requests
- **Database**: PostgreSQL with full-text search capabilities for idea content
- **Authentication**: Auth0 or Clerk for secure, hassle-free user management
- **Hosting**: Vercel for frontend, Railway/Supabase for backend to maximize free tier usage
- **State Management**: React Context for simplicity, avoiding complex state libraries for MVP
- **Offline Strategy**: Service Worker for caching committed ideas, local storage for draft persistence
- **Search Implementation**: PostgreSQL full-text search with potential upgrade to dedicated search service post-MVP
- **Data Migration Strategy**: Database migration scripts from day one to support schema evolution
