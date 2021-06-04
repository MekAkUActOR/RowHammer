# Haowen Liu (2020-10-17)

# Paper information
- Title: Revisiting RowHammer: An Experimental Analysis of Modern DRAM Devices and Mitigation Techniques
- Authors: Jeremie S. Kim, Minesh Patel, A. Giray Yağlıkçı, Hasan Hassan, Roknoddin Azizi, Lois Orosa, Onur Mutlu
- Venue: ISCA 2020
- Keywords: DRAM vulnerability, RowHammer characterization, RowHammer mitigation evaluation

# Paper content
## Summary
This paper experimentally demonstrates how vulnerable modern DRAM chips are to RowHammer at the circuit-level (RowHammer is a circuit-level interference phenomenon), studies how this vulnerability will **scale going forward**, and evaluates five state-of-the-art RowHammer **mitigation mechanisms** using cycle-accurate simulation. Finally it provides promising directions for future research on RowHammer mitigation.

To characterize RowHammer many detailed variable-controlling experiments were carried out on a broad range of real modern DRAM chips across different DRAM types, technology node generations, and manufacturers. Here are some important conclusions.

- Newer DRAM chips appear to be more vulnerable to RowHammer.
- The logarithm of the number of RowHammer bit flips has a linear relationship with the logarithm of HC (Hammer count).
- Chips of newer DRAM technology nodes can exhibit RowHammer bit flips 1) in more rows and 2) farther away from the aggressor row. The number of RowHammer bit flips that occur in a given row decreases as the distance from the victim row increases. A single word can contain several bit flips.
- The ECC plays a role in mitigating RowHammer bit flips.

The paper uses cycle-accurate simulation to evaluate the five state-of-the-art RowHammer mitigation mechanisms --- Increased Refresh Rate, PARA, ProHIT, MRLoc, TWiCe --- and the ideal refresh-based mitigation mechanism, and draws the conclusion that these mechanisms are not scalable in the face of deteriorating RowHammer vulnerability and even the ideal refresh-based mitigation mechanism significantly affects system performance when the DRAM is very vulnerable to RowHammer.

Modern DRAM chips are more vulnerable to RowHammer. However, existing RowHammer mitigation mechanisms suffer from large performance overheads at projected future hammer counts (not scalable) and are still far from ideal. A scalable and low-overhead solution to RowHammer is needed. The paper provides two promising directions --- *DRAM-System Cooperation* and *Profile-Guided Mechanisms* --- for future RowHammer mitigation research.

## Strengths
- Detailed describe experimental platform, infrastructure, condition and parameters, making it easier for researchers to reproduce experiments.
- Targeted and selective experiments. The paper first carries out some preliminary experiments to find the suitable and efficient experimental conditions and parameters (e.g., worst-case condition), and then continue experiments only in these selective conditions, avoiding invalid experiments and making the experiment more efficient.
- Provide two promising directions for future RowHammer mitigation research, which is the most valuable part of this paper.

## Weaknesses
- **Logical Error**. The paper uses RowHammer’s key observation in their 2014 work, that repeatedly accessing an arbitrary row causes the two directly **physically-adjacent** rows to contain the highest number of RowHammer bit flips, to reverse-engineer the confidential logical-to-physical DRAM-internal row address remapping, which all subsequent experiments are based on. It’s fine but in *RowHammer Spatial Effects* section they use experiments based on this to demonstrate RowHammer spatial effects. This means, they use experiments based on *RowHammer spatial effects* to demonstrate *RowHammer spatial effects*. Ultimately it is a *circular argument*.

## Paper presentation
- Some repeated sentences.

## Thoughts
- After all the RowHammer bit flip is a physical phenomenon, so it’s possible to come up with a physical model to describe it precisely. We can describe the interference of RowHammer to each DRAM cell using mathematical and physical model. Then we can simulate the interference of RowHammer to DRAM cells according to the mathematical and physical model using microelectronic technology. For mitigation usage we may just directly exert the simulating interference on all DRAM cells simultaneously instead of activating aggressor rows one by one. And in this way we easily find RowHammerable areas and protect them in a targeted way with much lower overhead, since RowHammer bit flips are repeatable. This is also what *Profile-Guided Mechanisms* means.
- *DRAM-System Cooperation* and *Profile-Guided Mechanisms* are not independent of each other. We can combine these two directions and create more scalable and low-overhead mitigations. For example, we can scan the RowHammer-able areas after manufacturing and record their information (address, risk) on extra ROM, and when the DRAM is used in a system the OS checks the ROM and then make special mitigation measures (like only storing less important data in these areas) in these areas. Or we can scan DRAM dynamically (when switching on/off the OS) (considering the RowHammer-able areas may change with time, which might haven’t been discussed by previous works) and do the same system mitigation mechanisms.
- Discuss the correlation between RowHammerable areas and DRAM using time to complement the characterization of RowHammer.

## Takeaways and questions
This paper is a good example in doing an effective and comprehensive experiment. First, clarify motivation and goal of the experiment. Second, decide the experimental methodology according to your goal. Third, establish a universal and effective experimental platform. Fourth, determine experimental parameters. Fifth, do preliminary experiments to narrow the scope of experimental conditions. Sixth, carry out main experiments, and modify the experimental condition dynamically according to observations in progress. Seventh, collect observations, explain them and make conclusions. Finally, explore more about your observations (if the experiments cost much you’d better do that to make it more valuable) and discuss some interesting/unexpected observations in experiments.

Here I have a question.

- Is it feasible to propose a physical model to profile the interference of RowHammer to each DRAM cell precisely? There are lots of work on RowHammer but few of them involve this aspect, why?