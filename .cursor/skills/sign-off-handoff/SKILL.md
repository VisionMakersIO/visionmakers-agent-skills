---
name: sign-off-handoff
description: >-
  Finds a pre-staged VisionMakers action and points the user to the app to
  sign it. Use when a chat mentions an armed order, a swap ready to sign, a
  payment, a card spend, or the user asks how to finish, broadcast, or pay.
---

# Sign-off handoff

MCP can read and talk. The website is where a swap, a conditional order, a distribution, or a payment gets a signature. This skill extracts the pending action and stops at the app door.

## Steps

1. Resolve the chat. Use the `chatId` already in context, or `list_chats` then `get_chat` on the matching `title`.
2. From the assistant messages and any tool notes in message metadata, copy the pending action in the agent's words: asset, size, chain, trigger or route, and whether it is armed, pre-staged, or only described.
3. Tell the user to open that same chat at https://app.visionmakers.io and sign there. Include the `chatId` so they can match the thread.
4. Stop.

## When the restatement is ambiguous

If the transcript names two different actions, or never states size and asset, call `send_message` once on that `chatId`:

"Restate the pending action in one sentence: asset, size, chain, and trigger. Do not execute it."

Then hand off with that sentence. Do not send a second message.

## Hard boundaries

- Do not ask for a seed phrase, private key, signature, or wallet password.
- Do not tell the user the trade, airdrop, or payment already happened because the agent described it.
- Armed is not executed. A condition can still be met in a way the user did not expect. Say that when the action is a conditional order.
- On `SCOPE_DENIED`, deliver whatever the transcript already says and point to the app. The handoff does not require a new message.
