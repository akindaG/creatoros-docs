# Local Development Setup

## Prerequisites

Install:

- Git
- Node.js and npm
- Python 3.12
- PostgreSQL or a Supabase PostgreSQL connection
- Ollama for local Qwen 3 generation

## 1. Clone the repositories

```bash
cd ~/Documents
git clone https://github.com/akindaG/creatoros-api.git
git clone https://github.com/akindaG/creatoros-web.git
```

## 2. Backend setup

```bash
cd ~/Documents/creatoros-api
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Set at minimum:

```env
DATABASE_URL=postgresql+psycopg://USER:PASSWORD@HOST:PORT/DATABASE
JWT_SECRET_KEY=replace-with-a-long-random-secret
FRONTEND_ORIGINS=http://localhost:3000
SOCIAL_PUBLISH_MODE=simulate
AI_FALLBACK_ENABLED=true
```

Optional production-like values:

```env
SUPABASE_URL=
SUPABASE_SERVICE_ROLE_KEY=
SUPABASE_STORAGE_BUCKET=creatoros-media
SOCIAL_TOKEN_ENCRYPTION_KEY=
CRON_SECRET=
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3
```

Run migrations:

```bash
alembic upgrade head
```

Start FastAPI:

```bash
uvicorn app.main:app --reload
```

Verify:

- API docs: `http://localhost:8000/docs`
- Health: `http://localhost:8000/health`
- Database health: `http://localhost:8000/health/db`

## 3. Ollama setup

```bash
ollama pull qwen3
ollama serve
```

The application can still demonstrate AI workflows with fallback enabled if Ollama is unavailable.

## 4. Frontend setup

Open a second terminal:

```bash
cd ~/Documents/creatoros-web
npm install
cp .env.example .env.local
```

Set:

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

Start Next.js:

```bash
npm run dev
```

Open:

`http://localhost:3000`

## 5. Local validation

Backend:

```bash
cd ~/Documents/creatoros-api
source .venv/bin/activate
python -m compileall app
alembic upgrade head
pytest -q --cov=app --cov-report=term-missing
```

Frontend:

```bash
cd ~/Documents/creatoros-web
npm run lint
npm run build
```

## 6. Recommended local demo configuration

Use:

```env
SOCIAL_PUBLISH_MODE=simulate
AI_FALLBACK_ENABLED=true
```

This keeps the project reliable for a classroom demonstration without requiring approved Meta publishing credentials or guaranteed Ollama availability.
