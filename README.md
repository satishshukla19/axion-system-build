# axion-system-build

Kubernetes-based microservices application for deploying and testing an IoT telemetry platform using Docker, PostgreSQL, and Kubernetes.

This repository contains schema, simulator, and other components used by the Axion platform. It is intended to be deployed inside a Kubernetes cluster (examples and manifests live in the axion-gitops repo referenced by the services).

Live service endpoints (may be environment-specific):

- http://13.75.218.68:8000/browser/  # Postgres server UI
- http://13.75.218.68:1000/docs#/    # Swagger / API docs
- http://13.75.218.68:81/topology   # Axion systems topology UI

Repository structure (high level):

- axion-data-simulator/       — Lightweight Python service that generates mock telemetry data
- axion-database-schema/     — PostgreSQL migration scripts and schema definitions
- axion-ingestion-service/   — FastAPI service that ingests telemetry and writes to the DB (referenced)
- axion-landing-page/        — Public-facing UI for the platform (referenced)

Quick start (local development)

1. Start a local PostgreSQL instance (or point to your cluster DB).
2. Apply the database schema (see axion-database-schema):

```bash
psql -h localhost -U postgres -d axiondb -f axion-database-schema/01-extensions.sql
psql -h localhost -U postgres -d axiondb -f axion-database-schema/02-telemetry.sql
psql -h localhost -U postgres -d axiondb -f axion-database-schema/03-alerts.sql
```

3. Run the data simulator locally (optional):

```bash
# from axion-data-simulator/
pip install -r requirements.txt
export API_URL="http://localhost:8000/api/v1/telemetry/ingest"
export INTERVAL_SECONDS="2"
python simulator.py
```

Deployment

- This project is intended to run on Kubernetes. Use the axion-gitops repository (ArgoCD manifests) to deploy services, or create your own Kubernetes manifests/Helm charts.

Notes

- The axion-data-simulator ships as a lightweight Python container and is configured via environment variables (API_URL, INTERVAL_SECONDS).
- The database schema requires PostgreSQL 13+ for extension support (pgcrypto).

Contributing

Contributions and issues are welcome. Open a GitHub issue or submit a pull request with a clear description of the change.

License

This project is open source and available under the MIT License.
