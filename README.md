# BitStream Channels - Bitcoin-Native Payment Channels on Stacks L2

Enterprise-grade payment channels enabling Bitcoin-final settlement through Stacks Layer 2 infrastructure.

## Table of Contents

- [BitStream Channels - Bitcoin-Native Payment Channels on Stacks L2](#bitstream-channels---bitcoin-native-payment-channels-on-stacks-l2)
	- [Table of Contents](#table-of-contents)
	- [Overview](#overview)
	- [Technical Specifications](#technical-specifications)
	- [Key Concepts](#key-concepts)
		- [Payment Channel Lifecycle](#payment-channel-lifecycle)
		- [Stacks L2 Advantage](#stacks-l2-advantage)
	- [Architecture Overview](#architecture-overview)
		- [Core Components](#core-components)
	- [Core Functionality](#core-functionality)
		- [1. Channel Creation (`create-channel`)](#1-channel-creation-create-channel)
		- [2. Channel Funding (`fund-channel`)](#2-channel-funding-fund-channel)
		- [3. Cooperative Closure (`close-channel-cooperative`)](#3-cooperative-closure-close-channel-cooperative)
		- [4. Unilateral Closure (`initiate-unilateral-close`)](#4-unilateral-closure-initiate-unilateral-close)
	- [Security Model](#security-model)
		- [Three-Layer Protection](#three-layer-protection)
	- [Dispute Resolution Protocol](#dispute-resolution-protocol)
	- [Compliance](#compliance)
		- [Regulatory Alignment](#regulatory-alignment)
	- [Installation](#installation)
		- [Prerequisites](#prerequisites)
	- [Contributing](#contributing)
	- [Acknowledgments](#acknowledgments)

## Overview

BitStream Channels implement non-custodial payment channels that combine:

- **Bitcoin-anchored security** via Stacks' proof-of-transfer mechanism
- **Subsecond off-chain transactions** with cryptographic guarantees
- **Enterprise compliance** through Bitcoin's regulatory recognition
- **STX/BTC interoperability** using Stacks' native Bitcoin peg

```ascii
User Flow:
[Channel Creation] → [Off-chain Updates] → [Settlement Choice]
       │                     │                  ├─[Cooperative Close]
       │                     │                  └─[Unilateral Close]
       └─[Bitcoin TX Anchor]←←←[Dispute Period]←─┘
```

## Technical Specifications

| Parameter             | Value                          |
| --------------------- | ------------------------------ |
| Clarity Version       | 3.1                            |
| STX Lockup            | Multi-sig 2-of-2               |
| Dispute Period        | 1008 Bitcoin blocks (~1 week)  |
| Network Compatibility | Stacks Mainnet/Mocknet         |
| Audit Status          | Undergoing formal verification |

## Key Concepts

### Payment Channel Lifecycle

1. **Channel Creation**: Lock STX in 2-of-2 multisig
2. **Off-chain Updates**: Exchange signed balance states
3. **Settlement**:
   - **Cooperative**: Instant mutual close
   - **Unilateral**: Bitcoin-timestamped dispute window

### Stacks L2 Advantage

- Inherits Bitcoin's proof-of-work security
- Uses Bitcoin blocks for dispute deadlines
- Enables BTC settlement via Stacks' sBTC protocol

## Architecture Overview

### Core Components

1. **Channel Registry**: Global mapping of active channels
2. **State Machine**: Enforces channel lifecycle rules
3. **Signature Verifier**: Schnorr-compatible auth system
4. **Settlement Engine**: Handles STX/BTC distributions

```clarity
Data Model:
PaymentChannel {
  channel-id: buffer32
  participants: (principal, principal)
  balances: (uint, uint)
  status: enum [OPEN, CLOSING, CLOSED]
  disputeDeadline: uint
  nonce: uint
}
```

## Core Functionality

### 1. Channel Creation (`create-channel`)

- Initializes 2-of-2 multisig escrow
- Requires minimum deposit
- Generates unique channel ID

**Parameters:**

```clarity
(define-public (create-channel
  (channel-id (buff 32))
  (counterparty principal)
  (deposit uint)
)
```

### 2. Channel Funding (`fund-channel`)

- Adds additional STX to open channel
- Updates multisig balance
- Maintains state versioning

### 3. Cooperative Closure (`close-channel-cooperative`)

- Requires mutual signatures
- Instant balance distribution
- Eliminates dispute risk

### 4. Unilateral Closure (`initiate-unilateral-close`)

- Starts Bitcoin-timed challenge period
- Requires latest signed state
- Enables emergency withdrawals

## Security Model

### Three-Layer Protection

1. **Multisig Enforcement**: 2-of-2 participant authorization
2. **Replay Protection**: State nonce increment system
3. **Bitcoin Timestamps**: Dispute deadlines tied to BTC blocks

## Dispute Resolution Protocol

1. **Initiation**: Submit last agreed state with signatures
2. **Challenge Period**: 1008 BTC blocks (~1 week)
3. **Finalization**: Automatic STX distribution post-deadline

```clarity
Dispute Timeline:
Block 0: Unilateral close initiated
Block 504: Counterparty can submit newer state
Block 1008: Funds automatically distributed
```

## Compliance

### Regulatory Alignment

- FATF Travel Rule compatible through Stacks addresses
- OFAC-compliant address screening support
- Bitcoin's monetary policy inheritance

## Installation

### Prerequisites

- Clarinet 2.1+
- Node.js 18.x

## Contributing

1. Fork repository
2. Create feature branch (`feat/your-feature`)
3. Submit PR with:
   - Technical specification
   - Test coverage report
   - Security impact analysis

## Acknowledgments

- Stacks Open Internet Foundation
- Bitcoin Core developers
- Clarity language community
