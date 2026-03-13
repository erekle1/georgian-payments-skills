# Georgian Banks AI Skills

> AI assistant skills for integrating with Georgian bank APIs — TBC Bank and Bank of Georgia (BOG). Drop these into any AI coding assistant that supports skills/context files and get expert-level API guidance instantly.

## What's inside

| Skill | Covers |
|-------|--------|
| [`tbc-bank-api`](./tbc-bank-api/) | PSD2 Open Banking, AIS (accounts/balances/transactions), PIS (payments, recurring, deferred), TPay gateway, installment loans, OAuth2, consents, SCA |
| [`bank-of-georgia-api`](./bank-of-georgia-api/) | PSD2 Open Banking, AIS + PIS, iPay payment gateway, installment loans, `BOG.Calculator` + `BOG.SmartButton` SDK, OAuth2, banking products, statements |

Each skill contains:
- `SKILL.md` — main skill file with quick reference, base URLs, and workflow guides
- `references/` — topic-specific deep-dives loaded on demand

---

## Installation

### Claude Code (Claude.ai CLI)

Copy skills into `~/.claude/skills/`:

```bash
# Clone the repo
git clone https://github.com/your-org/georgian-banks-skills
cd georgian-banks-skills

# Install both skills
cp -r tbc-bank-api ~/.claude/skills/
cp -r bank-of-georgia-api ~/.claude/skills/
```

Skills are picked up automatically — no restart needed. Trigger them by mentioning TBC Bank or BOG in your coding questions.

**Verify installation:**
```bash
ls ~/.claude/skills/
# tbc-bank-api/   bank-of-georgia-api/   ...
```

---

### Cursor

Cursor uses `.cursor/rules` for persistent context. Install as project rules:

```bash
# In your project root
mkdir -p .cursor/rules

cp -r path/to/georgian-banks-skills/tbc-bank-api .cursor/rules/
cp -r path/to/georgian-banks-skills/bank-of-georgia-api .cursor/rules/
```

Or add as a global rule in **Cursor Settings → Rules → User Rules**:

1. Open Cursor → `Cmd/Ctrl + Shift + P` → "Open Cursor Settings"
2. Go to **Rules** tab
3. Paste the contents of `tbc-bank-api/SKILL.md` or `bank-of-georgia-api/SKILL.md` into a new rule
4. Reference individual `references/` files as needed

**Pro tip:** For Cursor with large codebases, add a `.cursorrules` file at your project root:

```bash
cat tbc-bank-api/SKILL.md > .cursorrules
# or for BOG:
cat bank-of-georgia-api/SKILL.md > .cursorrules
```

---

### GitHub Copilot (VS Code)

Copilot uses VS Code's custom instructions feature.

**Per-workspace (recommended):**

1. Create `.github/copilot-instructions.md` in your project root:

```bash
mkdir -p .github
cat tbc-bank-api/SKILL.md > .github/copilot-instructions.md
# Append reference files if needed:
cat tbc-bank-api/references/auth.md >> .github/copilot-instructions.md
```

2. VS Code picks this up automatically for Copilot Chat in that workspace.

**Global instructions:**
1. Open VS Code → Settings (`Cmd/Ctrl + ,`)
2. Search for `github.copilot.chat.codeGeneration.instructions`
3. Add the skill content as a string value

---

### Windsurf (Codeium)

Windsurf supports `.windsurfrules` for persistent AI context:

```bash
# In your project root
cat tbc-bank-api/SKILL.md > .windsurfrules
echo "" >> .windsurfrules
cat tbc-bank-api/references/auth.md >> .windsurfrules
cat tbc-bank-api/references/pis.md >> .windsurfrules
```

Or for BOG:
```bash
cat bank-of-georgia-api/SKILL.md > .windsurfrules
cat bank-of-georgia-api/references/installments.md >> .windsurfrules
```

---

### Zed Editor

Zed supports assistant context via the `.zed/assistant_context` directory:

```bash
mkdir -p .zed
cp tbc-bank-api/SKILL.md .zed/tbc-bank-api.md
cp bank-of-georgia-api/SKILL.md .zed/bank-of-georgia-api.md
```

Then reference them in Zed's assistant with `/file .zed/tbc-bank-api.md`.

---

### Any AI Chat (ChatGPT, Claude.ai, Gemini, etc.)

Paste the relevant `SKILL.md` content as a system prompt or at the start of your conversation. Then paste the specific `references/*.md` file when you need deep-dive help on a topic.

```
# Paste this at the start of your chat:
[contents of tbc-bank-api/SKILL.md]

# Then ask:
"How do I initiate a recurring payment?"
# → AI will tell you to read pis.md, paste that too if needed
```

---

### Continue.dev (VS Code / JetBrains)

Add as a context provider in `.continue/config.json`:

```json
{
  "contextProviders": [
    {
      "name": "file",
      "params": {
        "nRetrieve": 10
      }
    }
  ],
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

1. Open any JetBrains IDE (IntelliJ, WebStorm, PyCharm, etc.)
2. Go to **Tools → AI Assistant → Personalization**
3. Add a new "Instruction" with the contents of `SKILL.md`

Or use the **@file** mention in AI chat: open the `SKILL.md` file and reference it with `@tbc-bank-api/SKILL.md` in your AI prompt.

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
georgian-banks-skills/
├── README.md
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

The source API documentation used to build these skills:
- TBC Bank Developer Portal: [developers.tbcbank.ge](https://developers.tbcbank.ge)
- Bank of Georgia API Docs: [api.bog.ge/docs](https://api.bog.ge/docs)

---

## License

MIT — free to use, modify, and distribute.
