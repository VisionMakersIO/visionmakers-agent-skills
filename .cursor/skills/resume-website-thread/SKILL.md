---
name: resume-website-thread
description: >-
  Continues a VisionMakers chat that started on the website, using the same
  chat id. Use when the user says to continue, pick up, or resume a thread,
  or mentions the website agent.
---

# Resume a website thread

Website chats and MCP chats are one list. Continuing means `send_message` with the existing `chatId`. Omitting `chatId` starts a second thread and splits the record.

## Steps

1. Call `list_chats` with `limit` 20.
2. Choose the chat:
   - The user named a topic: the `title` that matches it. If two match, take the newer `lastMessageAt` and say you did.
   - The user said "the last one" or did not name a topic: the first row. The list is newest activity first.
3. Call `get_chat` on that `id`.
4. Show two sentences: the last user message and the last assistant message, plus the `title` and `id`.
5. Send the next turn only when the user already wrote the next question. Call `send_message` with that `chatId` and their question unchanged in substance.
6. Return the reply and the same `chatId`.

## When the preview is the wrong thread

Stop after step 4 and ask which chat. Do not send.

## Rules

- A read-only key can preview and cannot continue. On `SCOPE_DENIED`, give the preview and say the key is read-only.
- If tool activity on the reply names a control or a plan, tell the user that control is confirmed in the VisionMakers app. The MCP reply is not a broadcast.
- On `INVALID_CHAT` or `CHAT_NOT_FOUND`, list again. Do not reuse the failed id.
