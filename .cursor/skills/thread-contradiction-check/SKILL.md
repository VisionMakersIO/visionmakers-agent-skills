---
name: thread-contradiction-check
description: >-
  Compares two VisionMakers chats about the same subject and lists agreements
  and conflicts. Use when answers disagree, the user asks which thread to
  trust, or a token was researched more than once. Works with a read-only MCP key.
---

# Thread contradiction check

Two chats can drift. This skill reads both and does not spend a message, so a **Read chats only** key is enough.

## Steps

1. Call `list_chats` with `limit` 40.
2. Select the two chats whose titles best match the subject the user named. Break ties with the newer `lastMessageAt`.
3. If fewer than two match, say so, name the one that did match, and stop.
4. Call `get_chat` on both.
5. Compare assistant claims about the same fact: price level, liquidity, holder concentration, route, buyer count, wallet flow, or a missing-data admission.

## Report

Label the newer transcript by `lastMessageAt`. Newer is not automatically right. A later reply that says the record is missing overrides an earlier confident number.

```text
Subject: <token, wallet, or narrative>
Newer: <title> · <id> · <lastMessageAt>
Older: <title> · <id> · <lastMessageAt>
Agree:
- <claim both chats support>
Conflict:
- <fact>: newer said "<quote>" · older said "<quote>"
Missing in either chat:
- <what the agent refused to assert>
```

Quote the conflicting sentences. Do not average two numbers into a third.

Do not call `send_message`. If the user wants the agent to reconcile the conflict, that is a separate turn: `lineage-follow-up` on the newer `chatId`, quoting both sentences.
