---
name: inbox-triage
description: >-
  Reviews recent VisionMakers chats and marks each as answered, waiting on a
  fact, or waiting on a signature. Use when the user asks what is open,
  unfinished, waiting, or which agent thread to pick up.
---

# Inbox triage

The account has one inbox across the website and MCP. Triage reads it. MCP cannot delete or rename chats, so this skill never offers to.

## Steps

1. Call `list_chats` with `limit` 50. Raise it toward 100 only if the user asked for a full pass and the first page is all recent.
2. Choose up to three chats to open with `get_chat`:
   - the user named a topic: the matches, newest `lastMessageAt` first
   - otherwise: the three newest
3. Read the last user message and the last assistant message in each.

## Status

Assign one status:

- **Answered.** The assistant closed the question and did not leave a signature, a missing record, or an open offer.
- **Waiting on a fact.** The assistant said the data was missing, stale, or unknown.
- **Waiting on a signature.** The assistant armed an order, pre-staged a swap, or described a control the user still has to approve in the app.

## Report

One line per opened chat, newest first. Then one recommended next step.

```text
1. <title> · <status> · <id>
   Last ask: <one line>
2. ...
3. ...
Next: <resume this id | sign in the app | no open work>
```

Do not call `send_message`. If the user then picks a row, `resume-website-thread` or `sign-off-handoff` is the follow-on. Mention the unopened count (`list` length minus the ones you read) so a long inbox is not mistaken for three chats.
