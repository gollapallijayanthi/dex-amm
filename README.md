# DEX AMM Project

## Overview

This project implements a simplified **Decentralized Exchange (DEX)** based on the **Automated Market Maker (AMM)** model, similar to Uniswap V2.
The DEX allows users to add liquidity, remove liquidity, and swap between two ERC-20 tokens without relying on centralized order books or intermediaries.

The implementation focuses on correctness, mathematical accuracy, security best practices, and comprehensive automated testing.

---

## Features

* Initial liquidity provision and LP token minting
* Subsequent liquidity additions with ratio enforcement
* Liquidity removal with proportional asset withdrawal
* Token swaps using the constant product formula (x × y = k)
* 0.3% trading fee retained in the pool
* Fee accumulation for liquidity providers
* Internal LP accounting
* Real-time reserve and price tracking
* Event emission for all critical actions
* Fully Dockerized development and test environment
* 25+ automated test cases with ≥ 80% code coverage

---

## Architecture

### Smart Contracts

* **DEX.sol**

  * Core AMM logic
  * Manages liquidity pools, swaps, pricing, and fee mechanics
  * Tracks LP ownership internally

* **MockERC20.sol**

  * Simple ERC-20 token used for testing
  * Supports minting for test scenarios

### Design Decisions

* LP tokens are tracked internally instead of deploying a separate ERC-20 LP token contract
* Solidity `^0.8.x` is used for built-in overflow/underflow protection
* Explicit reserve tracking avoids reliance on token balance queries
* Fees remain in the pool to increase LP value over time

---

## Mathematical Implementation

### Constant Product Formula

The DEX enforces the invariant:

```
x * y = k
```

Where:

* `x` = reserve of Token A
* `y` = reserve of Token B
* `k` = constant (increases slightly due to fees)

This invariant governs all swap pricing.

---

### Fee Calculation (0.3%)

A 0.3% trading fee is applied to each swap:

```
amountInWithFee = amountIn * 997
numerator        = amountInWithFee * reserveOut
denominator      = reserveIn * 1000 + amountInWithFee
amountOut        = numerator / denominator
```

* 99.7% of the input amount is used in pricing
* The remaining 0.3% stays in the pool
* Liquidity providers benefit automatically

---

### LP Token Minting

#### Initial Liquidity

```
liquidityMinted = sqrt(amountA * amountB)
```

The first liquidity provider sets the initial price.

#### Subsequent Liquidity

Liquidity must maintain the existing price ratio:

```
liquidityMinted = (amountA * totalLiquidity) / reserveA
```

---

### Liquidity Removal

Withdrawals are proportional to LP ownership:

```
amountA = (liquidityBurned * reserveA) / totalLiquidity
amountB = (liquidityBurned * reserveB) / totalLiquidity
```

Withdrawn amounts include accumulated fees.

---

## Setup Instructions

### Prerequisites

* Docker and Docker Compose
* Git
* Node.js (optional for local execution)

---

### Installation (Docker – Recommended)

```bash
git clone https://github.com/gollapallijayanthi/dex-amm.git
cd dex-amm
docker-compose up -d
```

---

### Compile Contracts

```bash
docker-compose exec app npm run compile
```

---

### Run Tests

```bash
docker-compose exec app npm test
```

---

### Run Coverage

```bash
docker-compose exec app npm run coverage
```

Code coverage is **≥ 80%**, satisfying submission requirements.

---

### Stop Docker

```bash
docker-compose down
```

---

## Running Locally (Without Docker)

```bash
npm install
npm run compile
npm test
npm run coverage
```

---

## Deployment

A deployment script is included:

```
scripts/deploy.js
```

Deploy locally using:

```bash
npm run deploy
```

---

## Contract Addresses

This project is intended for local testing and evaluation.
No testnet or mainnet deployment is included.

---

## Known Limitations

* Supports only a single trading pair
* No slippage protection parameters
* No deadline enforcement for swaps
* No flash swaps or multi-hop routing

These features are intentionally excluded to keep the core AMM logic simple and auditable.

---

## Security Considerations

* Solidity 0.8+ overflow and underflow protection
* Strict input validation (non-zero amounts, sufficient liquidity)
* Constant product invariant enforcement
* Fees retained within the pool
* State updates occur before external token transfers
* No privileged roles or centralized control

---

## Verification Checklist

Before submission, the following commands were executed successfully:

```bash
docker-compose exec app npm run compile
docker-compose exec app npm test
docker-compose exec app npm run coverage
```

Results:

* All contracts compile without errors
* All 25+ test cases pass
* Code coverage ≥ 80%
* Docker environment builds and runs successfully

---

