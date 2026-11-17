# VisionMakers Agent skills

We built these Cursor skills so our agent can work beside you in the editor. Each skill is a small workflow: research with us, resume a chat you already started, or hand a prepared action back to our app for your signature.

We are Vision Makers, the intelligence layer of Web3. We give you one conversational surface for research, discovery, trading, and automation on-chain. Our [docs](https://docs.visionmakers.io/) record where we are today: 630,000+ registered people, 110,000+ transactions, and about nine months in production, built with no paid acquisition.

You ask us in plain language. We answer from on-chain, social, and market data at the same time. When an answer implies a trade, we put that trade in the same view, and it executes from your own wallet after you sign.

## Our agent

Our agent is that conversational surface. It is the same agent on our website and over MCP. A thread you start in one place is the same thread in the other.

We resolve an instruction in six steps, which we document on the [Intelligence Layer](https://docs.visionmakers.io/platform/intelligence-layer):

1. We parse the intent, asset, size, and trigger.
2. We ground it. The asset has to exist on a network we support, liquidity has to be routable, and your balance has to cover it.
3. We reason from records in our intelligence core, with lineage attached.
4. We explain at the level of expertise you have shown us.
5. We offer one confirmable control.
6. We wait for your signature. Only then does anything execute.

We arm orders. You fire them. A swap we pre-stage waits for your signature, and you can cancel it before you sign. We do not copy-trade, and we do not replicate another wallet for you.

We trace factual claims to a record. Where our data is missing or stale, we say so. That answer ("I do not have that") is the one these skills are written to keep.

## Our MCP

MCP is how we let Cursor and Claude talk to that same agent. Your chats, our tools, and your subscription stay on your VisionMakers account. Signing, broadcasting, and payment stay in our app.

Create a key at [app.visionmakers.io](https://app.visionmakers.io): sign in with your wallet, open the wallet menu, then **MCP access**. We show the key once. It starts with `vmcp_`. You can hold five active keys. Each key is either **Chat with the agent** or **Read chats only**, and you choose an expiry of 7, 30, 90, or 365 days.

Our app copies a client config in this shape. Use the URL from that screen. The placeholder below is not a live host.

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

We expose three tools.

| Tool | What we do with it | Spends a message |
| --- | --- | --- |
| `send_message` | We send one message and wait for our agent's reply. Pass `chatId` to continue a thread. Omit `chatId` to start a new one. | Yes. Your free allowance and your paid subscription are shared with our website. |
| `list_chats` | We list this account's chats, including threads you started on our website. `limit` is 1–100 and defaults to 20. Newest activity first. | No |
| `get_chat` | We read one transcript: the chat, its messages, and your current usage. `chatId` is a UUID from `list_chats` or `send_message`. | No |

A read-only key can call `list_chats` and `get_chat`. `send_message` returns `SCOPE_DENIED`.

`get_chat` usage is your budget with us. The fields that matter are `dailyMessagesSent`, `dailyMessageLimit`, `messagesInChat`, `messagesPerChatLimit`, `freeQueriesUsed`, `freeQueryLimit`, `subscriptionActive`, `subscriptionMessagesUsed`, and `subscriptionMessageLimit`.

A `send_message` reply ends with the `chatId`. Tool activity on that reply is what our agent did inside the turn. A control or a plan there still needs you in our app.

## Skills we ship

We keep them in [`.cursor/skills`](.cursor/skills). Open this repository in Cursor and they load with the project. To use them in every project, copy the skill folders into your personal skills directory (`~/.cursor/skills` on macOS and Linux, `%USERPROFILE%\.cursor\skills` on Windows).

| Skill | Tools | What you get |
| --- | --- | --- |
| [check-message-budget](.cursor/skills/check-message-budget/SKILL.md) | `list_chats`, `get_chat` | Remaining daily, per-chat, free, and subscription messages before anything is sent |
| [reuse-answered-thread](.cursor/skills/reuse-answered-thread/SKILL.md) | `list_chats`, `get_chat` | An answer from a transcript you already paid for |
| [resume-website-thread](.cursor/skills/resume-website-thread/SKILL.md) | `list_chats`, `get_chat`, `send_message` | Your website thread, continued under the same id |
| [open-research-thread](.cursor/skills/open-research-thread/SKILL.md) | `send_message` | One new grounded research chat, with its id kept |
| [lineage-follow-up](.cursor/skills/lineage-follow-up/SKILL.md) | `get_chat`, `send_message` | A claim we kept, retracted, or marked unsupported |
| [route-and-risk-brief](.cursor/skills/route-and-risk-brief/SKILL.md) | `send_message` | Our winning route plus the five risk dimensions, with no armed order |
| [sign-off-handoff](.cursor/skills/sign-off-handoff/SKILL.md) | `get_chat`, sometimes `send_message` | The pending action, in our agent's words, ready for you to sign in our app |
| [thread-contradiction-check](.cursor/skills/thread-contradiction-check/SKILL.md) | `list_chats`, `get_chat` | Agreements and conflicts across two of your chats. Works on a read-only key |
| [inbox-triage](.cursor/skills/inbox-triage/SKILL.md) | `list_chats`, `get_chat` | Recent chats marked answered, waiting on a fact, or waiting on your signature |
| [transcript-to-note](.cursor/skills/transcript-to-note/SKILL.md) | `get_chat` | A local markdown copy plus the loops still open |
| [scheduled-agent-brief](.cursor/skills/scheduled-agent-brief/SKILL.md) | `send_message`, `get_chat` | A recurring brief with the pre-stage rules you set, confirmed stored |
| [claim-grounding-verdict](.cursor/skills/claim-grounding-verdict/SKILL.md) | `send_message`, `get_chat` | Grounded, partial, or unknown, plus the fact that would change our verdict |

Name the skill, or describe the job in those words. Cursor loads the matching skill.

## Our products

We ship nine products on one account, one balance, and one intelligence core. Each product produces signal the others consume. We write the suite up in the [product section of our whitepaper](https://docs.visionmakers.io/whitepaper/products) and in the platform docs linked below.

### Intelligence Layer

This is our co-pilot on top of execution. We price the route, explain which path won and by how much, interpret risk, and arm conditional orders when you ask. We choose a route on output after slippage, gas, and fill probability. We show risk as five dimensions: liquidity, holder concentration, contract flags, deployer behaviour, and flow anomalies. We keep those components visible so you can disagree with our conclusion and still see what produced it.

Our conditional orders are price triggers. They stay listed until they fire or you cancel them. A gap or a liquidity collapse can fill far from the trigger, so treat them as conditions you set, and review the ones you have armed. Our scheduled agents chain this into recurring work, such as a weekday morning brief that pre-stages swaps and moves nothing until you approve. We log every run: what ran, what we found, what we pre-staged, and what you signed.

[Intelligence Layer](https://docs.visionmakers.io/platform/intelligence-layer)

### MOFO & Mindshare

This is our intelligence product. We used to ship the two halves separately. We joined them because they answer one question from opposite directions.

With Mindshare we work top down, from narratives and sectors to tokens. We rank trending tokens by attention, map which themes are gaining, score KOL voices by historical accuracy, aggregate whale volume, summarise news, read on-chain flow, and surface new pairs and migrations.

With MOFO we work bottom up, from wallets to the tokens they move into. You follow addresses. We rank wallets by realised profit over a window you choose. Our alert feed streams what tracked wallets are doing, filtered by the order sizes and market caps you care about, and it can skip wallets whose holdings you already share. An alert is something for you to consider. If you trade, the swap goes through our vDEX, from your wallet.

We tell you what is happening. Every trade is one you chose and signed.

[MOFO & Mindshare](https://docs.visionmakers.io/platform/mindshare)

### vDEX

We put non-custodial swaps, charting, and your portfolio in the same view as the research. You connect MetaMask, Trust Wallet, OKX Wallet, Binance Wallet, or any WalletConnect wallet. Your keys stay yours. Your assets stay in your wallet the whole time, and every swap executes from it on your signature.

We compare paths across the venues we integrate, including Uniswap and OKX DEX, and we show you the decision: expected output, slippage, and which route won. A swap you sign and broadcast is final. The output we quote is an estimate, because price can move before execution. An approval you grant stays until you revoke it.

[vDEX](https://docs.visionmakers.io/platform/vdex)

### Vision Makers Pad

We run launch infrastructure for ICOs, IDOs, and initial token auctions, with KYC and allocation records we write on-chain. We set tier eligibility from your holdings and how long you have staked, so a position you have held for months outweighs one opened the day before a sale. Exact thresholds are a parameter our governance can change.

A sale with us is register, then commit, then claim on the project's own vesting schedule. Miss a window we published and you miss the sale. Alongside curated sales, we surface new pairs, tokens about to migrate, and tokens that just migrated, each with the buyer distribution behind the move so you can tell a broad launch from one wallet.

We list projects. We do not endorse them. Early-stage tokens often fail completely. Commit only what you can afford to lose.

[Vision Makers Pad](https://docs.visionmakers.io/platform/pad)

### vMakers

We run trading and analytics competitions with prize pools. You enter and we pay prizes in our own asset. We score realised performance over the window, so a volatile open position at the close does not decide the board. Our analytics competitions score the accuracy of calls, and you can compete without putting capital at risk. The comparable conditions feed wallet ranking in MOFO & Mindshare.

Once a window opens, we do not refund the entry fee. Multi-accounting, wash trading, and coordinated manipulation forfeit prizes.

[vMakers](https://docs.visionmakers.io/platform/vmakers)

### Vision Disperse

We send tokens to a whole address list in one transaction: airdrops, payroll, community rewards, contributor payouts. You upload the list. We show malformed addresses, duplicates, zero amounts, and the total. You approve a spend scoped to that total and sign once. Very large lists we split across several transactions, because block gas limits require it.

We can tell you an address is well formed. We cannot tell you it is the right recipient. A distribution you sign is final. Test with a small batch first.

[Vision Disperse](https://docs.visionmakers.io/platform/disperse)

### Neobank

We give you a non-custodial, blockchain-linked card that spends your on-chain balance directly. Your wallet is the account. The balance you see here is the balance you see in vDEX. You choose which asset a payment draws from, you set per-transaction, daily, and monthly limits, and you can freeze the card without cancelling it.

We are a spending surface for assets you already hold. We are not a bank, we do not take deposits, and we do not issue e-money. We do not insure balances. We offer no overdraft, no credit, and no interest. If the asset price falls, your spending power falls with it. Issuing the card requires KYC, which links your identity to the addresses the account draws from.

The loop we built the platform around: find it in MOFO & Mindshare, judge it in the Intelligence Layer, trade it on vDEX, spend it here.

[Neobank](https://docs.visionmakers.io/platform/neobank)

### Vision VPN

We provide encrypted connectivity because you transact on a public ledger. An observer who can see your network traffic can correlate a broadcast with your IP. Our tunnel encrypts the path to the exit, so your network provider sees a tunnel. That is the layer that matters on shared Wi-Fi.

What we record on-chain stays public and permanent. Our VPN changes who can watch you reach the network. Connect before you broadcast when network-level correlation is part of your threat model.

[Vision VPN](https://docs.visionmakers.io/platform/vpn)

### VM AI Hub

We put Vision Makers inside Telegram as a Mini App, approved in the Telegram Apps Center. This is our onboarding funnel: account and wallet setup, agent training, quests, mindshare and price checks, smart-money alerts, and referrals, without an install. Progress you make in the Hub counts toward the same account as our website. Our Hub marketplace settles in TON. The rest of our contracts are on EVM-compatible networks.

Our official Hub will never ask you for a seed phrase, never direct-message you offering support, and never ask you to move to a backup channel. Confirm you are in our Apps Center listing.

We have published a separate Telegram trading bot (in-chat charting, execution, portfolio) as Phase 2, and iOS and Android apps as Phase 3. We publish those phases as intentions, and Phase 1 is what we have live: conversational trading, vDEX, and the Telegram AI Hub. See our [roadmap](https://docs.visionmakers.io/whitepaper/roadmap).

[VM AI Hub](https://docs.visionmakers.io/platform/ai-hub)

## Where to go next

| If you want to | Come here |
| --- | --- |
| Use what we have shipped | [Getting started](https://docs.visionmakers.io/getting-started) |
| Read the whole design | [Our whitepaper](https://docs.visionmakers.io/whitepaper/products) |
| Connect our agent to Cursor | [app.visionmakers.io](https://app.visionmakers.io), wallet menu, MCP access |
| Check a claim with us | The `claim-grounding-verdict` skill |
| See what a chat already decided | The `inbox-triage` skill |

[Our docs](https://docs.visionmakers.io/)
