## Why

Loepie as a single HTML file requires users to manage their own API key and handle files manually. By building it as a Nextcloud app, authentication, file storage, user management, and hosting are all handled by Nextcloud — which the team already uses with Google Workspace SSO. The only new code is the chat interface and the server-side Anthropic API proxy.

## What Changes

- **BREAKING**: Replace `loepie.html` single-file app with a Nextcloud app
- Nextcloud handles authentication (including existing Google Workspace SSO)
- Users select files from their Nextcloud storage via the native file picker
- Backend proxies chat requests to the Anthropic API — API key configured once by admin
- Chat interface, streaming, markdown rendering, and Excel export remain as frontend features
- File parsing (docx, xlsx, csv, pdf, txt) moves server-side

## Capabilities

### New Capabilities

- `nextcloud-integration`: App registration, admin settings, file picker, Nextcloud API usage
- `api-proxy`: Server-side proxy to Anthropic Messages API with streaming passthrough

### Modified Capabilities

- `api-connection`: Browser no longer calls Anthropic directly; requests go through the Nextcloud backend
- `file-handling`: Files sourced from Nextcloud storage instead of local drag-and-drop
- `chat-interface`: Header shows Nextcloud user instead of API key input; file picker replaces drop zone
- `excel-export`: Generated files saved back to Nextcloud storage in addition to download

## Impact

- **New**: Nextcloud app structure (appinfo, lib, templates, js, css)
- **Removed**: `loepie.html` single-file, client-side API key management
- **Dependencies**: Nextcloud server 28+, PHP 8.1+, server-side libraries for docx/xlsx parsing
- **Admin config**: Anthropic API key, allowed user groups, model selection
