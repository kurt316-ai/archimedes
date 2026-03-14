# Railway Deployment Module

**Audience:** `for-claude`
**Purpose:** Product Effectiveness (PE), Product Efficiency (PI)
**Load when:** Project deploys to Railway
**Last updated:** Thu 12 Mar 2026 0300

---

## When to Load This Module

Load this module if the project:
- Deploys a service to Railway (HTTP server, cron job, or worker)
- Uses Railway for hosting any backend component
- Has a `railway.toml` in the project

Do NOT load for projects that only mention Railway as a possible future deployment target.

---

## Critical: Persistent Service vs. Cron Job

This is the most important distinction in Railway deployment. Getting it wrong breaks your service silently.

### Persistent HTTP Service (always-on)

The service runs continuously, listening for HTTP requests. Examples: Marvin's calendar scrubber (POST /run, POST /apply, GET /).

**Configuration:**
- `railway.toml`: NO `cronSchedule` field. Ever.
- Railway dashboard: Do NOT set cronSchedule in service settings.
- Health check: `GET /` → 200 OK
- Restart policy: ON_FAILURE

**How it gets triggered:** External cron (healthchecks.io, separate Railway cron service, or another scheduler) sends HTTP requests to the service endpoint.

**What goes wrong if you add cronSchedule:** Railway treats the deployment as a one-shot job. It starts, runs once, exits. Your HTTP server stops listening. Incoming requests fail. The service looks "healthy" in the dashboard but isn't serving traffic.

### Cron Job (runs on schedule)

The service runs on a schedule, does its work, then exits. Examples: Weekly Report's email sender.

**Configuration:**
- `railway.toml`: Include `cronSchedule` in `[deploy]` section
- Code: Must call `process.exit(0)` when done (prevents phantom re-runs)
- No health check endpoint needed (not always-on)

**Cron schedule is UTC:** Railway evaluates cron expressions in UTC, not local time. Account for timezone offset.

```toml
[deploy]
cronSchedule = "0 14 * * 1"  # Every Monday at 2 PM UTC = 6 AM PST / 7 AM PDT
```

---

## railway.toml Configuration

### Minimal config for persistent HTTP service

```toml
[build]
builder = "dockerfile"
dockerfilePath = "Dockerfile"

[deploy]
healthcheckPath = "/"
healthcheckTimeout = 300
restartPolicyType = "ON_FAILURE"
restartPolicyMaxRetries = 10
```

### Minimal config for cron job

```toml
[build]
builder = "dockerfile"
dockerfilePath = "Dockerfile"

[deploy]
cronSchedule = "0 14 * * 1"
restartPolicyType = "ON_FAILURE"
restartPolicyMaxRetries = 3
```

### What NOT to include

- `cronSchedule` on a persistent service (see above)
- `numReplicas` (Railway manages this)
- Hardcoded environment variables (use Railway dashboard)

---

## Dockerfile Patterns

### Python multi-stage build (Marvin pattern)

```dockerfile
FROM python:3.10-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.10-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.10/site-packages /usr/local/lib/python3.10/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin
COPY . .
CMD ["python", "src/main.py"]
```

### Node.js multi-stage build (Weekly Report pattern)

```dockerfile
FROM node:18-slim AS builder
WORKDIR /app
COPY package*.json tsconfig.json ./
RUN npm ci
COPY src/ src/
RUN npm run build

FROM node:18-slim
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
CMD ["node", "dist/index.js"]
```

**Key principles:**
- Multi-stage keeps the final image small
- Copy dependency manifests first (caching layer)
- Never copy `.env` or secrets into the image
- `.dockerignore` should exclude: `.env`, `.git`, `node_modules`, `__pycache__`

---

## Environment Variables

### Where secrets live

| Context | Where | Example |
|---|---|---|
| Local development | `.env` file (gitignored) | `NOTION_TOKEN=secret_abc123` |
| Railway production | Dashboard → service → Variables | Same key-value pairs |
| Documentation | `.env.example` (committed) | `NOTION_TOKEN=your_notion_integration_token` |

### .env.example pattern

Document every env var the service needs:
```bash
# Required
NOTION_TOKEN=your_notion_integration_token
GOOGLE_SA_KEY_JSON=stringified_service_account_json

# Optional (defaults shown)
DRY_RUN=false
PORT=3000
LOG_LEVEL=info

# Where to get these values:
# NOTION_TOKEN: notion.so/my-integrations → create integration → copy token
# GOOGLE_SA_KEY_JSON: stringify the .json key file → paste as single line
```

