# Setup Notes

## Requirements

- n8n
- Telegram bot(s)
- Google Sheets
- Google Gemini API access

## Import

Import both JSON files from `workflows/` into n8n.

## Configure

Replace the sanitized placeholders with credentials and environment-specific values:

- Telegram credentials
- Google Sheets credentials
- Gemini credentials
- Google Sheet ID
- Telegram chat IDs

## Testing

Test these paths before activation:

1. Photo + caption submission
2. Photo without caption
3. Text without photo
4. AI verification / low-risk path
5. AI anomaly / manual-review path
6. Grant approval
7. Grant hold/rejection
8. Grant override

The repository exports are intended as a public portfolio representation and require environment-specific configuration.
