# Georgian Banks AI Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills: 2](https://img.shields.io/badge/Skills-2-green.svg)](#whats-inside)

> AI assistant skills for integrating with Georgian bank APIs — TBC Bank and Bank of Georgia (BOG). Drop these into any AI coding assistant and get expert-level API guidance instantly.

## What's Inside

| Skill | Covers |
|-------|--------|
| [`tbc-bank-api`](./tbc-bank-api/) | PSD2 Open Banking, AIS (accounts/balances/transactions), PIS (payments, recurring, deferred), TPay gateway, installment loans, OAuth2, consents, SCA |
| [`bank-of-georgia-api`](./bank-of-georgia-api/) | PSD2 Open Banking, AIS + PIS, iPay payment gateway, installment loans, `BOG.Calculator` + `BOG.SmartButton` SDK, OAuth2, banking products, statements |

Each skill contains:
- `SKILL.md` — main skill file with quick reference, base URLs, and workflow guides
- `references/` — topic-specific deep-dives loaded on demand

---

## Quick Install

### npx skills (universal — 37+ agents)

The [`skills`](https://www.npmjs.com/package/skills) npm package supports Claude Code, Cursor, Codex, Windsurf, OpenCode, and 37+ more agents. One command installs to all detected agents:

```bash
# Install to ALL detected agents at once
npx skills add erekle1/georgian-payments-skills --all

# Or install to specific agents only
npx skills add erekle1/georgian-payments-skills -a claude-code -a cursor -a windsurf
```

### skillkit (44 agents, auto-translate)

[SkillKit](https://github.com/nicholasb2101/skillkit) is the open-source package manager for AI agent skills — write once, deploy to 44 agents:

```bash
npm install -g skillkit

# Install skill to all detected agents
skillkit install erekle1/georgian-payments-skills

# Translate to a specific agent's format
skillkit translate tbc-bank-api --to cursor
skillkit translate bank-of-georgia-api --to codex
```

### Antigravity Skills (symlink method)

If you use the [Antigravity Skills](https://github.com/guanyang/antigravity-skills) framework:

```bash
# Project-level installation
mkdir -p .agent/skills
ln -s ~/path/to/georgian-payments-skills/tbc-bank-api .agent/skills/
ln -s ~/path/to/georgian-payments-skills/bank-of-georgia-api .agent/skills/

# Global installation (Claude Code)
ln -s ~/path/to/georgian-payments-skills/tbc-bank-api ~/.claude/skills/
ln -s ~/path/to/georgian-payments-skills/bank-of-georgia-api ~/.claude/skills/
```

---

## Manual Installation

### Claude Code

```bash
git clone https://github.com/erekle1/georgian-payments-skills
cd georgian-payments-skills

cp -r tbc-bank-api ~/.claude/skills/
cp -r bank-of-georgia-api ~/.claude/skills/
```

Skills are picked up automatically — no restart needed.

**Verify:**
```bash
ls ~/.claude/skills/
# tbc-bank-api/   bank-of-georgia-api/
```

---

### Cursor

Install as project rules:

```bash
mkdir -p .cursor/rules
cp -r path/to/georgian-payments-skills/tbc-bank-api .cursor/rules/
cp -r path/to/georgian-payments-skills/bank-of-georgia-api .cursor/rules/
```

Or add as a global rule: **Cursor Settings → Rules → User Rules** → paste the contents of `SKILL.md`.

---

### GitHub Copilot (VS Code)

```bash
mkdir -p .github
cat tbc-bank-api/SKILL.md > .github/copilot-instructions.md
# Append reference files as needed:
cat tbc-bank-api/references/auth.md >> .github/copilot-instructions.md
```

---

### Windsurf (Codeium)

```bash
cat tbc-bank-api/SKILL.md > .windsurfrules
# Append references as needed:
cat tbc-bank-api/references/auth.md >> .windsurfrules
```

---

### Zed Editor

```bash
mkdir -p .zed
cp tbc-bank-api/SKILL.md .zed/tbc-bank-api.md
cp bank-of-georgia-api/SKILL.md .zed/bank-of-georgia-api.md
```

Reference in Zed's assistant with `/file .zed/tbc-bank-api.md`.

---

### Continue.dev (VS Code / JetBrains)

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

---

### JetBrains AI Assistant

**Tools → AI Assistant → Personalization** → add `SKILL.md` content as an instruction, or reference it with `@tbc-bank-api/SKILL.md` in chat.

---

### Any AI Chat (ChatGPT, Claude.ai, Gemini, etc.)

Paste the `SKILL.md` content as a system prompt or at the start of your conversation. Paste specific `references/*.md` files when you need deep-dive help.

---

## Usage Examples

Once installed, ask your AI assistant naturally:

**TBC Bank:**
```
How do I initiate a domestic payment with TBC Bank API?
Show me how to set up OAuth2 for TBC Open Banking.
What headers do I need for TBC PSD2 requests?
How do I create a consent for reading account transactions in TBC?
```

**Bank of Georgia:**
```
How do I integrate BOG.Calculator for installment payments on my e-commerce site?
Set up iPay payment flow with Bank of Georgia.
How do I handle BOG payment webhooks/callbacks?
What's the full PSD2 payment initiation flow for Bank of Georgia?
```

---

## Skill Structure

```
georgian-payments-skills/
├── README.md
├── plugin.json
├── tbc-bank-api/
│   ├── SKILL.md                 ← Quick reference, base URLs, workflows
│   └── references/
│       ├── auth.md              ← OAuth2, token exchange, mTLS, request signing
│       ├── ais.md               ← Accounts, balances, transactions
│       ├── pis.md               ← Payments, recurring, deferred, signing baskets
│       ├── consent.md           ← Consent management, SCA flows
│       ├── installments.md      ← Online installment loans
│       └── tpay.md              ← TPay gateway
└── bank-of-georgia-api/
    ├── SKILL.md                 ← Quick reference, base URLs, workflows
    └── references/
        ├── auth.md              ← OAuth2 client credentials + auth code flow
        ├── openbanking.md       ← PSD2 AIS + PIS, consents, signing baskets
        ├── ipay.md              ← iPay gateway, pre-auth, refunds
        ├── installments.md      ← BOG.Calculator SDK + direct API
        └── products.md          ← Banking products catalog, statements, rates
```

---

## Contributing

Pull requests welcome. If you spot outdated endpoints, missing examples, or want to add more Georgian bank integrations — open an issue or PR.

### Distribution

This repo is listed on [awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) — the community directory of AI agent skills compatible with Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot, Windsurf, and more.

### Source Documentation

- TBC Bank Developer Portal: [developers.tbcbank.ge](https://developers.tbcbank.ge)
- Bank of Georgia API Docs: [api.bog.ge/docs](https://api.bog.ge/docs)

---

## License

MIT — free to use, modify, and distribute.
