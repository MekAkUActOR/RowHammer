# Haowen Liu  2020-11-23

# Paper information

- Title: Experiments and Root Cause Analysis for Active-Precharge Hammering Fault in DDR3 SDRAM under 3 $\times$ nm Technology
- Authors: Kyungbae Park, Chulseung Lim, Donghyuk Yun, Sanghyeon Baeg
- Venue: MR 2016
- Keywords: failure mechanism of RowHammer, SPICE simulation, TCAD device model, DDR3 SDRAM

# Paper content

## Summary

To characterize the failure mechanism of RowHammer at the level of semiconductor, the paper carried out SPICE simulation on a TCAD device model of two DRAM adjasent cells. They first controled retention time and hammering times respectively, and investigated the correlation between $t_{RP}$ and hammering threshold to demonstrate accelerated discharging by hammering. Then they collected statistics of electron density, energy band and electrostatic potential at different part of a TCAD device model when hammering in a SPICE simulation to explain the mechanism of RowHammer. Finally they conducted experiments on a discrete component from three manufacturers with same 3 $\times$ nm technology to characterize `ACT` time parameter dependency, data pattern dependency, temperature dependency of RowHammer effect. 

Conclusions are as follows:

- AP hammering causes bit-flips by accelerating discharging. Hammer effect increases with increasing $t_{RP}$ in an exponential manner. 
- RowHammer fault is primarily caused by the charge recombination of the victim cell with the charges in the channel of the neighborhood cell.
- Leakage of the victim cell decreases with the increasing of **doping concentration**, geometrical distance between the aggressor and the victim cells. Data pattern $P_{0/1}$ is much vulnarable than $P_{1/1}$.


## Strengths

- Use a combination of SPICE simulation on TCAD model and experiments on real DRAM component to characterize RowHammer at the macro and micro levels.
- Explore the failure mechanism of RowHammer at the level of semiconductor, which is helpful to understand it at the micro level.
- Characterize the correlation between row hammer effect and temperature, and the effect of  `ACT` time parameter change on row hammer effect. A good supplement to the first paper (2014).
- Use row hammer threshold as the quantitative representation of row hammer effect, easy to measure and understand.

## Weaknesses

- RowHammer characterization at the macro and micro levels were split. There is no straight relationship between macro characterizations and micro characterizations.
- Only characterized row hammer effect on two data patterns, which is not comprehensive enough. And did not explain the mechanism of row hammer happening in the case of data pattern $P_{1/1}$ clearly.
- $t_{dead}$  in  Section 4.2 is not necessary, the varialble supposed to be controled is the number of hammering, not experimental time.

## Thoughts
- It seems that probability-triggering-refresh mechanism (PARA) is the only and the most efficient way to mitigate row hammer effect, due to RowHammer’s inevitable micro mechanism shown in this paper. 

## Takeaways and questions

