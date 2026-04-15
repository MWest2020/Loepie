## ADDED Requirements

### Requirement: Server-side Anthropic API proxy with per-user OAuth tokens
The application backend SHALL provide an API endpoint that accepts chat requests from the frontend and forwards them to the Anthropic Messages API (`https://api.anthropic.com/v1/messages`). Each request SHALL be authenticated using the requesting user's stored OAuth access token. No Anthropic credentials SHALL be sent to or accessible from the browser.

#### Scenario: User sends a chat message
- **WHEN** an authenticated user with a valid Anthropic OAuth token sends a chat message
- **THEN** the backend SHALL construct an Anthropic API request using that user's access token and forward it

#### Scenario: User has no Anthropic token
- **WHEN** a user without a stored Anthropic OAuth token attempts to send a message
- **THEN** the backend SHALL return an error prompting the user to connect their Claude account

#### Scenario: Tokens not exposed to client
- **WHEN** any frontend request or response is inspected
- **THEN** no Anthropic access token, refresh token, or OAuth client secret SHALL appear in any response body, header, or client-accessible storage

### Requirement: Automatic token refresh
The backend SHALL transparently refresh expired access tokens using the stored refresh token before or during API requests.

#### Scenario: Access token expired during request
- **WHEN** the Anthropic API returns 401 and the user has a valid refresh token
- **THEN** the backend SHALL refresh the access token, retry the request, and update the stored token

#### Scenario: Refresh token also expired
- **WHEN** both the access token and refresh token are invalid
- **THEN** the backend SHALL return an error prompting the user to re-authenticate with Claude

### Requirement: Streaming passthrough
The backend proxy SHALL support streaming responses. When `stream: true` is set in the request to Anthropic, the backend SHALL pass the SSE stream through to the frontend in real time.

#### Scenario: Streaming response forwarded
- **WHEN** the Anthropic API streams response tokens
- **THEN** the backend SHALL forward each SSE event to the frontend as it arrives, without buffering the full response

### Requirement: File content as content blocks
The backend SHALL read attached files from Nextcloud storage, parse them into content blocks, and include them in the Anthropic API request before the user's text message.

#### Scenario: Text files (.txt, .csv)
- **WHEN** the user attaches a .txt or .csv file
- **THEN** the backend SHALL read the file as UTF-8 text and include it as a text content block

#### Scenario: Word documents (.docx)
- **WHEN** the user attaches a .docx file
- **THEN** the backend SHALL extract text content server-side and include it as a text content block

#### Scenario: Excel files (.xlsx, .xls)
- **WHEN** the user attaches an .xlsx or .xls file
- **THEN** the backend SHALL parse the spreadsheet server-side, converting sheets to structured text (preserving sheet names, headers, and rows), and include it as a text content block

#### Scenario: PDF files (.pdf)
- **WHEN** the user attaches a .pdf file
- **THEN** the backend SHALL read the file as base64 and include it as a document content block with `type: "document"` and `source.type: "base64"`

### Requirement: Conversation history
The backend SHALL accept the full conversation history from the frontend and include it in each Anthropic API request. The backend SHALL NOT persist conversation history — it is stateless per request.

#### Scenario: Multi-turn conversation
- **WHEN** the frontend sends a request with prior messages
- **THEN** the backend SHALL include all provided messages in the Anthropic API request

### Requirement: System prompt
The backend SHALL include the system prompt in every Anthropic API request. The system prompt SHALL instruct Claude to act as a finance assistant, analyze documents carefully, respond in the user's language, and output modified spreadsheets as fenced JSON code blocks tagged `xlsx`.

#### Scenario: System prompt included
- **WHEN** any chat request is forwarded to the Anthropic API
- **THEN** the request SHALL include the system prompt

### Requirement: Error forwarding
The backend SHALL forward Anthropic API errors to the frontend with appropriate HTTP status codes and descriptive messages.

#### Scenario: Anthropic returns 401 after token refresh attempt
- **WHEN** the Anthropic API returns 401 and token refresh also fails
- **THEN** the backend SHALL return an error prompting the user to reconnect their Claude account

#### Scenario: Anthropic returns 429
- **WHEN** the Anthropic API returns 429 (rate limit)
- **THEN** the backend SHALL forward the 429 status with a message suggesting the user wait

#### Scenario: Network failure to Anthropic
- **WHEN** the backend cannot reach the Anthropic API
- **THEN** the backend SHALL return a 502 error with a descriptive message

#### Scenario: User's Claude subscription insufficient
- **WHEN** the Anthropic API returns 403 (e.g., plan does not support API access)
- **THEN** the backend SHALL return an error explaining the user's Claude plan may not support this feature

### Requirement: Request validation
The backend SHALL validate incoming requests: message text must not be empty, attached file IDs must be valid Nextcloud file references, and the user must be authenticated in both Nextcloud and with Anthropic.

#### Scenario: Empty message
- **WHEN** a request is received with no message text
- **THEN** the backend SHALL return a 400 error

#### Scenario: Invalid file reference
- **WHEN** a request references a file ID that does not exist or the user cannot access
- **THEN** the backend SHALL return a 404 error for that file and not include it
