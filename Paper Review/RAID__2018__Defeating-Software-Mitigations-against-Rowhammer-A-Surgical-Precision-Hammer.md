# Haowen Liu (2020-12-13)

# Paper information

- Title: Defeating Software Mitigations against Rowhammer: A Surgical Precision Hammer
- Authors: Andrei Tatar, Cristiano Giuffrida, Herbert Bos, and Kaveh Razavi
- Venue: RAID 2018
- Keywords: RowHammer attacks and defenses, memory address translation and manipulation, DRAM geometry

# Paper content

## Summary

To remain agnostic to the underlying DRAM hardware, lots of RowHammer attacks and software-only defenses make simplifying assumptions that physical memory addresses are mapped linearly by the memory controller to DRAM rows. This paper investigates this important assumption and finds it wrong, which makes these attacks and defenses less effective. 

So they develop *RAMSES*, a standalone C library modeling the address translation and manipulation that occurs between the CPU and DIMMs, which provides mapping functions for Intel Sandy Bridge, Ivy Bridge and Haswell memory controllers based on functions reverse engineered in previous work[^1], with the support for DDR *rank mirroring*[^2] (address pins are respectively interchanged in order to make the circuit layout simpler), the *on-DIMM remappings* (custom address remappings), and the possibility to easily add new remappings once they are discovered.

With near-perfect knowledge about all aspects of the memory system provided by RAMSES, they hammer DIMMs to obtain the *flip tables*, which contain a sequence of hammerings, each consisting of a set of target addresses along with bit flip locations in the victim rows. Then they design an attack simulator, using flip tables which can provide a lightweight alternative to full program execution for evaluating the feasibility of Rowhammer-based attacks. The flip tables and the attack simulator constitute *hammertime*[^3], a software suite for testing, profiling and simulating the Rowhammer DRAM defect.

Finally, they evaluate the effectiveness of RAMSES and find RowHammer attacks considering memory addressing cause much more bit flips. Using RAMSES and hammertime, they evaluate existing RowHammer attacks and find that RAMSES improves these attacks by orders of magnitude. They also evaluate two software-only defenses and find that they are unsafe against RowHammer because their wrong assumption about memory addressing.


## Strengths

- 

## Weaknesses

- 

## Thoughts
- 

## Takeaways and questions







[^1]: Pessl, P., Gruss, D., Maurice, C., Schwarz, M., Mangard, S.: DRAMA: exploiting DRAM addressing for cross-CPU attacks. In: SEC 2016 (2016)
[^2]: Address Mirroring Feature; Dram Pin Wiring Mirroring - Samsung M391B5273DH0 Hardware User Manual. https://www.manualslib.com/manual/145218/Samsung-M391b5273dh0.html?page=8
[^3]: https://github.com/vusec/hammertime