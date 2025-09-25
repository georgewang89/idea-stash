# Monitoring and Observability

## Monitoring Stack
- **Frontend Monitoring:** Vercel Analytics for Core Web Vitals and page performance
- **Backend Monitoring:** Vercel Functions Insights for API performance and errors
- **Error Tracking:** Sentry for comprehensive error reporting and performance monitoring
- **Performance Monitoring:** Real User Monitoring (RUM) via Sentry, synthetic monitoring via Vercel

## Key Metrics
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