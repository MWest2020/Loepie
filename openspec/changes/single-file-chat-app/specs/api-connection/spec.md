## ADDED Requirements

### Requirement: API key input and persistence
The application SHALL provide a text input in the header for entering an Anthropic API key. The key SHALL be persisted to `localStorage` and restored on page load. The key SHALL never be logged, displayed after entry, or sent to any endpoint other than `api.anthropic.com`.

#### Scenario: First-time user enters API key
- **WHEN** the user opens the application for the first time and enters an API key
- **THEN** the key is saved to `localStorage` and the input shows a masked/confirmed state

#### Scenario: Returning user has saved key
- **WHEN** the user opens the application and a key exists in `localStorage`
- **THEN** the key is loaded automatically and the input shows a masked/confirmed state

#### Scenario: Missing API key on send
- **WHEN** the user attempts to send a message without an API key configured
- **THEN** the application SHALL display a clear warning message and SHALL NOT send the request

### Requirement: Invalid API key handling
The application SHALL detect invalid API key responses (401) from the Anthropic API and display a clear error message instructing the user to check their key.

#### Scenario: API returns 401 authentication error
- **WHEN** the API responds with HTTP 401
- **THEN** an inline error message SHALL appear in the chat indicating the API key is invalid

### Requirement: Direct API communication
The application SHALL send requests directly to `https://api.anthropic.com/v1/messages` using `fetch()` with the header `anthropic-dangerous-direct-browser-access: true`. No proxy or backend SHALL be involved.

#### Scenario: Sending a chat message
- **WHEN** the user sends a message
- **THEN** the application sends a POST request to `https://api.anthropic.com/v1/messages` with the correct headers and the full conversation history

### Requirement: Streaming responses
The application SHALL use `stream: true` in API requests and render tokens progressively as they arrive from the SSE stream.

#### Scenario: Receiving a streamed response
- **WHEN** the API streams response tokens
- **THEN** each token SHALL be rendered in the chat UI as it arrives, with a visible cursor indicator

### Requirement: Model configuration
The model SHALL be configurable via a constant at the top of the file, defaulting to `claude-sonnet-4-20250514`. Max tokens SHALL be set to `4096`.

#### Scenario: Default model used
- **WHEN** the application sends a request without modification to the model constant
- **THEN** the request SHALL specify `model: "claude-sonnet-4-20250514"` and `max_tokens: 4096`

### Requirement: Rate limit handling
The application SHALL detect HTTP 429 responses and display a retry suggestion to the user.

#### Scenario: API returns 429 rate limit
- **WHEN** the API responds with HTTP 429
- **THEN** an inline message SHALL appear in the chat suggesting the user wait and retry

### Requirement: Network error handling
The application SHALL catch network errors (fetch failures) and display an inline error in the chat.

#### Scenario: Network failure during request
- **WHEN** a fetch request fails due to network issues
- **THEN** an inline error message SHALL appear in the chat describing the failure
