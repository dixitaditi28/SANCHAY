# SANCHAY

**Tokenized MSME inventory for instant financing and payment settlement**

*Built for the DRUNIX Hackathon (NPCI x Citi) | Track: Real Asset Tokenization*
*Secondary tracks: Real-Time Payments, Financial Inclusion, Innovative Fintech Ideas*

> Unlock liquidity from trusted physical assets without breaking the chain of ownership, custody, financing, and payment.

Working name in early documents: **AssetFlow**.

---

## Status

Hackathon prototype. This repository holds the product blueprint and the prototype implementation as it is built. It is **not** a production lending, securities, or payments system, and it makes no claim of regulatory or legal compliance.

---

## The problem

Indian MSMEs often hold significant value in physical inventory (grains, steel, raw materials, finished goods) but cannot use it as trusted, liquid collateral. The barrier is not only access to money. It is **trust and verifiability**. A lender cannot easily answer:

- Does the inventory exist, and in what quantity and quality?
- Who owns it, and where is it stored?
- Has it already been pledged to another lender?
- Has it been sold or released?
- Which payment should happen when the asset moves?

Records sit with the MSME, supplier, warehouse, transporter, lender, buyer, bank, and auditor, with no shared source of truth. The result is slow verification, duplicate-financing risk, ownership disputes, and delayed settlement.

Market context (from public reporting; verify against primary sources before citing):

| Indicator | Figure |
|---|---|
| MSME share of GDP | 31.1% (Economic Survey 2025-26) |
| Registered MSMEs | 7.47 crore |
| MSME share of merchandise exports, FY25 | 48.55% |
| Estimated MSME credit gap | ~₹28-30 lakh crore (Mavenark; SIDBI-Crisil 2025) |

---

## The solution

SANCHAY turns verified physical inventory into a digitally verifiable asset on a shared multi-party ledger (DRUNIX), then connects that asset to financing and payment settlement.

It is a **tokenization-to-finance-to-payment lifecycle**, not tokenization in isolation.

### Four connected layers

1. **Asset Verification Layer:** creates trusted digital records for physical inventory.
2. **Tokenization Layer:** records the asset and its lifecycle on DRUNIX.
3. **Financing Layer:** lets verified inventory back a financing request.
4. **Payment and Settlement Layer:** coordinates disbursement, buyer payment, repayment, and asset-state transitions.

### End-to-end lifecycle

```text
Physical inventory
  -> Warehouse / authorized verifier confirms quantity, quality, custody
  -> Digital asset record created
  -> Tokenized on DRUNIX
  -> MSME requests financing
  -> Lender evaluates the verified asset
  -> Financing approved
  -> Payment rail initiates disbursement
  -> Inventory stays pledged / controlled
  -> Inventory sold, buyer payment received
  -> Loan repayment and fees settled
  -> Encumbrance released
  -> Asset token transferred / closed
```

### Why tokenization (and not just a database)

| Need | How the shared ledger helps |
|---|---|
| Identity | Every verified batch gets a unique asset ID |
| Ownership | The network records who owns or controls the digital representation |
| Encumbrance | Explicit state: unencumbered, pledged, under dispute, frozen, released, transferred, redeemed/closed |
| Double-financing prevention | A lender queries shared state before financing; a pledged asset is rejected or escalated |

---

## Worked example: wheat inventory

| Item | Value |
|---|---|
| Commodity | Wheat, grade A, batch WHT-2026-001 |
| Quantity | 100 tonnes |
| Warehouse / owner | WH-001 / MSME-042 |
| Valuation snapshot | ₹4,50,000 |
| Financing requested | ₹2,00,000 for 30 days |
| Indicative LTV | 44.4% |
| Later sale to buyer | ₹4,60,000 |

Settlement waterfall on the buyer payment: loan principal, then accrued financing cost, then platform or transaction charges (if applicable), then the residual amount to the MSME. After repayment the asset moves from `PLEDGED` to `RELEASED / TRANSFERRED`.

---

## Users

| Role | What they do |
|---|---|
| MSME / borrower | Owns inventory, requests working capital |
| Warehouse / custodian | Verifies existence, quantity, quality, custody |
| Lender | Evaluates collateral and funds the request |
| Buyer | Purchases inventory and triggers settlement and release |

Secondary: banks, NBFCs, suppliers, logistics providers, auditors, insurers, and future regulators.

---

## Architecture

```text
Frontend (React + TypeScript + Tailwind)
   |
FastAPI application layer
   |
Domain services (asset, financing, settlement, risk)
   |
AI / agent tools
   |
DRUNIX ledger + payment adapters
```

Rules:

- The frontend never writes ledger state directly.
- The ledger is the source of truth for governed asset state.
- Raw sensitive documents and unnecessary PII stay off-chain.
- Payment confirmation is tied to financial state transitions.

### Planned stack

