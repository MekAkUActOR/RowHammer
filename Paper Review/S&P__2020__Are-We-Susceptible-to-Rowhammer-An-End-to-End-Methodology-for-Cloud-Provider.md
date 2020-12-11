# Haowen Liu (2020-12-09)

# Paper information

- Title: Are We Susceptible to Rowhammer? An End-to-End Methodology for Cloud Provider
- Authors: Lucian Cojocar, Jeremie Kim, Minesh Patel, Lillian Tsai, Stefan Saroiu, Alec Wolman, and Onur Mutlu
- Venue: S&P 2020
- Keywords: RowHammer susceptibility testing, Intel-based architecture cloud server, `clflushopt`, row adjacency reverse engineering

# Paper content

## Summary

This paper tries to propose an end-to-end methodology to determine if cloud servers are susceptible to RowHammer attacks. To construct worst-case testing conditions, they expect to overcome two challenges: 1) devising a sequence of CPU instructions that leads to a maximal rate of row activations in DRAM; 2) determining row adjacency in a DRAM device.

To overcome the first challenge, they first investigate CPU instruction sequences used in prior work to mount Rowhammer attacks and find none of them create near-optimal row activation rates ($167.4$ $ACTs/tREFI$) because of expensive memory barriers and the effects of out-of-order execution, and then they develop an instruction sequence that leverages **microarchitectural side-effects** (flushing a cache line in an invalid state using `clflushopt` instruction triggers accesses to that line in memory) to hammer DRAM at a near-optimal rate on modern Intel Skylake and Cascade Lake platforms.

To address the second challenge, they first discuss the three different remappings from virtual addresses to DRAM cells, and then analyze shortcomings of map reverse engineering in prior work. They propose that Rowhammer attack with an extended refresh interval would address these problems, so they design a plug-in fault injector (hardware circuit) mounted between memory controller and DIMMs a cloud server that blocks `REF`s sent by memory controller by manipulating electrical signals and **changing DRR4 commands**, to manually lower the refresh rates, ensuring RowHammer bit flips to reverse engineer row adjacency in a DRAM. They find logical rows do not always map linearly, but instead can follow a *half-row* pattern (For example, row 0x11410 is adjacent to *half* of rows 0x1140F and 0x1141F, and a whole row 0x11411 in the paper).

Finally they use the worst-case instruction sequences and row adjacency to test six server-class DIMMs, but *find it **very difficult** to flip bits using their testing methodology*. So they analyze limitations (TRR, scaling limitations, vendor preferences) in their methodology and plan to address them in the future.


## Strengths

- Though incomplete, this paper is the first to provide a RowHammer susceptibility testing methodology in end-to-end systems like cloud servers.
- This paper gives out two necessary conditions that RowHammer susceptibility testing needs: 1) CPU instruction sequences that hammer DRAM at a near-optimal rate; 2) row adjacency in a DRAM device.
- Summarize CPU instruction sequences used in prior work and evaluate their hammer rates; summarize map reverse engineering in prior work and analyze their shortcomings.
- Use side-effects of `clflushopt` rather than explicit memory accesses to hammer DRAM rows, to “bypass” *out-of-order execution* and without the use of memory barriers.
- This methodology boots the computer into the UEFI mode, leading to a linear virtual-to-physical address map.
- Test number of bit flips per row when suppressing REFs for $15$ sec in the absence of Rowhammer, eliminating the influence of *weak cells*[^1].
- Design a fault injector manually lowering the refresh rates by manipulating electrical signals and **changing DRR4 commands**, to ensure RowHammer bit flips to *reliably* reverse engineer row adjacency in a DRAM.

## Weaknesses

- The paper is incomplete. They fail to complete the final step: testing RowHammer susceptibility of real server-class DIMMs. They fail to flip bits in server-class DIMMs because of limitations of their methodology.
- This methodology needs to implement a fault injector circuit between memory controller and DIMMs when reverse engineering row adjacency, and takes a long time and causes too much overhead, which can’t be applyed to real running cloud servers and can't be performed at large scale.

- The fault injector drives A14 always low, and for ACT commands this bit encodes a row address, limiting the address space able to be reverse-engineered (those with a value of ’0’ for A14).
- The methodology is limited to Intel-based architecture cloud server, but loses effectiveness when encountering other architectures like AMD or ARM for it leverages Intel-based microarchitectural side-effects.
- Fail to bypass TRR. According to *TRRespass*[^2], such a simple access pattern is impossible to inject RowHammer bit flips in modern DDR4 equipped with TRR, which indicates that the first challenge they propose to construct worst-case testing conditions is incomplete.

## Thoughts
- The first challenge should be changed to *devising a sequence of CPU instructions that leads to a maximal rate of **effective** row activations in DRAM*. TRR invalidates most of activations, so an worst-case CPU instruction sequence should bypass TRR. To this end, one can substitute all accesses in access patterns found by *TRRespass* with `clflushopt` to achieve a near-optimal rate as well as bypassing TRR.
- Reverse engineering internal row adjacency in DRAM has nothing to do with CPU, so one can just use *SoftMC* platform to send crafted DRAM commands with a customized refresh interval rather than use faulted fault injector.

## Takeaways and questions

- How did they use single-sided RowHammer to reverse engineer row adjacency? Generally, one should access to two different rows in a bank in a loop to cause a hammer, or the row buffer will back the value rather than access to the row. But accessing two different rows in a bank leads to an attribution challenge – is the flipped bit adjacent to aggressor #1 or aggressor #2?



[^1]: Kim, Yoongu, et al. "Flipping bits in memory without accessing them: An experimental study of DRAM disturbance errors.” ISCA 2014.
[^2]: Frigo, Pietro, et al. "TRRespass: Exploiting the Many Sides of Target Row Refresh.” S&P 2020.