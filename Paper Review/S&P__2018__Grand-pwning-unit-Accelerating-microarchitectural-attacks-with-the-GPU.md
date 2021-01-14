# Haowen Liu (2021-1-13)

# Paper information

- Title: Grand pwning unit: Accelerating microarchitectural attacks with the GPU
- Authors: Frigo Pietro, Cristiano Giuffrida, Herbert Bos, Kaveh Razavi
- Venue: SP 2018
- Keywords: integrated GPU-based microarchitectural attacks, ARM platform, GPU caching hierarchy reverse engineering, remote side-channel and RowHammer attacks

# Paper content

## Summary

With the processor chip scaling, to improve speed as well as maintain low power consumption, processor vendors intend to add more specialized units such as integrated GPU on the basis of dark silicon, a technology that limits the portions of circuits in processors working at the same time. Nevertheless, this paper doubts the security of this strategy and aims to realize **end-to-end remote integrated GPU-based microarchitectural attacks** (steal data using variety of side channels or corrupt data using hardware vulnerabilities) to compromise mobile platforms (to make microarchitectural attacks more effective). Their exploitation contains three parts: 1) reverse engineer the caching hierarchy of a widely used integrated GPU; 2) use timing side channel by leveraging GPU timing sources in WebGL API to detect contiguous memory allocation in DRAM; 3) RowHammer the DRAM of a mobile phone directly from the integrated GPU to corrupt data.

Given known cache architecture which contains L1, L2 and UCHE (an Input/Output Memory Management Unit in between DRAM and the L2 cache), reverse engineering the caching hierarchy of *Adreno 330* (a widely-used integrated GPU) is conducted by fetching texture from DRAM to GPU using texture fetch functionality exposed from the WebGL shaders, and calculating the number of cache misses in UCHE and L1 by the GPU’s *Performance Monitoring Unit* (PMU) and they easily identify the **cacheline size**s and **cache size**s of L1 and UCHE. Then they retrieve the **set-associativity** by means of *dynamic eviction sets*, which continuously evicts cachelines from a set filling up the cache until forming a closed loop, and those evicted cachelines construct *eviction set* whose size is the *associativity* of the cache. Knowing cacheline size, cache size and set-associativity, they easily deduce a **FIFO replacement policy** for both L1 and UCHE.

The remote side-channel attack exploits the small size and the deterministic (FIFO) replacement policy of the caches in the integrated GPU to build **efficient eviction sets** to bypass two levels of GPU caches to reach DRAM. Then given the facts that all the allocated memory in the same block split by buddy allocator is physically contiguous and a DRAM row contains 16 pages (64KB), every allocation of order $\ge 4$ spans over a full row, and double-sided RowHammer requires an allocation of order $\lceil log_2(16_{pages}×3_{row})\rceil =6$, namely, 4 adjacent rows. Given another two facts that a *row hit* (the aimed row is already in the row buffer) takes much less time than a *row conflict* (the aimed row is not in the row buffer) and allocating a certain size memory causes more row conflicts if allocated into small order block (smaller than its size), one can construct a timing side channel, employing the `TIME_ELAPSED_EXT` asynchronous timer in WebGL API as the precise timer, to heuristically **identify the order of the underlying allocations** by measuring the access time of a certain size allocation. Thus, exploiting this side channel attacker can get the information of order 6 allocations (3 adjacent rows) to conduct subsequent double-sided RowHammer attack.

Since there is no `clflush` instruction in ARM platforms and without abusing the physically contiguous memory provided by the Android ION allocator, they conduct plain eviction-based RowHammer attack using efficient cache eviction strategy used in aforementioned side channel to access DRAM quickly enough (due to the small size and the deterministic replacement policy of the GPU caches the access rate is enough to trigger bit flips, which is unfeasible using CPU). They exploit memory reuse to allocate the sensitive data to their RowHammer-able pages, and then conduct RowHammer attack to corrupt targeted data. Finally they conduct the RowHammer attack called *GLitch* on several ARM platforms, which shows high attack success rates and great attack efficiency improvement compared to prior works. 


## Strengths

- This paper is the first work reverse engineering the caching hierarchy of the integrated GPU architecture and the first work realizing an end-to-end remote timing side channel and RowHammer attacks on mobile platforms directly from integrated GPU.
- Include extraordinarily plentiful content involving lots of fields of computer Arch and OS research, which is also well organized to construct innovative and effective attacks.
- 

## Weaknesses

- 

## Paper presentation

Many misspellings and some grammatical confusion.

## Thoughts
- 

## Takeaways and questions

