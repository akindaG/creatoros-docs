# Deployment Guide

CreatorOS AI V1 uses:

- Supabase for PostgreSQL and media storage
- Railway for the FastAPI backend
- Vercel for the Next.js frontend

## 1. Supabase

Create or use the CreatorOS Supabase project and collect:

- PostgreSQL connection string
- Project URL
- Service role key

Create a storage bucket named:

```text
creatoros-media
```

Do not commit Supabase secrets to GitHub.

## 2. Railway backend

Deploy `akindaG/creatoros-api` from the `main` branch.

The repository includes `railway.json`. The start command runs database migrations before FastAPI starts.

Recommended Railway environment variables:

```env
APP_ENV=production
DEBUG=false
DATABASE_URL=<Supabase PostgreSQL connection string>
JWT_SECRET_KEY=<strong random secret>
FRONTEND_ORIGINS=<Vercel frontend URL>
SUPABASE_URL=<Supabase project URL>
SUPABASE_SERVICE_ROLE_KEY=<Supabase service role key>
SUPABASE_STORAGE_BUCKET=creatoros-media
SOCIAL_TOKEN_ENCRYPTION_KEY=<strong random secret>
CRON_SECRET=<strong random secret>
SOCIAL_PUBLISH_MODE=simulate
AI_FALLBACK_ENABLED=true
```

If using a remotely reachable Ollama service, configure:

```env
OLLAMA_BASE_URL=<Ollama service URL>
OLLAMA_MODEL=qwen3
```

For the student MVP, AI fallback mode is recommended when the hosted environment cannot run Ollama reliably.

Verify after deployment:

```text
https://YOUR-RAILWAY-DOMAIN/health
https://YOUR-RAILWAY-DOMAIN/health/db
https://YOUR-RAILWAY-DOMAIN/docs
```

## 3. Scheduled publishing

The backend includes both a worker command and a protected cron endpoint.

Worker command:

```bash
python -m app.jobs.publish_due
```

Protected API endpoint:

```text
POST /api/v1/internal/process-due
X-Cron-Secret: <CRON_SECRET>
```

Configure a Railway scheduled service or another scheduler to process due posts periodically.

## 4. Vercel frontend

Deploy `akindaG/creatoros-web` from the `master` branch.

Set:

```env
NEXT_PUBLIC_API_URL=https://YOUR-RAILWAY-DOMAIN
```

After Vercel provides the production domain, update the Railway backend variable:

```env
FRONTEND_ORIGINS=https://YOUR-VERCEL-DOMAIN
```

Redeploy the backend if needed so the new CORS value is active.

## 5. Production verification

Verify:

1. Landing page loads.
2. Registration works.
3. Login works.
4. Authenticated dashboard loads.
5. Social account connect/disconnect works.
6. Media upload works.
7. Draft CRUD works.
8. AI endpoints return output.
9. Scheduling works.
10. Calendar loads scheduled posts.
11. Analytics loads.
12. Growth recommendations load.
13. Logout clears the session.

## 6. Meta publishing mode

Use for the project demonstration:

```env
SOCIAL_PUBLISH_MODE=simulate
```

Only switch to:

```env
SOCIAL_PUBLISH_MODE=live
```

when approved Meta credentials and correct Facebook or Instagram account identifiers are available.

## 7. Secret management

Never commit these values:

- Database passwords
- `JWT_SECRET_KEY`
- `SOCIAL_TOKEN_ENCRYPTION_KEY`
- `CRON_SECRET`
- Supabase service role key
- Meta access tokens

Store them only in Railway, Vercel, Supabase or local ignored environment files.