### Google service account keys on Railway

The service account JSON key file can't be uploaded as a file to Railway. Instead:
1. Stringify the entire JSON file: `JSON.stringify(keyFileContents)`
2. Set `GOOGLE_SA_KEY_JSON` in Railway dashboard to the stringified value
3. In code, parse it: `JSON.parse(process.env.GOOGLE_SA_KEY_JSON)`

For local dev, use the file directly:
```
GOOGLE_SA_KEY_FILE=./path-to-key.json
```

Code should handle both:
```python
if os.getenv('GOOGLE_SA_KEY_JSON'):
    credentials = json.loads(os.environ['GOOGLE_SA_KEY_JSON'])
elif os.getenv('GOOGLE_SA_KEY_FILE'):
    with open(os.environ['GOOGLE_SA_KEY_FILE']) as f:
        credentials = json.load(f)
```

---

## Health Check Endpoint

Every persistent HTTP service should have:

```python
@app.route('/')
def health():
    return 'OK', 200
```

Or in Express/Node:
```javascript
app.get('/', (req, res) => res.send('OK'));
```

**Why:** Railway uses this to detect crashes and restart the service. Without it, a crashed service sits dead until someone notices.

**Health check timeout:** Set `healthcheckTimeout` in railway.toml. Default is 300 seconds. If your service takes longer to start (e.g., loading large models), increase this.

---

## Auto-Deploy from GitHub

Railway auto-deploys when you push to the connected branch (usually `main`).

**Workflow:**
1. Push code to GitHub (`git push origin main`)
2. Railway detects the push (~30 seconds)
3. Builds new Docker image (~1-2 minutes)
4. Deploys new image, health check passes
5. Old instance is replaced

**Rollback:** Railway keeps previous deployments. If the new deploy fails, roll back from the dashboard.

**Testing before deploy:** Use `DRY_RUN=true` or `FORCE_RUN=true` env vars to test safely:
- `DRY_RUN=true` — runs the pipeline without side effects (no writes to Notion, no emails sent)
- `FORCE_RUN=true` — bypasses schedule check, runs immediately (useful for testing on deploy)

---

## Monitoring

### Logs

Railway provides real-time logs in the dashboard. Design your logging for Railway's log viewer:
- Use structured log messages: `[SCAN] CR-001: 3 hits, 0 errors`
- Include timestamps (Railway adds its own, but explicit timestamps help when debugging)
- Log every external API call (Notion, Google Calendar, email sends)
- Log scan/run summaries at the end of each execution

### healthchecks.io integration

For critical services, add healthchecks.io ping:
1. Create a check at healthchecks.io
2. Ping the check URL at the end of each successful run
3. healthchecks.io alerts if the ping doesn't arrive on schedule

```python
import requests
requests.get(os.environ['HEALTHCHECK_PING_URL'], timeout=10)
```

---

## Anti-Patterns

1. **cronSchedule on persistent services:** The #1 gotcha. Turns your HTTP server into a one-shot job. Service stops listening. See critical section above.

2. **Secrets in railway.toml:** This file is committed to Git. Never put credentials here. Use Railway dashboard Variables.

3. **No health check:** Service crashes, Railway doesn't know. Add `GET /` → 200.

4. **Hardcoded port:** Always read from `process.env.PORT` or `os.environ.get('PORT', '3000')`. Railway assigns the port.

5. **Fat Docker images:** Copying `node_modules` or `.git` into the image. Use multi-stage builds and `.dockerignore`.

6. **No dry-run mode:** Deploying untested changes that write to production data. Always have a way to run without side effects.

7. **Forgetting UTC for cron:** Railway cron is UTC. `0 9 * * *` is 9 AM UTC, which is 1 AM PST / 2 AM PDT. Always calculate the offset.

---

## Open Questions

> **⚠️ This module is based on patterns from Marvin and Weekly Report.** Additional patterns may emerge.

1. **Railway Postgres/Redis:** Neither project uses Railway's database services yet. Module should be extended when they do.
2. **Custom domains:** Neither project has a custom domain on Railway. Guidance needed when that becomes relevant.
3. **Cost optimization:** Railway charges by resource usage. No guidance yet on optimizing for cost (e.g., right-sizing instances, minimizing idle time).
4. **Multiple services in one project:** Railway supports multiple services in a single project. No experience with this pattern yet.
