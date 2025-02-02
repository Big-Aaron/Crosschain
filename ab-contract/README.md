# CrossChain Smart Contracts

## Table of contents

1. [General](#general)
2. [Why CrossChain?](#why)
   1. [Our Thesis](#thesis)
   2. [Ecosystem Problems](#ecosystem-problems)
   3. [Developer Problems](#developer-problems)
   4. [Solution](#solution)
4. [Architecture](#architecture)
   1. [Contract Flow](#contract-flow)
   2. [Diamond Helper Contracts](#diamond-helper-contracts)
5. [Repository Structure](#repository-structure)
6. [Getting Started](#getting-started)
   1. [INSTALL](#install)
   2. [TEST](#test)
   3. [TEST With Foundry/Forge](#foundry-forge)
9. [More Information](#more-information)

## General<a name="general"></a>

Our vision is to create a middle layer between DeFi infrastructure and the application layer.
CrossChain aims to aggregate and abstract away the most important bridges and connect them to DEXs and DEX aggregators on each chain to facilitate cross-chain any-2-any swaps.

To decide which bridge to use, we assess and measure the degree of decentralization, trust assumptions, fees, gas efficiency, speed, and other qualitative and quantitative factors.
Then, we use the thresholds and preferences of our integration partners and end-users to select the right path.

## Why CrossChain?<a name="why"></a>

### Our Thesis<a name="thesis"></a>

- The future is multi-chain
- Cross-chain bridging solutions will play a major role on infrastructure level
- Aggregation will pave the way for mass adoption

---

### Ecosystem Problems<a name="ecosystem-problems"></a>

**dApps**: Many users come across a new interesting dApp on a chain they don't have funds in and struggle to get their funds there. This is significant friction in user onboarding as they have to research and find bridges to that chain to start using the dApp.

**Yield Aggregators**: There are definitely protocols with better yield on new L2/side-chains but there isn't a secure, reliable way to transfer your funds.

**Wallets**: Multichain wallets want to compete with CEXes, but they don't have a way to allow easy swap between assets like CEXes.

**DeFi Protocols**: DeFi Dashboards, lending protocols, yield farms, etc., that are present on new chains create a need to do cross-chain swaps, but their users have to wander the ecosystem to quench this need.

---

### Developer Problems<a name="developer-problems"></a>

**Too many bridges** to educate yourself about.
It'd be good to have access to all of them and getting good guidance from people and algorithms that are specialized.

➔ CrossChain does that.

**Bridges are still immature** so it's good to have not only one bridge but fallback solutions in place.
Immaturity comes with security risks, insufficient liquidity and a lot of maintenance overhead.

➔ CrossChain maintains all bridge connections, gives you access to multiple ones and handles fallbacks and decision-making programmatically.

**Bridges are most often not enough**.
You also need DEXes/DEX aggregators as bridges are limited to stable-coins and native currencies.

➔ CrossChain not only aggregates bridges, but also connects to sorts of DEX aggregators and if not available, the DEXs directly in order to find the best swap possible to arrive at the desired token and to allow to start the whole process with any asset.

---

### Solution<a name="solution"></a>

A data mesh of cross-chain liquidity sources: cross-chain liquidity networks, bridges, DEXes, bridges, and lending protocols.

As a bridge and DEX aggregator, CrossChain can route any asset on any chain to the desired asset on the desired chain, thus providing a remarkable UX to their users.

All of this will be made available on an API/Contract level which comes as SDK, iFrame solution, and as a widget for other developers to plug directly into their products.
No need for users to leave your dApps anymore.

## Architecture<a name="architecture"></a>

The CrossChain Contract is built using the EIP-2535 (Multi-facet Proxy) standard. The contract logic lives behind a single contract that in turn uses DELEGATECALL to call **facet** contracts that contain the business logic.

All business logic is built using **facet** contracts which live in `src/Facets`.

For more information on EIP-2535 you can view the entire EIP [here](https://eips.ethereum.org/EIPS/eip-2535).

---

### Contract Flow<a name="contract-flow"></a>

A basic example would be a user bridging from one chain to another using Hop Protocol. The user would interact with the Diamond contract which would pass the Hop specific call to the HopFacet which then passes required calls + parameters to Hop Protocol's contracts.

The basic flow is illustrated below.

```mermaid
graph TD;
    D{Diamond}-- DELEGATECALL -->MultichainFacet;
    D{Diamond}-- DELEGATECALL -->HopFacet;
    D{Diamond}-- DELEGATECALL -->AnyswapFacet;
    D{Diamond}-- DELEGATECALL -->CBridgeFacet;
    D{Diamond}-- DELEGATECALL -->HyphenFacet;
    D{Diamond}-- DELEGATECALL -->StargateFacet;
```

---

### Diamond Helper Contracts<a name="diamond-helper-contracts"></a>

The Diamond contract is deployed along with some helper contracts that facilitate things like upgrading facet contracts, look-ups for methods on facet contracts, ownership checking and withdrawals of funds. For specific details please check out [EIP-2535](https://eips.ethereum.org/EIPS/eip-2535).

```mermaid
graph TD;
    D{Diamond}-- DELEGATECALL -->DiamondCutFacet;
    D{Diamond}-- DELEGATECALL -->DiamondLoupeFacet;
    D{Diamond}-- DELEGATECALL -->OwnershipFacet;
    D{Diamond}-- DELEGATECALL -->WithdrawFacet;
```

## Repository Structure<a name="repository-structure"></a>

```
contracts
│ README.md                   // you are here
│ ...                         // setup and development configuration files
│
├─── config                   // service configuration files
├─── constants                // general constants
├─── deploy                   // deployment scripts
├─── diamondABI               // Diamond ABI definition
├─── export                   // deployed results
├─── scripts                  // scripts containing sample calls for demonstration
│
├─── src                      // the contract code
│   ├── Facets                // service facets
│   ├── Interfaces            // interface definitions
│   └── Libraries             // library definitions
│
├───tasks
│   │ generateDiamondABI.ts   // script to generate Diamond ABI including all facets
│
├─── test                     // contract unit tests
│   ├─── facets               // facet tests
│   ├─── fixtures             // service fixtures for running the tests
│   └─── utils                // testing utility functions
│
└─── utils                    // utility scripts
```

## Getting Started<a name="getting-started"></a>

Make sure to copy `.env.example` to `.env` and fill out the missing values. Tests might fail with missing environment variables if some of the variables are blank.

### INSTALL<a name="install"></a>

```bash
yarn
```

### TEST<a name="test"></a>

```bash
yarn test
```

### TEST With Foundry/Forge<a name="foundry-forge"></a>

Make sure to install the latest version of Foundry by downloading the installer.

```
curl -L https://foundry.paradigm.xyz | bash
```

Then, in a new terminal session or after reloading your PATH, run it to get the latest forge and cast binaries:

```
foundryup
```

Install dependencies

```
forge install
```

Run tests

```
yarn test
```

## More Information<a name="more-information"></a>

- [Website](https://www.crosschain.tools/home)
- [General Documentation](https://www.crosschain.tools/home)
