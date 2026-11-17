---
name: scheduled-agent-brief
description: >-
  Sends a precise recurring-brief instruction to the VisionMakers agent and
  confirms the stored message matches it. Use when the user wants a morning
  brief, a scheduled agent, a weekday scan, or rules for what may be pre-staged.
---

# Scheduled agent brief

Scheduled agents on the Intelligence Layer run a recurring workflow and pre-stage trades. Pre-stage is not execution. Nothing moves until the user signs in the app. This skill writes the rule clearly, sends it once, and checks that the transcript stored those words.

## Write the instruction first

Use the user's cadence, chain or market, and constraints. Fill only what they said. Ask for a missing cadence or market before sending. The message should read like this:

```text
Every <cadence>, brief me on <market>.
Include: discovery, sentiment shift, and smart-money flow.
Pre-stage a swap only when all of these are true:
- <rule the user stated>
- <rule the user stated>
Leave everything else as a note.
Do not execute. I will sign in the app.
```

Show this text to the user when you had to invent a rule they did not state. Send without another confirmation when they already dictated the rules.

## Steps

1. Call `send_message`. Pass `chatId` if they named a thread. Otherwise omit it and start a chat whose first line is the instruction. The first line becomes the title.
2. Keep the returned `chatId`.
3. Call `get_chat` on that id.
4. Find the latest user message. If its content is not the instruction you sent, say the stored text differs and quote both. Do not send a correction unless the user asks.
5. Tell them delivery and arming are finished in the VisionMakers app. A daily agent can be delivered in Telegram from there. MCP does not run the schedule and does not sign the pre-staged swaps.

## Report

```text
Stored: yes | differs
chatId: <id>
Cadence: <cadence>
Market: <market>
Pre-stage only if: <rules>
Next: open this chat in the app to arm the schedule
```

One `send_message`. On `SCOPE_DENIED` or `CHAT_FAILED`, show the code and keep the unsent instruction in the reply so the user can paste it on the website.
