# System Conventions

Pre-populated from existing specs. Update as new patterns are established. Run `/learn` to extract from new specs.

## Language

- All spec documents are written in **Vietnamese**
- UI copy in specs uses Vietnamese
- Technical terms (API, modal, CTA, tab) are kept in English

## UI/UX Patterns

- **Apply flow**: modal on the same page — never redirect to a new page for apply
- **Content layout**: tab-based sections for deep content (e.g. Details / Company / Similar Jobs)
- **Mobile-first**: all layouts designed for mobile first, then desktop
- **Accessibility**: keyboard navigation and ARIA labels required on interactive elements
- **CTA hierarchy**: one primary CTA (Apply), secondary actions (Save, Share) visually subordinate
- **Trust indicators**: verified badges, employer size, industry — require backend confirmation
- **Loading states**: skeleton screens preferred over spinners for content-heavy pages

## API Patterns

- Style: {{api_style}}
- Base URL: {{api_base_url}}
- Auth header: {{api_auth_header}}
- Existing endpoints (from specs):
  - `GET /jobs/:id` — fetch job detail
  - `GET /employers/:id` — fetch employer detail
  - `POST /applications` — submit application
  - `GET /applications/check?job_id=` — check if user already applied

## Spec File Naming

- `quick-sdd-[feature-name].md` — initial quick spec
- `full-sdd-[feature-name].md` — expanded full spec
- `refined-sdd-[feature-name].md` — refined/updated spec
- All names use kebab-case, saved under `/spec/`

## SDD Writing Conventions

- Problem and Goal sections: 2–4 sentences, Vietnamese
- Main Flow: use arrow diagram `Step ↓ Step ↓ Step`
- Edge cases: always include expired/missing data/not-logged-in/mobile scenarios
- Validation rules: specify error message text, not just the rule
- Test cases: include pre-conditions, steps, expected result, priority
