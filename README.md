# Cysic Network Overview

Cysic Network is a full-stack compute infrastructure designed to power the next generation of verifiable AI, ZK proofs, and decentralized compute economies. By unifying hardware, on-chain coordination, and tokenized compute assets, Cysic bridges the gap between traditional cloud computing and blockchain-based verification—turning compute power into a transparent, tradable, and yield-generating asset class.

To understand how Cysic Network operates, we first define the actors, their interactions, and the threats the system must defend against.

## Roles <a href="#roles" id="roles"></a>

There are several different actors in Cysic Network:

* Compute Contributors: These are individuals or organizations who contribute raw computational power to the network. Providers may range from a single GPU in a desktop computer to industrial-scale clusters of ASIC miners. Providers register their hardware, receive workloads, execute them, and submit results back to the network.
* Task Requesters: These are users, dApps, or enterprises that require computation. A requester may be a rollup operator needing zkSNARK proofs, a zkVM prover network requiring proving cycles, or a miner outsourcing hashpower to a decentralized pool. Requesters specify workload requirements (e.g., GPU memory, runtime constraints, deadline) when submitting tasks.
* Verifiers: Independent participants who check the correctness of results. For some workloads, verification is trivial (e.g., hash difficulty checks, verifying ZK proofs). For others, it requires cryptographic proofs or redundancy (multiple providers executing the same AI inference). Verifiers anchor trust in the network.
* Marketplace: The coordination layer of ComputeFi. The marketplace matches tasks with providers, balancing performance, fairness, and reliability. The marketplace presents a user-facing interface where requesters can submit tasks and monitor execution.

For instance, the lifecycle of a ZK proof task is: the task requestor first specifies the essentials of a task, such as the software version, deadline, and reward. Multiple compute providers then proceed bidding for this task, with the rank of weighted sum of reserve tokens and bid. After the proof task is carried out by the winning provider, the result is sent out to multiple randomly selected verifiers to verify the result. After all the verification is done, the reward will be distributed to participated providers and verifiers and whole process is recorded on Cysic Network blockchain.

<figure><img src=".gitbook/assets/Screenshot 2025-10-20 at 16.35.09.png" alt=""><figcaption></figcaption></figure>

In ComputeFi, compute is represented as workload-specific units:

* GPU cycles, expressed in FLOPs (floating-point operations)
* ASIC cycles, expressed in hashes per second.
* Proofs, expressed in cycles in zkVM.

The protocol normalizes heterogeneous resources into a comparable model so that different workloads can be fairly priced, allocated, and traded.

## Architecture <a href="#architecture" id="architecture"></a>

Cysic Network is designed as a modular stack, ensuring flexibility while preserving coherence across domains. It is built using Cosmos CDK as a layer-1 blockchain. The layered architecture from bottom to top can be described as:

* Hardware Layer: This is the physical layer of the Cysic Network, where CPU/GPU/FPGA servers, mining rigs and portable computing devices, including cellphones and miners, constitutes the foundation of the network.
* Consensus Layer: As Cysic Network is built upon Cosmos CDK, which uses CometBFT algorithm. CometBFT follows the Byzantine Fault Tolerance (BFT) consensus model, meaning it is designed to handle situations where some nodes in the network behave maliciously or fail to follow the protocol correctly. It tolerates up to one-third of the nodes failing or behaving maliciously without compromising the system’s integrity. The consensus in Cysic Network, Proof-of-Compute, is developped based on this consensus mechanism. In this updated consensus mechanism, not only the staked tokens, but the amount of computation pledged in the system is taken into the consensus reaching process.
* Execution Layer: This layer is responsible for job scheduling, workload routing, bridging, voting and some other basic functionalities of the network. The functionalities are achieved by various smart contracts deployed on the EVM-compatible blockchain.
* Product Layer: The product layer is the interaction portal of multiple products in Cysic Network, which currently includes a ZK proof market, an AI inference framework, a crypto mining framework and some other products. These are domain-specific modules (ZK proving, AI inference, mining, HPC workloads). Each service defines how tasks are executed and verified.

The layered architecture provides several advantages. By separating concerns, it ensures that improvements or changes in one layer do not disrupt others. For example, new proof systems or AI models can be added at the product layer without modifying the underlying consensus or hardware logic. This modularity accelerates innovation, improves scalability, and makes the system resilient to evolving workloads. It also creates a clear interface for developers and hardware providers, enabling rapid onboarding of new compute resources while preserving protocol stability.

## Vision

Cysic’s vision is to make compute a universal public good, a resource as open, liquid, and programmable as money itself. By combining the reliability of ASIC hardware with the transparency of blockchain coordination, Cysic transforms compute from a cost center into an on-chain asset class powering the AI and cryptographic economies of the future.
