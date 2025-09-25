# Project Brief: IdeaStash

## Executive Summary

IdeaStash is a focused idea-capturing app that solves the problem of losing valuable startup ideas through accidental deletion or poor organization. Unlike generic note-taking apps, IdeaStash features a "commit" system that permanently preserves ideas in a timestamped pool, making it impossible to accidentally lose creative insights while maintaining intentional curation control.

**Target Market:** Entrepreneurs, creators, and innovators who generate frequent ideas but struggle with current tools like Tumblr or basic note apps that lack permanence and proper organization.

**Key Value Proposition:** Transform fleeting thoughts into a permanent, searchable, timestamped idea repository with git-like commit functionality.

## Problem Statement

**Current State:** Creative individuals, especially entrepreneurs, constantly generate startup and project ideas but lose them due to:
- Accidental deletion in note apps (hotkey mishaps)
- Poor organization in tools like Tumblr that weren't designed for idea management
- Lack of permanence - ideas disappear into scrolling feeds
- No intentional curation process between capture and commitment

**Impact:** Lost innovation potential, repeated brainstorming of forgotten concepts, inability to track idea evolution over time, and frustration with existing tools that treat all notes equally.

**Why Existing Solutions Fall Short:**
- **Notes apps:** Too easy to accidentally delete, no permanence concept
- **Tumblr:** Great for capture but poor for intentional idea management
- **General productivity tools:** Over-engineered for simple idea capture needs

**Urgency:** In the fast-paced startup ecosystem, losing a single great idea could mean missing significant opportunities.

## Proposed Solution

**Core Concept:** A lightweight app that treats ideas like code commits - easy to capture, intentional to preserve, impossible to accidentally lose.

**Key Differentiators:**
1. **Two-stage process:** Draft → Commit (prevents accidental loss while allowing curation)
2. **Timestamped idea pool:** Visual blog-like interface showing idea evolution over time
3. **Intentional deletion:** More than a hotkey but not overly complex
4. **Purpose-built for ideas:** Not trying to be everything to everyone

**Why This Will Succeed:** Combines the quick capture users love about simple tools with the permanence and organization they desperately need, without the complexity of full project management systems.

## Target Users

### Primary User Segment: Solo Entrepreneurs & Creators
- **Demographics:** Age 25-45, tech-savvy individuals in creative or business roles
- **Current Behavior:** Using multiple tools (Notes, Tumblr, random notebooks) inconsistently
- **Pain Points:** Lost ideas, poor organization, no sense of idea progression over time
- **Goals:** Capture ideas quickly, never lose them, review and build upon past concepts
- **Workflow:** Idea sparks → quick capture → occasional review → selective development

### Secondary User Segment: Innovation Teams
- **Demographics:** Small teams (2-10 people) working on creative projects
- **Current Behavior:** Scattered ideation across Slack, shared docs, whiteboards
- **Pain Points:** Ideas lost in chat history, no central repository, difficulty tracking who contributed what
- **Goals:** Centralized idea capture, contribution tracking, collaborative review

## Goals & Success Metrics

### Business Objectives
- Launch MVP within 3 months with core commit functionality
- Achieve 1,000+ daily active users within 6 months
- 70% user retention rate after first week of use
- Convert 10% of users to paid tier within first year

### User Success Metrics
- Average 5+ ideas committed per user per month
- 80% of committed ideas remain in system (low deletion rate)
- Users return to review old ideas at least once per month
- 90% of new ideas are committed (not left in draft state)

### Key Performance Indicators (KPIs)
- **Idea Velocity:** Number of ideas captured per user per session
- **Commit Rate:** Percentage of captured ideas that get committed
- **Retention:** Weekly and monthly active user rates
- **Engagement:** Time spent reviewing old ideas vs. capturing new ones

## MVP Scope

### Core Features (Must Have)
- **Quick Text Entry:** Fast, frictionless idea capture with minimal interface
- **Draft → Commit Flow:** Two-stage process with clear visual distinction between states
- **Idea Pool View:** Timestamped, chronological display of committed ideas
- **Intentional Delete:** Requires more than accidental hotkey (confirmation step)
- **Search Functionality:** Basic text search across committed ideas
- **Timestamps:** Clear capture and commit times for each idea

