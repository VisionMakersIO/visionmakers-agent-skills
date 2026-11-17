---
name: route-and-risk-brief
description: >-
  Asks the VisionMakers agent for the winning swap route and the five risk
  dimensions, and leaves signing in the app. Use when the user wants a
  route, a quote, slippage, gas, fill probability, or a risk read before a trade.
---

# Route and risk brief

vDEX shows the route that wins on output after slippage, gas, and fill probability. The Intelligence Layer shows risk as five dimensions and refuses a single safety score. This skill asks for both and tells the agent not to arm anything.

## The message

Include the user's asset, size, and chain. Ask for:

- the winning venue and route, the expected output, slippage, and how many routes were compared
- liquidity, holder concentration, contract flags, deployer behaviour, and flow anomalies, each stated separately
- a plain-language restatement of what a swap would do

End with this sentence: "Do not arm an order and do not pre-stage a swap."

## Steps

1. If the user named an existing chat, pass its `chatId`. Otherwise omit `chatId` so this brief is its own thread.
2. Call `send_message` once.
3. Read the reply and the tool activity.

## How to present it

Keep the five dimensions separate. Do not invent a safety score, a buy rating, or a "this is safe."

```text
Route: <venue, output, slippage, routes compared>
Liquidity:
Concentration:
Contract flags:
Deployer:
Flow:
Restatement: <the agent's sentence>
chatId: <id>
Signing: in the VisionMakers app, if you choose to trade
```

If the reply or the tool activity mentions an armed order, a sign control, or a plan id, say so at the top and tell the user to review or cancel it in the app before trusting the brief. MCP did not broadcast the trade.

On any tool error, show the code and stop. Do not send a second message to "try the swap."
