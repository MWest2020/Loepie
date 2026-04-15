# Changelog

## 2026-04-15

### Added
- OpenSpec change `nextcloud-chat-app` with specs for Nextcloud app with per-user Claude OAuth
  - `nextcloud-integration`: app registration, admin OAuth config, file picker, per-user OAuth flow
  - `api-proxy`: server-side proxy with user OAuth tokens, streaming, token refresh
  - `chat-interface`: chat UI within Nextcloud frame, Claude account connection state
  - `file-handling`: server-side parsing of txt, csv, docx, xlsx, xls, pdf
  - `excel-export`: xlsx JSON detection, generation, download + save to Nextcloud

### Archived
- OpenSpec change `sso-portal` (superseded by nextcloud-chat-app approach)

### Removed
- OpenSpec change `single-file-chat-app` (archived; replaced by nextcloud-chat-app)

## 2026-04-14

### Added
- `loepie.html` — single-file HTML chat application for document analysis with Claude
  - API key management with localStorage persistence
  - Drag-and-drop file handling: .txt, .csv, .docx (mammoth.js), .xlsx/.xls (SheetJS), .pdf (base64)
  - Streaming chat with full conversation history
  - Markdown rendering via marked.js
  - Excel export: detects `xlsx` code blocks in assistant output, generates downloadable .xlsx
  - Professional desktop-first UI (1280px target, responsive to 768px)
- OpenSpec change proposal `single-file-chat-app` with proposal, design, 4 specs, and tasks
