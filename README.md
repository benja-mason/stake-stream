# 📡 StakeStream Protocol

**StakeStream** is a Bitcoin-aligned staking and governance protocol built on **Stacks L2**. It combines **capital-efficient staking**, **tiered yield mechanics**, and **on-chain governance** within a **compliance-aware, Bitcoin-native design**. It empowers institutional and retail users to earn yield and participate in protocol evolution securely and transparently.

---

## ⚙️ System Overview

StakeStream is designed as a **non-custodial**, **tiered staking platform** allowing users to:

* Stake **STX** tokens to earn dynamic yield
* Participate in **on-chain governance** via proposal creation and quadratic voting
* Leverage **lock periods** for multiplier rewards
* Manage risk with **cooldown-based withdrawals** and emergency protection mechanisms
* Maintain regulatory alignment through compliance hooks and modular architecture

The system leverages the **Proof-of-Transfer (PoX)** model via **Stacks L2**, ensuring alignment with Bitcoin’s security guarantees while enabling expressive smart contracts.

---

## 🏗 Contract Architecture

```text
+------------------------+
|   StakeStream Contract |
+------------------------+
| - STX Pool             |
| - User Positions       |
| - Staking Positions    |
| - Tier Levels          |
| - Proposals (Governance)|
| - Emergency Controls   |
+------------------------+

        ▲           ▲             ▲
        |           |             |
        |           |             |
  +-----+----+  +----+-----+   +--+-------+
  | Stake STX |  | Vote/Prop|   | Withdraw |
  +----------+  +----------+   +----------+
```

---

## 🔑 Core Modules

### 1. **Tiered Staking Engine**

Users stake **STX** with optional lock durations:

* **1 month (4,320 blocks)** → 1.25x reward multiplier
* **2 months (8,640 blocks)** → 1.5x reward multiplier
* **No lock** → base multiplier

#### Tier Levels

| Tier   | Stake Threshold | Reward Multiplier |
| ------ | --------------- | ----------------- |
| Bronze | 1M STX          | 1.0x              |
| Silver | 5M STX          | 1.5x              |
| Gold   | 10M STX         | 2.0x              |

Multiplied rewards: `Base APY * Tier Multiplier * Lock Multiplier`

---

### 2. **On-Chain Governance**

Users gain voting power based on staked amount. Core governance actions include:

* Proposal creation (requires ≥ 1M voting power)
* Voting (for/against) with quadratic weighting (to be implemented)
* Proposal execution is not yet coded but facilitated

Each proposal includes:

* Creator
* Description
* Voting window (start + end block)
* Thresholds for execution

---

### 3. **Cooldown Withdrawals**

To enhance security and discourage flash unstaking:

* Users must **initiate** unstake
* A **24-hour cooldown** (1,440 blocks) applies
* Once elapsed, users can **complete** withdrawal of their STX

---

### 4. **Emergency Mode**

A contract-wide flag allows the **owner** to:

* Pause operations (e.g., in case of an exploit)
* Protect user funds via `contract-paused` and `emergency-mode` variables

---

### 5. **Compliance Layer**

Hooks and architecture allow for:

* **Auditing and transaction monitoring**
* Support for **enterprise reporting tools**
* Enabling optional **compliance tokens** (e.g., `ANALYTICS-TOKEN` defined for extensibility)

---

## 🧩 Data Model

### Data Variables

| Variable           | Description                          |
| ------------------ | ------------------------------------ |
| `stx-pool`         | Total STX locked in the contract     |
| `base-reward-rate` | Base APY multiplier (e.g., 500 = 5%) |
| `cooldown-period`  | Time delay before withdraw           |
| `proposal-count`   | Tracks number of governance props    |

### Maps

#### `UserPositions`

Tracks user's financial profile including:

* `stx-staked`, `voting-power`, `tier-level`, `rewards-multiplier`

#### `StakingPositions`

Tracks lock-specific info:

* `amount`, `start-block`, `last-claim`, `cooldown-start`, `lock-period`

#### `TierLevels`

Defines tier requirements:

* `minimum-stake`, `reward-multiplier`, `features-enabled`

#### `Proposals`

Holds governance proposal metadata.

---

## 🔄 Functionality Overview

### 📥 Staking

```clarity
(stake-stx amount lock-period)
```

* Locks STX in contract
* Calculates and updates reward multipliers
* Updates user’s staking and voting profile

### 🧊 Cooldown/Unstake

```clarity
(initiate-unstake amount)
(complete-unstake)
```

* Initiates 24hr cooldown → unlocks funds after period

### 🗳 Governance

```clarity
(create-proposal description voting-period)
(vote-on-proposal proposal-id vote-for)
```

* Users can propose and vote on changes
* Votes weighted by stake-derived power

### 🔐 Emergency Controls

```clarity
(pause-contract)
(resume-contract)
```

* Admin-only access for emergency handling

---

## 🔁 Reward Calculation (Simplified)

```text
reward = (stake * base-rate * multiplier * blocks) / 14400000
```

* `base-rate` and `multiplier` are %-scaled integers (e.g., 150 = 1.5x)

---

## ✅ Deployment Checklist

* [x] Tier system initialized via `initialize-contract`
* [x] Owner-controlled emergency pause/resume
* [x] Tiered rewards and lock multipliers
* [x] Governance proposal and voting
* [x] Cooldown withdrawal mechanism
* [ ] Reward claiming function (to be added)
* [ ] Proposal execution logic (TBD)
* [ ] Analytics/compliance integrations (optional)

---

## 🔒 Security Design

* **Cooldown enforcement** ensures capital stability
* **Emergency mode** disables staking/unstaking functions
* **Min stake threshold** prevents spam participation
* Designed for **upgradeability** and **auditability**

---

## 🧠 Future Work

* ✳️ Add reward claiming and auto-compounding
* 🧮 Implement quadratic voting enforcement
* 🔁 Governance-controlled parameters (e.g., APY, cooldown)
* 🛡 Extend safety vaults for slashing/failover

---

## 📜 License

MIT License — open for audit, modification, and integration. Built for Bitcoin-aligned, decentralized finance.
