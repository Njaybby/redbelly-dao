# Redbelly DAO

A next-generation governance, developer, and institutional hub for **Redbelly Network**, the compliant Layer 1 for real-world asset tokenisation. Built with Next.js (App Router), TypeScript, Tailwind, Framer Motion, and wagmi/viem.

> The application shell is real: it builds clean, ships to Vercel, and connects real wallets to the **verified** Redbelly network configuration. The governance data behind it is not connected yet. Every DAO screen renders a written brief describing what belongs there instead of placeholder numbers. See [What still needs data](#what-still-needs-data).

---

## Quick start

```bash
npm install
cp .env.example .env.local   # optional: add a WalletConnect project id
npm run dev                  # http://localhost:3000
```

Production:

```bash
npm run build
npm run start
```

Both `npm run build` and `npm run typecheck` pass with zero errors, and `npm run lint` is clean.

---

## Environment variables

| Variable | Required | Purpose |
| --- | --- | --- |
| `NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID` | No | Enables the WalletConnect option. Get one free at [cloud.reown.com](https://cloud.reown.com). Without it, MetaMask / injected wallets still work and the WC option is simply hidden. |
| `NEXT_PUBLIC_SITE_URL` | No | Canonical site URL for SEO metadata and the sitemap. Set to your Vercel domain in production. |

---

## Deploy to Vercel

1. Push this repo to GitHub.
2. Import it in Vercel. It auto-detects Next.js, no config needed.
3. Add the two env vars above in **Project → Settings → Environment Variables**.
4. Deploy. The `sitemap.xml` and `robots.txt` are generated automatically.

---

## Verified network configuration

Chain parameters are confirmed against the Redbelly developer portal, `ethereum-lists/chains`, and Routescan, not guessed. Defined once in [`lib/chains.ts`](lib/chains.ts) and reused everywhere.

| | Mainnet | Testnet |
| --- | --- | --- |
| Chain ID | `151` | `153` |
| RPC | `https://governors.mainnet.redbelly.network` | `https://governors.testnet.redbelly.network` |
| Currency | RBNT (18) | RBNT (18) |
| Explorer | `https://redbelly.routescan.io` | `https://redbelly.testnet.routescan.io` |

---

## Architecture

```
app/                  App Router routes (home, developers, dao/*, institutional)
  layout.tsx          Root layout: SEO metadata, JSON-LD, providers, nav, footer, ⌘K
  providers.tsx       wagmi + react-query + next-themes
  sitemap.ts robots.ts
components/            Navbar (mega menu + mobile), footer, wallet button, command menu,
                      theme toggle, network mesh (signature motif), UI primitives
features/dao/          DAO domain: types, service layer, proposal list, task board, vote panel
lib/                  chains.ts (verified), wagmi.ts, utils.ts
```

### The service layer pattern
Every DAO screen reads from [`features/dao/service.ts`](features/dao/service.ts), which exposes an **async API** (`listProposals`, `getTreasury`, `listTasks`, ...). Each function currently resolves empty. To fill a surface, replace that one function body with a real read. **The UI never changes.**

Three files matter:

| File | Role |
| --- | --- |
| [`features/dao/types.ts`](features/dao/types.ts) | The contract. Field names and shapes the UI expects. |
| [`features/dao/content-spec.ts`](features/dao/content-spec.ts) | The brief. What belongs in each slot, where it comes from, and what is still unresolved. |
| [`features/dao/service.ts`](features/dao/service.ts) | The seam. One function per surface, all returning empty today. |

## What still needs data

The site was handed over with invented proposals, a fabricated treasury, six made-up tasks and eight community projects that did not exist. All of it has been removed. Each screen below now renders its own implementation brief in place of the data.

| Screen | Service method | Source it needs |
| --- | --- | --- |
| `/dao/proposals` | `listProposals` | The DAO Snapshot space, via the Snapshot GraphQL API |
| `/dao/treasury` | `getTreasury` | Treasury addresses on chain 151, plus a pricing feed |
| `/dao/tasks` | `listTasks` | The live Redbelly DAO Community Task Board |
| `/dao` working groups | `listWorkingGroups` | The DAO working group register |
| `/dao/showcase` | `listShowcaseProjects` | Community submissions, every link verified |
| Home and `/dao` tiles | `metrics` | Derived from the above. Never author these by hand. |

Unresolved before any of this can be wired:

- The **Snapshot space identifier** is not recorded in this repository or in the DAO resource documents.
- The **treasury addresses** are not listed anywhere here.
- Whether `/dao/tasks` **mirrors the task board or links to it** is undecided. Running a second copy is how the two drift apart.
- The Proposal type has **no `body` field**, so the detail page has nowhere to render the full proposal text.

Voting is not connected either. The vote panel collects a choice and states plainly that nothing was submitted.

---

## What works right now

- **Wallet**: connect / disconnect / copy address / balance, one-click network switch, wrong-network detection, and `wallet_addEthereumChain` to add Redbelly. Session persists via cookie storage.
- **Navigation**: animated desktop mega menu, dedicated mobile sheet, everything ≤ 2 clicks from home.
- **Global search**: ⌘K / Ctrl-K, indexes nav, pages, proposals, treasury, and tasks.
- **DAO**: governance hub, filter/sort/search proposal list, proposal detail with tallies and quorum, wallet-gated vote panel, treasury dashboard, task board. All of these render their data brief until a source is connected.
- **Developers**: verified network table, add-to-wallet buttons, viem snippet, ecosystem links.
- **Institutional**: security, compliance, Project Acacia, metrics, contact.
- **Foundations**: dark/light with system detection + persistence, SEO metadata + OG/Twitter + JSON-LD + sitemap/robots, reduced-motion support, keyboard focus, skip link, responsive to mobile.

## Roadmap (same patterns, drop-in)

These extend the established components/service layer without new architecture:
- Governance calendar + delegation management screen
- Real governance writes (replace the inert submit in `vote-panel.tsx`)
- Data adapters in `service.ts`, one per surface listed above
- Per-working-group detail routes
- MDX-powered documentation section
- Playwright a11y + e2e tests, and self-hosted fonts via `next/font/local`

---

## Credits

This site was designed and built by **Cyon** ([@Cyon0x](https://github.com/Cyon0x)) as TASK-15 for the Redbelly DAO Community Task Board, and settled on-chain on 4 September 2026. See [CREDITS.md](CREDITS.md) for the full attribution, including the contributors whose deliverables are published here.

## Implementing the Task Board deliverables

Twenty-one paid deliverables from Cycles 1 and 2 are being published through this site. [IMPLEMENTATION.md](IMPLEMENTATION.md) sets out where each one goes, what has to be built to receive it, and in what order. It is the companion to `features/dao/content-spec.ts`, which covers the surfaces that already exist.

## Notes

Nothing here is financial advice. Fonts are self-hosted at build time through `next/font`, so the site makes no runtime font requests.
