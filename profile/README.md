# Kollat

**Privacy-Preserving Solvency Verification for Decentralized Finance**

<p align="center">
  <img src="https://img.shields.io/badge/Zero--Knowledge%20Proofs-STARKs-orange?style=for-the-badge" alt="ZK Proofs" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License" />
</p>

---

## The Problem

Users hold assets across cold storage, hot wallets, staking contracts, and centralized exchanges. Proving total solvency currently requires:

1. **Fragmented Liquidity** — Moving assets to a single location, incurring gas fees and opportunity costs
2. **Privacy Leaks** — "Doxing" wallets to expose entire transaction history and portfolio
3. **Capital Inefficiency** — Lenders can't assess "global" health, leading to over-collateralization

---

## The Solution

**Prove It, Don't Show It.**

Kollat is a privacy-preserving middleware built natively on **Starknet** (Cairo VM) that allows users to prove their total net worth or collateral solvency across fragmented sources—including Starknet wallets and EVM-compatible chains—without revealing specific balances, wallet addresses, or asset composition.

### How It Works

```
┌─────────────────┐     ┌──────────────┐     ┌─────────────────────┐
│   User Wallet   │────▶│  Proof API   │────▶│  Verifier Contract  │
│                 │     │  (Bun/Node)  │     │  (Starknet Sepolia) │
└─────────────────┘     └──────────────┘     └─────────────────────┘
                               │                       │
                               ▼                       ▼
                        ┌──────────────┐     ┌─────────────────────┐
                        │    Noir      │     │    On-Chain         │
                        │  Circuit     │     │    Verification     │
                        │(Barretenberg)│     │                     │
                        └──────────────┘     └─────────────────────┘
```

1. **Aggregation** — User connects wallets locally, signs ownership messages, fetches balances via oracles
2. **Proof Generation** — ZK circuit proves `Total_Value >= Threshold` without revealing balances
3. **Verification** — Verifier checks the proof and receives TRUE/FALSE

---

## Repositories

This organization contains three interconnected repositories:

| Repository | Description |
|-----------|-------------|
| [website](https://github.com/kollat-strk/website) | Frontend (Next.js) — Landing page, wallet dashboard |
| [server](https://github.com/kollat-strk/server) | Backend API (Hono) — Auth, wallet, identity endpoints |
| [privacy-toolkit](https://github.com/kollat-strk/privacy-toolkit) | ZK Circuits (Noir) + Verifier Contracts (Cairo) |

---

### 1. Website (`kollat-strk/website`)

> The main web application — frontend only

**Tech Stack:**
- **Frontend:** Next.js 16, React 19, Tailwind CSS 4, Framer Motion
- **Wallets:** @starknet-react/core (StarkNet), @rainbow-me/rainbowkit (EVM)

**Features:**
- Dual-wallet support (StarkNet + EVM)
- Wallet binding with signature verification
- Balance fetching via Rabby API (EVM) and Endur API (StarkNet)
- Identity proof generation UI
- Magic link authentication
- Landing page with system documentation

**Quick Start:**
```bash
npm install
npm run dev
```

---

### 2. Server (`kollat-strk/server`)

> Backend API — handles authentication, wallet management, and identity proofs

**Tech Stack:**
- **Framework:** Hono (Fast web framework)
- **Database:** Drizzle ORM + SQLite
- **Blockchain:** Starknet.js, Viem

**API Endpoints:**
- `/api/auth/*` — Registration, login, logout, magic links, JWT sessions
- `/api/wallet/*` — Wallet binding, list, primary wallet, balance queries (EVM + StarkNet)
- `/api/identity/*` — Identity proof creation and retrieval

**Quick Start:**
```bash
cd server
npm install
npm run dev
# Server runs on http://localhost:4000
# API docs available at /docs
```

---

### 3. Privacy Toolkit (`kollat-strk/privacy-toolkit`)

> ZK Proof System — Noir circuits + Cairo verifier contracts

**Tech Stack:**
- **Circuit:** Noir (Barretenberg backend)
- **Verifier:** Cairo (Scarb), Starknet
- **Proof API:** Bun/Node.js

**What It Does:**
Implements a **Balance Tier ZK Proof System** that allows users to prove their token balance tier (plankton, fish, shrimp, dolphin, whale) without revealing exact balances.

| Component | Status |
|-----------|--------|
| Noir Circuit (balance_tier) | ✅ Working — Compiles, all 5 tests pass |
| Verifier Contract | ✅ Deployed — Starknet Sepolia |
| Proof API | ✅ Working — Port 3001 |
| Account Deployment | ✅ Deployed — Starknet Sepolia |

**Verifier Contract:** `0x01eb1ec37365fd0ec6d95e9ed4a2e8a361f3734e7693ed8ebc5e7eae21324b25` — [View on Voyager](https://sepolia.voyager.online/contract/0x01eb1ec37365fd0ec6d95e9ed4a2e8a361f3734e7693ed8ebc5e7eae21324b25)

**Architecture:**
```
┌─────────────────┐     ┌──────────────┐     ┌─────────────────────┐
│   User Wallet   │────▶│  Proof API   │────▶│  Verifier Contract  │
│                 │     │  (Bun/Node)  │     │  (Starknet Sepolia) │
└─────────────────┘     └──────────────┘     └─────────────────────┘
                               │                       │
                               ▼                       ▼
                        ┌──────────────┐     ┌─────────────────────┐
                        │    Noir      │     │    On-Chain         │
                        │  Circuit     │     │    Verification     │
                        │(Barretenberg)│     │                     │
                        └──────────────┘     └─────────────────────┘
```

**Use Cases:**
- Airdrop eligibility verification
- Tier-based access control
- Privacy-preserving credit scoring
- Anonymous balance verification for lending protocols

**Installation:**
```bash
# Prerequisites
# - Node.js/Bun
# - Noir (nargo)
# - Barretenberg (bb)
# - Garaga
# - Scarb
# - Starkli

# Run proof API
cd proof-api
bun install
bun run dev
```

---

## Use Cases

### 1. Global Solvency DeFi Loans

A user wants a loan on Starknet but their $1M in ETH sits in cold storage on Ethereum. Using Kollat, they prove they have $1M without bridging or exposing their wallet. The lender offers better LTV ratios.

### 2. Institutional Dark Trades

A whale needs to prove $5M in funds for an OTC trade without doxxing their main vault. Kollat provides instant cryptographic proof while keeping the wallet anonymous.

### 3. Premium Whitelist Access

A token launchpad requires $10K+ in assets to participate. Users prove they meet the threshold without linking their wealth addresses to a public smart contract.

---

## The Proof Equation

The core ZK circuit logic:

$$
\exists \ \{w_1, w_2, ... w_n\} \ \text{such that} \ \left( \sum_{i=1}^{n} \text{Balance}(w_i) \times \text{Price}(t) \right) \geq \text{ReqCollateral}
$$

Where:
- $w$ = Authenticated wallet address
- $Balance$ = Whitelisted asset quantity
- $Price(t)$ = Oracle price at snapshot time
- $ReqCollateral$ = Public threshold

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Starknet Native** | Built on Cairo VM with STARK-based proofs |
| **EVM Compatible** | Multi-chain liquidity aggregation |
| **Flash-Loan Resistance** | Time-weighted checks prevent instant fund borrowing |
| **Client-Side Privacy** | Private keys never leave user's local environment |

---

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

For ZK circuit contributions, see [privacy-toolkit](https://github.com/kollat-strk/privacy-toolkit).

---

## License

MIT License — See individual repositories for details.

---

<p align="center">
  <strong>Prove it, without showing it.</strong>
</p>
