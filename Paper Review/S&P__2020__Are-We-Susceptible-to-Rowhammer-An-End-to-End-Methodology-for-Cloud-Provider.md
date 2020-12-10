# Haowen Liu (2020-12-05)

# Paper information

- Title: Are We Susceptible to Rowhammer? An End-to-End Methodology for Cloud Provider
- Authors: Lucian Cojocar, Jeremie Kim, Minesh Patel, Lillian Tsai, Stefan Saroiu, Alec Wolman, and Onur Mutlu
- Venue: S&P 2020
- Keywords: RowHammer susceptibility testing, Intel-based architecture cloud server, `clflushopt`, row adjacency reverse engineering

# Paper content

## Summary

This paper tries to propose an end-to-end methodology to determine if cloud servers are susceptible to RowHammer attacks. To construct worst-case testing conditions, they expect to overcome two challenges: 1) devising a sequence of CPU instructions that leads to a maximal rate of row activations in DRAM; 2) determining row adjacency in a DRAM device.

To overcome the first challenge, they first investigate CPU instruction sequences used in prior work to mount Rowhammer attacks and find none of them create near-optimal row activation rates ($167.4$ $ACTs/tREFI$) because of expensive memory barriers and the effects of out-of-order execution, and then they develop an instruction sequence that leverages microarchitectural side-effects (`clflushopt`ing an invalid cache line triggers reads to that line in memory) to hammer DRAM at a near-optimal rate on modern Intel Skylake and Cascade Lake platforms.

To address the second challenge, they first discuss the three different remappings from virtual addresses to DRAM cells, and then analyze shortcomings of map reverse engineering in prior work. They propose that Rowhammer attack with an extended refresh interval would address these problems, so they design a fault injector that blocks `REF`s sent by memory controller by manipulating electrical signals, to manually lower the refresh rates, ensuring RowHammer bit flips to reverse engineer row adjacency in a DRAM. They find logical rows do not always map linearly, but instead can follow a *half-row* pattern.

Finally they use the worst-case instruction sequences and row adjacency obtained by reverse-engineering to test six server-class DIMMs, but *find it **very difficult** to flip bits using their testing methodology*. So they analyze limitations (TRR, scaling limitations, vendor preferences) in their methodology and plan to address them in the future.


## Strengths

- Though incomplete, this paper is the first to provide a RowHammer susceptibility testing methodology in end-to-end systems like cloud servers.
- This paper gives out two necessary conditions that RowHammer susceptibility testing needs: 1) CPU instruction sequences that hammer DRAM at a near-optimal rate; 2) row adjacency in a DRAM device.
- Summarize CPU instruction sequences used in prior work and evaluate their hammer rates; summarize map reverse engineering in prior work and analyze their shortcomings.
- Use side-effects of `clflushopt` rather than explicit memory accesses to hammer DRAM rows, to “bypass” *out-of-order execution* and without the use of memory barriers.
- Design a fault injector manipulating electrical signals to manually lower the refresh rates to ensure RowHammer bit flips to *reliably* reverse engineer row adjacency in a DRAM.

## Weaknesses

- The paper is incomplete. They fail to complete the final step: testing RowHammer susceptibility of real server-class DIMMs. They fail to flip bits in server-class DIMMs because of limitations of their methodology.
- 

## Paper presentation

- 

## Thoughts
- 

## Takeaways and questions

