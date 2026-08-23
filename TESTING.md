# Testing and Validation

## Automated backend checks

The backend GitHub Actions workflow validates:

```bash
pip install -r requirements.txt
python -m compileall app
alembic upgrade head
python -m pytest -q --cov=app --cov-report=term-missing
```

The CI environment provisions PostgreSQL 16 and verifies the complete Alembic migration chain from a clean database.

Backend test coverage includes:

- API and database health
- Registration and login
- Profile access
- Password reset
- Social credential encryption
- Protected internal cron endpoint
- Instagram account connection
- Post creation
- Scheduling and calendar retrieval
- AI caption generation
- Content analysis
- Analytics snapshot creation
- Dashboard analytics
- Analytics overview
- Best posting-time recommendation
- Growth recommendations
- Publishing in simulation mode
- Post filtering
- Analytics CSV export

## Automated frontend checks

The frontend GitHub Actions workflow validates:

```bash
npm ci
npm run lint
npm run build
```

The production build also performs TypeScript validation and compiles the application routes.

## Manual smoke-test flow

After both applications are running locally, verify this sequence:

1. Open `http://localhost:3000`.
2. Register a new user.
3. Log in.
4. Open Social Accounts and connect a test Instagram or Facebook account.
5. Open Content Studio.
6. Upload an image or MP4.
7. Save a draft.
8. Open AI Assistant and generate a caption.
9. Analyze the generated content.
10. Use the caption in Content Studio.
11. Schedule the draft for a future time.
12. Confirm the post appears in Calendar.
13. Open Analytics and verify the dashboard loads without errors.
14. Open Growth Insights and verify recommendations load.
15. Test simulated publishing.
16. Log out and verify protected pages redirect to login.

## Important test configurations

For reliable demonstrations:

```env
SOCIAL_PUBLISH_MODE=simulate
AI_FALLBACK_ENABLED=true
```

For live AI testing, also run:

```bash
ollama pull qwen3
ollama serve
```

## Definition of V1 completion

CreatorOS AI V1 is considered code-complete when:

- Backend CI is green.
- Frontend CI is green.
- Database migrations complete successfully.
- Core demo workflow works end to end.
- Frontend and backend communicate through the configured API URL.
- V1 remains limited to Facebook and Instagram.
- No secrets are committed to GitHub.
