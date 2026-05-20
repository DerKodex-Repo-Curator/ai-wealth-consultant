# AI Wealth Consultant

A personal Chief Wealth Orchestrator powered by Claude Code. One main agent (**Jarvis**), two sub-orchestrators (**Vera** for tax, **Mira** for investments), and a roster of named specialists covering everything money-related — tax across multiple jurisdictions, asset allocation, brokerage execution, banking, insurance, retirement, business operations.

This is a **decision-support framework**, not a licensed advice service. Every material action requires sign-off from a qualified human professional (Steuerberater, chartered tax adviser, IFA). The agents flag who needs to bless what.

The framework's default coverage is **Germany + UK** for tax. Other jurisdictions can be added with additional sub-agents.

---

## What you get out of the box

- **Persistent client profile** in `client-profile/` — eight markdown files describing identity, jurisdictions, corporate stack, assets, banking, insurance, retirement, investments. Jarvis reads all eight at the start of every session.
- **24 specialist sub-agents** under `.claude/agents/`, each with a persona, a tightly-scoped beat, and a "verify live before quoting any rule" discipline.
- **Document drop zones** under `documents/<jurisdiction>/` — paste in tax letters, broker statements, contracts; Jarvis extracts the salient facts and writes them into the profile.
- **Interactive Brokers integration** via the `code-rabi/interactive-brokers-mcp` MCP server (optional — IBKR users only). Tutorial mode by default; live execution gated behind both an env-var flip and an explicit `EXECUTE: confirmed` token in each brief.
- **Onboarding command** (`/onboard`) that walks a new user through the questions whose answers populate the profile.

---

## Fork and setup

### 1. Prerequisites

- **Claude Code** — install from <https://claude.com/claude-code>. The CLI / desktop app / IDE extension all work; pick whichever you prefer.
- **An Anthropic account** with Claude Code access. Opus / Sonnet recommended — the agents are configured to run on Opus at maximum reasoning effort.
- **A GitHub account** (optional, only if you want to push your fork somewhere).
- **Node.js** (only if you want the IBKR integration — the MCP server runs via `npx`).
- **An Interactive Brokers account** (only if you want live brokerage execution — otherwise skip).

### 2. Fork or clone this repo

```bash
# Option A: fork on GitHub and clone your fork
gh repo fork <this-repo> --clone

# Option B: clone directly and re-point to your own remote
git clone <this-repo> my-wealth-advisor
cd my-wealth-advisor
git remote remove origin
gh repo create my-wealth-advisor --private --source=. --remote=origin --push
```

**Make your fork private**. Once you onboard, the `client-profile/` files will contain your financial data — names, account fingerprints, asset values, tax IDs. There is no reason for that to be public.

### 3. Open the directory in Claude Code

```bash
cd my-wealth-advisor
claude
```

Claude Code automatically picks up `CLAUDE.md` (project instructions), `.claude/agents/*.md` (the sub-agents), `.claude/commands/*.md` (the slash commands), and `.mcp.json` (MCP server registrations).

### 4. Run onboarding

In Claude Code, type:

```
/onboard
```

This walks you through ~20–30 minutes of questions across nine sections (identity, jurisdictions, corporate, assets, banking, insurance, retirement, investments, brokerage operator). Jarvis writes your answers into `client-profile/*.md` as you go. You can pause and resume anytime — the profile is saved incrementally.

If you'd rather populate the profile by hand, the files in `client-profile/` are markdown templates you can edit directly. Onboarding is just the friendlier path.

### 5. (Optional) Interactive Brokers MCP

If you want Iris (the IB operator agent) to query positions or place orders:

```bash
cp .env.example .env
# Edit .env and fill in IB_USERNAME and IB_PASSWORD_AUTH.
```

Defaults are deliberately conservative — `IB_PAPER_TRADING=true` and `IB_READ_ONLY_MODE=true`. Iris will operate against your paper account in tutorial mode without any further flips. Live execution requires:

1. Flip both env vars to `false` in `.env`.
2. Include the literal token `EXECUTE: confirmed` in any brief that asks Iris to place an order.

IBKR will trigger 2FA to your phone on first auth — approve it. The MCP server bundles its own IB Gateway and Java runtime, so it won't fight with a TWS desktop install (TWS for manual; Gateway for automation).

### 6. Ask Jarvis a question

```
What's the most obvious tax-optimisation lever in my current structure?
```

or

```
Walk me through the deadlines I have in the next 90 days.
```

Jarvis reads `client-profile/*.md`, fans out to whichever sub-agents the question touches, synthesises their output, and returns a structured answer with verified rule citations.

---

## The agent roster

### Top level

- **Jarvis** (`jarvis-wealth-orchestrator`) — Chief Wealth Orchestrator. The user-facing voice. Routes everything; never delegates the final synthesis. Owns `personal.md`, `jurisdictions.md`, and cross-cutting profile edits.

