# Haowen Liu (2020-10-22)

# Paper information
- Title: Understanding Latency Variation in Modern DRAM Chips: Experimental Characterization, Analysis, and Optimization
- Authors: Kevin K. Chang, Abhijith Kashyap, Hasan Hassan, Saugata Ghose, Kevin Hsieh, Donghyuk Lee, Tianshi Li, Gennady Pekhimenko, Samira Khan, Onur Mutlu
- Venue: SIGMETRICS 2016
- Keywords: DRAM performance, latency variation, memory performance optimization

# Paper content
## Summary
This paper experimentally characterizes and understands the latency variation across cells within a DRAM chip for three fundamental DRAM operations (`activation`, `precharge`, `restoration`), and develops new mechanisms that exploit the latency variation to reliably improve performance.

The experiments are conducted across a total of 30 DDR3 DRAM modules comprising 240 DRAM chips from the three major DRAM vendors, on an FPGA-based DRAM testing infrastructure. Here are some important conclusions.

- There is spatial locality in inherently slower cells: such cells are clustered in certain regions of a DRAM chip.
- The stored data pattern in cells affects access latency variation. Errors caused by reduced activation latency are dependent on the stored data pattern. Reading bit 1 is significantly more reliable than bit 0 at reduced activation latencies.
- Each latency (latency of each DRAM operation) exhibits a different level of impact on the inherently-slower cells. With the same amount of latency reduction, the number of precharge errors is significantly higher than the number of activation errors. And restoration latency ($t_{RAS}$) in modern DIMMs can be reduced without introducing any errors.
- Most of the erroneous cache lines at reduced latencies have a single-bit error, with only a small fraction of cache lines experiencing more than one bit flip.
- ECC is an effective mechanism to correct activation errors at reduced latencies, even in modules with a large fraction of erroneous cache lines.

Therefore, the paper proposes _FLY-DRAM_ (**F**lexible-**L**atenc**Y** **DRAM**) to exploit these findings to improve performance: (i) categorize the DRAM cells into fast and slow regions, (ii) expose this information to the memory controller, (iii) access the fast regions with a lower latency. The simulation-based analysis on the mechanism shows a good result. FLY-DRAM improves system performance significantly, by 17.6%, 13.3%, and 19.5% on average across all 40 workloads for the three real DIMMs. An idea of a DRAM-aware page allocator that places more frequently-accessed pages into lower-latency regions in DRAM is also discussed.

It is promising to understand and exploit the inherent latency variation within modern DRAM chips, which will improve system performance and perhaps reliability.

## Strengths
- This paper characterize the latency variation and possible latency reduction for three fundamental DRAM operations across cells within a DRAM chip.
- Targeted and selective experiments. The paper first carries out some preliminary experiments to find the suitable and efficient experimental parameter range, and then continue experiments only in this range, avoiding invalid experiments and making the experiment more efficient.

## Weaknesses
- Only conducted experiments on old DDR3 chips.
- Unrealistic mechanism.
  - It must take a huge amount of time to investigate the latency variation in a DRAM chip, for it took more than 2500 testing hours in its experiments of FPGA test for only activation latency to test 30 DRAM modules, for it has to test every row in every DRAM chip with a bunch of data like different data pattern and different latency parameters with many rounds. And latency variations in different chips are distinctive, so one should do the investigation on every chip. And the latency variation in one chip may change with time, making it more difficult.
  - No latency margin, unreliable. After all, most of “waste latency” in contemporary DRAM are because of margin.
  - Looking up latencies for every request also induces latencies.
- Flawed simulation, evading the crucial point. Didn’t discuss the overhead of looking up latency for every request, which will induce most overhead because the latency looking-up list is very big containing every row in the DRAM.

## Paper presentation
- Concise and easy to understand experimental pseudocode.
- Push process forward by questions, making the main line clearer.
- Show the chart selectively, making the conclusion more obvious and the paper more concise.

## Thoughts
- There may be correlation between latency variation and DRAM generation. So we can carry out similar experiments on across different generations to characterize the correlation.
- The latency variation in one chip may change with time because the physical properties of each cell may change, or the latency variation patterns are unstable and unrepeatable, so experiments should also be carried out to characterize it.

## Takeaways and questions
It’s not a new idea to characterize and exploit latency variation in DRAM, and the proposed mechanism is also not outstanding. The advantage of this paper is it has the resources and time to carry out the experiments.

The most important knowledge I learn is the DRAM accessing mechanism: (i) Row Activation & Sense Amplification: opening a row to transfer its data to the row buffer, (ii) Read/Write: accessing the target column in the row buffer, and (iii) Precharge: closing the row and the row buffer.

Here I have some questions.

- Why use WS (weighted speedup) to evaluate the performance in simulation? Is this the convention of computer architecture research?
- Does newer DRAM chip have longer latency?

## Q&As
**Q1:** In the third conclusion in Summary, where did the cache come into play? I thought the evaluations are for DRAM?

**A1:** "Cache line" here is a quantifier indicating a data size in memory. Once a row is activated, the memory controller can read from or write to it one **cache line** at a time. In a typical DRAM, a column read command reads out 8 data beats (also called an 8-beat burst), thus reading a complete 64-byte cache line (a data beat is a 64-bit data).