# Local Development Setup

## Prerequisites

Install:

- Git
- Node.js and npm
- Python 3.12
- PostgreSQL, or access to a Supabase PostgreSQL database
- Optional: Meta developer credentials for OAuth and live publishing
- Optional: Gemini API key for live AI generation

## 1. Clone the repositories

~~~bash
git clone https://github.com/akindaG/creatoros-api.git
git clone https://github.com/akindaG/creatoros-web.git
~~~

## 2. Backend setup

~~~bash
cd creatoros-api
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
~~~

Set at minimum:

~~~env
APP_ENV=development
DEBUG=true
DATABASE_URL=postgresql+psycopg://USER:PASSWORD@HOST:PORT/DATABASE
JWT_SECRET_KEY=replace-with-a-long-random-secret
FRONTEND_ORIGINS=http://localhost:3000
FRONTEND_URL=http://localhost:3000
AI_PROVIDER=gemini
AI_FALLBACK_ENABLED=true
SOCIAL_PUBLISH_MODE=simulate
~~~

### Google Gemini AI configuration

~~~env
AI_PROVIDER=gemini
GEMINI_API_KEY=
GEMINI_MODEL=gemini-3.5-flash-lite
AI_FALLBACK_ENABLED=true
~~~

For local development without a Gemini key, keep AI_FALLBACK_ENABLED=true so AI workflows can return deterministic fallback output.


### Supabase storage option

~~~env
SUPABASE_URL=
SUPABASE_SERVICE_ROLE_KEY=
SUPABASE_STORAGE_BUCKET=creatoros-media
MEDIA_LOCAL_DIR=media
MAX_UPLOAD_MB=25
~~~

If Supabase storage credentials are not supplied, the backend can use its local media fallback during development.

### Meta OAuth and live publishing

Facebook Page OAuth:

~~~env
META_GRAPH_BASE_URL=https://graph.facebook.com/v26.0
META_APP_ID=
META_APP_SECRET=
META_REDIRECT_URI=http://localhost:8000/api/v1/social-accounts/facebook/callback
~~~

Instagram Login OAuth:

~~~env
INSTAGRAM_APP_ID=
INSTAGRAM_APP_SECRET=
INSTAGRAM_REDIRECT_URI=http://localhost:8000/api/v1/social-accounts/instagram/callback
INSTAGRAM_GRAPH_BASE_URL=https://graph.instagram.com/v26.0
INSTAGRAM_OAUTH_AUTHORIZE_URL=https://www.instagram.com/oauth/authorize
INSTAGRAM_OAUTH_TOKEN_URL=https://api.instagram.com/oauth/access_token
INSTAGRAM_SCOPES=instagram_business_basic,instagram_business_content_publish,instagram_business_manage_insights
~~~

Token encryption and external cron:

~~~env
SOCIAL_TOKEN_ENCRYPTION_KEY=
CRON_SECRET=
PUBLISH_BATCH_SIZE=100
~~~

SOCIAL_TOKEN_ENCRYPTION_KEY is recommended. If it is omitted, the backend derives a compatible encryption key from JWT_SECRET_KEY.

## 3. Run database migrations

~~~bash
alembic upgrade head
~~~

Alembic migrations are the authoritative schema migration history. Do not rebuild the final report database section from an older standalone SQL export.

## 4. Start FastAPI

~~~bash
uvicorn app.main:app --reload
~~~

Verify:

- API docs: http://localhost:8000/docs
- Health: http://localhost:8000/health
- Database health: http://localhost:8000/health/db

The /health response also reports whether the exact-time in-process scheduler is running.

## 5. Frontend setup

Open a second terminal:

~~~bash
cd creatoros-web
npm ci
cp .env.example .env.local
~~~

Set:

~~~env
NEXT_PUBLIC_API_URL=http://localhost:8000
~~~

Start Next.js:

~~~bash
npm run dev
~~~

Open http://localhost:3000.

## 6. Local validation

Backend:

~~~bash
cd creatoros-api
source .venv/bin/activate
python -m compileall app
alembic upgrade head
pytest -q --cov=app --cov-report=term-missing
~~~

Frontend:

~~~bash
cd creatoros-web
npm run lint
npm run build
~~~

## 7. Publishing modes

Safe simulation:

~~~env
SOCIAL_PUBLISH_MODE=simulate
~~~

Live automatic publishing:

~~~env
SOCIAL_PUBLISH_MODE=live
~~~

Live mode requires valid connected Facebook Page or Instagram credentials. Facebook personal profiles remain an assisted manual-share workflow even when live mode is enabled.

## 8. Recommended demonstration configuration

For the least external-service risk:

~~~env
SOCIAL_PUBLISH_MODE=simulate
AI_FALLBACK_ENABLED=true
~~~

If the purpose of the demonstration is to prove real Meta publishing, use live mode only after verifying OAuth credentials and the publishing-readiness endpoint.