### Sub-orchestrators

- **Vera** (`vera-tax-orchestrator`) — Head of Tax. Fans out to the tax specialists. Owns tax-related profile edits.
- **Mira** (`mira-investment-orchestrator`) — Head of Investments. Fans out to the asset-class specialists and the portfolio-construction specialist. Owns `investments.md`.

### Tax specialists (Vera's roster)

- **Klaus** (`german-tax-specialist`) — All German personal and corporate tax. ESt, KSt, GewSt, Abgeltungsteuer, Solidaritätszuschlag, Kirchensteuer, § 8b KStG, Organschaft, Wegzugsbesteuerung, AStG, § 42 AO, ELSTER calendar.
- **Harriet** (`uk-tax-specialist`) — HMRC. Self Assessment, Statutory Residence Test, CGT, NRL scheme, the post-Apr-2025 long-term-resident (LTR) regime that replaced domicile, UK IHT residence test.
- **Nikolai** (`international-tax-treaty-specialist`) — Cross-border. Double-taxation agreements (tie-breakers, credit vs exemption), permanent establishment, withholding, EU directives (Parent-Subsidiary, Interest-Royalty, ATAD I/II/III), MLI / PPT, CFC under AStG, DAC6.
- **Zara** (`crypto-tax-specialist`) — Crypto across DE + UK. DE § 23 EStG one-year holding exemption, staking/lending extended clock, UK CGT share-pooling (s104 + same-day + 30-day), DeFi, NFTs, hard forks, airdrops, DAC8, MiCA.
- **Marco** (`real-estate-tax-specialist`) — UK NRL + NRCGT + 60-day return; DE treatment of foreign rental income (exemption-with-progression or credit); AfA / depreciation; base-cost planning.
- **Felix** (`corporate-structuring-specialist`) — German corporate stack strategy. Profit routing, salary vs dividend vs loan, Organschaft, § 8b holding privilege optimisation, Familienpersonengesellschaft design, UmwStG reorganisations, Einheits-KG, transfer pricing between related entities, thin-cap (§ 4h EStG Zinsschranke).
- **Ingrid** (`inheritance-estate-specialist`) — Cross-border inheritance and estate. German ErbSt (classes, allowances, Verschonungsabschlag for business assets §§ 13a/b ErbStG), UK IHT under the post-2025 LTR regime, EU Succession Regulation 650/2012, family holding structures, lifetime gifting strategies.
- **Sasha** (`tax-compliance-risk-officer`) — Gatekeeper. Scrutinises every proposed scheme against § 42 AO, UK GAAR + TAAR, EU ATAD GAAR, treaty PPT, DAC6 disclosure hallmarks, substance / beneficial-ownership / economic-reality, CFC. Independent voice on the criminal boundary between avoidance (legal) and evasion (§ 370 AO). Mandatory whenever a scheme is proposed or evaluated.

### Investment specialists (Mira's roster)

- **Quinn** (`equities-specialist`) — Stocks, ETFs, factor tilts (value / quality / momentum / min-vol / size), regional and sector allocation, fundamentals, index methodology and replication mechanics.
- **Bram** (`fixed-income-specialist`) — Government bonds (Bunds, gilts, treasuries, OATs, BTPs), investment-grade and high-yield credit, money market, duration and curve positioning, inflation-linked, cross-currency.
- **Rune** (`crypto-investment-specialist`) — Crypto allocation strategy. Sizing within a wealth portfolio, BTC/ETH thesis, L1/L2 due diligence, staking-as-investment, DeFi yield, custody choice (self-custody vs exchange vs qualified custodian). Strategy only — tax goes to Zara.
- **Tessa** (`real-estate-investment-specialist`) — REIT vs direct ownership, leverage strategy, yield maths (gross / net / cash-on-cash / IRR), market selection, refurb vs hold. Strategy only — tax goes to Marco.
- **Goldie** (`commodities-alternatives-specialist`) — Physical gold and precious metals, broad commodity exposure, private equity, hedge funds, structured products, art and collectibles as portfolio assets.
- **Yuki** (`fx-cash-specialist`) — Multi-currency cash architecture (EUR / GBP / USD), FX hedging strategy, money-market parking, treasury structure across personal and corporate accounts.
- **Delta** (`derivatives-options-specialist`) — Equity and index options, futures, hedging overlays (protective puts, collars, covered calls), Greeks, structured option strategies, leverage management via futures.
- **Pax** (`portfolio-construction-specialist`) — Strategic asset allocation, risk budgeting across asset classes, rebalancing rules, factor exposure aggregation, drawdown framing. Synthesises peer single-asset views into a coherent total-portfolio recommendation. Always include for non-trivial allocation decisions.

### Direct specialists (under Jarvis)

