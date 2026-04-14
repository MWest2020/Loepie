## 1. Scaffold and CDN setup

- [x] 1.1 Create `loepie.html` with HTML5 boilerplate, CDN script tags (mammoth.js, SheetJS, marked.js), and empty `<style>` and `<script>` blocks
- [x] 1.2 Add the model constant, max tokens constant, and system prompt at the top of the script block

## 2. API key management

- [x] 2.1 Build header bar with API key input, localStorage persistence, and masked display for saved keys
- [x] 2.2 Add validation: warn on missing key when sending, show inline error on 401 response

## 3. Chat UI layout

- [x] 3.1 Build the chat message container with right-aligned user messages and left-aligned assistant messages
- [x] 3.2 Build the textarea input with Enter-to-send and Shift+Enter-for-newline behavior
- [x] 3.3 Apply professional desktop-first CSS: muted palette, 1280px target, responsive to 768px

## 4. File handling

- [x] 4.1 Build the drag-and-drop zone (prominent when empty, compact bar when files attached) with click-to-browse fallback
- [x] 4.2 Implement file readers: plain text for .txt/.csv, mammoth.js for .docx, SheetJS for .xlsx/.xls, base64 for .pdf
- [x] 4.3 Build removable file chips UI below the input area
- [x] 4.4 Add 5MB file size warning and file read error handling

## 5. API communication and streaming

- [x] 5.1 Build the fetch call to `api.anthropic.com/v1/messages` with correct headers, conversation history, file content blocks, and `stream: true`
- [x] 5.2 Implement SSE stream reader that appends tokens to the DOM progressively with a cursor indicator
- [x] 5.3 On stream completion, re-render the full message with marked.js for markdown formatting
- [x] 5.4 Add error handling: network errors, 429 rate limit, and generic API errors shown inline

## 6. Excel export

- [x] 6.1 After markdown rendering, scan for fenced code blocks tagged `xlsx` and attempt JSON parse
- [x] 6.2 Generate downloadable .xlsx from valid JSON using SheetJS, with correct filename pattern
- [x] 6.3 Show download button below the message, or inline error for malformed JSON

## 7. Polish and verification

- [x] 7.1 Add auto-scroll behavior during streaming
- [x] 7.2 Test full flow: attach files, chat, receive streaming response, download generated xlsx
- [x] 7.3 Verify responsive layout at 1280px and 768px
