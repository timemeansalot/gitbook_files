# ZK Proof Layer

## Introduction

A Zero-Knowledge Proof (ZKP) is a cryptographic concept that allows one party (the prover) to prove the truth of a statement to another party (the verifier) without revealing any information beyond the validity of the statement itself.&#x20;

The core idea behind ZKPs is to enable succinct and private verification of claims while protecting the sensitive information of the prover. ZKPs have various applications in the crypto space, such as building private blockchains, improving the throughput of layer-1 blockchains, and enabling communication between separate blockchains. However, ZKPs are not without limitations, as generating proofs can be extremely resource-intensive in terms of both time and energy. Proof generation is often slowed down by the need for numerous complex mathematical operations, such as exponentiation, inversion, polynomial multiplication, and significant data movement. To address these challenges in all proposed ZKP constructions, it is crucial to develop hardware acceleration methods using Graphical Processing Units (GPUs) and Application-Specific Integrated Circuits (ASICs).

One purpose of building Cysic Network is to provide proof generation and verification services for the entire ZK ecosystem, which cultivates a sustainable environment for ZK proof generation and verification by addressing the following two major challenges:

* Prover Decentralization: The concept of decentralized provers provides a robust solution to prevent single points of failure in ZK projects, while simultaneously improving the efficiency and cost-effectiveness of proof generation. This design principle is inherently incorporated into all Layer 1 and Layer 2 ZK projects, reflecting the decentralized ethos of the blockchain community. Hardware acceleration, including the use of GPUs and ASICs, is likely inevitable if we aim to maintain performance without compromising decentralization.
* Latency and Cost in ZK Proof Verification: Ethereum has high costs and latency for settling ZK proofs. The current solution is to aggregate multiple proofs and then settle the aggregated proof on Ethereum. This approach reduces the gas fees required for verification but increases the waiting time. ZK proof verification is essentially a nearly stateless process, which means it can be verified off-chain and later aggregated and settled on Ethereum, should the users choose to do so. This two-stage settlement process strikes a balance between the latency and cost of ZK proof verification.



## Proof Task Workflow

Cysic Network allows participants to use their hardware—both powerful and less powerful—to contribute to the ZK proof generation and verification and, in return, earn Cysic token (credits). The workflow for a proof task is shown in the following figure:

<figure><img src="../.gitbook/assets/Screenshot 2025-06-04 at 11.03.05.png" alt=""><figcaption><p>ZK Task Workflow</p></figcaption></figure>

Once the Cysic Network reaches a stable state, any ZK project can deploy proving tasks via smart contracts as follows:

1. A ZK project first deposits the necessary tokens into the task smart contract. When it needs to generate a proof, it sends a notification to an automated agent smart contract, which then initiates the proof task on the blockchain.
2. A certain number of provers are then selected to compete for the task. The selection method is decentralized and is spontaneously executed by interested provers. More specifically, each interested prover runs a verifiable random function to determine if it is eligible to participate in the task. The probability of selection depends on the amount of ve-tokens held and the token system for proof task completion. Once a prover completes the proof generation, it posts a transaction on the blockchain to update the task status. Only the fastest three provers are allowed to update the task status.
3. After the proof generation for a task is completed, a larger number of verifiers is selected in a manner similar to prover selection to verify the generated proof. AVS services can also be included to enhance confidence in the proof verification. For a proof to be considered valid, at least a certain number of verifiers must approve the proof. Once multiple validity votes are cast for the same proof, the task is marked as successful on the blockchain.
4. When a task reaches a successful status on the chain, it triggers the task smart contract to send the agreed-upon amount of funds to the agent contract. This completes the proof task process.



<br>
