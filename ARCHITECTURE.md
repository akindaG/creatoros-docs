# CreatorOS AI Architecture

## Overview

CreatorOS AI uses a separated web frontend and FastAPI backend. The browser communicates with the backend through HTTPS REST requests and JWT authentication. PostgreSQL stores application data. Supabase Storage stores uploaded media. The AI layer selects either Google Gemini or Ollama with Qwen 3. Meta APIs provide Facebook Page and Instagram integration.

~~~text
User Browser
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
    +----------------------+----------------------+----------------------+
    |                      |                      |                      |
    v                      v                      v                      v
PostgreSQL            Supabase Storage      AI Provider Layer       Social APIs
Supabase                                    |                      |
                                            +--> Gemini            +--> Meta Graph API
                                            +--> Ollama/Qwen 3     +--> Instagram Graph API
                                            +--> Fallback
~~~

## Frontend responsibilities

The frontend repository is creatoros-web.

Main responsibilities:

- Authentication screens and session-aware navigation
- Dashboard presentation
- Content Studio draft and media workflows
- AI assistant and content analyzer workflows
- Single-platform and multi-platform Post Now controls
- Content calendar and scheduling controls
- Analytics presentation and CSV export entry point
- Growth recommendation presentation
- Facebook and Instagram connection management
- Facebook personal-profile assisted-share workflow
- Profile and settings workflows
- Public privacy, terms, and data-deletion pages required for Meta app compliance

The frontend reads the API base URL from NEXT_PUBLIC_API_URL.

## Backend responsibilities

The backend repository is creatoros-api.

Main responsibilities:

- JWT authentication and authorization
- User profile management
- Social OAuth account management
- Encryption of stored social credentials
- Post CRUD and filtering
- Media upload and image normalization
- Scheduling, grouped cross-platform scheduling, calendar, rescheduling, and cancellation
- Exact-time scheduled publishing
- Immediate and multi-platform publishing
- Facebook personal-profile assisted-share state
- AI caption, hashtag, and analysis endpoints
- AI provider selection and fallback behavior
- Analytics aggregation and CSV export
- Best posting-time calculation
- Growth recommendation generation
- Publishing readiness diagnostics

## Authentication flow

1. A user registers or logs in.
2. FastAPI returns a JWT access token.
3. The web MVP stores the token locally.
4. Authenticated API requests send Authorization: Bearer TOKEN.
5. Protected routes resolve the current user from the token.
6. Invalid or expired sessions are cleared by the frontend and redirected to login.

## Social account connection flow

### Facebook Page

1. The user starts Facebook OAuth from CreatorOS.
2. The backend creates a signed OAuth state bound to the authenticated CreatorOS user.
3. Meta authenticates the user and returns to the configured backend callback.
4. The backend exchanges the code for credentials, identifies an available Facebook Page, and stores the Page identifier.
5. Access credentials are encrypted before persistence.

### Instagram Business or Creator

1. The user starts Instagram Login OAuth.
2. The backend signs the OAuth state.
3. Instagram returns to the configured callback.
4. The backend exchanges the authorization code for account credentials.
5. The Instagram account identifier, username, expiry metadata, and encrypted access token are persisted.

The database currently enforces one connected account per automatic platform per CreatorOS user.

## Facebook personal-profile behavior

Facebook personal profiles are not treated as automatic API publishing targets.

CreatorOS instead:

1. Saves the content as a facebook_profile post.
2. Copies or exposes the caption to the user.
3. Opens Facebook so the user can complete the share.
4. Allows CreatorOS to mark the item as shared afterward.
5. Converts scheduled personal-profile items to ready_to_share when their scheduled time arrives.

The final report must describe this as assisted sharing, not automatic personal-profile publishing.

## AI architecture

~~~text
Frontend AI Assistant
        |
        v
FastAPI /api/v1/ai/*
        |
        v
AI Provider Abstraction
     /          \
    v            v
Gemini       Ollama/Qwen 3
     \          /
      v        v
 Structured JSON response
        |
        v
Optional deterministic fallback
~~~

Supported AI features:

- Caption generation
- Hashtag generation
- Content quality analysis

The selected provider is controlled by AI_PROVIDER.

When AI_FALLBACK_ENABLED=true and the selected provider is unavailable, deterministic fallback output keeps the workflow operational.

## Scheduling architecture

CreatorOS has an in-process exact-time scheduler in app/services/exact_scheduler.py.

Flow:

1. A user saves a draft.
2. A future date and time is selected.
3. FastAPI creates a scheduled_posts record.
4. For simultaneous Facebook and Instagram scheduling, CreatorOS fans out the content into platform-specific post records and assigns the schedules a common schedule_group_id.
5. The scheduler is notified whenever a schedule is created, changed, or cancelled.
6. It sleeps until the next exact database timestamp rather than rounding to a fixed polling interval.
7. At the due time it processes scheduled posts.
8. Automatic platforms are published through the relevant API. Facebook-profile items become ready_to_share.

The backend also retains a worker command and protected process-due endpoint as operational alternatives.

## Publishing modes

### Simulation mode

SOCIAL_PUBLISH_MODE=simulate

Useful for development and low-risk demonstrations. The backend simulates publishing and does not claim that content was sent to Meta.

### Live mode

SOCIAL_PUBLISH_MODE=live

Requires valid OAuth credentials and supported platform account identifiers.

Automatic publishing targets:

- Facebook Page
- Instagram Business or Creator account

Assisted target:

- Facebook personal profile

## Multi-platform publishing

For Facebook Page and Instagram, CreatorOS can fan out one draft into platform-specific copies and publish both from one user action.

If one platform succeeds and the other fails:

- the successful copy remains published
- the failed copy remains retryable
- the API reports a partial result rather than hiding the failure

## Data architecture

The authoritative schema is represented by SQLAlchemy models and Alembic migrations.

Primary current entities:

- users
- social_accounts
- posts
- scheduled_posts
- analytics
- recommendations
- ai_generation_logs
- notifications

See DATA_MODEL.md for the implementation-level schema summary.

## Analytics and recommendation architecture

The analytics layer stores performance snapshots and derives:

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

Best posting-time recommendations group historical engagement by day and hour. Growth recommendations combine engagement performance, posting consistency, and timing evidence.

## Deployment architecture

Production web deployment:

~~~text
Vercel frontend
      |
      v
Railway FastAPI
      |
      +--> Supabase PostgreSQL
      +--> Supabase Storage
      +--> Gemini, or configured Ollama endpoint
      +--> Meta / Instagram APIs
~~~

Secrets are stored in deployment environment variables and are not part of the Git repository.
