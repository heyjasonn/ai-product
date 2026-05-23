# System Context

Values marked `{{key}}` are loaded from `.claude/context/vars.json`. When reading this file, substitute each `{{key}}` with the matching value from vars.json. If a value is `null`, treat it as unknown/not yet decided.

## Tech Stack

- Frontend: {{frontend}}
- Backend: {{backend}}
- Database: {{database}}
- Auth: {{auth}}
- File storage: {{file_storage}}
- Search: {{search}}
- Cache: {{cache}}
- Deployment: {{deployment}}

## Architecture Decisions

- Apply flow: modal on same page (not page redirect)
- API style: {{api_style}}
- Rendering strategy: {{ssr_strategy}}
- Architecture: {{architecture_style}}
- Key libraries: {{key_libraries}}

## Current Product Phase

- Phase: {{product_phase}}
- Features already built: {{features_built}}
- Features in progress: {{features_in_progress}}
