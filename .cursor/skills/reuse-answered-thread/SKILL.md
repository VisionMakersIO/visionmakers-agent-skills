---
name: reuse-answered-thread
description: >-
  Answers from an existing VisionMakers transcript when the question was
  already asked. Use when the user repeats a token, narrative, wallet, or
  market question, or says the agent already covered it.
---

# Reuse an answered thread

A repeated question should come from the transcript the account already has. `send_message` is the fallback, and only when the user still wants a fresh turn.

## Steps

1. Call `list_chats` with `limit` 30.
2. Keep up to three chats whose `title` shares the token, chain, wallet, narrative, or question the user just asked. Prefer a newer `lastMessageAt` when several match.
3. Call `get_chat` on each match.
4. Read assistant messages. A usable answer states a conclusion and does not say the record was missing, stale, or unknown.
5. When a usable answer exists, answer the user from it. Quote the conclusion in the agent's words. Name the chat `title` and `id`, and the message time.
6. Stop. Do not call `send_message`.

## When nothing usable exists

Say which chats you opened and why they do not answer it (no match, or the agent said the data was missing).

Call `send_message` only if the user already asked you to ask the agent. Pass `chatId` when one of the opened chats is the right ongoing thread. Omit `chatId` when the topic is new. If they did not ask you to spend a message, ask once whether to open a turn.

## Rules

- Never invent a `chatId`. Use an `id` returned by `list_chats` or `send_message`.
- A read-only key is enough for the reuse path. If a later `send_message` returns `SCOPE_DENIED`, deliver the transcript finding and stop.
- Signing and payment stay in the VisionMakers app. If the old answer describes an armed order, say it still needs a signature there.
