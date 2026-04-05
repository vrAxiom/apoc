# 🧩 Claude-native Bug Tracking Loop (apoc)

This project implements a production-ready bug tracking and auto-fix loop connecting **Gitea**, **Claude API**, and **Slack**.

## 🚀 Deployment on Railway

### 1. Webhook Server (Node.js)
- **Repo**: vrAxiom/apoc
- **Root Directory**: `/packages/webhook`
- **Environment Variables**:
  - `CLAUDE_API_KEY`: Your Anthropic API Key.
  - `GITEA_TOKEN`: Gitea Personal Access Token.
  - `GITEA_BASE_URL`: URL of your Gitea instance (e.g., `https://gitea-production.up.railway.app`).
  - `SLACK_WEBHOOK_URL`: Slack Incoming Webhook URL.
  - `PORT`: `8080` (default).

### 2. Gitea (Docker)
- **Service Type**: New Service > Docker Image > `gitea/gitea:latest`
- **Persistence**: Add a Railway Volume and mount it to `/data`.
- **Database**:
  - If using a **shared/existing Postgres server**:
    - `GITEA__database__DB_TYPE`: `postgres`
    - `GITEA__database__HOST`: `your-db-host:5432`
    - `GITEA__database__NAME`: `gitea` (Gitea will attempt to create this or use the provided DB)
    - `GITEA__database__USER`: `postgres_user`
    - `GITEA__database__PASSWD`: `postgres_password`
  - *Note: If using Railway's managed Postgres in the same project, use `${{Postgres.DATABASE_URL}}`.*

---

## 🛠️ How it works
1. **Bug Reported**: A new issue is opened in Gitea.
2. **Webhook Trigger**: Gitea sends a JSON payload to the Webhook Server.
3. **Claude Triage**: The server sends the issue to Claude for analysis, severity scoring, and root cause hypothesis.
4. **Slack Alert**: A notification is sent to Slack with Claude's initial findings.
5. **Auto-fix (Experimental)**: If Claude identifies a clear fix, the server:
   - Clones the repo.
   - Creates a fix branch.
   - Commits the change.
   - Opens a **Pull Request** in Gitea automatically.

---

## 📦 Project Structure
- `/packages/webhook`: Express server logic.
- `/packages/gitea`: Deployment configurations.
- `railway.toml`: Deployment orchestration.
