# MLflow 3 — Docker Stack

Self-hosted MLflow Tracking Server with persistent PostgreSQL metadata store and dedicated artifact storage.

---

## 🏗 Architecture

```
mlflow/
├── postgres:16            # Relational database storing experiments, runs, params, metrics
└── mlflow                 # MLflow Tracking Server & Web UI (Port 7777)
```

---

## 📁 Directory Structure

```
mlflow/
├── .env.example         # Safe configuration template
├── .env                 # Local secrets and MLflow port mapping (not committed)
├── Dockerfile           # MLflow base image + uv package installer
├── docker-compose.yml   # Multi-container orchestration definition
└── requirements.txt     # Extra Python packages required by the server
```

---

## 🚀 Quick Start

### 1. Configure Environment (`.env`)

Copy `.env.example` to `.env`, then replace `change-me` with a strong database password.

```env
POSTGRES_USER=mlflow
POSTGRES_PASSWORD=change-me
POSTGRES_DB=mlflow
MLFLOW_PORT=7777
```

### 2. Build & Start

```bash
# Build custom image
docker compose build

# Start services
docker compose up -d
```

### 3. Verify Health

```bash
# Check container status
docker compose ps

# Test tracking server health endpoint
curl http://localhost:7777/health
```

- **MLflow Web UI**: [http://localhost:7777](http://localhost:7777)

The default Compose configuration binds MLflow to localhost and proxies artifacts through the server into the persistent `mlflow-artifacts` volume. If `MLFLOW_PORT` is changed, use that port in the commands and URLs above.

---

## 🔌 Connecting from Python Clients

In your training scripts or notebooks:

```python
import mlflow

# Point tracking client to the self-hosted server
mlflow.set_tracking_uri("http://localhost:7777")
mlflow.set_experiment("my-experiment")

with mlflow.start_run():
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_metric("accuracy", 0.94)
    # Log model artifacts
    # mlflow.sklearn.log_model(model, "model")
```

---

## 🛠 Useful Commands

| Task | Command |
|---|---|
| Stop stack | `docker compose down` |
| View logs | `docker compose logs -f mlflow` |
| Restart server | `docker compose restart mlflow` |
| Tear down + wipe storage | `docker compose down -v` *(deletes database and artifact volumes)* |
