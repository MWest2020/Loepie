## ADDED Requirements

### Requirement: Detect xlsx JSON code blocks
The frontend SHALL detect fenced code blocks tagged `xlsx` in rendered assistant messages and attempt to parse the JSON content.

#### Scenario: Valid xlsx code block
- **WHEN** the assistant response contains a fenced code block tagged `xlsx` with valid JSON
- **THEN** the application SHALL parse the JSON and show a download button below the message

#### Scenario: Malformed xlsx code block
- **WHEN** the assistant response contains a fenced code block tagged `xlsx` with invalid JSON
- **THEN** the application SHALL show an inline error message instead of a download button

### Requirement: Excel file generation
The frontend SHALL generate a downloadable `.xlsx` file from the parsed JSON. The JSON structure SHALL follow: `{"sheets": [{"name": "SheetName", "data": [[row], [row]]}]}`.

#### Scenario: Single sheet
- **WHEN** the JSON contains one sheet
- **THEN** a valid .xlsx file SHALL be generated with correct sheet name and data

#### Scenario: Multiple sheets
- **WHEN** the JSON contains multiple sheets
- **THEN** the generated .xlsx SHALL contain all sheets with their names and data

### Requirement: Download and save to Nextcloud
The application SHALL offer two actions for generated Excel files:
1. Download to local machine (browser download)
2. Save to Nextcloud storage (via the Nextcloud save dialog)

#### Scenario: User clicks download
- **WHEN** the user clicks the download button
- **THEN** the browser SHALL download the .xlsx file

#### Scenario: User clicks save to Nextcloud
- **WHEN** the user clicks the save-to-Nextcloud button
- **THEN** the Nextcloud file save dialog SHALL open, allowing the user to choose a destination folder, and the file SHALL be saved there

### Requirement: Filename convention
The generated filename SHALL follow the pattern `{original_filename}_aangepast_{YYYYMMDD_HHmm}.xlsx`. If no original Excel file was attached, use `export_aangepast_{YYYYMMDD_HHmm}.xlsx`.

#### Scenario: Original file was budget.xlsx
- **WHEN** an xlsx block is generated and the user had attached `budget.xlsx`
- **THEN** the filename SHALL be `budget_aangepast_{YYYYMMDD_HHmm}.xlsx`

#### Scenario: No original Excel attached
- **WHEN** an xlsx block is generated without a prior Excel attachment
- **THEN** the filename SHALL be `export_aangepast_{YYYYMMDD_HHmm}.xlsx`
