# Georgian Banks AI Skills

### Your AI assistant doesn't know Georgian bank APIs. These skills fix that.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills: 2](https://img.shields.io/badge/Skills-2-green.svg)](#whats-inside)

Drop-in skills for **TBC Bank** and **Bank of Georgia** — give any AI coding assistant expert-level knowledge of payment APIs, Open Banking, installments, and merchant integration flows.

---

## See It Work

Once installed, just ask naturally:

```
# TBC Bank
"Set up TBC Checkout payment flow for my e-commerce site"
"Handle TBC callback webhooks in Express.js"
"Implement the TBC XML billing protocol (CHECK/PAY) for my utility service"
"Create a recurring payment flow with saved cards via TPay"
"Generate the integration docs TBC requires for go-live"

# Bank of Georgia
"Integrate iPay card payments with my Python backend"
"Add BOG installment calculator (BOG.Calculator SDK) to my product pages"
"Set up BOG webhook handler with idempotency in FastAPI"
"Initiate a PSD2 domestic payment through BOG Open Banking"
"Generate the technical spec and go-live checklist for BOG"
```

The skill automatically pulls in the right reference — auth flows, endpoint details, error codes, code examples — so you don't have to look anything up.

---

## Install

```bash
npx skills add erekle1/georgian-payments-skills --all
```

This installs to all detected AI agents (Claude Code, Cursor, Windsurf, Codex, and 37+ more).

<details>
<summary><b>Other install methods</b></summary>

### Claude Code (manual)

```bash
git clone https://github.com/erekle1/georgian-payments-skills
cp -r georgian-payments-skills/tbc-bank-api ~/.claude/skills/
cp -r georgian-payments-skills/bank-of-georgia-api ~/.claude/skills/
```

### Cursor

```bash
mkdir -p .cursor/rules
cp -r tbc-bank-api .cursor/rules/
cp -r bank-of-georgia-api .cursor/rules/
```

Or paste `SKILL.md` content into **Cursor Settings → Rules → User Rules**.

### Windsurf

```bash
cat tbc-bank-api/SKILL.md > .windsurfrules
```

### GitHub Copilot

```bash
mkdir -p .github
cat tbc-bank-api/SKILL.md > .github/copilot-instructions.md
```

### Zed

```bash
mkdir -p .zed
cp tbc-bank-api/SKILL.md .zed/tbc-bank-api.md
cp bank-of-georgia-api/SKILL.md .zed/bank-of-georgia-api.md
```

Reference in assistant with `/file .zed/tbc-bank-api.md`.

### Continue.dev

Add to `.continue/config.json`:

```json
{
  "customCommands": [
    {
      "name": "tbc",
      "prompt": "{{{ input }}}\n\nContext:\n{{{ read_file('path/to/tbc-bank-api/SKILL.md') }}}",
      "description": "Ask about TBC Bank API"
    },
    {
      "name": "bog",
      "prompt": "{{{ input }}}\n\nContext:\n{{{ read_file('path/to/bank-of-georgia-api/SKILL.md') }}}",
      "description": "Ask about Bank of Georgia API"
    }
  ]
}
```

### JetBrains AI Assistant

**Tools → AI Assistant → Personalization** → add `SKILL.md` content as an instruction.

### SkillKit (44 agents)

```bash
npm install -g skillkit
skillkit install erekle1/georgian-payments-skills
```

### Symlink (Antigravity Skills)

```bash
ln -s ~/path/to/georgian-payments-skills/tbc-bank-api ~/.claude/skills/
ln -s ~/path/to/georgian-payments-skills/bank-of-georgia-api ~/.claude/skills/
```

### Any AI Chat (ChatGPT, Claude.ai, Gemini)

Paste `SKILL.md` content as a system prompt. Add specific `references/*.md` files for deep-dive help.

</details>

---

## What's Inside

| Skill | What It Covers |
|-------|---------------|
| [`tbc-bank-api`](./tbc-bank-api/) | Checkout/TPay, XML billing protocol (CHECK/PAY), installments, exchange rates, PSD2, TBC ID, QR payments, DBI, design guidelines |
| [`bank-of-georgia-api`](./bank-of-georgia-api/) | iPay gateway, installments + BOG SDK, PSD2 (AIS/PIS), BOG-ID SSO, BOnline corporate banking, payment gateway (billing), banking products |

