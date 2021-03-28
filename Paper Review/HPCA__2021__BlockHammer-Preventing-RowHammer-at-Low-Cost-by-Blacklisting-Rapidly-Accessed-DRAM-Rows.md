# Haowen Liu (2021-3-28)

# Paper information

- Title: BlockHammer: Preventing RowHammer at Low Cost by Blacklisting Rapidly-Accessed DRAM Rows
- Authors: Abdullah Giray Yağlıkçı, Minesh Patel, Jeremie S. Kim, Roknoddin Azizi, Ataberk Olgun, Lois Orosa, Hasan Hassan, Jisung Park, Konstantinos Kanellopoulos, Taha Shahroodi, Saugata Ghose, Onur Mutlu
- Venue: HPCA 2021
- Keywords: Blacklisting- and throttling-based RowHammer prevention, Bloom filter, memory controller

# Paper content

## Summary

This paper proposes to detect and prevent RowHammer attacks by using an area-efficient dual counting Bloom filter to track the number of activations upon DRAM rows to blacklist aggressor rows without false negative, and delay unsafe activations (subsequent activations to blacklisted rows) by per-DRAM-rank FIFO history buffers. To mitigate the system-wide performance degradation that a RowHammer attack could inflict upon benign applications, the authors throttle the memory bandwidth utilization of potential RowHammer attack threads by applying a dynamic quota, which leverages the inherent feature of RowHammer attack threads that they attempt to issue more activations to a blacklisted row than a benign application would. They proposed the *RowHammer likelihood index* (*RHLI*) per bank to quantify the similarity between a given thread’s memory access pattern and a real RowHammer attack and apply the quota inversely proportional to the RHLI.

Their simulation experiments on two types of multiprogrammed workloads — 1) 8 benign applications; 2) 1 RowHammer attack application and 7 benign applications — were carried out on BlockHammer and other six state-of-the-art RowHammer mitigation mechanisms to evaluate the performance, energy overheads, and scalability with worsening RowHammer vulnerability. The simulation results show that with negligible false positive rate and penalty, BlockHammer 1) introduces very low performance and DRAM energy overheads for workloads with no RowHammer attack present; 2) scalably provides much higher benign application performance and lower DRAM energy consumption than all state-of-the-art mechanisms when a RowHammer attack is present.

Finally, the paper proposes four desired characteristics of a RowHammer mitigation mechanism: comprehensive protection, compatibility with commodity DRAM chips, scaling with RowHammer vulnerability, and deterministic protection. So far, BlockHammer is the only mechanism providing all the four characteristics. This paper also discusses the scenario that BlockHammer faces many-sided RowHammer attacks by modifying the blacklisting threshold.


## Strengths

- Installing the blacklist on the memory controller to directly prevent the malicious application from activating the DRAM rows guarantees comprehensive protection.
- The idea is innovative and effective. Proactively blacklisting the potential aggressor rows instead of reactively refreshing potential victim rows allows the defender to require no proprietary DRAM information (e.g., mapping) and modifications to DRAM chip design, namely, compatibility with commodity DRAM chips.
- The key innovation is designing the AttackThrottler to throttle the memory bandwidth utilization of RowHammer attack applications, freeing up additional memory bandwidth for concurrently-running benign threads. This strategy not only mitigates the system-wide performance degradation that a RowHammer attack could inflict upon benign applications, but also gives BlockHammer significant scalability with worsening RowHammer vulnerability.
- The evaluation experiments consider other six state-of-the-art RowHammer mitigation mechanisms, showing the effectiveness of the method.
- The authors prove that no RowHammer attack can defeat BlockHammer using the *proof by contradiction* method.

## Weaknesses

- Delaying activations is not that simple. The authors did not give us the way to schedule the DRAM commands after delaying unsafe activations. Besides `active`, there are many other kind of DRAM commands, and several dependent DRAM commands constitute a CPU instruction execution. The paper did not indicate that how to buffer or reschedule other DRAM commands of the same CPU instruction execution when the activation of this execution is delayed, not to mention the dependency between CPU instructions. This may need to be coordinated with the CPU scheduling.
- To reduce the time overhead, BlockHammer implements the Bloom filter on a relatively large piece of SRAM, significantly increasing the cost.
- BlockHammer can be separated to two parts: the blacklist and the throttler, and they have very different characteristics, so BlockHammer cannot be only categorized to a throttling-based RowHammer prevention as the authors did, which interferes with the reader's understanding of the idea.
- The AttackThrottler has an apparent defect that it will lose its scalability with worsening RowHammer vulnerability and its effect to mitigate the performance degradation by RowHammer attack when facing multi-thread RowHammer attack, because its throttling mechanism is based on the behavior of single thread and will be ineffective when facing multi-thread or even multi-process RowHammer attacks.
- Unlike refresh-based mitigation, blacklisting- and throttling-based prevention mechanisms like BlockHammer require no proprietary DRAM information, but they generate another vulnerability: Deny-of-Service (DoS). The blacklist inherence of throttling-based prevention gives attackers the chance to manipulate the mechanism to blacklist benign applications and DRAM rows. Though I cannot figure out how to exploit this vulnerability now, as far as it exists there, there is a risk.

## Thoughts
- Blacklist-based prevention vs. Refresh-based mitigation: 
  - Pros: Blacklist-based prevention requires no proprietary DRAM information. It just blacklist the aggressor rows, so it achieves better compatibility with commodity DRAM chips. But refresh-based mitigation requires these information to address victim rows.
  - Cons: 
    - Blacklist-based prevention needs a large area and new mechanisms to buffer and reschedule the delayed commands, but the refresh-based mitigation just refreshes the victim rows, requiring no extra area.
    - Blacklist-based prevention gives attackers the chance to blacklist benign applications and DRAM rows (DoS), but the refresh-based mitigation does not affect the execution of either benign applications or RowHammer attack applications.
- Throttling-based mitigation can be based on either blacklist-based prevention or refresh-based mitigation, because it requires a pre-mechanism to reveal the potential RowHammer applications. Throttling-based mitigation mitigates the performance degradation by RowHammer attack and provides scalability with worsening RowHammer vulnerability.

## Takeaways and questions

- How did the authors complete the simulation without a feasible rescheduling mechanism in the memory controller or CPU? So I doubt that their experiment on RowHammer attack workloads is unrigorous, which cannot guarantee correct program execution.