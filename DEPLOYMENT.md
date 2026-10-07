# Deployment Guide

CreatorOS AI V1 uses:

- Supabase for PostgreSQL and media storage
- Railway for the FastAPI backend
- Vercel for the Next.js frontend
- Google Gemini for hosted AI generation and analysis
- Meta and Instagram APIs for live social integration

## 1. Supabase

Create or use the CreatorOS Supabase project and collect:

- PostgreSQL connection string
- Project URL
- Service role key

Create a storage bucket named creatoros-media.

Never commit Supabase secrets to GitHub.

## 2. Railway backend

Deploy akindaG/creatoros-api from the main branch.

The repository start command runs Alembic migrations before FastAPI starts.

Recommended Railway variables:

~~~env
APP_ENV=production
DEBUG=false

DATABASE_URL=<Supabase PostgreSQL connection string>

JWT_SECRET_KEY=<strong random secret>
FRONTEND_ORIGINS=<Vercel frontend URL>
FRONTEND_URL=<Vercel frontend URL>

AI_PROVIDER=gemini
GEMINI_API_KEY=<Gemini API key>
GEMINI_MODEL=gemini-3.5-flash-lite
AI_FALLBACK_ENABLED=true

SUPABASE_URL=<Supabase project URL>
SUPABASE_SERVICE_ROLE_KEY=<Supabase service role key>
SUPABASE_STORAGE_BUCKET=creatoros-media

SOCIAL_TOKEN_ENCRYPTION_KEY=<strong random secret>
SOCIAL_PUBLISH_MODE=simulate

META_GRAPH_BASE_URL=https://graph.facebook.com/v26.0
META_APP_ID=<Meta app ID>
META_APP_SECRET=<Meta app secret>
META_REDIRECT_URI=https://YOUR-RAILWAY-DOMAIN/api/v1/social-accounts/facebook/callback

INSTAGRAM_APP_ID=<Instagram app ID if separate>
INSTAGRAM_APP_SECRET=<Instagram app secret if separate>
INSTAGRAM_REDIRECT_URI=https://YOUR-RAILWAY-DOMAIN/api/v1/social-accounts/instagram/callback
INSTAGRAM_GRAPH_BASE_URL=https://graph.instagram.com/v26.0
INSTAGRAM_OAUTH_AUTHORIZE_URL=https://www.instagram.com/oauth/authorize
INSTAGRAM_OAUTH_TOKEN_URL=https://api.instagram.com/oauth/access_token
INSTAGRAM_SCOPES=instagram_business_basic,instagram_business_content_publish,instagram_business_manage_insights

CRON_SECRET=<strong random secret>
PUBLISH_BATCH_SIZE=100
~~~

The production AI configuration should keep AI_PROVIDER=gemini and store GEMINI_API_KEY only in Railway environment variables.


## 3. Scheduled publishing

The deployed API starts an exact-time in-process scheduler automatically through the FastAPI lifespan.

Its health state is exposed through:

~~~text
GET /health
~~~

The backend also supports two operational alternatives.

Worker command:

~~~bash
python -m app.jobs.publish_due
~~~

Protected cron endpoint:

~~~text
POST /api/v1/internal/process-due
X-Cron-Secret: <CRON_SECRET>
~~~

A separate cron service is therefore optional rather than the only scheduling mechanism.

## 4. Meta OAuth callback setup

Configure the production callback URLs in the Meta application so they exactly match the Railway URLs configured in environment variables.

Facebook callback:

~~~text
https://YOUR-RAILWAY-DOMAIN/api/v1/social-accounts/facebook/callback
~~~

Instagram callback:

~~~text
https://YOUR-RAILWAY-DOMAIN/api/v1/social-accounts/instagram/callback
~~~

The frontend production URL must also match FRONTEND_URL and be included in FRONTEND_ORIGINS.

## 5. Vercel frontend

Deploy akindaG/creatoros-web from the master branch.

Set:

~~~env
NEXT_PUBLIC_API_URL=https://YOUR-RAILWAY-DOMAIN
~~~

After Vercel provides the production domain, update Railway:

~~~env
FRONTEND_ORIGINS=https://YOUR-VERCEL-DOMAIN
FRONTEND_URL=https://YOUR-VERCEL-DOMAIN
~~~

Redeploy the backend if environment changes require it.

## 6. Production verification

Verify all of the following with the deployed URLs:

1. Landing page loads.
2. Registration works.
3. Login works.
4. Protected dashboard loads.
5. /health reports healthy status and scheduler state.
6. /health/db reports a database connection.
7. Facebook OAuth starts and returns correctly.
8. Instagram OAuth starts and returns correctly.
9. Media upload works.
10. Draft CRUD works.
11. Google Gemini returns caption output.
12. Content analysis returns a score and suggestions.
13. Single-platform scheduling works.
14. Multi-platform scheduling works.
15. Calendar shows grouped simultaneous schedules consistently.
16. Post Now works in the intended publishing mode.
17. Multi-platform Post Now reports full, partial, or failed outcomes correctly.
18. Analytics loads.
19. Growth recommendations load.
20. Logout clears the session.

## 7. Publishing modes

Safe demonstration mode:

~~~env
SOCIAL_PUBLISH_MODE=simulate
~~~

Live publishing mode:

~~~env
SOCIAL_PUBLISH_MODE=live
~~~

Live automatic publishing applies to Facebook Pages and Instagram Business or Creator accounts.

Facebook personal-profile posting remains an assisted manual-share workflow. Do not claim that the backend automatically publishes to personal timelines.

## 8. Public Meta compliance pages

The web application includes public privacy, terms, and data-deletion pages. Verify their production URLs before Meta app review or demonstration.

## 9. Secret management

Never commit:

- Database passwords
- JWT_SECRET_KEY
- GEMINI_API_KEY
- SOCIAL_TOKEN_ENCRYPTION_KEY
- CRON_SECRET
- Supabase service role key
- Meta app secret
- Instagram app secret
- Meta or Instagram access tokens

Store secrets only in Railway, Vercel, Supabase, or ignored local environment files.
