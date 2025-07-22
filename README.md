# BitVault Protocol

**Advanced Bitcoin Collateral Management System for Stacks Blockchain**

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Stacks](https://img.shields.io/badge/blockchain-Stacks-orange.svg)
![Clarity](https://img.shields.io/badge/language-Clarity-purple.svg)

## Overview

BitVault is a next-generation DeFi protocol built specifically for Bitcoin Layer 2, enabling sophisticated collateral management, synthetic asset creation, and decentralized liquidity provision with enterprise-grade security. The protocol revolutionizes Bitcoin DeFi by providing comprehensive infrastructure for Bitcoin holders to unlock liquidity without selling their assets.

## Key Features

### 🔐 Bitcoin-Native Collateralization

- Secure over-collateralized vaults with 150% minimum collateral ratio
- Real-time risk monitoring and automated safety checks
- Advanced liquidation mechanisms at 130% threshold

### 💰 Synthetic Stablecoin Minting

- USD-pegged tokens backed by Bitcoin collateral
- Flexible minting and burning capabilities
- Configurable supply limits and safety parameters

### 🌊 Automated Market Making

- Efficient price discovery and liquidity provision
- Dual-asset liquidity pools with fee rewards
- Proportional withdrawal mechanisms

### ⚡ Risk Management

- Oracle-based pricing with validation
- Multi-layered security and authorization
- Comprehensive error handling and edge case protection

### 📊 Real-time Monitoring

- Live collateral ratio calculations
- Pool health metrics and statistics
- Liquidity provider position tracking

## System Architecture

### Core Components

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Collateral    │    │   Stablecoin    │    │   Liquidity     │
│     Vaults      │    │    Minting      │    │     Pools       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │  Oracle Price   │
                    │   Management    │
                    └─────────────────┘
```

### Contract Architecture

#### Data Storage Maps

- **`balances`**: User Bitcoin balance tracking
- **`stablecoin-balances`**: User stablecoin holdings
- **`collateral-vaults`**: Vault positions and debt records
- **`liquidity-providers`**: LP token positions and contributions

#### State Variables

- **`oracle-price`**: Current Bitcoin price feed
- **`total-supply`**: Total stablecoin supply in circulation
- **`pool-btc-balance`**: AMM pool Bitcoin reserves
- **`pool-stable-balance`**: AMM pool stablecoin reserves

#### Security Constants

- **Minimum Collateral Ratio**: 150% (over-collateralization)
- **Liquidation Threshold**: 130% (risk protection)
- **Minimum Deposit**: 0.01 BTC (1,000,000 satoshis)
- **Pool Fee**: 0.3% (trading incentive)

## Core Functions

### Vault Management

```clarity
;; Deposit Bitcoin collateral
(deposit-collateral (btc-amount uint))

;; Mint stablecoins against collateral
(mint-stablecoin (amount uint))

;; Burn stablecoins to reduce debt
(burn-stablecoin (amount uint))
```

### Liquidity Operations

```clarity
;; Add dual-asset liquidity
(add-liquidity (btc-amount uint) (stable-amount uint))

;; Remove liquidity with proportional returns
(remove-liquidity (lp-tokens uint))
```

### Read-Only Functions

```clarity
;; Get vault details and health metrics
(get-vault-details (owner principal))
(get-collateral-ratio (owner principal))

;; Get pool statistics
(get-pool-details)

;; Get liquidity provider information
(get-lp-details (provider principal))
```

## Data Flow

### Collateral Deposit Flow

```
User → deposit-collateral() → transfer-balance() → update vault record
```

### Stablecoin Minting Flow

```
User → mint-stablecoin() → check-collateral-requirement() → update balances → mint tokens
```

### Liquidity Provision Flow

```
User → add-liquidity() → calculate-lp-tokens() → transfer assets → update pool state
```

## Getting Started

### Prerequisites

- [Clarinet](https://github.com/hirosystems/clarinet) for local development
- [Stacks CLI](https://docs.stacks.co/docs/cli) for deployment
- Node.js and npm for testing framework

### Installation

1. Clone the repository:

```bash
git clone https://github.com/donald-oputims/bitvault.git
cd bitvault
```

2. Install dependencies:

```bash
npm install
```

3. Run contract checks:

```bash
clarinet check
```

4. Run tests:

```bash
npm test
```

### Contract Deployment

1. Initialize the protocol:

```clarity
(contract-call? .bitvault initialize u5000000000) ;; $50,000 initial BTC price
```

2. Update oracle price (owner only):

```clarity
(contract-call? .bitvault update-price u5500000000) ;; Update to $55,000
```

## Usage Examples

### Creating a Collateral Vault

```clarity
;; Deposit 1 BTC as collateral (100,000,000 satoshis)
(contract-call? .bitvault deposit-collateral u100000000)

;; Mint $30,000 worth of stablecoins (at 150% collateral ratio)
(contract-call? .bitvault mint-stablecoin u30000000000)
```

### Providing Liquidity

```clarity
;; Add 0.5 BTC and $25,000 stablecoins to liquidity pool
(contract-call? .bitvault add-liquidity u50000000 u25000000000)
```

### Monitoring Vault Health

```clarity
;; Check vault collateral ratio
(contract-call? .bitvault get-collateral-ratio 'SP1ABCD...)

;; Get complete vault details
(contract-call? .bitvault get-vault-details 'SP1ABCD...)
```

## Security Considerations

### Risk Parameters

- **Over-collateralization**: 150% minimum ratio ensures buffer against price volatility
- **Liquidation Protection**: 130% threshold provides early warning system
- **Price Validation**: Oracle price bounds prevent manipulation attacks
- **Access Control**: Owner-only functions for critical protocol parameters

### Safety Mechanisms

- Input validation on all public functions
- Overflow/underflow protection through careful arithmetic
- State consistency checks before and after operations
- Comprehensive error handling with descriptive error codes

## Error Codes

| Code | Constant | Description |
|------|----------|-------------|
| 1000 | ERR-NOT-AUTHORIZED | Unauthorized access attempt |
| 1001 | ERR-INSUFFICIENT-BALANCE | Insufficient balance for operation |
| 1002 | ERR-INVALID-AMOUNT | Invalid amount parameter |
| 1003 | ERR-INSUFFICIENT-COLLATERAL | Below minimum collateral ratio |
| 1004 | ERR-POOL-EMPTY | Liquidity pool is empty |
| 1005 | ERR-SLIPPAGE-TOO-HIGH | Excessive price slippage |
| 1006 | ERR-BELOW-MINIMUM | Below minimum required amount |
| 1007 | ERR-ABOVE-MAXIMUM | Above maximum allowed amount |
| 1008 | ERR-ALREADY-INITIALIZED | Contract already initialized |
| 1009 | ERR-NOT-INITIALIZED | Contract not yet initialized |
| 1010 | ERR-INVALID-PRICE | Invalid oracle price value |

## Testing

Run the comprehensive test suite:

```bash
# Check contract syntax and types
clarinet check

# Run integration tests
npm test

# Run specific test file
npm test -- bitvault.test.ts
```

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/new-feature`
3. Commit changes: `git commit -am 'Add new feature'`
4. Push to branch: `git push origin feature/new-feature`
5. Submit a pull request

## Roadmap

- [ ] Advanced liquidation mechanisms
- [ ] Multi-oracle price aggregation
- [ ] Governance token integration
- [ ] Cross-chain bridge support
- [ ] Advanced trading strategies
- [ ] Mobile wallet integration

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built on [Stacks Blockchain](https://stacks.co)
- Powered by [Clarity Smart Contracts](https://clarity-lang.org)
- Inspired by leading DeFi protocols

---

## ⚡ Securing Bitcoin's Future in DeFi ⚡
