# DRAM

- [ ] S. Hong, “Memory Technology Trend and Future Challenges,” in IEDM, 2010.
- [x] K. K. Chang et al., “Understanding Latency Variation in Modern DRAM Chips: Experimental Characterization, Analysis, and Optimization,” in SIGMETRICS, 2016.
- [x] J. Liu et al., “An Experimental Study of Data Retention Behavior in Modern DRAM Devices: Implications for Retention Time Profiling Mechanisms,” in ISCA, 2013.

# Cause of RowHammer

- [x] T. Yang et al., “Trap-Assisted DRAM Row Hammer Effect,” EDL, 2019.

# RowHammer Characterization

- [x] K. Park et al., “Experiments and Root Cause Analysis for Active-Precharge Hammering Fault in DDR3 SDRAM under 3×nm Technology,” MR, 2016.
- [x] K. Park et al., “Statistical Distributions of Row-Hammering Induced Failures in DDR3 Components,” MR, 2016.

# Vulnerability and Attacks

### Vulnerability

- [x] L. Cojocar et al., “Exploiting Correcting Codes: On the Effectiveness of ECC Memory Against RowHammer Attacks,” in SP, 2019.
- [x] P. Frigo et al., “TRRespass: Exploiting the Many Sides of Target Row Refresh,” in SP, 2020.
- [ ] D. Gruss et al., “Another Flip in the Wall of RowHammer Defenses,” in SP, 2018.
- [ ] A. Kwong et al., “RAMBleed: Reading Bits in Memory Without Accessing Them,” in SP, 2020.
- [ ] A. Tatar *et al.*, “Defeating Software Mitigations against Rowhammer: A Surgical Precision Hammer,” in *RAID*, 2018.

### Attacks

- [x] K. Razavi et al., “Flip Feng Shui: Hammering a Needle in the Software Stack,” in USENIX Security, 2016.
- [x] V. Van Der Veen et al., “Drammer: Deterministic Rowhammer Attacks on Mobile Platforms,” in CCS, 2016.
- [ ] M. Lipp et al., “Nethammer: Inducing RowHammer Faults Through Network Requests,” in EuroS&PW, 2018.

# Mitigations

### Software-Level

- [ ] Z. B. Aweke et al., “ANVIL: Software-Based Protection Against Next-Generation Rowhammer Attacks,” in ASPLOS, 2016.
- [ ] C. Bock et al., “RIP-RH: Preventing Rowhammer-Based Inter-Process Attacks,” in ASIA-CCS, 2019.
- [ ] F. Brasser et al., “Can’t Touch This: Practical and Generic Software-only Defenses Against RowHammer Attacks,” USENIX Security, 2017.
- [ ] A. Chakraborty et al., "Deep Learning Based Diagnostics for Rowhammer Protection of DRAM Chips," in ATS, 2019.
- [ ] R. K. Konoth et al., “ZebRAM: Comprehensive and Compatible Software Protection Against Rowhammer Attacks,” in OSDI, 2018.
- [x] L. Cojocar et al. "Are We Susceptible to Rowhammer? An End-to-End Methodology for Cloud Provider,” in SP, 2020.

### DRAM-Level

- [ ] B. Aichinger, “DDR Memory Errors Caused by Row Hammer,” in HPEC, 2015.
- [ ] D.-H. Kim et al., “Architectural Support for Mitigating Row Hammering in DRAM Memories,” IEEE CAL, 2014.
- [ ] M. Son et al., “Making DRAM Stronger Against Row Hammering,” in DAC, 2017.
- [ ] J. M. You et al., “MRLoc: Mitigating Row-Hammering Based on Memory Locality,” in DAC, 2019.
- [x] E. Lee et al., “TWiCe: Preventing Row-Hammering by Exploiting Time Window Counters,” in ISCA, 2019.
- [ ] H. Gomez et al., “DRAM Row-Hammer Attack Reduction using Dummy Cells,” in NORCAS, 2016.
- [ ] H. Hassan et al., “CROW: A Low-Cost Substrate for Improving DRAM Performance, Energy Efficiency, and Reliability,” in ISCA, 2019.
- [ ] M. Kim et al., “An Effective DRAM Address Remapping for Mitigating Rowhammer Errors,” TC, 2019.

### DRAM-System Cooperation

- [ ] O. Mutlu, “Memory Scaling: A Systems Architecture Perspective,” in IMW, 2013.

### Profile-Guided Mechanisms

None

# Experiment and Simulation Methodology

- [ ] H. Hassan et al., “SoftMC: A Flexible and Practical Open-Source Infrastructure for Enabling Experimental DRAM Studies,” in HPCA, 2017.
  - [ ] SAFARI Research Group, “SoftMC Source Code,” https://github.com/CMU-SAFARI/SoftMC.
- [ ] Y. Kim et al., “Ramulator: A Fast and Extensible DRAM Simulator,” in CAL, 2016.
  - [ ] SAFARI Research Group, “Ramulator Source Code,” https://github.com/CMU-SAFARI/ramulator.