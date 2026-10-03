# Architecture

## System Overview

The NGO Field Audit system is split into two n8n workflows:

1. **Field Audit Workflow** — handles field submissions, validates required evidence, performs AI-assisted image analysis, processes the result with JavaScript, and stores the audit record.
2. **Grant Office Workflow** — handles the reviewer's decision, updates the audit record, creates a grant transaction when appropriate, and sends notifications.

The separation keeps field verification and grant-office decision handling as distinct workflow responsibilities.

## 1. Field Audit Workflow

```text
Telegram Trigger
       |
       v
     Switch
   /    |       /     |      Complete  Missing Caption  Missing Photo
   |          |              |
   v          v              v
Gemini     Telegram        Telegram
Analysis     Alert           Alert
   |
   v
JavaScript
Processing
   |
   v
Google Sheets
Field_Audits
   |
   v
      If
    /      true    false
   |        |
   v        v
Telegram   Telegram
Result     Anomaly/Review
Notification
```

### Responsibilities

- Receive field submissions through Telegram.
- Check whether the required photo and caption are present.
- Route incomplete submissions to appropriate alerts.
- Send complete submissions to Gemini for image analysis.
- Process the AI response with JavaScript.
- Store the structured audit result in Google Sheets.
- Route the result to the appropriate notification/review path.

## 2. Grant Office Workflow

```text
Telegram Callback
       |
       v
 Answer Query
       |
       v
 JavaScript
       |
       v
 Google Sheets
 Update Audit
       |
       v
      If
    /      true    false
   |        |
   v        v
Append    Notify
Grant     Grant Officer
Txn
   |
   v
Notify Trainer /
In-charge
```

### Responsibilities

- Receive the grant officer's Telegram decision.
- Parse the callback data.
- Update the corresponding audit record.
- Create a grant transaction for the relevant approved path.
- Notify the grant officer and trainer/in-charge of the outcome.

## Data Flow

```text
Field Evidence
      |
      v
Telegram
      |
      v
n8n Validation
      |
      v
Gemini Image Analysis
      |
      v
JavaScript Business Logic
      |
      v
Google Sheets
      |
      v
Human Review
      |
      v
Grant Decision
      |
      +------------------+
      |                  |
      v                  v
Grant Transaction     Notification
```

## AI and Custom-Code Boundary

The system deliberately separates AI interpretation from deterministic processing.

**Gemini** is used for image-based analysis and returns structured observations.

**JavaScript** handles deterministic processing such as:

- Parsing the model response
- Extracting claimed attendance
- Comparing claimed and observed values
- Determining status
- Generating audit identifiers
- Mapping program information
- Preparing structured spreadsheet data

This makes the workflow easier to inspect and reduces dependence on the LLM for business-rule execution.

## Human-in-the-Loop Design

The AI result is not treated as the final funding decision.

The workflow routes the audit to a human reviewer, who can make the appropriate grant decision. This creates a control point before an approved grant transaction is recorded.

```text
AI-assisted verification
          |
          v
     Human review
          |
     +----+----+
     |         |
  Approve   Hold/Reject
     |
     v
Transaction
```

## External Services

| Service | Role |
|---|---|
| n8n | Workflow orchestration |
| Telegram | Submission, review actions, notifications |
| Google Gemini | Image analysis |
| JavaScript | Custom business logic and transformation |
| Google Sheets | Audit and transaction records |

## Repository Artifacts

- `workflows/field-audit-workflow.json` — sanitized Field Audit n8n workflow export
- `workflows/grant-office-workflow.json` — sanitized Grant Office n8n workflow export
- `architecture/field-audit-workflow.png` — workflow screenshot
- `architecture/grant-office-workflow.png` — workflow screenshot

The JSON exports are sanitized portfolio artifacts and require environment-specific credentials and identifiers before they can be executed.
