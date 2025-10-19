# Cysic Network Overview

ComputeFi is Cysic's vision for a decentralized compute economy, transforming GPUs, ASIC miners, and servers into a programmable, verifiable, and liquid resource. Today, compute remains siloed and centralized, driving up costs for AI, zero-knowledge proofs, and mining. ComputeFi solves this by matching providers and requesters through a decentralized marketplace, where tasks are executed and verified via cryptography, redundancy, or consensus. With Cysic’s vertically integrated hardware stack, from custom ASICs to GPU clusters and portable miners, ComputeFi creates scalable, real-world compute liquidity, positioning compute as the missing pillar of Web3 infrastructure alongside DeFi, storage, and bandwidth.

#### What is ComputeFi <a href="#what-is-computefi" id="what-is-computefi"></a>

ComputeFi is the financialization of compute resources. It turns raw computation into a programmable, tradable, and verifiable primitive, just as DeFi transformed idle capital into liquidity pools. In ComputeFi, compute cycles and hashrates are contributed by providers, requested by users, and verified by the network. The result is a unified marketplace where ZK proofs, AI inference, and mining workloads coexist.

ComputeFi is built on four foundational principles:

* Hardware-Agnostic: The network is open to GPUs, ASIC miners, CPUs, and specialized accelerators. No single hardware vendor dominates the protocol.
* Workload-Agnostic: ComputeFi supports multiple domains, from blockchain proving to AI to HPC. Modules define how each workload type is executed and verified.
* Verifiable: Correctness is enforced at the protocol level. Different workloads employ different mechanisms — zkSNARK validity for proofs, redundancy for AI inference, and hash verification for mining.
* Composable: Developers can integrate ComputeFi into dApps, protocols, or AI services via standardized APIs, treating compute as a first-class resource.

Cysic is uniquely positioned to realize this vision because it integrates silicon design, infrastructure, and blockchain coordination. Unlike software-only protocols, Cysic can onboard real hardware, from ASICs to portable miners, ensuring that ComputeFi is not just theoretical, but practical and scalable.



### Cysic Network <a href="#cysic-network" id="cysic-network"></a>

To understand how Cysic Network operates, we first define the actors, their interactions, and the threats the system must defend against.

#### Roles <a href="#roles" id="roles"></a>

There are several different actors in Cysic Network:

* Compute Providers: These are individuals or organizations who contribute raw computational power to the network. Providers may range from a single GPU in a desktop computer to industrial-scale clusters of ASIC miners. Providers register their hardware, receive workloads, execute them, and submit results back to the network.
* Task Requesters: These are users, dApps, or enterprises that require computation. A requester may be a rollup operator needing zkSNARK proofs, a zkVM prover network requiring proving cycles, or a miner outsourcing hashpower to a decentralized pool. Requesters specify workload requirements (e.g., GPU memory, runtime constraints, deadline) when submitting tasks.
* Verifiers: Independent participants who check the correctness of results. For some workloads, verification is trivial (e.g., hash difficulty checks, verifying ZK proofs). For others, it requires cryptographic proofs or redundancy (multiple providers executing the same AI inference). Verifiers anchor trust in the network.
* Marketplace: The coordination layer of ComputeFi. The marketplace matches tasks with providers, balancing performance, fairness, and reliability. The marketplace presents a user-facing interface where requesters can submit tasks and monitor execution.

For instance, the lifecycle of a ZK proof task is: the task requestor first specifies the essentials of a task, such as the software version, deadline, and reward. Multiple compute providers then proceed bidding for this task, with the rank of weighted sum of reserve tokens and bid. After the proof task is carried out by the winning provider, the result is sent out to multiple randomly selected verifiers to verify the result. After all the verification is done, the reward will be distributed to participated providers and verifiers and whole process is recorded on Cysic Network blockchain.

In ComputeFi, compute is represented as workload-specific units:

* GPU cycles, expressed in FLOPs (floating-point operations)
* ASIC cycles, expressed in hashes per second.
* Proofs, expressed in cycles in zkVM.

The protocol normalizes heterogeneous resources into a comparable model so that different workloads can be fairly priced, allocated, and traded.

#### Threat Model <a href="#threat-model" id="threat-model"></a>

To make sure every task runs smoothly, we need to defend against some attacks. We list some canonical attacks below:

* Malicious Results: Providers may attempt to return incorrect or fabricated results.
* Dropouts: Providers may fail to deliver tasks due to hardware failure or intentional disruption.
* Collusion: Groups of providers or verifiers may attempt to manipulate outcomes.
* Sybil Attacks: An adversary may create many fake identities to increase influence in scheduling or verification.
* Censorship: Schedulers or dominant providers may attempt to block certain tasks.

#### Architecture <a href="#architecture" id="architecture"></a>

Cysic Network is designed as a modular stack, ensuring flexibility while preserving coherence across domains. It is built using Cosmos CDK as a layer-1 blockchain. The layered architecture from bottom to top can be described as:

* Hardware Layer: This is the physical layer of the Cysic Network, where CPU/GPU/FPGA servers, mining rigs and portable computing devices, including cellphones and miners, constitutes the foundation of the network.
* Consensus Layer: As Cysic Network is built upon Cosmos CDK, which uses CometBFT algorithm. CometBFT follows the Byzantine Fault Tolerance (BFT) consensus model, meaning it is designed to handle situations where some nodes in the network behave maliciously or fail to follow the protocol correctly. It tolerates up to one-third of the nodes failing or behaving maliciously without compromising the system’s integrity. The consensus in Cysic Network, Proof-of-Compute, is developped based on this consensus mechanism. In this updated consensus mechanism, not only the staked tokens, but the amount of computation pledged in the system is taken into the consensus reaching process.
* Execution Layer: This layer is responsible for job scheduling, workload routing, bridging, voting and some other basic functionalities of the network. The functionalities are achieved by various smart contracts deployed on the EVM-compatible blockchain.
* Product Layer: The product layer is the interaction portal of multiple products in Cysic Network, which currently includes a ZK proof market, an AI inference framework, a crypto mining framework and some other products. These are domain-specific modules (ZK proving, AI inference, mining, HPC workloads). Each service defines how tasks are executed and verified.

The layered architecture provides several advantages. By separating concerns, it ensures that improvements or changes in one layer do not disrupt others. For example, new proof systems or AI models can be added at the product layer without modifying the underlying consensus or hardware logic. This modularity accelerates innovation, improves scalability, and makes the system resilient to evolving workloads. It also creates a clear interface for developers and hardware providers, enabling rapid onboarding of new compute resources while preserving protocol stability.



