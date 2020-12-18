# Haowen Liu (2020-12-17)

# Paper information

- Title: ZebRAM: Comprehensive and Compatible Software Protection Against Rowhammer Attacks
- Authors: Radhesh Krishnan Konoth, Marco Oliverio, Andrei Tatar, Dennis Andriesse, Herbert Bos, Cristiano Giuffrida, Kaveh Razavi
- Venue: OSDI 2018
- Keywords: software-level RowHammer protection, guard rows, swap space

# Paper content

## Summary

To provide a software-level RowHammer protection for DRAM not equipped with effective hardware-level mitigation like TRR and ECC, this paper designs *ZebRAM* (**Zeb**ra D**RAM**), which isolates every DRAM row that contains data with guard rows that absorb any RowHammer-induced bit flips. And rather than leaving guard rows unused, ZebRAM improves performance by using the guard rows as efficient, integrity-checked and optionally compressed swap space. 

ZebRAM enforces all odd rows to be guard rows (inaccessible), forming unsafe region, and all even rows form safe region which can be directly accessed by the processor (unsafe odd rows and safe even rows comprise a **zebra pattern**). Hence, given that the unsafe region is unused, the attacker cannot flip bits in the data used by the system, i.e., safe region.

To maintain good performance, ZebRAM exposes the unsafe region as a swap device, which is integrity-checked to handle potential bit flips in the unsafe region, to the protected operating system, and reuses existing page replacement policies of the operating system. To protect the safe region from accesses to the unsafe region, ZebRAM uses a **cache** in front of the unsafe region, which is allocated from the safe region and ZebRAM is free to choose its size and replacement policy (*least-recently-added*) in a way that protects the safe region.

The prototype implementation of ZebRAM contains four components: the *Memory Remapper*, the *Integrity Manager*, the *Swap Manager*, and the *Cache Manager*. ZebRAM prototype implements Memory Remapper in the hypervisor and the other three components in the guest OS. Memory Remapper implements the split of physical memory into a safe and unsafe region, using reverse engineered mapping. Integrity Manager protects the integrity of the unsafe region, using a commonly-used Single-Error Correction and Double-Error Detection (SECDED) code and optionally collision resistant hash functions such as SHA-256. Swap Manager (LKM for the guest OS) uses the unsafe region to implement an efficient swap disk in memory. Cache Manager implements a fully associative cache that speeds up access to the swap space while simultaneously preventing RowHammer attacks against safe rows by reducing the access frequency on memory rows in the unsafe region.

Finally their evaluation of ZebRAM defending against RowHammer shows a good effectiveness with negligible performance reduction (an overhead of $5\%$ on the SPEC CPU 2006 benchmarks).


## Strengths

- As a software-level protection, ZebRAM addresses the problem that existing software-based defenses have proven ineffective against advanced RowHammer attacks, while hardware defenses are impractical to deploy in the billions of devices already in operation, with considerably low overhead.
- ZebRAM proposes an innovative idea that using guard rows as integrity-checked swap space, and shows good effectiveness.
- Leverage the virtualization extensions in commodity processors as much as possible, doing minimum modification to system and no hardware modifications.

## Weaknesses

- Though ZebRAM shows low overhead on small working sets, on larger working sets the overhead is non-trivial ($5\times$), due to the smaller (half) available memory and relatively long swap latency.
- ZebRAM is only suitable for old DRAM chips like DDR3, for ZebRAM can only absorb bit flips in adjacent rows, but newer DRAM chips is much more vulnerable to RowHammer that attacker can induce bit flips in rows at a farther distance (one line apart or more). To address this problem ZebRAM should add more guard rows, which will greatly increase the overhead.
- The ZebRAM prototype implementation requires modification in guest OS, making ZebRAM not transparent to the guest, limiting its scalability.
- ZebRAM requires hypervisor, and lots of specific hardware and system supports like *Second Level Address Translation (SLAT)* and Qemu-KVM’s `mmap` function, limiting its universality.

## Paper presentation

- Used a lot of abbreviations without explaining their meaning.

## Thoughts
- To address the 3rd and 4th weakness above, the ZebRAM mechanism can be implemented directly on DRAM, by modifying the pins of chips and bootloader of the operating system. But in this way we will lose the most important advantage of ZebRAM: providing protection for billions of devices already in operation.
- The size of swap cache should be calculated formally, or may be unnecessarily large and induce more overhead to system.

## Takeaways and questions

Software-level protection research needs abundant knowledge about operating systems, as well as hardware (processor) support. And for the threat scenarios are usually in servers, so virtualization technology is also very important.

Security goes two ways. The first is to provide most advanced security technology for new-born or not-yet-born products to reach the highest effectiveness. The second is to provide patches for products already in operation that cannot update thoroughly, to offer at least basic security support. Different from the former, the latter pursues the minimum modifications to the original products, while struggling to reach the highest possible effectiveness. ZebRAM goes the latter way.

**Second Level Address Translation** (**SLAT**), also known as **nested paging**, is a hardware-assisted virtualization technology which makes it possible to avoid the overhead associated with software-managed shadow page tables (guest’s physical address to host’s virtual address).

**Loadable Kernel Module (LKM)** is an object file that contains code to extend the running kernel, or so-called *base kernel*, of an operating system. LKMs are typically used to add support for new hardware (as device drivers) and/or filesystems, or for adding system calls. When the functionality provided by a LKM is no longer required, it can be unloaded in order to free memory and other resources.

