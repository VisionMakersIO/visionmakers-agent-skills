---
name: check-message-budget
description: >-
  Reads VisionMakers account usage before a new agent message. Use when the
  user asks how many messages are left, whether MCP chat will spend quota, or
  before send_message when the free allowance or subscription may be tight.
---

# Check message budget

`send_message` spends the same free allowance and paid subscription as the website. `list_chats` and `get_chat` do not. Read the budget before spending a message.

## Steps

1. Call `list_chats` with `limit` 1.
2. If `chats` is empty, say that usage appears on a transcript and stop. Do not call `send_message` to create a probe chat.
3. Call `get_chat` with that chat's `id`.
4. Read `usage`:
   - `dailyMessagesSent` and `dailyMessageLimit`
   - `messagesInChat` and `messagesPerChatLimit`
   - `freeQueriesUsed` and `freeQueryLimit`
   - `subscriptionActive`, `subscriptionExpiresAt`, `subscriptionMessagesUsed`, `subscriptionMessageLimit`

## How to judge it

Remaining on a cap is limit minus used. The budget is the smallest remaining cap.

When `subscriptionActive` is true, the subscription cap is the one that matters for paid use, and the free counters are historical. When it is false, the free cap is the one that matters.

A follow-up needs a second message. Treat the budget as comfortable only when at least 2 remain on every cap that applies.

## If the user also wanted a question sent

Send it only when the budget is comfortable. Call `send_message` with their question. If they named a thread, pass its `chatId`. Otherwise omit `chatId`.

When the budget is short, report the numbers and stop. Tell them the next message has to wait for the daily reset, a new chat under the per-chat cap, or a subscription renewal in the VisionMakers app. Payment stays in the app.

## Report

```text
Daily: <used> / <limit> (<remaining> left)
This chat: <used> / <limit> (<remaining> left)
Plan: <free remaining of freeQueryLimit | subscription remaining until subscriptionExpiresAt>
Send now: yes | no — <one reason>
```

On `SCOPE_DENIED`, `CHAT_NOT_FOUND`, or any other tool error, show the code and stop.
