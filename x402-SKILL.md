---
name: aurex-x402
description: Build and consume x402 payment-gated APIs using Aurex virtual cards. Use when agents need to autonomously pay for HTTP 402 protected resources, build monetized APIs that accept USDC on Base or Solana, or integrate x402 payments with Aurex card infrastructure. Covers both server-side (protecting your API) and client-side (paying for APIs) workflows.
---

# Aurex x402 Payments

Build and consume payment-gated APIs using the HTTP 402 protocol with Aurex virtual card infrastructure.

## What is x402?

x402 is a payment protocol built on HTTP 402 "Payment Required". When an AI agent hits a paid endpoint:

1. Server responds with `402` + payment details (amount, wallet, network)
2. Agent pays automatically with USDC on Base or Solana
3. Agent retries the request with payment proof
4. Server verifies and returns the data

No API keys. No subscriptions. Pay per use.

## Quick Start

```bash
npm install x402-axios @coinbase/x402-express
```

## Part 1 — Consuming x402 APIs (Client)

Use this when your agent needs to pay for external APIs automatically.

### With axios

```typescript
import axios from 'axios';
import { withPaymentInterceptor } from 'x402-axios';
import { createWalletClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { base } from 'viem/chains';

// Setup wallet (use Aurex-funded wallet)
const account = privateKeyToAccount(process.env.PRIVATE_KEY as `0x${string}`);
const walletClient = createWalletClient({
  account,
  chain: base,
  transport: http()
});

// Add x402 payment interceptor
const client = withPaymentInterceptor(axios.create(), walletClient);

// Now any 402 response is handled automatically
const response = await client.get('https://api.example.com/paid-data');
console.log(response.data);
```

### With fetch

```typescript
import { wrapFetchWithPayment } from 'x402-fetch';

const fetchWithPayment = wrapFetchWithPayment(fetch, walletClient);

const response = await fetchWithPayment('https://api.example.com/paid-endpoint');
const data = await response.json();
```

## Part 2 — Building x402 APIs (Server)

Use this when you want to monetize your own API endpoints.

### Express middleware

```typescript
import express from 'express';
import { paymentMiddleware } from '@coinbase/x402-express';

const app = express();

// Protect routes with x402 payments
app.use(paymentMiddleware({
  facilitatorUrl: 'https://x402.org/facilitator',
  routes: {
    '/api/premium-data': {
      price: '$0.01',
      network: 'base',
      description: 'Access premium market data'
    },
    '/api/issue-card': {
      price: '$0.10',
      network: 'base',
      description: 'Issue a virtual card via Aurex'
    }
  }
}));

app.get('/api/premium-data', (req, res) => {
  res.json({ data: 'premium content here' });
});

app.listen(3000);
```

### Next.js middleware

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { createX402Middleware } from 'x402-next';

const x402 = createX402Middleware({
  '/api/paid': { price: '$0.05', network: 'base' }
});

export function middleware(req: NextRequest) {
  return x402(req);
}
```

## Part 3 — Aurex + x402 Together

The most powerful combination: fund agent wallets via Aurex deposits, then use those wallets to autonomously pay for x402 APIs.

```typescript
import { createAurexTools } from '@aurexcash/agent';
import { withPaymentInterceptor } from 'x402-axios';

// Step 1: Set up Aurex agent tools
const aurex = createAurexTools({ apiKey: process.env.AUREX_API_KEY });

// Step 2: Create user and get their funded wallet
const user = await aurex.createUser({ name: 'Agent Wallet' });
const wallet = await aurex.getWallet({ userId: user.id });
// wallet.address — send USDC here to fund

// Step 3: Use that wallet for x402 payments
const walletClient = createWalletClient({
  account: privateKeyToAccount(wallet.privateKey),
  chain: base,
  transport: http()
});

const client = withPaymentInterceptor(axios.create(), walletClient);

// Agent now autonomously pays for any x402 API
const result = await client.get('https://any-x402-api.com/data');
```

## Common Workflows

### Build a paid API in 5 minutes
1. Install: `npm install @coinbase/x402-express`
2. Add `paymentMiddleware` to your Express app
3. Set price per endpoint
4. Deploy — your API now earns USDC per request

### Make your agent pay autonomously
1. Create Aurex wallet and fund with USDC
2. Wrap axios/fetch with `withPaymentInterceptor`
3. Agent handles all 402 responses automatically

### Monetize Aurex operations via x402
1. Protect `/api/issue-card` with x402 ($0.10 per card)
2. Agents pay USDC to issue cards
3. Revenue flows to your wallet automatically

## Supported Networks

| Network | Token | Speed | Cost |
|---------|-------|-------|------|
| Base | USDC | ~2s | <$0.01 |
| Solana | USDC | ~1s | <$0.001 |

## Environment Variables

```bash
# For consuming x402 APIs
PRIVATE_KEY="0x..."           # Wallet private key
RPC_URL="https://..."         # Base or Solana RPC

# For Aurex integration
AUREX_API_KEY="your-key"      # From aurex.cash dashboard

# For building x402 APIs
X402_FACILITATOR_URL="https://x402.org/facilitator"
PAYMENT_RECIPIENT="0x..."     # Your wallet to receive payments
```

## Error Handling

```typescript
try {
  const response = await client.get('https://api.example.com/paid');
} catch (error) {
  if (error.response?.status === 402) {
    // Payment failed — check wallet balance
    console.log('Payment failed:', error.response.data);
  }
}
```

## Resources

- x402 protocol: [x402.org](https://x402.org)
- Coinbase x402 SDK: [github.com/coinbase/x402](https://github.com/coinbase/x402)
- Aurex SDK: [github.com/aurexcards/aurex-agent](https://github.com/aurexcards/aurex-agent)
- Aurex dashboard: [aurex.cash](https://aurex.cash)
