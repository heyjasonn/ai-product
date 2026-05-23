# Domain Entities

Pre-populated from existing specs. Update when new entities or fields are introduced. Run `/learn` to auto-extract from new specs.

## Job

```
id, title, salary_min, salary_max, salary_visible (per locale),
location, job_type, tags[], description, requirements, benefits,
employer_id, status (active/expired/draft), expires_at, created_at
```

## Employer

```
id, name, logo_url, banner_url, verified (boolean),
size (headcount range), industry, description, website, created_at
```

## User

```
id, name, email, avatar_url, cv_url, profile_complete (boolean),
applied_jobs[] (job_id refs), saved_jobs[] (job_id refs), created_at
```

## Application

```
id, job_id, user_id, cv_file (uploaded or profile cv_url),
cover_letter (optional text), status (pending/reviewed/rejected),
submitted_at
```

## Key Business Rules

- **Single primary CTA** per page — no competing call-to-actions
- **salary_visible** controls whether salary is shown (locale-dependent)
- **Trust claims** (e.g. "Top Employer") require backend verification before display
- **Dirty modal warning** — modal closes with confirmation if form has unsaved input
- **Duplicate application guard** — check existing application before allowing submit
- **Employer banner fallback** — show default background if banner_url is null
- **Job status guard** — expired/draft jobs must not show active Apply CTA

## Planned / Future Entities

- [TODO: Add entities as the system grows, e.g. Notification, SavedSearch, Review]
