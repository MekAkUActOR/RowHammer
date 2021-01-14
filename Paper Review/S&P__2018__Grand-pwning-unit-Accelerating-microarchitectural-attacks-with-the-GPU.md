# Haowen Liu (2021-1-13)

# Paper information

- Title: Grand pwning unit: Accelerating microarchitectural attacks with the GPU
- Authors: Frigo Pietro, Cristiano Giuffrida, Herbert Bos, Kaveh Razavi
- Venue: SP 2018
- Keywords: integrated GPU-based microarchitectural attacks, ARM platforms, GPU caching hierarchy reverse engineering, remote side-channel and RowHammer attacks

# Paper content

## Summary

With the processor chip scaling, to improve processor speed as well as maintain low power consumption, processor vendors intend to add more specialized units such as integrated GPU on the basis of dark silicon, a technique that limits the portion of circuits in a processor chip working at the same time. Nevertheless, this paper doubts the security of this strategy and aims to realize **end-to-end remote integrated GPU-based microarchitectural attacks** (steal data using variety of side channels or corrupt data using hardware vulnerabilities) to compromise mobile platforms (to make microarchitectural attacks more effective). Their exploitation contains three parts: 1) reverse engineer the caching hierarchy of a widely used integrated GPU; 2) use timing side channel by leveraging GPU timing sources in WebGL API to detect contiguous memory allocation in DRAM; 3) RowHammer the DRAM of a mobile phone directly from the integrated GPU to corrupt data.

Given known cache architecture which contains L1, L2 and UCHE (an IOMMU in between DRAM and the L2 cache), reverse engineering the caching hierarchy of *Adreno 330* (a widely-used integrated GPU) is conducted by fetching texture from DRAM to GPU using texture fetch functionality exposed from the WebGL shaders, and calculating the number of cache misses in UCHE and L1 by the GPU’s *Performance Monitoring Unit* (PMU) and they easily identify the **cacheline size**s and **cache size**s of L1 and UCHE. Then they retrieve the **set-associativity** by means of *dynamic eviction sets*, which continuously evicts cachelines from a set filling up the cache until forming a closed loop, and those evicted cachelines construct *eviction set* whose size is the *associativity* of the cache. Knowing cacheline size, cache size and set-associativity, they easily deduce a **FIFO replacement policy** for both L1 and UCHE.

The remote side-channel attack exploits the small size and the deterministic (FIFO) replacement policy of the caches in the integrated GPU to build **efficient eviction sets** to bypass two levels of GPU caches to reach DRAM. Then given the facts that all the allocated memory in the same allocation block allocated by buddy allocator is physically contiguous and that a DRAM row contains 16 pages (64KB), every allocation of order $\ge 4$ spans over a full row, and double-sided RowHammer requires an allocation of order $\lceil log_2(16_{pages}×3_{row})\rceil =6$, namely, 4 adjacent rows. Given another two facts that a *row hit* (the aimed row is already in the row buffer) takes much less time than a *row conflict* (the aimed row is not in the row buffer) and that allocating a certain size memory causes more row conflicts if allocated into small order allocations (smaller than its size), one can construct a timing side channel, employing the `TIME_ELAPSED_EXT` asynchronous timer in WebGL API, to heuristically **identify the order of the underlying allocations** by measuring the access time of allocating a certain size memory. Thus, by exploiting this side channel attacker can get the information of order $\ge 6$ allocations (more than 3 adjacent rows) to conduct subsequent double-sided RowHammer attack.

Since there is no `clflush` instruction in ARM platforms and without abusing the uncached DMA memory provided by the Android ION allocator, they conduct plain eviction-based RowHammer attack using efficient cache eviction strategy used in aforementioned side channel to access DRAM quickly enough (due to the small size and the deterministic replacement policy of the GPU caches the access rate is enough to trigger bit flips, which is unfeasible when using CPU). They exploit memory reuse to allocate the sensitive data to their RowHammer-able pages, and then conduct RowHammer attack to corrupt targeted data. Finally they conduct the RowHammer attack called *GLitch* on several ARM platforms, which shows high attack success rates and great attack efficiency improvement compared to prior works. 


## Strengths

- This paper is the first work reverse engineering the caching hierarchy of the integrated GPU architecture and the first work realizing an end-to-end remote timing side channel and RowHammer attacks on mobile platforms directly from integrated GPU.
- Include plentiful content involving many fields of computer Arch and OS research, which is also well organized to construct innovative and effective attacks.
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

- Why did they only reverse engineer the parameters of L1 and UCHE, but omit L2? Obviously L2 is also an important part in caching hierarchy of the integrated GPU, but they even did not mention L2 when conducting their exploitation, why? Does L2 not cache textures of shaders?
- Why do integrated GPU caches use FIFO replacement policy instead of LRU?