**Both skills also include:**
- Merchant integration guides (callbacks, webhooks, environment setup)
- Merchant document generators (technical specs, go-live checklists — output as `.md`)
- Endpoint validation scripts and doc scrapers

---

## Use Cases

### E-Commerce Payments

```
"Create a payment checkout flow with TBC TPay"
"Set up iPay with pre-authorization (hold & capture) for my hotel booking site"
"Implement card tokenization for recurring payments with BOG"
```

### Service Provider / Utility Billing

```
"Build the CHECK/PAY XML endpoint that TBC calls for my internet service"
"Integrate with BOG Payment Gateway using HMAC-SHA256 auth"
```

### Installment Loans

```
"Add TBC online installment loan option to my checkout"
"Embed BOG.Calculator and BOG.SmartButton on my product pages"
"Create installment order via BOG direct API with callback handling"
```

### Open Banking (PSD2)

```
"Read account transactions via TBC AIS with consent and mTLS"
"Initiate a domestic payment through BOG PIS"
"Set up OAuth2 + PKCE for TBC Open Banking"
```

### Authentication & SSO

```
"Implement TBC ID login with OpenID Connect"
"Add BOG-ID single sign-on — get user's personal number and name"
"Set up OAuth2 client credentials for server-to-server TBC calls"
```

### Merchant Documentation

```
"Generate the technical integration spec TBC needs for go-live review"
"Create a go-live readiness checklist for my BOG iPay integration"
"Write the XML billing protocol specification document for TBC"
"Generate BOG SDK integration details document"
```

### Corporate Banking

```
"Make a domestic transfer via BOG BOnline API"
"Get commercial exchange rates from TBC"
"Fetch account statements from BOG BOnline"
```

---

## Skill Structure

```
georgian-payments-skills/
├── tbc-bank-api/
│   ├── SKILL.md
│   ├── references/
│   │   ├── auth.md                  # OAuth2, apikey, mTLS, DBI auth
│   │   ├── checkout.md              # TPay payments, pre-auth, recurring, QR
│   │   ├── xml-billing-protocol.md  # CHECK/PAY for service providers
│   │   ├── installments.md          # Online installment loans
│   │   ├── exchange-rates.md        # Commercial + NBG rates
│   │   ├── openbanking.md           # PSD2 AIS/PIS, TBC ID
│   │   ├── errors.md               # Error codes + troubleshooting
│   │   ├── design.md               # TBC brand, buttons, typography
│   │   ├── merchant-integration.md  # Callbacks, IP whitelist, env config
│   │   └── merchant-doc-generator.md # Templates for TBC-required docs
│   └── scripts/
│       ├── validate_endpoints.sh    # Check API reachability
│       └── scrape_docs.py           # Fetch latest from developers.tbcbank.ge
│
└── bank-of-georgia-api/
    ├── SKILL.md
    ├── references/
    │   ├── auth.md                  # OAuth2 client credentials + auth code
    │   ├── openbanking.md           # PSD2 AIS/PIS, consents, signing baskets
    │   ├── ipay.md                  # iPay gateway, pre-auth, refunds, recurring
    │   ├── installments.md          # BOG SDK + direct API
    │   ├── products.md              # Banking products, statements, rates
    │   ├── bogid.md                 # BOG-ID SSO
    │   ├── bonline.md               # BOnline corporate banking
    │   ├── payment-gateway.md       # Billing/service provider gateway
    │   ├── merchant-integration.md  # Webhooks, OAuth redirect, env config
    │   ├── merchant-documents.md    # Required registration documents
    │   └── merchant-doc-generator.md # Templates for BOG-required docs
    └── scripts/
        ├── validate_endpoints.sh
        └── scrape_docs.py
```

---

## Contributing

PRs welcome — especially for outdated endpoints, missing examples, or new Georgian bank integrations.

**Source docs:** [developers.tbcbank.ge](https://developers.tbcbank.ge) | [api.bog.ge/docs](https://api.bog.ge/docs)

Listed on [awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills).

## License

MIT
