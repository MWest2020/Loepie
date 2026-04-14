## ADDED Requirements

### Requirement: Drag-and-drop file upload zone
The application SHALL provide a drag-and-drop zone that also supports click-to-browse. The zone SHALL be prominent when no files are attached and collapse to a compact bar after the first file is added.

#### Scenario: User drags a file onto the drop zone
- **WHEN** the user drags a supported file onto the drop zone
- **THEN** the file SHALL be processed and displayed as a removable chip below the input

#### Scenario: User clicks the drop zone to browse
- **WHEN** the user clicks the drop zone
- **THEN** a file browser dialog SHALL open allowing selection of supported file types

### Requirement: Plain text file handling
The application SHALL read `.txt` and `.csv` files as plain text and send them as text content blocks in the API request.

#### Scenario: User attaches a .txt file
- **WHEN** the user attaches a `.txt` file
- **THEN** the file content SHALL be read as UTF-8 text and included as a text content block

#### Scenario: User attaches a .csv file
- **WHEN** the user attaches a `.csv` file
- **THEN** the file content SHALL be read as UTF-8 text and included as a text content block

### Requirement: Word document handling
The application SHALL extract text from `.docx` files client-side using mammoth.js and send the extracted text as a text content block.

#### Scenario: User attaches a .docx file
- **WHEN** the user attaches a `.docx` file
- **THEN** the text content SHALL be extracted via mammoth.js and included as a text content block

### Requirement: Excel file handling
The application SHALL parse `.xlsx` and `.xls` files client-side using SheetJS, converting them to readable text that preserves sheet names, headers, and row data. The result SHALL be sent as a text content block.

#### Scenario: User attaches an .xlsx file
- **WHEN** the user attaches a `.xlsx` file
- **THEN** SheetJS SHALL parse the file and the content SHALL be converted to structured text preserving sheet names, headers, and rows

#### Scenario: User attaches a multi-sheet workbook
- **WHEN** the user attaches an Excel file with multiple sheets
- **THEN** all sheets SHALL be included in the text representation, each prefixed with the sheet name

### Requirement: PDF file handling
The application SHALL read `.pdf` files as base64 and send them as document content blocks using the Anthropic API's native PDF support.

#### Scenario: User attaches a .pdf file
- **WHEN** the user attaches a `.pdf` file
- **THEN** the file SHALL be read as base64 and sent as a document content block with `type: "document"` and `source.type: "base64"`

### Requirement: Multiple files per session
The application SHALL support multiple attached files in a single session. Files SHALL be shown as removable chips and persist across messages until manually removed by the user.

#### Scenario: User attaches three files and sends multiple messages
- **WHEN** the user attaches three files and sends two messages without removing any
- **THEN** all three files SHALL be included as content blocks in both messages

#### Scenario: User removes a file chip
- **WHEN** the user clicks the remove button on a file chip
- **THEN** that file SHALL be removed and no longer included in subsequent messages

### Requirement: File content ordering in messages
All file content blocks SHALL be sent before the user's text content block in each API message.

#### Scenario: Message with files and text
- **WHEN** the user sends a message with attached files
- **THEN** the API request SHALL include file content blocks first, followed by the user's text block

### Requirement: Large file warning
The application SHALL display a warning (not block) when a single file exceeds 5 MB.

#### Scenario: User attaches a 6 MB file
- **WHEN** the user attaches a file larger than 5 MB
- **THEN** a warning SHALL be displayed but the file SHALL still be accepted and processed

### Requirement: File read error handling
The application SHALL handle file read failures gracefully, showing which file failed while keeping other successfully read files.

#### Scenario: One of three files fails to read
- **WHEN** one file fails to read while two others succeed
- **THEN** an error SHALL be shown for the failed file and the two successful files SHALL remain attached
