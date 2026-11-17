---
name: open-research-thread
description: >-
  Opens one new VisionMakers research chat and returns the grounded reply
  with its chat id. Use when the user wants a fresh question about a token,
  narrative, wallet, or market and did not name an existing chat.
---

# Open a research thread

One question, one new chat, one reply. A second message in the same turn spends another unit of quota and belongs to `lineage-follow-up` or `claim-grounding-verdict`.

## Write the message

Keep the user's asset, chain, size, and time window. Add one sentence: answer from current records, and say "I do not have that" where a fact is missing. Leave out trade instructions, order arming, and wallet signing.

## Steps

1. Call `send_message` with that `message`. Omit `chatId`.
2. Keep the `chatId` from the tool result. The reply text also ends with `chatId:`.
3. Give the user the reply, then the `chatId` on its own line so a later skill can resume it.
4. Stop.

## After the reply

If tool activity lists a control, a plan, or an armed order, add one line: that action is confirmed in the VisionMakers app, and this chat did not broadcast it.

If the reply says the data is missing, report that as the result. Do not rephrase it into a guess, and do not send a follow-up unless the user asks.

## Errors

`MESSAGE_REQUIRED`, `CHAT_FAILED`, `CHAT_UNAVAILABLE`, and `SCOPE_DENIED` end the skill. Show the code. A read-only key cannot open a thread. Point the user at a **Chat with the agent** key in MCP access.