| Layer | Technology |
|---|---|
| Frontend | React, TypeScript, Tailwind |
| Backend | FastAPI, PostgreSQL |
| Ledger | DRUNIX (NPCI's open-source multi-organization blockchain platform) with chaincode |
| Payments | Hackathon-provided NPCI / payment APIs where available; an isolated sandbox adapter only where they are not |
| AI | Asset risk scoring, anomaly detection, document extraction, plain-language finance explanations |
| Agents | Collateral Verification, Financing Assessment, Settlement (with approval gates) |

### AI and agent safety

AI recommends and explains. Deterministic systems authorize and record. Agents cannot execute unrestricted payment or ledger writes. Every action follows:

```text
Agent proposal -> Backend validation -> Policy / authorization -> Human approval when required -> DRUNIX / payment execution -> Confirmation
```

---

## MVP scope

One commodity (wheat), one financing scenario, one end-to-end demo with four organizations: 1 MSME, 1 warehouse, 1 lender, 1 buyer.

Definition of done:

- [ ] Inventory record created and verified by an authorized verifier
- [ ] Tokenized asset record on DRUNIX with visible ownership and encumbrance
- [ ] Financing request created and approved by a lender
- [ ] Disbursement shown through the actual payment API or a clearly isolated sandbox adapter
- [ ] Asset enters pledged state
- [ ] Buyer purchase and settlement waterfall executed
- [ ] Financing marked repaid; asset released or transferred
- [ ] Duplicate-financing attempt blocked
- [ ] At least one AI/risk capability working
- [ ] At least one agentic workflow working with approval gates
- [ ] Every important transition auditable
- [ ] System runs reproducibly from this repository

### Test scenarios

| Scenario | Expected result |
|---|---|
| Happy path (create to release) | Full lifecycle completes |
| Duplicate pledge | Transaction rejected |
| Financing on unverified inventory | Financing unavailable |
| Payment timeout | Financing stays pending, never falsely succeeds |
| Stale valuation | Flagged for revaluation or manual review |
| Unexpected quantity change | Asset moves to exception / dispute state |
| Unauthorized transfer | Chaincode rejects the transition |

### Not in the MVP

See `docs/architecture.md` for the out-of-scope list as it is maintained.

---

## Repository structure

```text
sanchay/
├── frontend/        React + TypeScript + Tailwind interfaces
├── backend/         FastAPI app: api, models, schemas, services, payments, settlement, risk, agents
├── chaincode/       asset, financing, settlement contracts for DRUNIX
├── ai/              extraction, anomaly_detection, risk, evaluation
├── scripts/         seed_demo_data.py, reset_demo.py, test_scenarios.py
├── docs/            architecture, api-capabilities, verified-capabilities, demo-script, security
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Getting started

The commands below describe the intended workflow. They will be confirmed against the real environment as the prototype is built.

1. Clone the repository.

```bash
git clone https://github.com/<your-username>/sanchay.git
cd sanchay
```

2. Copy the environment template and fill in the values supplied by the hackathon.

```bash
cp .env.example .env
```

3. Set up DRUNIX and the payment APIs using the official hackathon documentation. Record anything you verify in `docs/verified-capabilities.md`. Do not assume API endpoints, request formats, authentication methods, or DRUNIX commands.

4. Start the stack.

```bash
docker compose up --build
```

5. Seed demo data.

```bash
python scripts/seed_demo_data.py
```

---

## Security and trust principles

1. Identity and role-based authorization for every actor.
2. Idempotency and replay protection on payment and settlement calls.
3. Data minimization: no raw sensitive documents or unnecessary PII on-chain.
4. Every consequential transition is auditable.
5. Human approval gates on consequential agent actions.
6. Use real hackathon APIs where available, and mock only what is genuinely unavailable.

---

## Pilot metrics (for evaluation, not market claims)

- **Operational:** time to verify collateral, request-to-disbursement time, share of automated verification steps, settlement time, manual reconciliation steps
- **Risk:** duplicate pledge attempts blocked, inconsistent records detected, suspicious transactions flagged
- **Financial:** financing value enabled, average collateral-to-financing ratio, settlement accuracy
- **Scalability:** assets per minute, transactions per second in the test network, organizations simulated, latency per lifecycle transition

---

## Roadmap

Agricultural inventory, then raw materials, manufacturing inventory, warehouse assets, and supply-chain collateral. The network grows from 1 MSME, 1 warehouse, 1 lender, and 1 buyer to thousands of MSMEs, hundreds of warehouses, multiple lenders, multiple buyers, and multiple payment providers. The shared ledger becomes more valuable as more independent parties join.

---

## Team

| Name | Role |
|---|---|
| [Name] | [Role] |

---

## References

- DRUNIX on GitHub: https://github.com/npci/drunix
- DRUNIX architecture doc: https://github.com/npci/drunix/blob/main/docs/drunix-arch.md
- DRUNIX releases: https://github.com/npci/drunix/releases

---

## Disclaimer

This is a product and engineering blueprint for a hackathon prototype, not a legal, banking, lending, securities, or regulatory opinion. Any production use involving tokenized assets, lending, collateral enforcement, payment execution, KYC/AML, data sharing, or regulated financial activity must be validated against applicable laws, RBI/NPCI requirements, participating-bank policies, and partner API contracts.

## License

[Choose a license, e.g. MIT or Apache-2.0]
