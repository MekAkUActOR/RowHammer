# Haowen Liu (2020-12-09)

# Paper information
- Title: Are We Susceptible to Rowhammer? An End-to-End Methodology for Cloud Provider
- Authors: Lucian Cojocar, Jeremie Kim, Minesh Patel, Lillian Tsai, Stefan Saroiu, Alec Wolman, Onur Mutlu
- Venue: IEEE S&P 2020
- Keywords: RowHammer susceptibility testing, Intel-based architecture cloud server, `clflushopt`, row adjacency reverse engineering

# Paper content
## Summary
This paper tries to propose an end-to-end methodology to determine if cloud servers are susceptible to RowHammer attacks. To construct worst-case testing conditions, they expect to overcome two challenges: 1) devising a sequence of CPU instructions that leads to a maximal rate of row activations in DRAM; 2) determining row adjacency in a DRAM device.

To overcome the first challenge, they first investigate CPU instruction sequences used in prior work to mount RowHammer attacks and find none of them create near-optimal row activation rates ($167.4$ activations per refresh interval) because of slow memory barriers and the effects of out-of-order execution, and then they develop an instruction sequence that leverages **microarchitectural side-effects** (flushing a cache line in an invalid state using `clflushopt` instruction triggers accesses to that line in memory) to hammer DRAM at a near-optimal rate on modern Intel Skylake and Cascade Lake platforms.

To address the second challenge, they first discuss the three different mappings (Virtual-to-Physical, Physical-to-Logical and Logical-to-Internal), and then analyze shortcomings of map reverse engineering in prior works. They propose that RowHammer attack with an extended refresh interval would address these problems, so they design a plug-in fault injector (hardware circuit) mounted between memory controller and DIMMs of a server that blocks refresh commands sent by memory controller by manipulating electrical signals and **changing DRR4 commands**, to manually lower the refresh rates, ensuring RowHammer bit flips to reverse engineer row adjacency in a DRAM. They find logical rows do not always map linearly, but instead can follow a *half-row* pattern (For example, row 0x11410 is adjacent to *half* of rows 0x1140F and 0x1141F, and a whole row 0x11411 in the paper).

Finally they use the worst-case instruction sequences and row adjacency to test six server-class DIMMs, but *find it **very difficult** to flip bits using their testing methodology* (mostly because of TRR, I think). So they analyze limitations (TRR, scaling limitations, vendor preferences) in their methodology and plan to address them in the future.

## Strengths
- Though incomplete, this paper is the first to provide a RowHammer susceptibility testing methodology in end-to-end systems like cloud servers.
- This paper gives out two necessary conditions that RowHammer susceptibility testing needs: 1) CPU instruction sequences that hammer DRAM at a near-optimal rate; 2) row adjacency in a DRAM device.
- Summarize CPU instruction sequences used in prior work and evaluate their hammer rates; summarize map reverse engineering in prior work and analyze their shortcomings.
- Use side-effects of `clflushopt` rather than explicit memory accesses to hammer DRAM rows, to “bypass” *out-of-order execution* and without the use of memory barriers.
- This methodology boots the computer into the UEFI mode, leading to a linear virtual-to-physical address map, which makes the testing easier.
- Test number of bit flips per row when suppressing REFs for $15$ sec in the absence of RowHammer, eliminating the influence of *weak cells*[^1].
- Design a fault injector manually lowering the refresh rates by manipulating electrical signals and **changing DRR4 commands**, to ensure RowHammer bit flips to *reliably* reverse engineer row adjacency in a DRAM.

## Weaknesses
- The paper fails to complete the final step: testing RowHammer susceptibility of real server-class DIMMs. They fail to flip bits in server-class DIMMs because of limitations of their methodology, which means they fail to demonstrate the correctness of their methodology.
- This methodology needs to implement a fault injector circuit between memory controller and DIMMs when reverse engineering row adjacency. It also takes a long time and causes too much overhead, which can’t be applied to real running cloud servers and performed at large scale (the purpose of this paper is to test the RowHammer vulnerability of cloud servers, so to implement a fault injector the cloud server providers should either test servers before using or stop them for testing).
- The fault injector drives A14 always low, and for ACT commands this bit encodes a row address, limiting the address space able to be reverse-engineered (those with a value of ’0’ for A14).
- The methodology is limited to Intel-based architecture cloud server, but loses effectiveness when encountering other architectures like AMD or ARM for it leverages Intel-based microarchitectural side-effects (flushing a cache line in an invalid state using `clflushopt` instruction triggers accesses to that line in memory).
- Fail to bypass TRR. According to *TRRespass*[^2], such a simple access pattern is impossible to inject RowHammer bit flips in modern DDR4 equipped with TRR, which indicates that the first challenge they propose to construct worst-case testing conditions is incomplete.

## Paper presentation
- ***Writing Error.*** In the first paragraph of Section III-C, they mistake *mapping* as *remapping*.

## Thoughts
- The first challenge should be changed to *devising a sequence of CPU instructions that leads to a maximal rate of **effective** row activations in DRAM*. TRR invalidates most of activations, so a worst-case CPU instruction sequence should bypass TRR. To this end, one can substitute all accesses in access patterns found by *TRRespass* with `clflushopt` to achieve a near-optimal rate as well as bypassing TRR.
- Reverse engineering internal row adjacency in DRAM has nothing to do with CPU, so one can just use *SoftMC* (an FPGA-based memory controller which provides fine-grained control over the commands sent to DRAM) platform to send crafted DRAM commands with a customized refresh interval rather than use faulted fault injector.

## Takeaways and questions
- How did they use single-sided RowHammer to reverse engineer row adjacency? Generally, one should access to two different rows in a bank in a loop to cause a hammer, or the row buffer will back the value rather than access to the row. But accessing two different rows in a bank leads to an attribution challenge – is the flipped bit adjacent to aggressor #1 or aggressor #2?
- Why did they say the **microarchitectural side-effects** happens *only when* the cache line is invalid? Why not if a cache line is in another state?
- Why can out-of-order execution increase the likelihood of cache hit, since out-of-order execution is a paradigm to make use of instruction cycles that would otherwise be wasted?

## Q&As
**Q1:** In the second para of summary, why does "memory barriers and the effects of out-of-order execution" matter here?

**A1:** To achieve a high rate of activations, one significant problem supposed to be address is out-of-order execution --- the CPU constantly re-orders memory accesses to increase the likelihood they are served from the cache. *Out-of-order execution can act as a de facto rate limiter to RowHammer attacks*. So most of instruction sequences in prior works use memory barriers to order their memory accesses to prevent out-of-order execution. Although barriers do prevent out-of-order execution, they are too slow. *Instruction sequences that use memory barriers lack the performance necessary to create a high rate of activations.* (This answer is summarized from the paper, but I cannot understand why out-of-order execution can increase the likelihood of cache hit, since out-of-order execution is a paradigm to make use of instruction cycles that would otherwise be wasted, so I also put that question in the T&Qs part.)


[^1]: Kim, Yoongu, et al. "Flipping bits in memory without accessing them: An experimental study of DRAM disturbance errors.” ISCA 2014.
[^2]: Frigo, Pietro, et al. "TRRespass: Exploiting the Many Sides of Target Row Refresh.” S&P 2020.

