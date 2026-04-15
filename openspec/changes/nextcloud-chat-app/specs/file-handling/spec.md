## ADDED Requirements

### Requirement: Supported file types
The application SHALL support the following file types from Nextcloud storage: `.txt`, `.csv`, `.docx`, `.xlsx`, `.xls`, `.pdf`.

#### Scenario: User selects a supported file
- **WHEN** the user selects a .txt, .csv, .docx, .xlsx, .xls, or .pdf file
- **THEN** the file SHALL be accepted and attached to the chat session

#### Scenario: User selects an unsupported file
- **WHEN** the user selects a file with an unsupported extension
- **THEN** the file picker SHALL filter it out or the application SHALL show an error

### Requirement: Server-side file parsing
All file parsing SHALL happen server-side in the Nextcloud backend. The frontend sends Nextcloud file IDs, not file contents.

#### Scenario: Text file parsing
- **WHEN** the backend processes a .txt or .csv file
- **THEN** it SHALL read the file as UTF-8 text

#### Scenario: Word document parsing
- **WHEN** the backend processes a .docx file
- **THEN** it SHALL extract the text content using a PHP library (e.g., PHPWord)

#### Scenario: Excel file parsing
- **WHEN** the backend processes a .xlsx or .xls file
- **THEN** it SHALL parse all sheets, preserving sheet names, headers, and row data as structured text

#### Scenario: PDF handling
- **WHEN** the backend processes a .pdf file
- **THEN** it SHALL read the raw file as base64 for the Anthropic document content block

### Requirement: File size warning
The application SHALL display a warning (not block) when a selected file exceeds 5 MB.

#### Scenario: Large file selected
- **WHEN** a user selects a file larger than 5 MB
- **THEN** a warning SHALL be displayed but the file SHALL still be accepted

### Requirement: File read error handling
The application SHALL handle file read failures gracefully, reporting which file failed while keeping other successfully attached files.

#### Scenario: One file fails to read
- **WHEN** the backend fails to read one of several attached files
- **THEN** an error SHALL be reported for the failed file and the remaining files SHALL be sent to the API

### Requirement: Multiple files per session
The application SHALL support multiple attached files per chat session. All attached files SHALL be included as content blocks in each API request until the user removes them.

#### Scenario: Three files attached across multiple messages
- **WHEN** the user attaches three files and sends two messages
- **THEN** all three files SHALL be included in both API requests

### Requirement: File content ordering
All file content blocks SHALL be placed before the user's text content block in the API request.

#### Scenario: Files before text
- **WHEN** the backend constructs the API request
- **THEN** file content blocks SHALL precede the user's text block
