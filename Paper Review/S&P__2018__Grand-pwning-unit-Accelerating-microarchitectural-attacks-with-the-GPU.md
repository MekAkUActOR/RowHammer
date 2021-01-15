# Haowen Liu (2021-1-13)

# Paper information

- Title: Grand Pwning Unit: Accelerating Microarchitectural Attacks with the GPU
- Authors: Frigo Pietro, Cristiano Giuffrida, Herbert Bos, Kaveh Razavi
- Venue: IEEE S&P 2018
- Keywords: integrated GPU-based microarchitectural attacks, ARM platforms, GPU caching hierarchy reverse engineering, remote side-channel and RowHammer attacks

# Paper content

## Summary

With the processor chip scaling, to improve processor speed as well as to maintain low power consumption, processor vendors intend to add more specialized units such as integrated GPU on the basis of dark silicon (a technique that limits the portion of circuits in a processor chip working at the same time). Nevertheless, this paper doubts the security of this strategy and aims to realize **end-to-end remote integrated GPU-based microarchitectural attacks** (steal data using variety of side channels or corrupt data using hardware vulnerabilities) to compromise mobile platforms (to make microarchitectural attacks more effective). Their exploitation contains three parts: 1) reverse engineer the caching hierarchy of a widely used integrated GPU; 2) use timing side channel by leveraging GPU timing sources in WebGL API to detect contiguous memory allocation in DRAM; 3) RowHammer the DRAM of a mobile phone directly from the integrated GPU to corrupt data.

Given known cache architecture which contains L1and L2 (known as UCHE), reverse engineering the caching hierarchy of *Adreno 330* (a widely-used integrated GPU) is first conducted by fetching texture from DRAM to GPU using texture fetch functionality exposed from the WebGL shaders. By calculating the number of cache misses in UCHE and L1 by the GPU’s *Performance Monitoring Unit* (PMU), they easily identify the **cache line size**s and **cache size**s of L1 and UCHE. Then they retrieve the **set-associativity** by means of *dynamic eviction sets*, which continuously evicts cache lines from a set filling up the cache until forming a closed loop, and those evicted cache lines construct *eviction set* whose size is the *associativity* of the cache. Knowing cache line size, cache size and set-associativity, they easily deduce a **FIFO replacement policy** for both L1 and UCHE.

The remote side-channel attack exploits the small size and the deterministic (FIFO) replacement policy of the caches in the integrated GPU to build **efficient eviction sets** to bypass two levels of GPU caches to reach DRAM. Then given the facts that all the allocated memory in the same allocation block allocated by buddy allocator is physically contiguous, and that a DRAM row contains 16 pages (64KB), every allocation of order $\ge 4$ spans over a full row, and double-sided RowHammer requires an allocation of order $\lceil log_2(16_{pages}×3_{row})\rceil =6$, namely, 4 adjacent rows. Given another two facts that a *row hit* (the aimed row is already in the row buffer) takes much less time than a *row conflict* (the aimed row is not in the row buffer), and that allocating a certain size of memory causes more row conflicts if allocated into small order allocations (smaller than its size), one can construct a timing side channel, employing the `TIME_ELAPSED_EXT` asynchronous timer in WebGL API, to heuristically **identify the order of the underlying allocations** by measuring the access time of allocating a certain size of memory. Thus, by exploiting this side channel, attacker can get the information of order $\ge 6$ allocations (more than 3 adjacent rows) to conduct subsequent double-sided RowHammer attack.

Since there is no `clflush` instruction in ARM platforms, and to avoid abusing uncached memory, they conduct plain eviction-based RowHammer attack using the cache eviction strategy used in aforementioned side channel to access DRAM quickly enough. And due to the small size and the deterministic replacement policy of the GPU caches, the access rate is enough to trigger bit flips, which is unfeasible when using ARM CPU. They exploit memory reuse to allocate the sensitive data to their RowHammer-able pages, and then conduct RowHammer attack to corrupt targeted data. Finally they conduct the RowHammer attack called *GLitch* on several ARM platforms, which shows high attack success rates and great attack efficiency improvement compared to prior works. 


## Strengths

- This paper is the first work to reverse engineer the caching hierarchy of the integrated GPU and to realize end-to-end remote timing side channel and RowHammer attacks on mobile platforms directly from the integrated GPU.
- Include plenty of content involving many fields of computer Arch and OS research, which is also well organized to construct innovative and effective attacks.
- Investigate and demonstrate available precise timers from WebGL API, which revives currently mitigated side channel attacks by providing an attacker with a high-precision timer.
- Their GPU-based exploitation improve the effectiveness of microarchitectural attacks (revive mitigated side channel attacks and enable remote RowHammer attack on ARM platforms simply through browser).
- Point out the fact that specialized units in current commodity processors may cause vulnerabilities and processor vendors should perfect their threat model when adding specialized units into commodity processors.

## Weaknesses

- GLitch exploits buddy allocator to derive the information of enough physically adjacent rows (order $\ge 6$ allocations) by side channel. However, it’s the usual case that order $\ge 6$ allocations only take up a small portion of memory, which greatly limits the RowHammer-able memory space.

## Paper presentation

- Did not present the background knowledge all at once, but split them into each relevant part.
- Many misspellings and some grammatical confusion.
- Perhaps because of space constraints their RowHammer exploitation part is not very clear.

## Thoughts
- GPU-based RowHammer attacks can naturally bypass CPU-based mitigations, so if we want to mitigate RowHammer attacks from CPU, GPU, DMA and other devices together, we may have to design mitigations inside MC and DRAM chips.
- Given that all CPU/GPU-based exploitations need to bypass cache by flushing or eviction, we can design a cache controller inside cache hierarchy to handle frequent cache flushing and repeated cache eviction.
- Cache eviction/misses information can be a strong reference for CPU/OS-based mitigations.

## Takeaways and questions

***Dark silicon:*** dark silicon is the amount of circuitry of an integrated circuit that cannot be powered-on at the nominal operating voltage for a given thermal design power (TDP) constraint.

***GPU Rendering Pipeline:*** Application => Geometry Processing => Rasterization => Pixel Processing. Geometry Processing: Vertex Shading => Projection => Clipping => Screen Mapping. Rasterization: Triangle Setup => Triangle Traversal => Pixel Shading => Merging. 

- I still don’t understand Fig.6(c), why there are row conflicts in $a$ at the same row, but no row conflict in $b$ whose allocation changes row?

- Why do integrated GPU caches use FIFO replacement policy instead of LRU?

## Q&As

**Q1:** In *Weaknesses* part, what’s the relationship between side channel and buddy allocator? What do you mean by “enough physically adjacent rows”? And why does it limit, and what does it mean to limit?

**A1:** RE: relationship, GLitch exploits the fact that all the allocated memory in the same allocation block allocated by buddy allocator is physically contiguous, and the fact that a DRAM row contains 16 pages (64KB), to derive physically adjacent rows to conduct double-sided RowHammer (for example, if you have an allocation of 48 pages in one single block, you will get 3 physically adjacent rows to conduct double-sided RowHammer). RE: enough physically adjacent rows, double-sided RowHammer needs at least 3 physically adjacent rows, so order $\ge 6$ allocations (4 physically contiguous rows) are required. RE: limit, attackers can only conduct double-sided RowHammer in existing order $\ge 6$ allocations, so the RowHammer-able memory space is limited. For more details please read the third para of *Summary*.