## Context

This is a greenfield project. There is no existing codebase beyond a README. The target user is a finance employee who needs to discuss documents with an AI assistant without any IT support or installation process. The entire application must live in a single HTML file that works when opened directly from the filesystem (`file://` protocol).

## Goals / Non-Goals

**Goals:**

- Zero-install experience: double-click an HTML file, start chatting
- Client-side document parsing for common office formats (docx, xlsx, xls, csv, txt, pdf)
- Streaming responses from the Anthropic Messages API
- Professional, trust-evoking UI suitable for a finance/accounting context
- Excel generation from structured assistant output

**Non-Goals:**

- Server-side processing or backend of any kind
- User authentication or multi-user support
- Persistent chat history across page reloads
- In-place file editing
- Mobile-first design (responsive down to 768px is sufficient)
- Custom markdown parser — use a proven library or accept minimal formatting

## Decisions

### D1: Single HTML file with inline CSS/JS, CDN-only externals

All application code is inline. External dependencies load from `cdnjs.cloudflare.com`:
- **mammoth.js** — .docx to text extraction
- **SheetJS (xlsx)** — .xlsx/.xls parsing and .xlsx generation
- **marked.js** — lightweight markdown to HTML rendering

**Why**: No build step, no `node_modules`, no bundler config. A finance user can receive this file via email or shared drive and open it immediately. CDN dependencies are a pragmatic trade-off — the file won't work fully offline, but avoids bundling 500KB+ of library code inline.

**Alternative considered**: Bundling all JS inline. Rejected because it would make the file enormous and unmaintainable.

### D2: Direct browser fetch to Anthropic API

Use `fetch()` with `anthropic-dangerous-direct-browser-access: true` header. The API key is stored in `localStorage` and sent only in request headers to `api.anthropic.com`.

**Why**: No proxy server means no deployment, no hosting costs, no additional attack surface. The `dangerous-direct-browser-access` header is Anthropic's official mechanism for this use case.

**Alternative considered**: A small proxy server. Rejected because it contradicts the zero-install requirement.

### D3: Files sent as content blocks per message

Each attached file is converted to an Anthropic content block and included in every user message. Files are not stored in conversation history — they are re-sent with each message they're relevant to (i.e., while their chip is visible).

**Why**: The Anthropic API is stateless per request. To maintain file context across turns, files must be re-sent. Sending them as content blocks before the user text follows Anthropic's recommended pattern.

**Trade-off**: Large files sent repeatedly increase token usage and latency. The 5MB warning helps users stay aware.

### D4: Streaming with ReadableStream

Use `stream: true` in the API request and process the SSE stream via `ReadableStream` / `getReader()`. Tokens are appended to the DOM as they arrive.

**Why**: Streaming is essential for perceived performance. Finance documents can produce long analyses — waiting for the full response before rendering would feel broken.

### D5: marked.js for markdown rendering

Use `marked.js` from CDN for rendering assistant markdown. Apply it to the complete message after streaming finishes, then re-render the final HTML.

**Why**: marked is small (~7KB gzipped), well-maintained, and handles the full CommonMark spec. During streaming, render raw text; on completion, re-render with markdown. This avoids partial-markdown rendering glitches.

**Alternative considered**: Custom regex-based markdown. Rejected as fragile and incomplete.

### D6: SheetJS for both parsing and generation

Use the same SheetJS library (community edition) for reading uploaded .xlsx/.xls files and for generating downloadable .xlsx files from assistant JSON output.

**Why**: One library covers both directions. SheetJS community edition is free, well-documented, and handles the xlsx format reliably.

## Risks / Trade-offs

- **CORS / browser restrictions**: Direct API calls from `file://` origin may be blocked by some browsers. Mitigation: the `anthropic-dangerous-direct-browser-access` header should handle this; document that Chrome/Edge are recommended.
- **CDN availability**: If cdnjs is down, the app won't load libraries. Mitigation: acceptable for a desktop tool — if the network is down, the API won't work anyway.
- **Token usage with large files**: Re-sending file content every turn is expensive. Mitigation: the 5MB warning and removable chips let users manage this. Future improvement could cache file content in conversation history.
- **API key in localStorage**: Accessible to any JS on the same origin. Mitigation: since this is a single local file with no third-party scripts (only trusted CDN libs), the risk is minimal. The key is never logged or displayed after entry.
- **No CSP headers**: As a local file, we can't set Content-Security-Policy headers. Mitigation: we only load scripts from cdnjs.cloudflare.com and only connect to api.anthropic.com — the attack surface is small for a local-only tool.
