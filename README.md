# BitLock Protocol

**Transform idle Bitcoin into productive capital through decentralized, trust-minimized stablecoin liquidity.**

---

## 🧠 Overview

BitLock is a collateralized lending protocol that enables Bitcoin holders to unlock liquidity without selling their BTC. Built on the Stacks blockchain, it leverages smart contracts and price oracles to mint stablecoins against locked BTC while maintaining trustless, algorithmic risk management and smart liquidation mechanisms.

This protocol empowers users to:

* Lock BTC as collateral via Stacks.
* Mint stablecoins proportional to the value of locked BTC.
* Maintain BTC price exposure and custody.
* Rely on protocol-governed health checks and liquidations.

---

## ⚙️ System Architecture

### Components

* **BTC Custody Layer**: Off-chain BTC is locked through mechanisms such as Peg-out contracts or custodial multisigs integrated via Stacks.
* **BitLock Smart Contract**: Core Clarity smart contract managing loan issuance, repayments, liquidations, and collateral tracking.
* **Price Oracle Feeds**: Admin-updated on-chain oracle for BTC pricing used to determine loan health.
* **User Interface (dApp)**: External front-end interacting with the smart contract via Clarity calls and transactions.
* **Risk Engine (On-chain)**: Fully on-chain enforcement of health factors, collateral ratios, and liquidation logic.

---

## 📜 Contract Architecture

### Constants

Defined constants include:

* Authorization: `CONTRACT-OWNER`
* Supported assets: `"BTC"`, `"STX"`
* Error codes for authorization, collateral validation, loan integrity, and price feed sanity checks.

### Data Variables

* **Platform Configuration**: `platform-initialized`, `minimum-collateral-ratio`, `liquidation-threshold`, `platform-fee-rate`
* **Metrics**: `total-btc-locked`, `total-loans-issued`

### Maps

* `loans`: Tracks individual loan positions by ID.
* `user-loans`: Indexes active loan IDs per user.
* `collateral-prices`: On-chain oracle storing asset prices.

---

## 🔁 Data Flow

### 1. **Initialization**

Admin sets up the platform with `initialize-platform` and configures:

* Minimum collateral ratio.
* Liquidation threshold.
* BTC price via oracle.

### 2. **Collateral Deposit**

User locks BTC off-chain and reflects the deposit on-chain via `deposit-collateral(amount)`.

### 3. **Loan Request**

User requests stablecoin loan using:

```clojure
(request-loan (collateral) (loan-amount))
```

Protocol checks:

* BTC price via oracle.
* Collateralization ratio.
* Enforces upper limit on loans per user.

If valid, the loan is issued, and metadata is stored in the `loans` map.

### 4. **Repayment**

Users repay via `repay-loan(loan-id, amount)`, which includes:

* Principal + interest based on block height.
* Removal of user-loan association.
* Release of collateral (off-chain flow assumed post-payment).

### 5. **Liquidation Check**

Automated check using:

```clojure
(check-liquidation loan-id)
```

If the current collateral ratio < liquidation threshold:

* Loan is marked `liquidated`
* Removed from `user-loans`

---

## 🔐 Risk Management

* **Collateral Ratio**: Enforced minimum (e.g., 150%).
* **Liquidation Threshold**: Triggered at 120% or below.
* **Interest Calculation**: Block-based, accrued on repayment.
* **Oracle Sanity**: Only whitelisted assets and bounded prices accepted.

---

## ✅ Key Features

* **Trustless & Algorithmic**: Collateral safety and loan integrity are enforced on-chain.
* **Decentralized Oracle Integration**: Only valid BTC pricing data is accepted.
* **Efficient User Experience**: Loan and repayment flows are simplified and gas-efficient.
* **Modular & Extensible**: Supports multiple assets (e.g., STX), more to be integrated.

---

## 🚧 Future Improvements

* Integration with BTC Peg-in/Peg-out mechanisms (e.g., sBTC).
* Automated price oracles (via Chainlink, or delegated signer networks).
* Stablecoin minting and burning (future module).
* Multi-asset collateral pools.

---

## 🧪 Testing & Deployment

Before deployment:

* Ensure `platform-initialized` is set.
* Oracle prices are seeded via `update-price-feed`.

Sample test flow:

1. Initialize contract.
2. Deposit BTC equivalent.
3. Update BTC price feed.
4. Request and repay loan.
5. Simulate liquidation via price drop.

---

## 📄 License

This protocol is licensed under the MIT License.
