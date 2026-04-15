## ADDED Requirements

### Requirement: Nextcloud app registration
The application SHALL be a registered Nextcloud app with an `appinfo/info.xml` manifest declaring compatibility with Nextcloud 28+, PHP 8.1+, and the required Nextcloud APIs (Files, User, Settings).

#### Scenario: App appears in Nextcloud app list
- **WHEN** the app directory is placed in Nextcloud's `apps/` or `custom_apps/` folder
- **THEN** the app SHALL appear in the admin app management page and be installable

### Requirement: Admin settings page
The application SHALL provide an admin settings page under Settings > Administration where the following can be configured:
- Anthropic OAuth client ID
- Anthropic OAuth client secret (stored encrypted via Nextcloud's credential store)
- Model name (default: `claude-sonnet-4-20250514`)
- Max tokens per response (default: 4096)
- Allowed user groups (empty = all users)

#### Scenario: Admin configures OAuth credentials
- **WHEN** an admin enters the Anthropic OAuth client ID and client secret
- **THEN** the credentials SHALL be stored (secret encrypted) and used for user OAuth flows

#### Scenario: Admin restricts access to a user group
- **WHEN** the admin selects specific groups in the allowed user groups setting
- **THEN** only users in those groups SHALL see the app in their Nextcloud navigation

#### Scenario: OAuth credentials not configured
- **WHEN** a user opens the app and no OAuth credentials have been configured by the admin
- **THEN** the app SHALL display a message stating that the application is not yet configured

### Requirement: Per-user Anthropic OAuth flow
Each user SHALL authenticate with their own Claude.ai account via OAuth2 Authorization Code flow. The app SHALL redirect the user to Anthropic's authorization endpoint, receive a callback with an authorization code, and exchange it for access and refresh tokens.

#### Scenario: First-time user opens the app
- **WHEN** a user opens the app and has no stored Anthropic OAuth token
- **THEN** the app SHALL show a "Connect your Claude account" button

#### Scenario: User initiates OAuth
- **WHEN** the user clicks "Connect your Claude account"
- **THEN** the app SHALL redirect the user to Anthropic's authorization endpoint with the configured client ID, a redirect URI pointing back to the Nextcloud app, and appropriate scopes

#### Scenario: OAuth callback received
- **WHEN** Anthropic redirects back to the app with an authorization code
- **THEN** the backend SHALL exchange the code for an access token and refresh token, store both encrypted per user in the Nextcloud database, and redirect the user to the chat interface

#### Scenario: User has valid token
- **WHEN** a user opens the app and has a valid (non-expired) access token
- **THEN** the chat interface SHALL load immediately without prompting for authentication

#### Scenario: Access token expired with valid refresh token
- **WHEN** an API request fails with 401 and a refresh token exists
- **THEN** the backend SHALL use the refresh token to obtain a new access token, retry the request, and update the stored tokens

#### Scenario: Refresh token expired or revoked
- **WHEN** both the access token and refresh token are invalid
- **THEN** the app SHALL prompt the user to re-authenticate with "Connect your Claude account"

### Requirement: OAuth CSRF protection
The OAuth authorization request SHALL include a cryptographically random `state` parameter bound to the user's Nextcloud session. The callback handler SHALL reject any response where the `state` does not match.

#### Scenario: Valid OAuth callback
- **WHEN** Anthropic redirects back with a `state` parameter matching the user's session
- **THEN** the backend SHALL proceed with the token exchange

#### Scenario: Missing or mismatched state
- **WHEN** the callback `state` does not match the stored value or is absent
- **THEN** the backend SHALL reject the callback and log the event as a potential CSRF attempt

### Requirement: Refresh token rotation
Each time a refresh token is used to obtain a new access token, the backend SHALL also obtain a new refresh token and invalidate the old one. If Anthropic does not support rotation, the backend SHALL enforce a maximum refresh token lifetime.

#### Scenario: Token refresh with rotation
- **WHEN** the backend uses a refresh token to get a new access token
- **THEN** the new refresh token SHALL replace the old one, and the old refresh token SHALL be invalidated

#### Scenario: Maximum refresh token lifetime exceeded
- **WHEN** a refresh token has been in use for longer than the admin-configured maximum lifetime (default: 30 days)
- **THEN** the backend SHALL reject the refresh token and prompt the user to re-authenticate

### Requirement: Token-session binding
OAuth tokens SHALL be invalidated when the user's Nextcloud account is disabled, deleted, or the user's password is changed.

#### Scenario: Admin disables a user account
- **WHEN** a Nextcloud admin disables a user account that has stored Anthropic OAuth tokens
- **THEN** the stored tokens SHALL be deleted

#### Scenario: User changes password
- **WHEN** a user changes their Nextcloud password
- **THEN** the stored Anthropic OAuth tokens SHALL be deleted and the user SHALL need to reconnect their Claude account

#### Scenario: User account deleted
- **WHEN** a Nextcloud user account is deleted
- **THEN** all associated Anthropic OAuth tokens SHALL be deleted from the database

### Requirement: User disconnect
The application SHALL allow users to disconnect their Claude account from their personal settings.

#### Scenario: User disconnects Claude account
- **WHEN** the user clicks "Disconnect Claude account" in their personal settings
- **THEN** the stored tokens SHALL be deleted and the user SHALL see the "Connect your Claude account" button on next visit

### Requirement: Admin token management
The admin settings page SHALL display an overview of all users with active Anthropic OAuth grants and allow the admin to force-revoke tokens for individual users or all users.

#### Scenario: Admin views active grants
- **WHEN** the admin opens the Lupé admin settings page
- **THEN** a list of users with active Anthropic OAuth tokens SHALL be displayed, including the grant date and last usage date

#### Scenario: Admin revokes a user's token
- **WHEN** the admin clicks "Revoke" next to a user
- **THEN** that user's stored tokens SHALL be deleted and the user SHALL need to reconnect on next visit

#### Scenario: Admin revokes all tokens
- **WHEN** the admin clicks "Revoke all tokens"
- **THEN** all stored Anthropic OAuth tokens SHALL be deleted for all users

### Requirement: Navigation entry
The application SHALL add a navigation entry to the Nextcloud top bar so users can access it as a regular Nextcloud app.

#### Scenario: User navigates to the app
- **WHEN** a logged-in user with access clicks the Lupé navigation entry
- **THEN** the Lupé chat interface SHALL load within the Nextcloud frame (header, sidebar intact)

### Requirement: Nextcloud file picker integration
The application SHALL use the Nextcloud file picker (`@nextcloud/dialogs` OC.dialogs.filepicker or the Vue FilePicker component) to let users select files from their Nextcloud storage.

#### Scenario: User clicks attach file
- **WHEN** the user clicks the attach button in the chat interface
- **THEN** the Nextcloud file picker SHALL open, showing the user's files and folders

#### Scenario: User selects multiple files
- **WHEN** the user selects multiple files in the file picker
- **THEN** all selected files SHALL be attached to the chat session and displayed as chips

#### Scenario: Supported file types
- **WHEN** the user selects a file
- **THEN** the file picker SHALL accept `.txt`, `.csv`, `.docx`, `.xlsx`, `.xls`, and `.pdf` files

### Requirement: File content retrieval via Nextcloud API
The application backend SHALL retrieve file contents via the Nextcloud internal file API (not WebDAV) using the user's storage context, respecting Nextcloud file permissions.

#### Scenario: User attaches a file they own
- **WHEN** a user attaches a file from their storage
- **THEN** the backend SHALL read the file content using the Nextcloud filesystem API as that user

#### Scenario: User attaches a shared file
- **WHEN** a user attaches a file shared with them (read permission)
- **THEN** the backend SHALL read the file content respecting the share permissions

#### Scenario: User attaches a file they cannot read
- **WHEN** a user attempts to attach a file they do not have read access to
- **THEN** the backend SHALL return an error and the file SHALL NOT be attached

### Requirement: Authentication via Nextcloud session
The application SHALL NOT implement its own authentication. All requests SHALL be authenticated via the existing Nextcloud session. The app relies on Nextcloud's login mechanism (which may include Google Workspace SSO configured at the Nextcloud level).

#### Scenario: Unauthenticated request
- **WHEN** an unauthenticated request is made to any app endpoint
- **THEN** Nextcloud SHALL redirect to the login page

#### Scenario: Authenticated user accesses the app
- **WHEN** a user logged into Nextcloud opens the app
- **THEN** the app SHALL recognize the user and load their chat interface without additional login
