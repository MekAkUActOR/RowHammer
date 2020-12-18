# Haowen Liu (2020-12-17)

# Paper information

- Title: ZebRAM: Comprehensive and Compatible Software Protection Against Rowhammer Attacks
- Authors: Radhesh Krishnan Konoth, Marco Oliverio, Andrei Tatar, Dennis Andriesse, Herbert Bos, Cristiano Giuffrida and Kaveh Razavi
- Venue: OSDI 2018
- Keywords: software-level RowHammer protection, guard rows, swap space

# Paper content

## Summary

To provide a software-level RowHammer protection for DRAM not equipped with effective hardware-level mitigation like TRR and ECC, this paper designs *ZebRAM* (**Zeb**ra D**RAM**), which isolates every DRAM row that contains data with guard rows that absorb any RowHammer-induced bit flips. And rather than leaving guard rows unused, ZebRAM improves performance by using the guard rows as efficient, integrity-checked and optionally compressed swap space. 

Instead of sacrificing a guard row to separate user space and kernel space to prevent RowHammer attacks against the kernel from user space, ZebRAM enforces all odd rows to be guard rows (inaccessible), forming unsafe region, and all even rows form safe region which can be directly accessed by the processor (unsafe odd rows and safe even rows comprise a **zebra pattern**). Hence, given that the unsafe region is unused, the attacker cannot flip bits in the data used by the system, i.e., safe region. To be compatible with the Buddy page allocation scheme used in commodity operating systems, ZebRAM employs virtualization extensions to translate safe region (even rows) into a contiguous physical address space for the guest.

However, ZebRAM wastes half of the memory that makes up the unsafe region. To maintain good performance, ZebRAM exposes the unsafe region as a swap device, which is integrity-checked to handle potential bit flips in the unsafe region, to the protected operating system, and reuses existing page replacement policies of the operating system to decide which memory pages should be evicted to the swap (i.e., unsafe region). ZebRAM swap is prioritized above any other swap disks (e.g., hard disk or SSD) to maximize efficiency. To protect the safe region from accesses to the unsafe region, ZebRAM uses a **cache** in front of the unsafe region, which is allocated from the safe region and ZebRAM is free to choose its size and replacement policy in a way that protects the safe region.

The prototype implementation of ZebRAM contains four components: the *Memory Remapper*, the *Integrity Manager*, the *Swap Manager*, and the *Cache Manager*. ZebRAM prototype implements Memory Remapper in the hypervisor and the other three components in the guest OS. Memory Remapper implements the split of physical memory into a safe and unsafe region, using reverse engineered mapping. Integrity Manager protects the integrity of the unsafe region, using a commonly-used Single-Error Correction and Double-Error Detection (SECDED) code and optionally collision resistant hash functions such as SHA-256. Swap Manager uses the unsafe region to implement an efficient swap disk in memory, protected by the Integrity Manager and accessible only by the OS. Cache Manager implements a fully associative cache that speeds up access to the swap space while simultaneously preventing RowHammer attacks against safe rows by reducing the access frequency on memory rows in the unsafe region.

Finally their evaluation of ZebRAM defending against RowHammer shows a good effectiveness with negligible performance reduction (an overhead of 5% on the SPEC CPU 2006 benchmarks).


## Strengths

- 

## Weaknesses

- 

## Thoughts
- 

## Takeaways and questions

