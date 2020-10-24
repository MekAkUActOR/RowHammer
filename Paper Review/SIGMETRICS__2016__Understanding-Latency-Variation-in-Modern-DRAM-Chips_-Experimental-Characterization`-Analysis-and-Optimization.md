# Haowen Liu  2020-10-22

# Paper information

- Title: Understanding Latency Variation in Modern DRAM Chips: Experimental Characterization, Analysis, and Optimization
- Authors: Kevin K. Chang, Abhijith Kashyap, Hasan Hassan, Saugata Ghose, Kevin Hsieh, Donghyuk Lee, Tianshi Li, Gennady Pekhimenko, Samira Khan, Onur Mutlu
- Venue: SIGMETRICS 2016
- Keywords: DRAM, latency, optimization

# Paper content

## Summary
This paper experimentally demonstrates how vulnerable modern DRAM chips are to RowHammer at the circuit-level, studies how this vulnerability will **scale going forward**, and evaluates five state-of-the-art RowHammer **mitigation mechanisms** using cycle-accurate simulation. Finally it provides promising directions for future research on RowHammer mitigation.

To characterize RowHammer many detailed variable-controlling experiments were carried out on a broad range of real modern DRAM chips across different DRAM types, technology node generations, and manufacturers. Here are some important academic conclusions.

- Newer DRAM chips appear to be more vulnerable to RowHammer, and the first RowHammer bit flips appear earlier.
- The log of the number of RowHammer bit flips has a linear relationship with the log of HC(Hammer count).
- Chips of newer DRAM technology nodes can exhibit RowHammer bit flips 1) in more rows and 2) farther away from the victim row. The number of RowHammer bit flips that occur in a given row decreases as the distance from the victim row increases. A single word can contain several bit flips.
- The ECC plays a role in mitigating RowHammer bit flips.

The paper uses cycle-accurate simulation to evaluate the five state-of-the-art RowHammer mitigation mechanisms — **Increased Refresh Rate**, **PARA**, **ProHIT**, **MRLoc**, **TWiCe** — and the **ideal refresh-based** mitigation mechanism, and draws the conclusion that these mechanisms are not scalable in the face of worsening RowHammer vulnerability and even the **ideal refresh-based** mitigation mechanism affects system performance at very low $HC_{first}$ values(means very vulnerable to RowHammer).

RowHammer bit flips cast tremendous thread on security of hardwares and softwares, and modern DRAM chips that use smaller process technology node sizes are significantly more vulnerable to RowHammer. However, existing RowHammer mitigation mechanisms suffer from prohibitively large performance overheads at projected future hammer counts(not scalable) and are still far from an ideal selective-refresh-based RowHammer mitigation mechanism. A scalable and low-overhead solution to RowHammer is needed. The paper provides two promising directions — ***DRAM-System Cooperation*** and ***Profile-Guided Mechanisms*** — for future RowHammer mitigation research.

## Strengths

- Carry out detailed variable-controlling experiments on a broad range of real modern DRAM chips across different DRAM types, especially the new ones, providing evidence that modern DRAM chips are becoming more and more vulnerable to RowHammer.
- Detailed explain experimental platform, infrastucture, condition and parameters, making it easier for researchers to reproduce experiments.
- Targeted and selective experiments. The paper first carries out some preliminary experiments to find the suitable and efficient experimental conditions and parameters(e.g., worst-case condition), and then continue experiments only in these selective conditions, avoiding invalid experiments and making the experiment more efficient.
- Carry out simulation to not only evaluate the performance of existing mitigation mechanisms now but also study how the mitigation mechanisms scale with chip vulnerability in the future.
- Provide two promising directions for future RowHammer mitigation research, which is the most valuable part of this paper.

## Weaknesses

- **Logical Error**. The paper uses RowHammer’s key observation in their 2014 work that repeatedly accessing an arbitrary row causes the two directly **physically-adjacent** rows to contain the highest number of RowHammer bit flips to reverse-engineer the confidential logical-to-physical DRAM-internal row address remapping, which all subsequent experiments are based on. It’s fine but in ***RowHammer Spatial Effects*** section they use experiments based on this to demonstrate RowHammer spatial effects. This means, they use experiments based on *==RowHammer spatial effects==* to demonstrate *==RowHammer spatial effects==*. Ultimately it is a ***circular argument***. And after all in their 2014 work it’s not an observation. The true observation in their 2014 work is *accessing an arbitrary row causes the two directly **logically-adjacent** rows to contain the highest number of RowHammer bit flips*, not **physically-adjacent**. And the physically-adjacent one is only a ***hypothesis*** in their 2014 work(but might have been widely accepted by the academic community).
- Few new ideas, only repeated the experiments they had done in 2014 on new DRAM chips and got the similar conclusions, no new observation.
- Inefficient experimental methodology. Carried out complex and costly experiments, but only drew superficial conclusions, and most of them are *modern DRAM chips are more vulnerable to RowHammer*. Didn't further explore the physical nature from the experimental observations or try to propose a physical model to precisely describe RowHammer bit flips phenomenon.
- A lot of repeated sentences.

## Thoughts
- After all the RowHammer bit flip is a physical phenomenon, so it’s possible to come up with a physical model to describe it precisely. We can describe the interference of RowHammer to each DRAM cell using mathematical and physical model. Then we can simulate the interference of RowHammer to DRAM cells according to the mathematical and physical model using microelectronic technology. For mitigation usage We may just directly exert the simulating interference on all DRAM cells simultaneously instead of activating aggressor rows one by one. And in this way we easily find RowHammerable areas and protect them in a targeted way with much lower overhead, since RowHammer bit flips are repeatable. This is also what ***Profile-Guided Mechanisms*** in the paper means.
- ***DRAM-System Cooperation*** and ***Profile-Guided Mechanisms*** are not independent of each other. We can combine these two directions and create more scalable and low-overhead mitigations. For example, we can scan the RowHammerable areas after manufacturing and record their information(address, risk) on extra ROM, and when the DRAM is used in a system the OS checks the ROM and then make special mitigation measures(like only storing less important data in these areas) in these areas. Or we can scan DRAM dynamically(when switching on / off the OS)(considering the RowHammerable areas may change with time, which might haven’t been discussed by previous researches) and do the same system mitigation mechanisms.
- Discuss the correlation between RowHammerable areas and DRAM using time to complement the characterization of RowHammer.

## Takeaways and questions

This paper is a good example in doing an effective and comprehensive experiment. First, clarify motivation and goal of the experiment. Second, decide the experimental methodology according to your goal. Third, establish a universal and effective experimental platform. Fourth, determine experimental parameters. Fifth, do preliminery experiments to narrow the scope of experimental conditions. Sixth, carry out main experiments, and modify the experimental condition dynamically according to observations in progress. Seventh, collect observations, explain them and make conclusions. Finally, explore more about your observations(if the experiments cost much you’d better do that to make it more valuable) and discuss some interesting/unexpected observations in experiments.

The paper also disappoints me for it carried out comprehensive and costly experiments that few lab can do, but only drew some simple and superficial conclusions. It's like a richman spending a lot of money on worthless things.

Here I have some questions.

- Is it realistic to try to propose a physical model to describe the interference of RowHammer to each DRAM cell precisely? There are lots of work on RowHammer but few of them involve this aspect, why?
- How about the scalability of system-level mitigation mechanisms with the increasing RowHammer vulnerability? If it’s more scalable(I think so intuitively) it’s also a good idea to develop mitigations in this direction.

