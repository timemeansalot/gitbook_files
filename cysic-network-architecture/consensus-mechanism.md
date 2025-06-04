# Consensus Mechanism

The consensus mechanism of the Cysic Network is based on the CometBFT algorithm. CometBFT follows the Byzantine Fault Tolerance (BFT) consensus model, meaning it is designed to handle situations where some nodes in the network behave maliciously or fail to follow the protocol correctly. It tolerates up to one-third of the nodes failing or behaving maliciously without compromising the system’s integrity. The consensus algorithm consists of three main stages:

* **Propose**: One validator is chosen as the proposer in each round to propose a new block of transactions.
* **Pre-vote**: After a block is proposed, validators broadcast their pre-vote messages for the proposed block.
* **Pre-commit**: Validators then send their pre-commit messages for the block if they receive enough pre-votes (typically two-thirds or more).

If enough pre-commit votes are received, the block is committed, and the transactions within it are finalized. The consensus algorithm uses voting rounds and timeouts to ensure progress is made in the event of network delays or failures. Validators vote on blocks, and if a round fails (due to network partitions or Byzantine behavior), the process continues with a new round and a different proposer. Once a block is committed, it is final and cannot be reverted. This differs from traditional Proof of Work (PoW) systems, where finality is probabilistic, and there is always a chance of chain reorganization. CometBFT is highly secure and guarantees consensus as long as more than two-thirds of the validators are honest.

The consensus process relies on a set of validators responsible for proposing and validating new blocks. Validators are selected based on a staking mechanism, where nodes with more stake (tokens) have a higher probability of being chosen as proposers.&#x20;



In Cysic Network, this staking mechanism is modified to incentivize computing providers to contribute to the network, which we called Proof of Compute. **We will announce the computing puzzle in Q3 2025.**
