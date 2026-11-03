# n8n AI Voice Assistant

An n8n workflow that receives an audio upload, transcribes it with ElevenLabs, generates a reply with OpenAI, and returns a spoken response.

## How it works

1. **Webhook** accepts a POST request containing an audio file.
2. **ElevenLabs** transcribes the audio to text.
3. **AI Agent** sends the transcript to **OpenAI GPT-5 mini**.
4. **ElevenLabs** converts the AI response into speech using the configured Roger voice.
5. **Respond to Webhook** returns the generated audio as binary data.

This is a single-turn workflow. It has no conversation memory, connected agent tools, or included browser interface.

## Requirements

- An n8n instance supporting the node types and versions in the workflow.
- The ElevenLabs node package: `@elevenlabs/n8n-nodes-elevenlabs`.
- OpenAI and ElevenLabs API credentials with access and sufficient credit for the configured services.
- An audio file for testing.

## Setup

1. Download [the workflow JSON](workflows/ai-voice-assistant.json) and import it into n8n.
2. If either ElevenLabs node is unavailable, install or enable its node package in your n8n environment.
3. Create an OpenAI credential in n8n and select it in **OpenAI Chat Model**.
4. Create an ElevenLabs credential and select it in both ElevenLabs nodes.
5. Confirm the model and voice are available to your accounts; choose alternatives if needed.
6. Open **Webhook** and copy its test URL. The template path is `ai-voice-assistant`; change it if another workflow uses that path.
7. Start listening for a test event, then send an audio file using the example below.
8. Inspect the execution and confirm the transcription, AI reply, speech output, and returned audio.
9. After successful testing, activate/publish the workflow and use its production webhook URL.

### Test request

Run in Bash or Git Bash, replacing the URL with the exact test URL copied from n8n:

```bash
curl --fail-with-body --request POST \
  'https://YOUR-N8N-HOST/webhook-test/ai-voice-assistant' \
  --form 'audio=@sample.mp3' \
  --output reply.mp3
```

The multipart field must be named **audio** because the transcription node reads that binary property. Let curl set the multipart Content-Type and boundary.

Open the returned file in an audio player. If you change the speech output format, use a matching file extension.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Missing node | Make the ElevenLabs node package available in n8n. |
| Authentication or quota error | Re-select credentials and check provider access/credit. |
| Missing audio input | Use multipart form-data with a file field named `audio`; inspect the Webhook binary output. |
| Empty AI prompt | Confirm the transcription output contains `text`. |
| No audio response | Confirm the speech node produces binary output. In Respond to Webhook, select the matching binary field if your n8n version requires it. |
| Test URL does not respond | Start listening for a test event again and copy the current URL. |

## Privacy and deployment

The published template removes the original credential references, instance metadata, workflow/version identifiers, and original webhook identifier. Add your own credentials inside n8n; never commit API keys or recordings.

The supplied webhook has no authentication configured. Before exposing it publicly, configure authentication or access controls and usage limits to prevent unauthorized API spending. Audio is sent to ElevenLabs, and transcribed text is sent to OpenAI. Review n8n execution-data retention for your use case.

## Validation status

- Workflow JSON parses successfully.
- All connection endpoints reference existing nodes.
- The original six-node processing logic and model/voice configuration are preserved.
- Live execution has **not** been tested from this repository; it requires your n8n instance and credentials.

## Files

- `workflows/ai-voice-assistant.json` — sanitized importable workflow.
- `.gitignore` — excludes local secrets and sample audio.
