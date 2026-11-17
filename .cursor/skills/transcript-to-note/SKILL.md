---
name: transcript-to-note
description: >-
  Saves one VisionMakers chat transcript as a local markdown note with open
  loops. Use when the user wants a copy, an export, notes, or a record of a
  chat outside the website.
---

# Transcript to note

`get_chat` returns the full transcript. Write it locally so the user can keep it after the session. This does not spend a message.

## Steps

1. Resolve `chatId` from context, or call `list_chats` and match `title`.
2. Call `get_chat`.
3. Write one markdown file under `visionmakers-notes/` in the workspace. Slug the chat title for the filename. If a file with that name exists, append the first 8 characters of the chat id. Do not overwrite.
4. Tell the user the path and the `chatId`.

## File contents

```markdown
# <title>

- chatId: `<id>`
- status: <status>
- updated: <updatedAt>
- messages: <messageCount>

## Transcript

### <role> · <createdAt>

<content>

## Open loops

- <a question the assistant left as missing or unknown>
- <an action that still needs a signature in the VisionMakers app, quoted>
```

Preserve role, time, and content in order. Keep the agent's "I do not have that" sentences. They are part of the record.

## Redaction

Before writing, remove any `vmcp_` token, seed phrase, or private key if one appears in message content. Replace it with `[redacted]`. Do not put the MCP bearer token in the note.

Do not call `send_message`. On `CHAT_NOT_FOUND` or `INVALID_CHAT`, list chats and ask which id to export.
