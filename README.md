# SiteLens AI

## Repository structure

- `backend/`: FastAPI API + ingestion services
  - `app/`: API application package
  - `worker/`: background jobs for crawling + embeddings
- `frontend/`: Next.js application (chat UI, public share page, embed snippet)
- `shared/`: shared contracts, schemas, and utilities
- `infra/`: deployment and infrastructure notes

## Next steps

- Implement FastAPI service in `backend/app`.
- Stand up ingestion worker in `backend/worker`.
- Scaffold Next.js app in `frontend`.