- **Iris** (`interactive-brokers-operator`) — Interactive Brokers operator. Two modes: tutorial (click-paths for TWS desktop / IBKR Mobile / Client Portal) and execution (queries positions, places orders via the IBKR MCP). Defaults to tutorial / paper / read-only. Live execution requires both env-var flips and the literal `EXECUTE: confirmed` token in her brief.
- **Penny** (`personal-finance-specialist`) — Household budgeting, cashflow architecture, savings rate, expense optimisation, emergency fund sizing, family financial education.
- **Hugo** (`insurance-risk-specialist`) — Life (term), disability (BU / IP), liability, private health (PKV), household contents (Hausrat), property building, professional indemnity, D&O, umbrella excess.
- **Rita** (`retirement-pension-specialist`) — DE Rürup (Basisrente), Riester, bAV (Direktversicherung / Pensionskasse / Pensionsfonds / Unterstützungskasse), DRV state pension; UK SIPP, workplace schemes, state pension and voluntary NI top-ups; cross-border pension transfers and consolidation.
- **Bruno** (`banking-credit-specialist`) — Mortgages (DE Annuitätendarlehen, UK BTL), personal and corporate loans, account architecture, credit-profile management, multi-currency banking.
- **Max** (`business-manager`) — Business strategy and operations for any company entities the user owns. Revenue model, pricing, margin, unit economics, cost base, vendor / SaaS consolidation, KPIs, buy-side M&A screening, exit-readiness. Pure business — tax-driven structuring stays with Felix.

---

## Directory layout

```
.
├── CLAUDE.md                          Project instructions Jarvis loads on every turn
├── README.md                          This file
├── .env.example                       IBKR credentials template
├── .gitignore
├── .mcp.json                          MCP server registrations (currently: IBKR)
├── .claude/
│   ├── agents/                        24 specialist sub-agent definitions
│   └── commands/
│       └── onboard.md                 /onboard slash command
├── client-profile/                    Persistent client snapshot (8 files)
│   ├── personal.md
│   ├── jurisdictions.md
│   ├── corporate-structure.md
│   ├── assets.md
│   ├── investments.md
│   ├── banking.md
│   ├── insurance.md
│   └── retirement.md
└── documents/                         Drop-zone for source documents
    ├── brokerage/                       — broker statements, IB Flex reports
    ├── corporate/                       — articles, share registers, board minutes
    ├── crypto/                          — wallet reports, exchange CSVs
    ├── finance/                         — bank statements, payslips
    ├── germany/                         — DE tax letters, Steuerbescheide
    └── uk/                              — HMRC correspondence, P60s, SA returns
```

---

## Adding your own jurisdiction or specialist

The framework is opinionated about DE + UK. If you want native support for another jurisdiction:

1. Add a new file in `.claude/agents/` modelled on `uk-tax-specialist.md` (Harriet) — give it a persona, a tightly-scoped beat, the same "verify live before quoting" discipline, and the plain-language rule.
2. Add the new specialist to Vera's routing table in `.claude/agents/vera-tax-orchestrator.md`.
3. Mention the new jurisdiction in `CLAUDE.md`'s "default coverage" section so Jarvis stops flagging it as a gap.
4. If the jurisdiction has a regional / municipal sub-system (Cantons, States, Comunidades), make sure the specialist's discipline forces them to verify regional rates live, not from training data.

Same pattern for adding new asset-class specialists under Mira, or new domain specialists (e.g. an estate-planning lawyer agent, an art-as-an-asset agent).

---

## Privacy

- `.env` is `.gitignore`'d.
- Document drop-zones (`documents/<jurisdiction>/`) are committed to your fork — **keep your fork private** unless you've sanitised the documents.
- `client-profile/*.md` contains personal financial information by design — **keep your fork private**.
- The framework never transmits your data anywhere except to the Anthropic API for inference; nothing leaves your machine other than the conversation Claude Code sends to Claude.

---

## Limitations

- This is decision-support, not regulated advice. Every material action needs a qualified professional to sign off.
- The agents will hallucinate if you don't enforce the verification discipline. The "What I verified for this answer" block in Jarvis's answer format exists for a reason — read it. If the response cites a rule without a verification stamp, push back: "where did you verify this?"
- Tax law moves. The agents are trained to verify deadlines, thresholds, BMF-Schreiben, post-Apr-2025 UK LTR rules, DAC6/8 transposition, ATAD III, AStG amendments, etc. at point of citation. If a citation looks suspiciously old, it probably is.
- The IBKR MCP integration is community-built (`code-rabi/interactive-brokers-mcp`), unaffiliated with IBKR. Treat live execution as experimental — test in paper mode first.

---

## License

MIT — fork freely, modify freely, commercial use OK. The agent personalities and the directory architecture are the contribution; everything else is your own setup.
