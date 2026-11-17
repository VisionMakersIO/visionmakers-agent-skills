---
name: claim-grounding-verdict
description: >-
  Runs a two-turn VisionMakers check and returns a verdict of grounded,
  partial, or unknown. Use when the user pastes a market claim, rumor, or
  "is this real" question about a token, wallet, listing, or narrative.
---

# Claim grounding verdict

Two messages, one new chat. The first forces a verdict. The second asks what would change it. `get_chat` is the check that both turns were stored before you report.

## Turn 1

Call `send_message` with the user's claim quoted, and this ask:

```text
Verdict this claim as grounded, partial, or unknown.
Grounded means every factual part traces to a record you can name.
Partial means some parts trace and some do not.
Unknown means the records are missing or stale.
Name the missing record. Do not fill the gap with a guess.
Do not arm an order.
```

Omit `chatId`. Keep the id from the result.

If this call errors, show the code and stop. Do not open a second chat.

## Turn 2

Call `send_message` with the same `chatId` and this message:

```text
What single new fact would change this verdict?
```

If this call errors, report turn 1's reply, the error code, and the `chatId`. Do not send a third message.

## Turn 3 is a read

Call `get_chat` on that `chatId`. Base the report on the stored assistant messages, so a dropped reply is visible.

```text
Verdict: grounded | partial | unknown
Evidence: <records the agent named>
Would change it: <the single fact from turn 2>
chatId: <id>
```

Use the verdict word the agent used. If the agent never chose one of the three words, the verdict is `unknown` and the evidence line says the agent did not choose.

Do not add a trade recommendation. If tool activity shows a control or an armed order, lead with that and point the user to the VisionMakers app to cancel it. The check was not supposed to stage a swap.