### Out of Scope for MVP
- Multi-user collaboration features
- Rich text formatting or media attachments
- Categories, tags, or complex organization
- Export functionality
- Mobile apps (web-first approach)
- Integration with external tools
- Advanced analytics or idea scoring

### MVP Success Criteria
Users can capture, commit, and retrieve startup ideas more reliably than their current workflow, with measurable reduction in lost ideas and increased long-term idea review behavior.

## Post-MVP Vision

### Phase 2 Features
- **Idea Connections:** Link related ideas together with simple visual connections
- **Mobile App:** Native iOS/Android apps with offline sync
- **Export Options:** Export to various formats (Markdown, PDF, etc.)
- **Basic Analytics:** Personal insights on idea patterns and themes

### Long-term Vision (1-2 Years)
Transform from personal idea repository to collaborative innovation platform, helping individuals and small teams turn ideas into actionable projects with lightweight project progression tracking.

### Expansion Opportunities
- **Team Collaboration:** Shared idea pools with contribution tracking
- **Idea Development:** Simple tools to evolve ideas toward viability
- **Integration Hub:** Connect with project management and development tools
- **AI Enhancement:** Idea clustering, trend identification, and suggestion features

## Technical Considerations

### Platform Requirements
- **Target Platforms:** Web-first (responsive), Progressive Web App (PWA)
- **Browser Support:** Modern browsers (Chrome, Firefox, Safari, Edge)
- **Performance Requirements:** <200ms response time, offline-capable for basic functionality

### Technology Preferences
- **Frontend:** React with TypeScript for component reusability and type safety
- **Backend:** Node.js with Express, or potentially serverless (Vercel functions)
- **Database:** PostgreSQL for structured data with full-text search capabilities
- **Hosting:** Vercel or Netlify for frontend, Railway/Supabase for backend

### Architecture Considerations
- **Repository Structure:** Monorepo with separate client/server directories
- **Service Architecture:** Simple REST API, consider GraphQL for future complexity
- **Integration Requirements:** Authentication (Auth0/Clerk), basic analytics (Plausible)
- **Security:** User data encryption, secure authentication, GDPR compliance ready

## Constraints & Assumptions

### Constraints
- **Budget:** Bootstrap/minimal budget - prioritize free tiers and open source solutions
- **Timeline:** 3-month MVP target with single developer
- **Resources:** Solo development initially, potential for contractor support
- **Technical:** Must work reliably across devices, minimal maintenance overhead

### Key Assumptions
- Users will adopt two-stage capture/commit workflow
- Timestamps and chronological view provide sufficient organization for MVP
- Web-first approach acceptable for target users
- Simple text capture sufficient initially (no rich media needed)
- Users prefer lightweight tool over feature-rich alternatives

## Risks & Open Questions

### Key Risks
- **User Adoption:** Will users change their existing idea-capture habits?
- **Feature Creep:** Risk of over-building beyond core value proposition
- **Differentiation:** Can we maintain simplicity while providing enough value?
- **Technical:** Database performance with large numbers of ideas per user

### Open Questions
- What's the optimal flow for moving ideas from draft to commit?
- How much friction is ideal for the deletion process?
- Should we support any categorization in MVP or stay purely chronological?
- What's the best onboarding flow to teach the commit concept?

### Areas Needing Further Research
- Competitive analysis of existing idea management tools
- User interviews with target entrepreneurs about current workflows
- Technical feasibility of offline-first approach
- Pricing strategy research for future monetization

## Next Steps

### Immediate Actions
1. Create detailed wireframes for core user flows (capture, commit, browse)
2. Set up development environment and basic project structure
3. Design database schema for users, ideas, and commits
4. Build basic authentication and idea CRUD operations
5. Implement core UI components for idea capture and pool view

### PM Handoff
This Project Brief provides the full context for IdeaStash. Please start in 'PRD Generation Mode', review the brief thoroughly to work with the user to create the PRD section by section as the template indicates, asking for any necessary clarification or suggesting improvements.