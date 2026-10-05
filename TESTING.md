# Testing and Validation

## Testing strategy

CreatorOS combines backend automated tests, migration checks, frontend static validation, production build validation, and manual end-to-end smoke testing.

Do not report a pass percentage in the final report until the current branch has been executed and the result has been recorded. The repository currently contains 30 backend test functions, but test count and pass count are different facts.

## Automated backend checks

The backend CI workflow is designed to validate:

~~~bash
pip install -r requirements.txt
python -m compileall app
alembic upgrade head
python -m pytest -q --cov=app --cov-report=term-missing
~~~

The CI setup provisions PostgreSQL 16 and verifies the Alembic chain against a clean database.

## Current backend test inventory

The current test suite contains 30 test functions across:

- tests/test_ai_provider.py
- tests/test_backend.py
- tests/test_core_routes.py
- tests/test_social_and_publishing.py
- tests/test_storage_social_images.py

Coverage areas include:

- API and database health
- Registration and login
- Profile access and profile updates
- Password reset and password changes
- Social credential encryption
- Protected internal processing endpoint
- Post CRUD
- Filtering
- Media upload type validation
- Social image normalization
- Scheduling
- Rescheduling
- Cancellation
- Calendar retrieval
- Analytics history
- Analytics CSV export
- Gemini provider selection
- AI fallback behavior
- Caption generation and content analysis
- Facebook OAuth
- Instagram OAuth
- Facebook Page text publishing
- Facebook Page video publishing
- Instagram image publishing
- Publishing mode normalization
- Publishing-readiness diagnostics
- Facebook personal-profile assisted sharing
- Personal-profile ready-to-share scheduling
- Multi-platform Post Now
- Multi-platform scheduling
- Due multi-platform publishing

## Automated frontend checks

The frontend CI workflow validates:

~~~bash
npm ci
npm run lint
npm run build
~~~

This provides:

- dependency installation validation
- ESLint validation
- TypeScript compilation through the Next.js production build
- route and production bundle compilation

The current web repository does not contain a separate frontend unit-test framework. Do not describe lint and build checks as frontend unit tests.

## Recommended final-report test table

Record actual results after execution.

| Test area | Evidence to record |
| --- | --- |
| Backend compile | Command result |
| Alembic migration | Clean upgrade result |
| Backend pytest | Passed / failed count and coverage |
| Frontend lint | Pass / fail |
| Frontend production build | Pass / fail |
| Production health | /health response |
| Production DB health | /health/db response |
| OAuth | Facebook and Instagram connection result |
| AI | Provider source and generated response |
| Immediate publishing | Simulation or live result |
| Scheduling | Calendar and due-processing result |
| Multi-platform scheduling | Same-time grouped entries |
| Analytics | Real dashboard output |
| Recommendations | Best-time and growth output |

## Manual end-to-end smoke test

Use the deployed system or local applications and verify:

1. Open the frontend.
2. Register a new user.
3. Log in.
4. Verify protected navigation.
5. Connect Facebook Page through OAuth.
6. Connect Instagram through OAuth.
7. Upload a valid image.
8. Verify an invalid image upload is rejected clearly.
9. Save a draft.
10. Generate a caption with the selected AI provider.
11. Generate hashtags.
12. Analyze the content.
13. Publish one automatic target.
14. Publish to Facebook Page and Instagram together.
15. Schedule one target.
16. Schedule both automatic targets for the same timestamp.
17. Confirm Calendar grouping.
18. Reschedule a grouped item.
19. Cancel a grouped item.
20. Verify Facebook profile assisted sharing.
21. Verify analytics.
22. Export CSV.
23. Verify best posting-time recommendation.
24. Verify growth recommendations.
25. Log out.
26. Confirm protected pages require authentication.

## AI test configuration

CI should avoid dependence on a real external AI API where possible.

The provider-selection tests mock Gemini behavior. Fallback tests verify that the application remains usable when the selected provider is unavailable and AI_FALLBACK_ENABLED=true.

For manual hosted AI testing:

~~~env
AI_PROVIDER=gemini
GEMINI_API_KEY=<configured secret>
AI_FALLBACK_ENABLED=true
~~~

For local Qwen testing:

~~~env
AI_PROVIDER=ollama
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3
AI_FALLBACK_ENABLED=true
~~~

## Publishing test configuration

Simulation:

~~~env
SOCIAL_PUBLISH_MODE=simulate
~~~

Live Meta testing:

~~~env
SOCIAL_PUBLISH_MODE=live
~~~

When using simulation mode, the final report must say that publishing was simulated. Do not use simulation output as evidence that a real Facebook or Instagram post was created.

## Definition of V1 completion

CreatorOS AI V1 should be treated as verified only when:

- Database migrations succeed from a clean schema.
- The current backend tests pass.
- Frontend lint passes.
- Frontend production build passes.
- Frontend and backend communicate through the deployed API URL.
- OAuth callbacks work in the deployment environment.
- The intended publishing mode is clearly identified.
- Scheduling works end to end.
- Analytics and recommendations load.
- No secrets are committed to GitHub.
