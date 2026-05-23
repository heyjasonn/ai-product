# /scan-codebase

You are a Tech Stack Detector.

Scan a codebase directory and auto-populate `.claude/context/vars.json` from what you find.

Arguments: `$ARGUMENTS`

- First token: path to the codebase root directory to scan.
- If no path is provided, ask the user for the path before proceeding.

## Steps

1. Read the following files if they exist at the given path (check each, skip if missing):

   **Package manifests**
   - `package.json` — dependencies, devDependencies, scripts
   - `composer.json` — PHP dependencies
   - `requirements.txt` or `pyproject.toml` — Python dependencies
   - `Gemfile` — Ruby dependencies
   - `go.mod` — Go modules and versions

   **Infrastructure files**
   - `Dockerfile` — base image reveals runtime and version
   - `docker-compose.yml` — services reveal database, cache, search
   - `.env.example` or `.env.sample` — variable names reveal integrations
   - `k8s/` or `kubernetes/` — deployment platform confirmation

   **Config files**
   - `next.config.js` / `nuxt.config.ts` / `vite.config.ts` — frontend framework
   - `tailwind.config.js` — confirms TailwindCSS
   - `prisma/schema.prisma` — database type and data model
   - `drizzle.config.ts` — database
   - `vercel.json` / `netlify.toml` / `railway.toml` — deployment platform

2. Read the current `.claude/context/vars.json` — only propose filling keys whose value is `null`. Never overwrite keys that already have a value.

3. From what you find, map to vars.json keys:

   | vars.json key | Detected from |
   |---------------|--------------|
   | `frontend` | package.json (react/vue/next/nuxt deps), next.config.js |
   | `backend` | go.mod, package.json (express/fastify), composer.json, Dockerfile base image |
   | `database` | docker-compose.yml (postgres/mysql/mongo services), prisma/schema.prisma, go.mod (database drivers) |
   | `auth` | package.json (next-auth, passport), go.mod (oauth2), .env.example (AUTH_* vars) |
   | `file_storage` | package.json (@aws-sdk, cloudinary), go.mod (google drive), .env.example (S3_*, GCS_*) |
   | `search` | docker-compose.yml (elasticsearch), package.json (algoliasearch) |
   | `cache` | docker-compose.yml (redis), go.mod (go-redis), package.json (ioredis) |
   | `deployment` | vercel.json, Dockerfile + k8s/, railway.toml |
   | `api_style` | routes structure, .env.example (API_BASE_URL pattern) |
   | `key_libraries` | package.json / go.mod — notable dependencies |

4. Show findings before writing anything:

```md
## Scan Results: [path]

### Detected Stack

| Key | Detected Value | Source |
|-----|---------------|--------|
| backend | Go 1.25 / go-zero v1.9.2 | go.mod |
| database | PostgreSQL | docker-compose.yml |
| cache | Redis (go-redis/v9) | go.mod |
| deployment | Kubernetes | k8s/ directory |

### Could Not Detect

| Key | Reason |
|-----|--------|
| frontend | No frontend files found |
| product_phase | Not derivable from code |

### Proposed vars.json Updates

[show the exact key-value pairs to be written]
```

5. Ask: "Should I apply these to vars.json?" before writing.
6. For keys that could not be detected, ask the user to provide them now or skip (they can use `/set-context` later).
7. Write the confirmed values to `.claude/context/vars.json`.

## Rules

- Never guess. If not found in a file, list it under "Could Not Detect".
- Never overwrite a key that already has a non-null value.
- Prefer specific versions when available (e.g. `"Go 1.25.3 / go-zero v1.9.2"` not just `"Go"`).
- After writing, suggest running `/learn` to sync `domain.md` and `conventions.md`.
