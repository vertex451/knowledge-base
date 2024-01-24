an open-sourse framework to build application-specific blockchains - blockchains that customized to operate a single application. 

Instead of building a decentralized application on top of an underlying blockchain like Ethereum, developers build their own blockchain from the ground up. This means building a full-node client, a light-client, and all the necessary interfaces (CLI, REST, ...) to interact with the nodes.

```
                ^  +-------------------------------+  ^
                |  |                               |  |   Built with Cosmos SDK
                |  |  State-machine = Application  |  |
                |  |                               |  v
                |  +-------------------------------+
                |  |                               |  ^
Blockchain node |  |           Consensus           |  |
                |  |                               |  |
                |  +-------------------------------+  |   Tendermint Core
                |  |                               |  |
                |  |           Networking          |  |
                |  |                               |  |
                v  +-------------------------------+  v
```

## What is wrong with Smart contracts?

- Smart Contracts are all run by the same virtual machine or it's shard, in both case they compete for resources to be interpreted, which would limit performance compared to a native application implemented at state-machine level.
- Smart Contracts share the same underlying environment is the resulting limitation in **sovereignty**. 
- Limitations that are caused by smart contracts immaturity, for example the Ethereum Virtual Machine does not allow developers to implement automatic execution of code.

## Application-Specific Blockchains Benefits

### 1. Flexibility

- ABCI (**A**pplication **B**lock**C**hain **I**nterface) can be wrapped in any programming language, meaning developers can build their state-machine in the programming language of their choice.
- The ABCI also allows developers to swap the consensus engine of their application-specific blockchain.
- Automatic execution of code. In the Cosmos SDK, logic can be automatically triggered at the beginning and the end of each block.

The goal of Cosmos and the Cosmos SDK is to make developer tooling as generic and composable as possible, so that each part of the stack can be forked, tweaked and improved without losing compatibility.

### 2. Cosmos SDK Modules

The ecosystem has open-soursed modules you can use for building you applicaiton(And you can create your own modules as well). Here are some of them:

- Auth - Authentication of accounts and transactions for Cosmos SDK applications.
- Bank - Token transfer functionalities.
- . . .
- Distribution - Fee distribution, and staking token provision distribution.
- Governance - On-chain proposals and voting.
- Params - Globally available parameter store.
- Slashing - Validator punishment mechanisms.
- Staking - Proof-of-Stake layer for public blockchains.
- Consensus - Consensus module for modifying Tendermint's ABCI consensus params.
- Upgrade - Software upgrades handling and coordination.
- Mint - Creation of new units of staking token.
- NFT - NFT module implemented based on ADR43.

### 3. Performance

- the application does not compete with others for computation and storage.

### 4. Security

- Developers are not constrained by the cryptographic functions made available by the underlying virtual-machines. They can use their own custom cryptography, and rely on well-audited crypto libraries.

### 5. Sovereignty

## Architecture overview

### Store types

#### KVStore

#### Multistore

`Multistore` is a store of `KVStores` that follows the [`Multistore interface`](https://github.com/cosmos/cosmos-sdk/blob/v0.40.0-rc6/store/types/store.go#L104-L133)

#### prefix.Store

is a `KVStore` wrapper which provides automatic key-prefixing functionalities over the underlying `KVStore`


## Tenderming

[[Tendermint]]

## CosmWasm

CosmWasm (Cosmos WebAssambley) is a smart contracting platform built for the Cosmos ecosystem.

## Ethermint 

Ethermint is a software developed to port the EVM into a Cosmos module. It makes scalable, high-throughput, PoS blockchains possible. These are fully compatible with Ethereum and the Cosmos SDK.
All Ethereum tools (such as Truffle and Metamask) are compatible with Ethermint. Developers can even port their Solidity smart contracts to interact with the Cosmos Ecosystem.

## Ignite CLI

Ignite scaffold will generate next dirs:
- `app` - Files that wire together the blockchain. The most important file is `app.go` that contains type definition of the blockchain and functions to create and initialize it.
- `cmd` - The main package responsible for the CLI of compiled binary.
- ...
-  `x` - Cosmos SDK modules and custom modules.
	- `keeper` handles the queries and txs and returns data

And creates next modules:
-   [`staking`](https://docs.cosmos.network/main/modules/staking/) for delegated Proof-of-Stake (PoS) consensus mechanism
-   [`bank`](https://docs.cosmos.network/main/modules/bank/) for fungible token transfers between accounts
-   [`gov`](https://docs.cosmos.network/main/modules/gov/) for on-chain governance
-   And other Cosmos SDK [modules](https://docs.cosmos.network/main/modules/) that provide the benefits of the extensive Cosmos SDK framework

Proto dir will contain next basic files:
- query.proto (opens new window)- for objects related to reading the state.
- tx.proto (opens new window)- for objects that relate to updating the state.
- genesis.proto (opens new window)- for the genesis. Ignite CLI modifies this file according to how your new storage elements evolve.

## Hello world

```
// to create blockchain with the default directory structure
ignite scaffold chain hello

// start your chain with hot reload
ignite chain serve

// create query
ignite scaffold query hello --response text

// Register the query handlers in Golang code 
	

```



