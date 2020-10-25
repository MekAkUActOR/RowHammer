# Haowen Liu  2020-10-22

# Paper information

- Title: Understanding Latency Variation in Modern DRAM Chips: Experimental Characterization, Analysis, and Optimization
- Authors: Kevin K. Chang, Abhijith Kashyap, Hasan Hassan, Saugata Ghose, Kevin Hsieh, Donghyuk Lee, Tianshi Li, Gennady Pekhimenko, Samira Khan, Onur Mutlu
- Venue: SIGMETRICS 2016
- Keywords: DRAM, latency variation, performance optimization

# Paper content

## Summary
This paper experimentally characterizes and understands the latency variation across cells within a DRAM chip for three fundamental DRAM operations(`activation`, `precharge`, `restoration`), and develops new mechanisms that exploit the latency variation to reliably improve performance.

The experiments are carried across a total of 30 __DDR3__ DRAM modules comprising 240 DRAM chips from the three major DRAM vendors, on an FPGA-based DRAM testing in- frastructure. Here are some important conclusions.

- Significant variation is present in modern DRAM chips for the latencies of all three fundamental DRAM operations.
- There is spatial locality in inherently slower cells: such cells are clustered in certain regions of a DRAM chip.
- Each latency exhibits a different level of impact on the inherently-slower cells.
- Most of the erroneous cache lines have a single-bit error, with only a small fraction of cache lines experiencing more than one bit flip.
- No clear correlation is between temperature and variation in cell access latency.
- The stored data pattern in cells affects access latency variation

Therefore, the paper proposes _FLY-DRAM_ to exploit these findings to improve performance: (i) categorize the DRAM cells into fast and slow regions, (ii) expose this information to the memory controller, (iii) access the fast regions with a lower latency. The simulation-based analysis on the mechanism shows a good result. An idea of a DRAM-aware page allocator that places more frequently-accessed pages into lower-latency regions in DRAM is also discusses.

It is promising to understand and exploit the inherent latency variation within modern DRAM chips, which will improve system performance and perhaps reliability.

## Strengths

- Concise and easy to understand experimental pseudocode.
- Push process forward by questions, making the main line clearer.
- Targeted and selective experiments. The paper first carries out some preliminary experiments to find the suitable and efficient experimental parameter range, and then continue experiments only in this range, avoiding invalid experiments and making the experiment more efficient.
- Show the chart selectively, making the conclusion more obvious and the paper more concise.

## Weaknesses

- Only carried out experiments on old DDR3 chips.
- Unrealistic mechanism.
  - It must take a huge amount of time to investigate the latency variation in a DRAM chip, for it takes 1300 hours in similar experiments.
  - No latency margin, unrealistic. After all, most of “waste latency” in contemporary DRAM are because of margin.
  - Looking up and applying latency for every request also induces latencies.
- Flawed simulation, evading the crucial point. Didn’t discuss the overhead for looking up and applying latency for every request.

## Thoughts
- 

## Takeaways and questions

This paper is a good example in doing an effective and comprehensive experiment. First, clarify motivation and goal of the experiment. Second, decide the experimental methodology according to your goal. Third, establish a universal and effective experimental platform. Fourth, determine experimental parameters. Fifth, do preliminery experiments to narrow the scope of experimental conditions. Sixth, carry out main experiments, and modify the experimental condition dynamically according to observations in progress. Seventh, collect observations, explain them and make conclusions. Finally, explore more about your observations(if the experiments cost much you’d better do that to make it more valuable) and discuss some interesting/unexpected observations in experiments.

The paper also disappoints me for it carried out comprehensive and costly experiments that few lab can do, but only drew some simple and superficial conclusions. It's like a richman spending a lot of money on worthless things.

Here I have some questions.

- Is it realistic to try to propose a physical model to describe the interference of RowHammer to each DRAM cell precisely? There are lots of work on RowHammer but few of them involve this aspect, why?
- How about the scalability of system-level mitigation mechanisms with the increasing RowHammer vulnerability? If it’s more scalable(I think so intuitively) it’s also a good idea to develop mitigations in this direction.

