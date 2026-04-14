## ADDED Requirements

### Requirement: Detect xlsx JSON code blocks
The application SHALL detect fenced code blocks tagged `xlsx` in rendered assistant messages and attempt to parse the JSON content.

#### Scenario: Assistant outputs an xlsx code block
- **WHEN** the assistant response contains a fenced code block tagged `xlsx` with valid JSON
- **THEN** the application SHALL parse the JSON and show a download button below the message

#### Scenario: Assistant outputs an xlsx code block with malformed JSON
- **WHEN** the assistant response contains a fenced code block tagged `xlsx` with invalid JSON
- **THEN** the application SHALL show an inline error message instead of a download button

### Requirement: Excel file generation
The application SHALL generate a downloadable `.xlsx` file from the parsed JSON using SheetJS. The JSON structure SHALL follow the format: `{"sheets": [{"name": "SheetName", "data": [[row], [row]]}]}`.

#### Scenario: Valid xlsx JSON with single sheet
- **WHEN** the parsed JSON contains one sheet with rows of data
- **THEN** a valid `.xlsx` file SHALL be generated with correct sheet name, headers, and row data

#### Scenario: Valid xlsx JSON with multiple sheets
- **WHEN** the parsed JSON contains multiple sheets
- **THEN** the generated `.xlsx` file SHALL contain all sheets with their respective names and data

### Requirement: Download button and filename
A download button SHALL appear below the message containing the xlsx code block. The filename SHALL follow the pattern `{original_filename}_aangepast_{YYYYMMDD_HHmm}.xlsx`. If no original filename is available, use `export_aangepast_{YYYYMMDD_HHmm}.xlsx`.

#### Scenario: Download with known original filename
- **WHEN** the user had attached a file named `budget.xlsx` and the assistant produces an xlsx block
- **THEN** the download button SHALL offer a file named `budget_aangepast_{YYYYMMDD_HHmm}.xlsx`

#### Scenario: Download without original filename
- **WHEN** no original Excel file was attached and the assistant produces an xlsx block
- **THEN** the download button SHALL offer a file named `export_aangepast_{YYYYMMDD_HHmm}.xlsx`

#### Scenario: User clicks download
- **WHEN** the user clicks the download button
- **THEN** the browser SHALL download the generated `.xlsx` file
