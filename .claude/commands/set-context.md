# /set-context

Update a single field in `.claude/context/vars.json`.

Arguments: `$ARGUMENTS`

Format: `key "value"` — first token is the key name, the rest is the value.

Examples:
```
/set-context frontend "Next.js 14 + TailwindCSS"
/set-context features_in_progress "search filters, employer analytics dashboard"
/set-context product_phase "Production"
/set-context api_style "REST — base path /api/v1/, Bearer token auth"
```

## Valid Keys

| Key | What it represents |
|-----|--------------------|
| `frontend` | Frontend framework and UI library |
| `backend` | Backend language, framework, version |
| `database` | Database(s) used |
| `auth` | Authentication method |
| `file_storage` | File/media storage solution |
| `search` | Search solution |
| `cache` | Cache layer |
| `deployment` | Deployment platform |
| `api_style` | REST vs GraphQL, base URL, auth header format |
| `ssr_strategy` | SSR / CSR / hybrid rendering decision |
| `architecture_style` | Monolith / microservices / etc. |
| `key_libraries` | Key libraries and versions |
| `product_phase` | MVP / Beta / Production |
| `features_built` | Summary of already-built features |
| `features_in_progress` | Features currently being built |

## Steps

1. Read the current `.claude/context/vars.json`.
2. Parse the key and value from `$ARGUMENTS`.
3. If the key is not in the valid keys list, warn the user and stop.
4. Show the change: `key: [old value] → [new value]`.
5. Write the updated `vars.json` with only that key changed.
6. Confirm: "Updated `key` in vars.json."

## Rules

- Never remove or rename existing keys — only update values.
- If no arguments are provided, print the current state of vars.json as a table (key | current value) so the user can see what needs filling.
- Null values are shown as `(not set)` in the table.
