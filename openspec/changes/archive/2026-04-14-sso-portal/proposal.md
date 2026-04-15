## Why

The current single-file approach requires each user to manage their own Anthropic API key. For a finance team using Google Workspace, this is a barrier: keys get lost, shared insecurely, or never set up. A lightweight portal with Google SSO lets users log in with their existing work account while the API key stays server-side, managed once by an admin.

## What Changes

- **BREAKING**: Replace `loepie.html` single-file app with a client-server architecture
- Introduce a Node.js/Express backend that serves the frontend and proxies Anthropic API requests
- Add Google OAuth2 login — only users from the configured Google Workspace domain can access the app
- Move the Anthropic API key to server-side environment config
- Keep all existing chat, file handling, markdown, and Excel export functionality in the frontend
- Backend handles file uploads and forwards them as content blocks to the Anthropic API
- Session management via server-side sessions (cookie-based)

## Capabilities

### New Capabilities

- `google-sso`: Google OAuth2 authentication with Workspace domain restriction, session management, and logout
- `api-proxy`: Backend proxy that accepts chat requests from authenticated users and forwards them to the Anthropic API with streaming passthrough

### Modified Capabilities

- `api-connection`: No longer direct browser-to-API; frontend sends requests to the backend proxy instead. API key removed from client entirely.
- `file-handling`: Files are uploaded to the backend and forwarded as content blocks to Anthropic, rather than being read and sent client-side only. Client-side parsing (docx, xlsx) remains for preview/chips, but the raw file data goes through the proxy.
- `chat-interface`: Login/logout UI added to the header. API key input replaced by user profile display.

## Impact

- **New files**: `server.js`, `package.json`, `.env` (API key + Google OAuth credentials)
- **Modified**: `loepie.html` becomes the frontend served by Express (or split into a `public/` directory)
- **New dependencies**: `express`, `passport`, `passport-google-oauth20`, `express-session`, `dotenv`, `node-fetch` or built-in fetch
- **Infrastructure**: Requires a Node.js runtime to host (local machine, VM, or container)
- **Google Cloud Console**: OAuth2 client credentials must be configured with authorized redirect URI
