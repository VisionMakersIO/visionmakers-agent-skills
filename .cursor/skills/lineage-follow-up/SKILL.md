---
name: lineage-follow-up
description: >-
  Asks the VisionMakers agent, in the same chat, to attach lineage to a
  specific claim or to say the record is missing. Use when a reply lacks
  sources or the user asks where a figure, route, or risk flag came from.
---

# Lineage follow-up

The agent is supposed to trace factual claims to a record. This skill spends one message to force that trace on one claim, inside the chat that made it.

## Find the chat

Use the `chatId` from the conversation. If you do not have one, call `list_chats` with `limit` 20, pick the thread whose `title` matches the claim, then `get_chat` and confirm the claim is in an assistant message.

Do not open a new chat. Lineage belongs next to the original answer.

## The one message

Call `send_message` with that `chatId`. The message quotes the claim and asks for two things only:

- the record the claim came from, named specifically enough to check
- or a retraction, plus which record is missing

Ask for nothing else. Do not add a new asset, a trade, or a second claim.

## Report

```text
Claim: <quoted>
Result: kept | retracted | still unsupported
Lineage: <what the agent named, or "none given">
chatId: <same id>
```

`kept` means the follow-up names a record. `retracted` means the agent withdrew the claim. `still unsupported` means the follow-up repeated the claim without a record.

One follow-up. If `send_message` errors, show the code and stop.
