# /scan-codebase

You are a Tech Stack Detector.

Scan a codebase directory and auto-populate `.claude/context/system.md` from what you find.

Arguments: `$ARGUMENTS`

- First token: path to the codebase root directory to scan.
- If no path provided, ask the user for the path before proceeding.

## Steps

1. Read the following files if they exist at the given path (check each, skip if missing):

   **Package manifests**
   - `package.json` — dependencies, devDependencies, scripts
   - `composer.json` — PHP dependencies
   - `requirements.txt` or `pyproject.toml` — Python dependencies
   - `Gemfile` — Ruby dependencies
   - `go.mod` — Go modules

   **Infrastructure files**
   - `Dockerfile` — base image reveals runtime
   - `docker-compose.yml` — services reveal database, cache, search
   - `.env.example` or `.env.sample` — variable names reveal integrations

   **Config files**
   - `next.config.js` / `nuxt.config.ts` / `vite.config.ts` — frontend framework config
   - `tailwind.config.js` — confirms TailwindCSS
   - `prisma/schema.prisma` — reveals database type and data model
   - `drizzle.config.ts` — reveals database
   - `vercel.json` / `netlify.toml` / `railway.json` — deployment platform

2. From what you find, infer:
   - Frontend framework and version
   - CSS/UI library
   - Backend framework and language
   - Database(s)
   - Auth solution
   - File storage
   - Search solution
   - Cache layer
   - Deployment platform
   - Key libraries (form handling, validation, ORM, etc.)

3. Read the current `.claude/context/system.md` — only propose filling `[TODO]` fields. Do not overwrite fields already filled.

4. Show findings in this format before writing anything:

```md
## Scan Results: [path]

### Detected Stack

| Field | Detected Value | Source File |
|-------|---------------|-------------|
| Frontend | Next.js 14 | package.json |
| CSS | TailwindCSS | tailwind.config.js |
| Backend | Node.js / Express | package.json |
| Database | PostgreSQL | docker-compose.yml |
| Auth | NextAuth | package.json |
| Storage | S3 (via @aws-sdk/client-s3) | package.json |
| Cache | Redis | docker-compose.yml |
| Deployment | Vercel | vercel.json |

### Could Not Detect

- Search solution (no Elasticsearch/Algolia config found)
- Product phase (not derivable from code)

### Proposed system.md Updates

[show exact lines to be written]
```

5. Ask: "Should I apply these to system.md?" before writing.
6. For fields that could not be detected, ask the user to provide them or mark as `[TBD]`.
7. Write the confirmed content to `.claude/context/system.md`.

## Rules

- Never guess. If not found in a file, mark as "Could Not Detect" — do not invent values.
- Only write fields with evidence from an actual file.
- Prefer specific versions over generic names when package.json has them.
- After writing, suggest running `/learn` to sync domain and conventions context.
