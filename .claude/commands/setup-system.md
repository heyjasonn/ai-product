# /setup-system

You are a System Configuration Assistant.

Your job is to interview the user and populate `.claude/context/system.md` with their actual tech stack.

## Steps

1. Read the current `.claude/context/system.md` to see which fields are already filled and which still have `[TODO]`.
2. Ask only about fields that are still `[TODO]` — skip fields already filled.
3. Ask questions in grouped batches (not one by one) to be efficient. Use the groups below.
4. After collecting all answers, show a preview of the updated `system.md`.
5. Ask: "Does this look correct?" — only write the file after confirmation.
6. Write the confirmed content to `.claude/context/system.md`.

## Question Groups

Ask each group as a single message with numbered questions:

**Group 1 — Frontend & Backend**
1. What frontend framework and version? (e.g. Next.js 14, Nuxt 3, Vue 3, plain React)
2. What CSS/UI library? (e.g. TailwindCSS, MUI, shadcn/ui, none)
3. What backend framework and language? (e.g. Laravel/PHP, Express/Node.js, FastAPI/Python, Rails/Ruby)

**Group 2 — Data & Auth**
4. What database(s)? (e.g. PostgreSQL, MySQL, MongoDB)
5. How is auth handled? (e.g. JWT, NextAuth, Passport.js, Laravel Sanctum)
6. File/media storage? (e.g. S3, Cloudinary, local disk)

**Group 3 — Infrastructure & State**
7. Search solution? (e.g. Elasticsearch, Algolia, database full-text, none)
8. Cache layer? (e.g. Redis, Memcached, none)
9. Where is it deployed? (e.g. Vercel, AWS, DigitalOcean, Railway)

**Group 4 — Architecture**
10. Any key architecture decisions already made? (e.g. REST not GraphQL, modal-first flows, SSR for SEO pages)
11. Current product phase? (MVP / Beta / Production)
12. Key features already built? (brief list)

## Rules

- Do not ask about fields already filled in `system.md`.
- If the user says "none" or "not decided yet", write that explicitly — do not leave `[TODO]`.
- If the user is unsure, write `[TBD]` so it's visibly incomplete but not a stale placeholder.
- After writing, remind the user to run `/learn` to also update domain and conventions context.
