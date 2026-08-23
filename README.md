# CreatorOS AI Documentation

CreatorOS AI is an AI-powered Social Growth Intelligence Platform for creators and small businesses. Version 1.0 focuses on Facebook and Instagram content management, AI-assisted copy, scheduling, analytics, and growth recommendations.

## Project repositories

- Frontend: https://github.com/akindaG/creatoros-web
- Backend: https://github.com/akindaG/creatoros-api
- Documentation: https://github.com/akindaG/creatoros-docs

## Version 1.0 scope

CreatorOS AI V1 includes:

- User registration, login, logout, password reset, profile and password management
- Facebook and Instagram account connections
- Image and video uploads
- Draft creation, editing and deletion
- Content scheduling and calendar management
- AI caption generation
- AI hashtag generation
- AI content analysis
- Analytics dashboard and CSV export
- Best posting-time recommendations
- Growth recommendations
- Simulated or live Meta publishing

The V1 scope intentionally excludes TikTok, LinkedIn, YouTube, competitor analysis, trend prediction, mobile apps, agency management, custom AI training and autonomous agents.

## Technology stack

### Frontend

- Next.js
- TypeScript
- React
- Tailwind CSS

### Backend

- FastAPI
- Python
- SQLAlchemy
- Alembic

### Data and storage

- PostgreSQL
- Supabase
- Supabase Storage

### AI

- Ollama
- Qwen 3

### Deployment

- Vercel for the frontend
- Railway for the backend
- Supabase for PostgreSQL and storage

## High-level architecture

```text
User
  |
  v
Next.js frontend
  |
  | REST API + JWT
  v
FastAPI backend
  |
  +--> PostgreSQL / Supabase
  +--> Supabase Storage
  +--> Ollama / Qwen 3
  +--> Meta Graph API for Facebook and Instagram
```

## Current project state

The V1 codebase is integrated and merged into the primary branches:

- `creatoros-api`: `main`
- `creatoros-web`: `master`
- `creatoros-docs`: `main`

Automated validation covers backend compilation, database migrations, pytest, frontend linting, TypeScript validation and the production Next.js build.

## Documentation index

- [Architecture](ARCHITECTURE.md)
- [Local Setup](LOCAL_SETUP.md)
- [Testing](TESTING.md)
- [Deployment](DEPLOYMENT.md)
- [Demo Guide](DEMO_GUIDE.md)
- [Final Project Status](FINAL_STATUS.md)

## Primary demo flow

1. Register and log in.
2. Connect Instagram or Facebook.
3. Upload media.
4. Save a content draft.
5. Generate or improve a caption with AI.
6. Analyze content quality.
7. Schedule the post.
8. View it in the calendar.
9. Review analytics and growth recommendations.
10. Publish in simulation mode or live Meta mode when approved credentials are configured.
