# CreatorOS AI Architecture

## Overview

CreatorOS AI uses a separated frontend and backend architecture. The browser communicates with a FastAPI REST API. PostgreSQL stores application data, Supabase Storage stores media, Ollama with Qwen 3 provides AI generation, and Meta APIs support Facebook and Instagram publishing.

```text
Browser
  |
  v
Next.js + TypeScript
  |
  | HTTPS REST + JWT
  v
FastAPI
  |
  +--> PostgreSQL / Supabase
  +--> Supabase Storage
  +--> Ollama / Qwen 3
  +--> Meta Graph API
```

## Frontend responsibilities

The frontend repository is `creatoros-web`.

Main responsibilities:

- Authentication screens and session-aware navigation
- Dashboard and analytics presentation
- Content Studio draft and media workflows
- AI assistant workflows
- Content calendar and scheduling controls
- Growth recommendation presentation
- Facebook and Instagram connection management
- Profile and settings workflows

The frontend reads the API base URL from `NEXT_PUBLIC_API_URL`.

## Backend responsibilities

The backend repository is `creatoros-api`.

Main responsibilities:

- JWT authentication and authorization
- User profile management
- Social account management
- Post CRUD and filtering
- Media upload handling
- Scheduling and calendar endpoints
- Scheduled publishing processing
- AI caption, hashtag and analysis endpoints
- Analytics aggregation and CSV export
- Best posting-time calculation
- Growth recommendation generation
- Meta publishing abstraction

## Core data model

Primary entities:

- `users`
- `social_accounts`
- `posts`
- `scheduled_posts`
- `analytics`
- `recommendations`
- `ai_generation_logs`

Key relationships:

- A user can own multiple posts.
- A user can connect Facebook and Instagram accounts.
- A post can have a scheduling record.
- A post can have multiple analytics snapshots.
- Recommendations belong to a user.

## Authentication flow

1. User registers or logs in.
2. FastAPI returns a JWT access token.
3. The frontend stores the token locally for the MVP.
4. Authenticated API requests send `Authorization: Bearer <token>`.
5. Protected backend routes resolve the current user from the token.
6. Invalid or expired sessions are cleared by the frontend and redirected to login.

## Social account security

Facebook and Instagram access and refresh tokens are encrypted before being persisted. The backend uses `SOCIAL_TOKEN_ENCRYPTION_KEY`, with backward-compatible derivation from `JWT_SECRET_KEY` when a dedicated key is not configured.

The V1 data model enforces one connected account per platform per user.

## AI flow

```text
Frontend AI Assistant
        |
        v
FastAPI AI route
        |
        v
Ollama service
        |
        v
Qwen 3
```

Supported AI features:

- Caption generation
- Hashtag generation
- Content quality analysis

When `AI_FALLBACK_ENABLED=true`, deterministic fallback output keeps the MVP usable if Ollama is temporarily unavailable.

## Scheduling flow

1. User saves a draft.
2. User selects a future date and time.
3. FastAPI creates or updates the scheduling record.
4. The calendar reads scheduled posts from the backend.
5. A worker or protected cron endpoint processes due posts.
6. Publishing uses simulation mode by default or live Meta mode when credentials are configured.

## Publishing modes

### Simulation mode

`SOCIAL_PUBLISH_MODE=simulate`

Recommended for demonstrations and testing. The backend returns simulated external post IDs without publishing to Meta.

### Live mode

`SOCIAL_PUBLISH_MODE=live`

Requires approved Meta credentials and valid Facebook or Instagram account identifiers.

## Analytics and recommendations

The analytics layer stores post performance snapshots and calculates:

- Followers
- Reach
- Likes
- Comments
- Shares
- Engagement rate
- Growth rate
- Post count
- Platform reach
- Performance series
- Top-performing posts

Best posting-time recommendations group historical engagement by day and hour. Growth recommendations combine engagement performance, posting consistency and timing evidence.
