# aurex-x402-skill

The official x402 payments skill for Aurex — teaches AI agents how to build and consume payment-gated APIs using HTTP 402 protocol with Aurex virtual card infrastructure.

## What it does

One install. Your AI agent knows how to:

- **Consume x402 APIs** — automatically pay for HTTP 402 protected endpoints
- **Build x402 APIs** — monetize your own API with per-request USDC payments
- **Combine Aurex + x402** — fund agent wallets via Aurex, pay for APIs autonomously
- **Deploy on Base or Solana** — sub-cent transaction fees

## Install

### Claude Code

```
/plugin marketplace add aurexcards/aurex-x402-skill
```

### Manual

```bash
git clone https://github.com/aurexcards/aurex-x402-skill.git
cp -r aurex-x402-skill ~/.claude/skills/aurex-x402/
```

### Cursor / Windsurf

```bash
cp aurex-x402-skill/SKILL.md ./.cursorrules
```

## Example prompts after installing

```
"Set up my Express API to charge $0.01 per request in USDC"
"Make my agent automatically pay for x402 protected APIs"
"Fund an agent wallet via Aurex and use it for x402 payments"
"Build a paid endpoint that issues Aurex cards for $0.10 USDC"
```

## What's inside

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill — client, server, and Aurex+x402 workflows |

## Related

- [aurex-skill](https://github.com/aurexcards/aurex-skill) — Core Aurex API skill
- [@aurexcash/agent](https://npmjs.com/package/@aurexcash/agent) — TypeScript SDK

## Built by

[Aurex](https://aurex.cash) — Virtual crypto-funded cards for AI agents and everyday payments.

## License

MIT
