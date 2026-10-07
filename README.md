# CreatorOS AI Documentation

CreatorOS AI is an AI-powered Social Growth Intelligence Platform for creators and small businesses. Version 1.0 focuses on Facebook and Instagram content management, AI-assisted copy, scheduling, analytics, publishing, and growth recommendations.

This repository documents the implementation that exists in the current CreatorOS codebase. Earlier proposal, SRS, database, and design submissions remain useful historical artifacts, but the live code, SQLAlchemy models, and Alembic migrations are the source of truth for the final implementation.

## Project repositories

- Frontend: https://github.com/akindaG/creatoros-web
- Backend: https://github.com/akindaG/creatoros-api
- Documentation: https://github.com/akindaG/creatoros-docs
- Experimental mobile extension: https://github.com/akindaG/creatoros-mobile

## Version 1.0 core scope

CreatorOS AI V1 includes:

- User registration, login, logout, password reset, profile management, and password changes
- Facebook Page connection through Meta OAuth
- Instagram Business or Creator account connection through Instagram Login OAuth
- Encrypted social access credentials at rest
- Image and MP4 upload
- Draft creation, editing, filtering, and deletion
- Post Now publishing
- One-click Facebook Page and Instagram multi-platform publishing
- Content scheduling, rescheduling, cancellation, and calendar management
- One-click multi-platform scheduling with grouped calendar entries
- Assisted Facebook personal-profile sharing
- AI caption generation
- AI hashtag generation
- AI content analysis
- Analytics dashboards and CSV export
- Best posting-time recommendations
- Growth recommendations
- Simulated or live Meta publishing
- Exact-time scheduled publishing inside the FastAPI process, with worker and protected cron alternatives

The approved V1 product scope remains centered on the web application. TikTok, LinkedIn, YouTube, competitor intelligence, trend prediction, agency management, autonomous agents, and enterprise multi-tenancy are future work.

The separate Expo mobile application is an experimental post-MVP extension and should not be presented as part of the originally approved V1 scope.

## Actual technology stack

### Frontend

- Next.js 16
- React 19
- TypeScript
- Tailwind CSS 4

The current web repository does not depend on Zustand, Recharts, or shadcn/ui. Those technologies appeared in earlier planning but are not part of the implemented web dependency set.

### Backend

- FastAPI
- Python
- SQLAlchemy
- Alembic
- PostgreSQL

### Data and storage

- Supabase PostgreSQL
- Supabase Storage
- Local media fallback for development

### AI layer

- Google Gemini for hosted caption generation, hashtag generation, and content analysis
- Deterministic fallback output when AI_FALLBACK_ENABLED=true and the AI service is temporarily unavailable

### Social integration

- Meta OAuth for Facebook Pages
- Instagram Login OAuth for Instagram Business or Creator accounts
- Meta Graph API and Instagram Graph API for live publishing
- Assisted manual sharing for Facebook personal profiles

### Deployment

- Vercel for the Next.js frontend
- Railway for the FastAPI backend
- Supabase for PostgreSQL and media storage

## High-level architecture

~~~text
Browser
  |
  v
Next.js 16 + React 19 + TypeScript
Vercel
  |
  | HTTPS REST + JWT
  v
FastAPI
Railway
  |
  +--> PostgreSQL / Supabase
  +--> Supabase Storage
  +--> Google Gemini
  +--> Meta Graph API
  +--> Instagram Graph API
~~~

## Documentation index

- [Architecture](ARCHITECTURE.md)
- [Data Model](DATA_MODEL.md)
- [Local Setup](LOCAL_SETUP.md)
- [Testing](TESTING.md)
- [Deployment](DEPLOYMENT.md)
- [Demo Guide](DEMO_GUIDE.md)
- [Report Alignment](REPORT_ALIGNMENT.md)
- [Final Project Status](FINAL_STATUS.md)

## Primary demonstration flow

1. Register and log in.
2. Connect Instagram or a Facebook Page.
3. Upload media.
4. Save a content draft.
5. Generate or improve a caption with AI.
6. Analyze content quality.
7. Publish immediately to one or both supported automatic channels, or schedule the post.
8. Confirm scheduled entries in Calendar.
9. Review analytics and growth recommendations.
10. Demonstrate Facebook personal-profile assisted sharing separately if required.

## Documentation accuracy rule

When final report content conflicts with an older proposal, SRS, diagram, SQL export, or mockup, describe the difference explicitly. Do not claim a planned library or feature was implemented unless it exists in the current codebase.
