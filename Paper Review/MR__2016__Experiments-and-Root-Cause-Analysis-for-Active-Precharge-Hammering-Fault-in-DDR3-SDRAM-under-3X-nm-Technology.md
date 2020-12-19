# Haowen Liu (2020-11-25)

# Paper information

- Title: Experiments and Root Cause Analysis for Active-Precharge Hammering Fault in DDR3 SDRAM under 3 $\times$ nm Technology
- Authors: Kyungbae Park, Chulseung Lim, Donghyuk Yun, Sanghyeon Baeg
- Venue: MR 2016
- Keywords: failure mechanism of RowHammer, SPICE simulation, TCAD device model, DDR3 SDRAM

# Paper content

## Summary

To characterize the failure mechanism of RowHammer at the semiconductor level, the paper carried out SPICE simulation on a TCAD device model of two DRAM adjacent cells. They first controlled retention time and number of hammering respectively, and investigated the correlation between $t_{RP}$ (precharge latency parameter) and hammering effect to demonstrate accelerated discharging by hammering. Then they collected statistics of electron density, energy band and electrostatic potential in different part of a TCAD device model when hammering to explain the micro mechanism of RowHammer. Finally they conducted experiments on a discrete component from three manufacturers with same 3 $\times$ nm technology to characterize `ACT` time parameter dependency, data pattern dependency and temperature dependency of RowHammer effect. 

Conclusions are as follows:

- Row hammer causes bit-flips by accelerating discharging. Hammer effect increases with increasing $t_{RP}$. 
- RowHammer fault is primarily caused by the charge recombination (electrons and charges combine to electrical neutrality) of the victim cell with the charges in the channel of the neighborhood cell.
- Leakage of the victim cell decreases with the increasing of **doping concentration**, geometrical distance between the aggressor and the victim cells. Data pattern $P_{0/1}$ is much vulnerable than $P_{1/1}$ ($P_{a/b}$ means the left cell stores value $a$ and the right cell stores the value $b$). 


## Strengths

- Use a combination of SPICE simulation on TCAD model and experiments on real DRAM component to characterize RowHammer at the micro (semiconductor level cause) and macro (circuit level vulnerability) levels.
- Explore the failure mechanism of RowHammer at the level of semiconductor, which is helpful to understand it at the micro level.
- Characterize the correlation between row hammer effect and temperature, and the effect of  `ACT` time parameters (three time parameters of DRAM `ACT` command: activation, restoration and precharge) change on row hammer effect. A good supplement to the first paper (2014).
- Use hammering threshold as the quantitative representation of row hammer effect, easy to measure and understand.

## Weaknesses

- RowHammer characterizations at the macro and micro levels were split. There is no straight relationship between macro characterization and micro characterization.
- Only characterized row hammer effect on two data patterns, which is not comprehensive enough. And did not explain the mechanism of row hammer happening in the case of data pattern $P_{1/1}$ clearly.
- $t_{dead}$ (an extra time parameter to fulfill a testing cycle with no action)  in  Section 4.2 is not necessary. The variable supposed to be controlled is the number of hammering, not experimental time.

## Paper presentation

- Figures are unnecessarily big and a bit blurry.

## Thoughts
- It seems that probability-triggering-refresh mechanism (PARA) is the most ideal mitigation, due to RowHammer’s inevitable micro mechanism shown in this paper. 
- An ideal mitigation should be stateless (do not need to track and store the number of activations or the state of every row) and won’t change the basic structure of DRAM and system running mechanism.
- Mitigation maybe lie in cache mechanism. 

## Takeaways and questions

- SPICE simulation and TCAD device model are often used in semiconductor device investigation and demonstration.
- RowHammer fault is primarily caused by the charge recombination of the victim cell with the charges in the channel of the neighborhood cell.