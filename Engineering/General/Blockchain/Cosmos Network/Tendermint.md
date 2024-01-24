Tendermint is an application-agnostic engine that is responsible for handling the networking and consensus layers of a blockchain.

Any application built on Tendermint needs to implement the ABCI interface in order to communicate with the underlying local Tendermint engine. Fortunately, you do not have to implement the ABCI interface. The Cosmos SDK provides a boilerplate implementation of it.


## Intro to ABCI

Tendermint Core (opens new window)(the "consensus engine") communicates with the application via a socket protocol that satisfies the ABCI.

Each ABCI Applicaiton mainteins three connection with Tendermint-Core:
1. Mempool connection - to validate Txs
2. Consensus connection - for committing new block only
3. Query connection - to query the application without engaging consensus connection.

The ABCI consists of 3 primary message types:
1. DeliverTx message - each transaction in the blockchain is delivered with this message. 
2. CheckTx message- is similar to **DeliverTx**, but it's only for validating transactions.
3. Commit message -is used to compute a cryptographic commitment to the current application state, to be placed into the next block header.

## Block life cycle

1. All user Txs goes into `Mempool cache` and after validation they go to `Mempool`
2. Proposer select Txs from `Mempool` into block and broadcast it.
4. Other nodes broadcast `Pre-Vote Block` signal to tell that see proposal in time and this proposal is valid, otherwise `Pre-Vote Nil`
5. If 2/3 `Pre-Votes Block` happen in time, nodes they broadcast `Pre-Commit Block`, otherwise `Pre-Commit Nil`
6. if 2/3 `Pre-Commit Block` happen in time, Proposer commits Block and Application updates the state(Balances etc)
7. If there are not `Pre-Commit Block`, block failed and new round begins with a new proposer.


- 2/3 pre votes is called polka and 2/3 pre commit is commit.
- Each broadcast uses a Secure P2P Encryption protocol
- All messages include a Validator Signature.
- 


![[Pasted image 20221112181821.png]]
