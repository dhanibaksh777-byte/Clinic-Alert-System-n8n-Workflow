# Clinic Inquiry Intake (n8n)

An n8n workflow that receives a patient inquiry from a website form, validates it, classifies it with an LLM, and alerts clinic staff.

## Flow

Webhook (POST) → IF (validation) → LLM classification → clean output → alert

1. **Webhook:** receives JSON (`name`, `contact`, `message`, `preferred_time`).
2. **IF:** checks that `name` and `message` are not empty. Invalid requests go to the false branch.
3. **HTTP Request (LLM, OpenRouter):** classifies the message as `appointment`, `question` or `emergency`, using a system prompt that allows one word only.
4. **Edit Fields:** trims the model output into a clean `category` field.
5. **HTTP Request (Discord webhook):** sends the alert, e.g. `New inquiry: emergency`.

## Design notes

- The alert recipient and message format are fixed. The LLM only classifies, it never decides who gets contacted.
- API keys are stored as n8n credentials, not in the workflow file. The Discord webhook URL in this export is a placeholder.

## Limitations

- Built and tested with fake data only. Not production-ready for real patient data (no GDPR/hosting setup yet).
- The false branch does not return an error response yet.
- No retries or error alerts yet.
- Uses a free LLM model for testing.

## Run it

Import the JSON into n8n, set your own LLM API credential and Discord webhook URL, then send a test POST to the webhook URL.
