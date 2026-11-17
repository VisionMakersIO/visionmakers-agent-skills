# VisionMakers Agent skills

Cursor skills for the VisionMakers Agent MCP. Each skill is a small workflow the agent follows when you research, resume a chat, or hand a prepared action back to the app.

Vision Makers is the intelligence layer of Web3: one conversational surface for research, discovery, trading, and automation on-chain. The [docs](https://docs.visionmakers.io/) publish 630,000+ registered people, 110,000+ transactions, and about nine months in production, built with no paid acquisition.

You ask in plain language. The platform answers from on-chain, social, and market data together. When an answer implies a trade, the trade sits in the same view, and it executes from your own wallet after you sign.

## The agent

The agent is that conversational surface. It is the same agent on the website and over MCP. A thread started in one place is the same thread in the other.

An instruction resolves in six steps, as the [Intelligence Layer](https://docs.visionmakers.io/platform/intelligence-layer) documents them:

1. Parse the intent, asset, size, and trigger.
2. Ground it: the asset has to exist on a supported network, liquidity has to be routable, and the balance has to cover it.
3. Reason from records in the intelligence core, with lineage attached.
4. Explain at the level of expertise you have shown.
5. Offer one confirmable control.
6. Wait for your signature. Only then does anything execute.

The assistant arms orders. You fire them. A pre-staged swap waits for your signature and can be cancelled before you sign. There is no copy trading and no automated replication of another wallet.

Factual claims are supposed to trace to a record. Where data is missing or stale, the answer says so. That failure mode ("I do not have that") is the one these skills are written to keep.

## The MCP

MCP is how Cursor and Claude talk to that same agent. Chats, tools, and the subscription stay on your VisionMakers account. Signing, broadcasting, and payment stay in the app.

Create a key at [app.visionmakers.io](https://app.visionmakers.io): sign in with your wallet, open the wallet menu, then **MCP access**. The key is shown once. It starts with `vmcp_`. You can hold five active keys. Each key is either **Chat with the agent** or **Read chats only**, and it expires after 7, 30, 90, or 365 days.

The app copies a client config in this shape. Use the URL from that screen. The placeholder below is not a live host.

```json
{
  "mcpServers": {
    "visionmakers": {
      "url": "https://<the URL copied from MCP access>",
      "headers": {
        "Authorization": "Bearer vmcp_…"
      }
    }
  }
}
```

The server exposes three tools.

| Tool | What it does | Spends a message |
| --- | --- | --- |
| `send_message` | Sends one message and waits for the reply. Pass `chatId` to continue a thread. Omit `chatId` to start a new one. | Yes. The free allowance and the paid subscription are shared with the website. |
| `list_chats` | Lists this account's chats, including threads started on the website. `limit` is 1–100 and defaults to 20. Newest activity first. | No |
| `get_chat` | Reads one transcript: the chat, its messages, and current usage. `chatId` is a UUID from `list_chats` or `send_message`. | No |

A read-only key can call `list_chats` and `get_chat`. `send_message` returns `SCOPE_DENIED`.

`get_chat` usage is the budget. The fields that matter are `dailyMessagesSent`, `dailyMessageLimit`, `messagesInChat`, `messagesPerChatLimit`, `freeQueriesUsed`, `freeQueryLimit`, `subscriptionActive`, `subscriptionMessagesUsed`, and `subscriptionMessageLimit`.

A `send_message` reply ends with the `chatId`. Tool activity on that reply is what the agent did inside the turn. A control or a plan there still needs you in the app.

## Skills

These live in [`.cursor/skills`](.cursor/skills). Open this repository in Cursor and the skills load with the project. To use them everywhere, copy the skill folders into your personal skills directory (`~/.cursor/skills` on macOS and Linux, `%USERPROFILE%\.cursor\skills` on Windows).

| Skill | Tools | What you get |
| --- | --- | --- |
| [check-message-budget](.cursor/skills/check-message-budget/SKILL.md) | `list_chats`, `get_chat` | Remaining daily, per-chat, free, and subscription messages before anything is sent |
| [reuse-answered-thread](.cursor/skills/reuse-answered-thread/SKILL.md) | `list_chats`, `get_chat` | An answer from a transcript you already paid for |
| [resume-website-thread](.cursor/skills/resume-website-thread/SKILL.md) | `list_chats`, `get_chat`, `send_message` | The website thread, continued under the same id |
| [open-research-thread](.cursor/skills/open-research-thread/SKILL.md) | `send_message` | One new grounded research chat, with its id kept |
| [lineage-follow-up](.cursor/skills/lineage-follow-up/SKILL.md) | `get_chat`, `send_message` | A claim kept, retracted, or marked unsupported |
| [route-and-risk-brief](.cursor/skills/route-and-risk-brief/SKILL.md) | `send_message` | Winning route plus the five risk dimensions, with no armed order |
| [sign-off-handoff](.cursor/skills/sign-off-handoff/SKILL.md) | `get_chat`, sometimes `send_message` | The pending action, in the agent's words, ready to sign in the app |
| [thread-contradiction-check](.cursor/skills/thread-contradiction-check/SKILL.md) | `list_chats`, `get_chat` | Agreements and conflicts across two chats. Works on a read-only key |
| [inbox-triage](.cursor/skills/inbox-triage/SKILL.md) | `list_chats`, `get_chat` | Recent chats marked answered, waiting on a fact, or waiting on a signature |
| [transcript-to-note](.cursor/skills/transcript-to-note/SKILL.md) | `get_chat` | A local markdown copy plus the loops still open |
| [scheduled-agent-brief](.cursor/skills/scheduled-agent-brief/SKILL.md) | `send_message`, `get_chat` | A recurring brief with explicit pre-stage rules, confirmed stored |
| [claim-grounding-verdict](.cursor/skills/claim-grounding-verdict/SKILL.md) | `send_message`, `get_chat` | Grounded, partial, or unknown, plus the fact that would change it |

Name the skill, or describe the job in those words. Cursor loads the matching skill.

## Products

Nine products share one account, one balance, and one intelligence core. Each one produces signal the others consume. The suite is written up in the [product section of the whitepaper](https://docs.visionmakers.io/whitepaper/products) and in the platform docs linked below.

### Intelligence Layer

The co-pilot on top of execution. It prices the route, explains which path won and by how much, interprets risk, and can arm conditional orders. Route choice is output after slippage, gas, and fill probability. Risk is five visible dimensions: liquidity, holder concentration, contract flags, deployer behaviour, and flow anomalies. There is no single safety score, because a composite number hides the distinction that matters.

Conditional orders are price triggers. They stay listed until they fire or you cancel them. They are not stop-losses: a gap or a liquidity collapse can fill far from the trigger. Scheduled agents chain this into recurring work, such as a weekday morning brief that pre-stages swaps and moves nothing until you approve. Every run is logged: what ran, what it found, what it pre-staged, and what you signed.

[Intelligence Layer](https://docs.visionmakers.io/platform/intelligence-layer)

### MOFO & Mindshare

The intelligence product. The two halves used to be separate and now answer one question from opposite directions.

Mindshare works top down, from narratives and sectors to tokens: trending tokens ranked by attention rather than raw volume, narrative heatmaps, KOL dashboards scored by historical accuracy, whale volume, scored news, on-chain flow, and discovery of new pairs and migrations.

MOFO works bottom up, from wallets to the tokens they move into. You follow addresses. Rankings use realised profit over a window you choose. A streaming alert feed filters by order size and market cap, and it can skip wallets whose holdings you already share. An alert is something to consider. The swap, if you take one, goes through vDEX from your wallet.

Neither half trades for you.

[MOFO & Mindshare](https://docs.visionmakers.io/platform/mindshare)

### vDEX

Non-custodial swaps, charting, and portfolio in the same view as the research. You connect MetaMask, Trust Wallet, OKX Wallet, Binance Wallet, or any WalletConnect wallet. Vision Makers does not hold keys. There is no deposit, no platform balance, and no withdrawal. Assets stay in your wallet, and every swap executes from it on your signature.

Routing compares paths across integrated venues, including Uniswap and OKX DEX, and shows the decision: expected output, slippage, and which route won. Swaps are irreversible. Quoted output is an estimate. Approvals persist until you revoke them.

[vDEX](https://docs.visionmakers.io/platform/vdex)

### Vision Makers Pad

Launch infrastructure for ICOs, IDOs, and initial token auctions, with KYC and allocation records written on-chain. Tier eligibility comes from holdings and staking duration, so a position held for months outweighs one opened the day before a sale. Exact thresholds are a governance parameter.

A sale is register, then commit, then claim on the project's own vesting schedule. Missing a published window means missing the sale. Beyond curated sales, the pad surfaces new pairs, tokens about to migrate, and tokens that just migrated, each with buyer distribution so one wallet is visible against a broad launch.

A listing is not an endorsement. Early-stage tokens often fail completely.

[Vision Makers Pad](https://docs.visionmakers.io/platform/pad)

### vMakers

Trading and analytics competitions with prize pools. Entry and prizes settle in the platform's own asset. Scoring uses realised performance over the window, not unrealised value at the close. Analytics competitions score the accuracy of calls, so people can compete without putting capital at risk. Comparable conditions feed wallet ranking in MOFO & Mindshare.

Entry fees are not refundable once the window opens. Multi-accounting, wash trading, and coordinated manipulation forfeit prizes.

[vMakers](https://docs.visionmakers.io/platform/vmakers)

### Vision Disperse

Token distribution to a whole address list in one transaction: airdrops, payroll, community rewards, contributor payouts. You upload the list, review malformed addresses, duplicates, zero amounts, and the total, approve a spend scoped to that total, and sign once. Very large lists split across several transactions because of block gas limits.

Validation checks form. It cannot know that a well-formed address is the right recipient. Distribution is irreversible. Test with a small batch first.

[Vision Disperse](https://docs.visionmakers.io/platform/disperse)

### Neobank

A non-custodial, blockchain-linked card that spends an on-chain balance directly. The wallet is the account. The balance you see here is the balance you see in vDEX. You choose which asset a payment draws from, set per-transaction, daily, and monthly limits, and freeze the card without cancelling it.

This is not a bank account, a deposit, or e-money. Balances are not insured. There is no overdraft, no credit, and no interest. If the asset price falls, spending power falls with it. Issuance requires KYC, which links your identity to the addresses the account draws from.

The loop the platform is built around: find it in MOFO & Mindshare, judge it in the Intelligence Layer, trade it on vDEX, spend it here.

[Neobank](https://docs.visionmakers.io/platform/neobank)

### Vision VPN

Encrypted connectivity for people who transact on a public ledger. An observer who can see your network traffic can correlate a broadcast with your IP. The tunnel encrypts the path to the exit, so the network provider sees a tunnel, and it is the layer that matters on shared Wi-Fi.

Chain data stays public and permanent. A VPN does not hide transactions, undo a KYC link, or protect you from a malicious signature. Connect before broadcasting when network-level correlation is part of your threat model.

[Vision VPN](https://docs.visionmakers.io/platform/vpn)

### VM AI Hub

The Telegram Mini App, approved in the Telegram Apps Center. It is the onboarding funnel: account and wallet setup, agent training, quests, mindshare and price checks, smart-money alerts, and referrals, without an install. Progress counts toward the same account as the web platform. The Hub marketplace settles in TON. The rest of the platform's contracts are on EVM-compatible networks.

The official Hub does not ask for a seed phrase, does not direct-message support, and does not move you to a backup channel. Confirm you are in the Apps Center listing.

A separate Telegram trading bot (in-chat charting, execution, portfolio) is a Phase 2 roadmap item. iOS and Android apps are Phase 3. Those phases are published intentions, not commitments. The [roadmap](https://docs.visionmakers.io/whitepaper/roadmap) says Phase 1 is what is live: conversational trading, vDEX, and the Telegram AI Hub.

[VM AI Hub](https://docs.visionmakers.io/platform/ai-hub)

## Where to go next

| If you want to | Go to |
| --- | --- |
| Use the product | [Getting started](https://docs.visionmakers.io/getting-started) |
| Read the whole design | [The whitepaper](https://docs.visionmakers.io/whitepaper/products) |
| Connect this agent to Cursor | [app.visionmakers.io](https://app.visionmakers.io), wallet menu, MCP access |
| Check a specific claim from a chat | The `claim-grounding-verdict` skill |
| See what a chat already decided | The `inbox-triage` skill |

[docs.visionmakers.io](https://docs.visionmakers.io/)
