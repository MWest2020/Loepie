## Why

Finance employees need a zero-friction way to chat with Claude about their documents — Word files, Excel spreadsheets, PDFs, CSVs — without installing software, logging in, or depending on a backend. A single HTML file they can double-click from Explorer covers this: no IT ticket, no deployment, no data leaving their machine except to the Anthropic API.

## What Changes

- Introduce a single-file HTML application (`loepie.html`) with all CSS and JS inline
- API key input stored in `localStorage`, sent only to `api.anthropic.com`
- Direct browser fetch to Anthropic Messages API with streaming
- Client-side file parsing: plain text (.txt, .csv), Word (.docx via mammoth.js), Excel (.xlsx/.xls via SheetJS), PDF (base64 document block)
- Drag-and-drop + click-to-browse file attachment with removable chips
- Streaming chat with full conversation history in memory
- Markdown rendering in assistant responses
- Excel download feature: when assistant outputs a fenced `xlsx` JSON block, generate a downloadable `.xlsx` via SheetJS
- Desktop-first, professional UI (1280px target, responsive to 768px)

## Capabilities

### New Capabilities

- `api-connection`: Direct browser-to-Anthropic API communication with key management, streaming, and error handling
- `file-handling`: Client-side document ingestion (txt, csv, docx, xlsx, xls, pdf) with drag-and-drop UX
- `chat-interface`: Streaming chat UI with conversation history, markdown rendering, and message layout
- `excel-export`: Parse assistant xlsx JSON output and generate downloadable .xlsx files via SheetJS

### Modified Capabilities

_(none — greenfield project)_

## Impact

- **New file**: `loepie.html` — the entire application
- **External CDN dependencies**: mammoth.js (docx parsing), SheetJS (xlsx read/write), optionally a lightweight markdown renderer
- **APIs**: Anthropic Messages API v1 (`api.anthropic.com/v1/messages`)
- **No server-side code, no build tooling, no package.json**
