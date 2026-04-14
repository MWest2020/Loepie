## ADDED Requirements

### Requirement: Message input with keyboard shortcuts
The application SHALL provide a textarea where Enter sends the message and Shift+Enter inserts a newline.

#### Scenario: User presses Enter
- **WHEN** the user presses Enter in the textarea
- **THEN** the message SHALL be sent and the textarea cleared

#### Scenario: User presses Shift+Enter
- **WHEN** the user presses Shift+Enter in the textarea
- **THEN** a newline SHALL be inserted in the textarea

### Requirement: Message layout
User messages SHALL be right-aligned and assistant messages SHALL be left-aligned, clearly distinguishable by visual styling.

#### Scenario: User sends a message
- **WHEN** a user message appears in the chat
- **THEN** it SHALL be displayed right-aligned with user-specific styling

#### Scenario: Assistant responds
- **WHEN** an assistant message appears in the chat
- **THEN** it SHALL be displayed left-aligned with assistant-specific styling

### Requirement: Conversation history
The application SHALL maintain full conversation history in memory and include it in each API request. History SHALL NOT persist across page reloads.

#### Scenario: Multi-turn conversation
- **WHEN** the user has exchanged several messages with the assistant
- **THEN** all prior messages SHALL be included in the next API request

#### Scenario: Page reload
- **WHEN** the user reloads the page
- **THEN** all conversation history SHALL be cleared

### Requirement: Streaming response rendering
Assistant responses SHALL be rendered progressively as tokens arrive from the stream, with a subtle cursor indicator showing the response is still being generated.

#### Scenario: Streaming in progress
- **WHEN** the assistant is generating a response
- **THEN** tokens SHALL appear in real-time and a cursor indicator SHALL be visible

#### Scenario: Streaming completes
- **WHEN** the assistant finishes generating
- **THEN** the cursor indicator SHALL disappear and the final message SHALL be rendered with full markdown formatting

### Requirement: Markdown rendering in assistant messages
Assistant messages SHALL render markdown as formatted HTML, supporting at minimum: bold, italic, code (inline and fenced blocks), lists (ordered and unordered), and headers.

#### Scenario: Assistant responds with markdown
- **WHEN** the assistant response contains markdown formatting
- **THEN** the response SHALL be rendered as formatted HTML with proper styling

#### Scenario: Assistant responds with a fenced code block
- **WHEN** the assistant response contains a fenced code block
- **THEN** the code SHALL be displayed in a monospace, visually distinct block

### Requirement: System prompt
The application SHALL include a predefined system prompt instructing the assistant to act as a finance employee helper, analyze attached documents carefully, respond in the user's language, and output modified spreadsheets as fenced JSON code blocks tagged `xlsx`.

#### Scenario: First message sent
- **WHEN** the user sends their first message
- **THEN** the API request SHALL include the system prompt defining finance assistant behavior

### Requirement: Auto-scroll on new content
The chat SHALL auto-scroll to show the latest content as streaming tokens arrive.

#### Scenario: Long streaming response
- **WHEN** the assistant generates a response longer than the visible chat area
- **THEN** the chat SHALL scroll down to keep the latest content visible

### Requirement: Professional desktop-first UI
The application SHALL present a clean, professional, desktop-first interface targeting 1280px width and responsive down to 768px. The palette SHALL be muted and trust-evoking. No emoji or playful copy SHALL appear in the UI.

#### Scenario: Desktop viewport
- **WHEN** the application is viewed at 1280px width
- **THEN** the layout SHALL use the full width appropriately with comfortable spacing

#### Scenario: Tablet viewport
- **WHEN** the application is viewed at 768px width
- **THEN** the layout SHALL remain functional and readable
