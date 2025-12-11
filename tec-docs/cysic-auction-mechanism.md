# Cysic Auction Mechanism

## **1. Motivation**

Efficient task assignment in decentralized computation requires a fair, incentive-compatible, and penalty-enforced bidding system. The auction mechanism ensures that tasks are allocated to provers with sufficient computational resources, while maintaining economic alignment between requesters, provers, and verifiers.

## **2. Design Goals**

* **Fairness**: Guarantee that tasks are allocated based on competitive bidding under a capped maximum price.
* **Efficiency**: Encourage timely completion by rewarding faster provers with higher payouts.
* **Security**: Ensure that dishonest or underperforming provers are penalized through slashing.
* **Incentive Alignment**: Incorporate token reserves into the reward distribution, aligning long-term commitment with higher earnings.

## **3. Task Definition**

A task is published by the **Requester** with the following parameters:

* **Task Difficulty (task\_difficulty)**: Expressed in cycles.
* **Maximum Acceptable Bid (bid\_max)**: The upper bound of unit price, in **CYS per million cycles**.
* **Task Deadline (task\_ddl)**: The maximum allowable computation time.

## **4. Prover Requirements**

* **Token Reservation**: Each prover must lock a minimum amount of tokens to be eligible.
* **Bidding Configuration**: Provers specify their bidding price in the configuration file, which serves as the reference for subsequent auctions.
* **Performance Evaluation**: By running the client software, a prover’s computational capacity is automatically benchmarked.

## **5. Auction Flow**

1. **Task Broadcast**: The requester publishes the task, which is propagated to all registered provers.
2. **Capability Assessment**: Each prover determines whether it can complete the task within task\_ddl.
3. **Bid Submission**: Provers submit bids reflecting their desired compensation.
   1. Bids exceeding bid\_max are automatically discarded.
4. **Bid Selection**: Upon bidding closure, the system sorts valid bids by price.
   1. The **two winning provers** are chosen starting from the **second-lowest bid**.
      * If two provers submit the same price, the **more reserved** one will be chosen.
   2. The **selected bid price (bid\_select)** is the lowest bid among these winners.

## **6. Reward Distribution**

### **6.1 Prover Rewards**

The total reward pool for provers is defined as: $$task\_reward\_prover = bid\_select \times task\_difficulty \times 80\%$$

This reward is distributed based on completion speed among the three winning provers:

* **Fastest prover**: 80% of task\_reward\_prover
* **Second fastest**: 20%

### **6.2 Failure and Slashing**

* If a prover fails to meet the deadline, the task is reassigned to a backup prover.
*   The failing prover incurs a penalty:

    $$slash = \beta \times bid\_max \times task\_difficulty$$
* where $$\beta = 1$$.

### **6.3 Verifier Rewards**

* **20 verifiers** are randomly selected to validate the proof.
*   They share the remaining 20% of the task reward equally:

    $$verifier\_reward = \frac{bid\_select \times task\_difficulty \times 20\%}{20}$$<br>

## **7.** **Reserve-Weighted Incentives**

To strengthen long-term commitment and discourage opportunistic behavior, prover rewards are further adjusted by token reserves:\$$final\\\_task\\\_reward\\\_prover = task\\\_reward\\\_per\\\_prover \times \big(1 + \gamma \times R\big)\$$\
Where:

* $$\gamma$$: **Initially set to 0.25**, but subject to adjustment in the future depending on the overall reserve distribution.
* $$R = \frac{reserve\_token}{total\_reserve\_token}$$: relative reserve ratio.

\
This ensures that provers with larger token commitments receive proportionally higher rewards.

## **8. Security Considerations**

* **Sybil Resistance**: Token reservation reduces the risk of Sybil attacks by requiring economic commitment.
* **Collusion Prevention**: Pricing determined by the second-lowest bid mitigates collusion among provers.
* **Reliability Enforcement**: Slashing enforces accountability, ensuring underperforming provers bear economic costs.
* **Verifier Integrity**: Distributing rewards among multiple verifiers enhances the robustness of proof validation.

<br>
