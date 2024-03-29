# Haowen Liu (2020-10-30)

# Paper information
- Title: An Experimental Study of Data Retention Behavior in Modern DRAM Devices: Implications for Retention Time Profiling Mechanisms
- Authors: Jamie Liu, Ben Jaiyen, Yoongu Kim, Chris Wilkerson, Onur Mutlu
- Venue: ISCA 2013
- Keywords: DRAM refresh, retention time profiling, data pattern dependence, variable retention time

# Paper content
## Summary
**Retention time profiling** is a way to mitigate overhead caused by unnecessary refreshes in DRAM. However, two phenomena make it prohibitive: Data Pattern Dependence (DPD) (the retention time of each DRAM cell depends on the nearby data pattern) and Variable Retention Time (VRT) (many DRAM cells transition between multiple leakage current states, and consequently multiple retention time states). This paper experimentally characterizes them and gets some conclusions:

- Different DPDs have distinctive effects on retention time, and different DRAM chips have different DPD affecting most, making retention time profiling prohibitive;
- No DPD can cover (expose) all weak cells;
- The impact of DPD increases with technology scaling;
- In VRT, the length of time that DRAM cells spend in a given retention time state distributes exponentially, making retention time profiling difficult.

The paper explains DPD as the result of complex line coupling determined by mapping, remapping and circuit organization, and VRT as the result of the presence of a charge trap in the gate oxide, which is a memoryless random process. The paper advocates that DPD and VRT should be addressed in future retention time profiling research which is predicted to focus on *online profiling* (developing a mechanism to efficiently profile DRAM while it in use).


## Strengths
- This paper conducted experiments on a wide range of commodity DRAM chips across different manufacturers and generations.
- Designed robust experimental methodology like decoupling the effect of DPD and VRT by running all tests at a given refresh interval extension time together. And conducted preliminary experiments to demonstrate the correctness of their apparatus and methodology by comparing with previous researches’ results.
- Pointed out the flaws in previous works on DPD that only all 0s/1s is insufficient, and introduced three more DPDs to ensure the effectiveness of characterization.

## Weaknesses
- Though the paper explained different DPD in different DRAM chips as the result of circuit architecture variation, they did not explore the correlation between DPD and circuit architecture, which is necessary for retention time profiling.

## Paper presentation
- Figures are apparent and easy-to-understand.
- Some figures are useless. For example, Figure 7(a) is unnecessary for it can be described in one sentence, and Figure 8(a) is also unnecessary for there is a Logarithmic y-axis figure. And all figures are too big, no distinction between primary and secondary. These figures take up the space in vain, leading to less compact writing and inefficient characterization of VRT.
- Article architecture is not appropriate. Talk too much about experiment design and experiment correctness demonstration, but the exploration depth of DPD and VRT is not that enough, which are exactly the key part of the paper.

## Thoughts
- *Online profiling* is promising, for actual usage can provide much more data patterns to characterize DPD, and minimum retention time of all cells can be exposed in enough length of using time, which leads to sound and accurate retention time profiling. With enough time the retention time profiling will reach a thermal equilibrium, in which state we can apply our mechanisms to mitigate the overhead of unnecessary refreshes. However it is not easy to implement online profiling because retention time profiling needs to extend refresh interval to cause bit flips, which will insert big interference to running system.

## Takeaways and questions
- I guess running all tests at a given refresh interval extension time together cannot completely decouple the effects of DPD and VRT, but can decrease the effects to minimum.
- In Section 5.1, why the decrease of lowest-coverage data pattern’s coverage can indicate that the impact of DPD increases with technology scaling?

## Q&As
**Q1:** What is circuit architecture in Weaknesses?

**A1:** Circuit architecture of DRAM is how manufacturers organize electronic components at the bottom level of DRAM chips.