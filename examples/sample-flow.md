# Example End-to-End Flow

1. A field trainer sends a field photo with a caption containing the claimed attendance and program tag.
2. The Field Audit workflow validates the submission.
3. Gemini analyzes the image and returns structured observations.
4. JavaScript normalizes the result and determines the audit status.
5. The audit is stored in Google Sheets.
6. A reviewer receives a Telegram message with the appropriate decision options.
7. The reviewer approves, holds, rejects, or overrides the claim.
8. The Grant Office workflow updates the audit record.
9. An approved decision creates a grant transaction record.
10. Telegram notifications communicate the final status.
