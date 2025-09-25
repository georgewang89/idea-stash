# Deployment Architecture

## Deployment Strategy
**Frontend Deployment:**
- **Platform:** Vercel Edge Network with automatic deployments
- **Build Command:** `pnpm run build`
- **Output Directory:** `.next` (handled by Vercel automatically)
- **CDN/Edge:** Global CDN with edge caching and ISR support

**Backend Deployment:**
- **Platform:** Supabase for database, Vercel for API routes
- **Build Command:** Built with frontend (API routes)
- **Deployment Method:** Git-based automatic deployments

## Environments

| Environment | Frontend URL | Backend URL | Purpose |
|-------------|-------------|-------------|---------|
| Development | http://localhost:3000 | http://localhost:3000/api | Local development |
| Staging | https://staging.ideastash.com | https://staging.ideastash.com/api | Pre-production testing |
| Production | https://ideastash.com | https://ideastash.com/api | Live environment |
