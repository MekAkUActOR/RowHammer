# Haowen Liu (2020-12-04)

# Paper information

- Title: Flip Feng Shui: Hammering a Needle in the Software Stack
- Authors: Kaveh Razavi, Ben Gras, Erik Bosman, Bart Preneel, Cristiano Giuffrida, Herbert Bos
- Venue: USENIX Security 2016
- Keywords: RowHammer exploitation, memory deduplication, memory massaging, RSA & Diffie-Hellman cryptanalysis, breaking weakened cryptosystems

# Paper content

## Summary

This paper introduces Flip Feng Shui, a new exploitation vector which allows an attacker to induce bit flips over *arbitrary* physical memory in a *fully controlled way*. Using FFS, they conduct dFFS (deduplication-based FFS) to mount end-to-end corruption attacks against OpenSSH public keys, and Debian/Ubuntu update URLs and trusted public keys in a co-hosted victim VM. FFS strategy consists of three steps. 

First, FFS conducts ***memory templating*** to get the knowledge of *usable* bit-flip patterns (direction, offset, and contents of the page) using a variant of double-sided RowHammer. In dFFS, they rely on THP to know the host physical addresses inside the VM.

Second, FFS exploits memory deduplication, an operating system optimization, to derive *physical **memory massaging** primitive*, to steer targeted sensitive data towards the vulnerable physical memory locations found in memory templating step. In dFFS, they leverage KSM’s property that KSM merges pages having same contents and backs the first registered physical page, so they start the attacker VM before the victim and craft a page with the same contents as the victim page and place it at the desired physical memory page. 

Finally, FFS uses the former two primitives to conduct ***exploitations***, triggering the hardware vulnerability (RowHammer) to corrupt intended data. In dFFS, they first carry out cryptanalysis of RSA (and of Diffie-Hellman in the appendix) with bit flips, and prove that for a $1024$-to-$2048$-bit RSA modulus with *an arbitrary bit flip*, one can expect to effeciently factorize it with a probability of $12$-$22\%$. Then they attack corrupted RSA public keys by factorizing them and generating private keys, and successfully compromise OpenSSH and `apt` package distribution system in a co-hosted cloud VMs.

In the end, they discuss the possible mitigations against FFS at the hardware (ECC, TRR) and software (disabling or modifying memory deduplication) levels, and they give some suggestions at the software layer to improve current practices.


## Strengths

- Leveraging RowHammer vulnerability, this paper introduces a novel exploitation vector that can induce bit flips over *arbitrary* physical memory in a *fully controlled way*, to mount devastatingly powerful end-to-end attacks with great scalability, in complete absence of software vulnerabilities.
- By abusing modern memory management features, they demonstrate the assumption that attackers can derive the physical memory of the victim to make Rowhammer exploitation possible. Using FFS, an attacker can seize control of nearly arbitrary physical memory in the software stack.
- Use memory deduplication to conduct memory massaging, beyond information disclosure attacks, demonstrating that memory deduplication has stronger security implications than previously shown.
- Perform a formal cryptanalysis of RSA public keys in the presence of bit flips, and prove that for a $1024$-to-$2048$-bit RSA modulus with *an arbitrary bit flip*, one can expect to effeciently factorize it with a probability of $12$-$22\%$.
- A complete attack chain, relatively easy to conduct and with a high success rate.

## Weaknesses

- Did not state the *memory templating* step clearly. Actually due to data pattern dependency of RowHammer bit flips, an available template is not easy to find.
- Attack chain is relatively fragile. Memory deduplication mechanism in host system is necessary for *memory massaging* step, so if memory deduplication is disabled, the exploitation fails.

## Paper presentation

- Write in a layer-wise method, from summary to detail, exactly appropriate for this paper.
- ***Writting error***. In the 5th paragraph of Section 3, the *Carmichael function* should be $\lambda(n)=lcm(p_1-1,p_2-1)$, not $lcm(p_1,p_2)$.
- ***Typos***. In the 7th paragraph of Section 3, the first two fomula should be $n’=\Pi_{j=1}^{s’}{p’}^{\widetilde{\gamma}_i}_i$ and $c’=m^e$ $mod$ $n’$.
- Some charts are a bit difficult to understand.

## Thoughts
- All information channels are under risk. Exploiting those risks causes attackes. FFS is one representation of exploiting risky channel: the page cache in DRAM is information channel and the hardware vulnerability (RowHammer) is the risk. Keeping the integrity, confidenciality and availability of messages in information channels is the basic principle of information security. So we should stay alert about the risky channels. For sensitive information, one should not ignore the risk because no vulnerability has been found yet, but should include integrity or authenticity verification mechanism at the end of channels.

## Takeaways and questions

- ***KSM***, the Linux implementation of memory deduplication, uses a kernel thread that periodically scans memory to find memory pages with the same contents that are candidates for merging. It then keeps a single physical copy of a set of candidate pages, marks it read-only, and updates the page-table entries of all the other copies to point to it before releasing their physical pages to the system.
- ***THP*** is a Linux kernel feature that runs in the background and merges virtually contiguous normal pages (i.e., 4 kB pages) into huge pages (i.e., 2 MB pages) that rely on contiguous pieces of physical memory. 