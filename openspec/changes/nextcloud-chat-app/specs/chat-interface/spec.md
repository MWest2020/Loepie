## ADDED Requirements

### Requirement: Chat page within Nextcloud
The application SHALL render a chat interface within the Nextcloud app frame, preserving the Nextcloud header and navigation. The chat page SHALL be the app's main view.

#### Scenario: User opens the app
- **WHEN** a user navigates to the Loepie app
- **THEN** the chat interface SHALL load within the Nextcloud content area

### Requirement: Message input with keyboard shortcuts
The application SHALL provide a textarea where Enter sends the message and Shift+Enter inserts a newline.

#### Scenario: User presses Enter
- **WHEN** the user presses Enter in the textarea
- **THEN** the message SHALL be sent and the textarea cleared

#### Scenario: User presses Shift+Enter
- **WHEN** the user presses Shift+Enter in the textarea
- **THEN** a newline SHALL be inserted in the textarea

### Requirement: Message layout
User messages SHALL be right-aligned and assistant messages SHALL be left-aligned, clearly distinguishable by visual styling. The styling SHALL be consistent with Nextcloud's design language.

#### Scenario: User sends a message
- **WHEN** a user message appears in the chat
- **THEN** it SHALL be displayed right-aligned with user-specific styling

#### Scenario: Assistant responds
- **WHEN** an assistant message appears in the chat
- **THEN** it SHALL be displayed left-aligned with assistant-specific styling

### Requirement: Conversation history in session
The application SHALL maintain full conversation history in the browser session (JavaScript memory) and send it with each request to the backend. History SHALL NOT persist across page reloads.

#### Scenario: Multi-turn conversation
- **WHEN** the user has exchanged several messages with the assistant
- **THEN** all prior messages SHALL be included in the next request

#### Scenario: Page reload
- **WHEN** the user reloads the page
- **THEN** conversation history SHALL be cleared

### Requirement: Streaming response rendering
Assistant responses SHALL be rendered progressively as SSE events arrive from the backend, with a subtle cursor indicator showing the response is still being generated.

#### Scenario: Streaming in progress
- **WHEN** the assistant is generating a response
- **THEN** tokens SHALL appear in real-time and a cursor indicator SHALL be visible

#### Scenario: Streaming completes
- **WHEN** the stream ends
- **THEN** the cursor indicator SHALL disappear and the final message SHALL be rendered with full markdown formatting

### Requirement: Markdown rendering
Assistant messages SHALL render markdown as formatted HTML, supporting: bold, italic, code (inline and fenced blocks), lists (ordered and unordered), headers, and tables.

#### Scenario: Markdown content rendered
- **WHEN** the assistant response contains markdown
- **THEN** the response SHALL be rendered as formatted HTML

### Requirement: File attachment UI
The chat interface SHALL show an attach button that opens the Nextcloud file picker. Selected files SHALL appear as removable chips below the input area. Files persist across messages until manually removed.

#### Scenario: User attaches files
- **WHEN** the user selects files via the file picker
- **THEN** the files SHALL appear as chips showing filename and size, each with a remove button

#### Scenario: User removes a file chip
- **WHEN** the user clicks remove on a file chip
- **THEN** the file SHALL be removed from the attachment list

### Requirement: Auto-scroll
The chat SHALL auto-scroll to show the latest content as streaming tokens arrive.

#### Scenario: Long streaming response
- **WHEN** the assistant generates a response longer than the visible area
- **THEN** the chat SHALL scroll to keep the latest content visible

### Requirement: Claude account connection state
The chat interface SHALL reflect the user's Anthropic OAuth connection state. If not connected, the interface SHALL show a "Connect your Claude account" prompt instead of the chat input.

#### Scenario: User not connected to Claude
- **WHEN** the user has no stored Anthropic OAuth token
- **THEN** the chat area SHALL display a "Connect your Claude account" button and the message input SHALL be disabled

#### Scenario: User connected to Claude
- **WHEN** the user has a valid Anthropic OAuth token
- **THEN** the chat interface SHALL be fully functional

#### Scenario: Token becomes invalid during session
- **WHEN** a request fails because the user's Claude token is invalid and cannot be refreshed
- **THEN** the chat SHALL show an inline message prompting the user to reconnect

### Requirement: Error display
API errors, file errors, and configuration errors SHALL be displayed inline in the chat as styled error messages, not as browser alerts.

#### Scenario: API error
- **WHEN** the backend returns an error
- **THEN** the error SHALL appear as a styled inline message in the chat

#### Scenario: App not configured by admin
- **WHEN** no OAuth client credentials have been configured by the admin
- **THEN** a clear message SHALL explain that an admin needs to configure the app
