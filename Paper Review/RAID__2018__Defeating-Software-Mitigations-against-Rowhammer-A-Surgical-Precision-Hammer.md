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

With near-perfect knowledge about all aspects of the memory system provided by RAMSES, they hammer DIMMs to obtain the *flip tables*, which contain a sequence of hammerings, each consisting of a set of target addresses along with bit flip locations in the victim rows. Then they design an attack simulator, using flip tables which can provide a lightweight alternative to full program execution for evaluating the feasibility of Rowhammer-based attacks. The flip tables and the attack simulator constitute *Hammertime*[^3], a software suite for testing, profiling and simulating the Rowhammer DRAM defect.

Finally, they evaluate the effectiveness of RAMSES and find RowHammer attacks with right memory addressing cause much more bit flips. Using RAMSES and Hammertime, they evaluate existing RowHammer attacks and find that RAMSES improves these attacks by orders of magnitude. They also evaluate two software-only defenses and find that they are unsafe against RowHammer because their wrong assumption about memory addressing.


## Strengths

- This paper points out and demonstrates the wrong assumptions about memory addressing made by lots of prior researches. And it provides a methodology and tool to model memory addressing, greatly improving the effectiveness of existing attacks, which is a good complement to the prior works.
- This paper proposes a formal format to describe DRAM address and contiguity at memory cell level. They define a DRAM address to be a 6-tuple of the form <channel, DIMM, rank, bank, row, column>, with the order of the fields reflecting hardware hierarchy levels. And they define two DRAM addresses are contiguous if they are row co-located (two addresses are co-located on a particular hierarchy level if they compare equal on all fields up to and including that level) and have consecutive column indexes.
- This paper releases a software suite for testing, profiling and simulating the Rowhammer DRAM defect as open source (Hammertime), which allows researchers to profile a large set of DIMMs for bit flips and later use the resulting data to simulate the Rowhammer defect in software, making Rowhammer research much faster, more comparable, and more reproducible.

## Weaknesses

- Less innovative. Basically it uses and improves other works’ methods.
- Describe the reverse engineering step of RAMSES too briefly, by only citing a prior work. But this part is an important part of this paper.
- RAMSES is **not universal**, for memory address mapping varies with DRAM vendors, configurations and even manufacturing variation. One flip table for a certain DRAM cannot be used by other DRAM.
- They said their support for DDR4 memory controllers as well as AMD CPUs was a work in progress, but I went to their GitHub repository and found its update has already stopped for over 2 years, and existing bugs also haven’t been addressed yet.
- ***Writting error.*** In the second paragraph of Section 6, the root cause of RowHammer is not repeated toggling of the DRAM row buffer, but repeated toggling of a certain *wordline*.

## Thoughts
- Using a plain text file storing RowHammer knowledge of a certain real DRAM for software attack simulation is a good idea, which can greatly accelerate the process of tesing a new RowHammer exploitation vector. The data structure and format are worth meticulously designing.
- Though considering memory addressing seems very promising in this paper, however getting flip tables is not an easy job and inefficient, for memory address mapping varies with DRAM vendors, configurations and even manufacturing variation. So it’s reasonable to treat memory addressing as a black-box for prior work. But to achieve the near-perfect effectiveness, taking memory addressing into consideration is necessary. To avoid the cumber of reverse engineering the whole memory address mapping, one can just only take the universal part like DDR *rank mirroring* into consideration, making it clear but abandon the rest custom address remappings. Then one can use a fuzzer like *TRRespass* but combine the address mapping knowledge reverse engineered, to generate effective access patterns in a more targeted way than *TRRespass*, and more universal than RAMSES.

## Takeaways and questions

***DDR rank mirroring:*** There is a via grid located under the DRAMs for wiring the CA signals (address, bank address, command, and control lines) to the DRAM pins. The length of the traces from the vias to the DRAMs places limitations on the bandwidth of the module. The shorter these traces, the higher the bandwidth. To extend the bandwidth of the CA bus for DDR3 modules, a scheme was defined to reduce the length of these traces.The pins on the DRAM are defined in a manner that allows for these short trace lengths.

- It sounds a good RowHammer simulation and testing suite but the citation is not that much, and later researches often do their own memory addressing reverse engineering but not use RAMSES, why?



[^1]: Pessl, P., Gruss, D., Maurice, C., Schwarz, M., Mangard, S.: DRAMA: exploiting DRAM addressing for cross-CPU attacks. In: SEC 2016 (2016)
[^2]: Address Mirroring Feature; Dram Pin Wiring Mirroring - Samsung M391B5273DH0 Hardware User Manual. https://www.manualslib.com/manual/145218/Samsung-M391b5273dh0.html?page=8
[^3]: https://github.com/vusec/hammertime