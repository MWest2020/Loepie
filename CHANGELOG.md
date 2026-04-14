# Changelog

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
