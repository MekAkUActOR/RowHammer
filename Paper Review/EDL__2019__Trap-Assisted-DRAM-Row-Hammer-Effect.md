# Haowen Liu (2020-11-22)

# Paper information

- Title: Trap-Assisted DRAM Row Hammer Effect
- Authors: Thomas Yang and Xi-Wei Lin
- Venue: EDL 2019
- Keywords: RowHammer Effect, TCAD simulations, charge capture and emission, carrier mitigation

# Paper content

## Summary

This paper conducted 3D TCAD **simulations** to explore the fundamental mechanism of RowHammer effect in DRAM at the level of semiconductor and circuit. They constructed a 3D DRAM structure consisting of two cells with saddle-fin, buried metal word-line, sharing the same active area and a common bit-line, with dimensions close to a typical 2y nm node. The paper placed single trap at different positions in their TCAD model to find the different RowHammer effects at different positions, through which the paper explained the mechanism of trap-assisted RowHammer effect. Conclusions are as follows:

- The mechanism of trap-assisted row hammer effect consists of two processes: a) trap charge capture and emission and b) subsequent carrier migration towards victim node to discharge its capacitance.
- A deeper bit-line junction mitigates row hammer effect, since it intercepts more electron carriers during migration.
- Trap-assisted row hammer effect reaches maximum around room temperature and decreases at either lower or elevated temperature.
- Feature size scaling aggravates the row hammer effect.


## Strengths

- Provide a direct evidence to the fundamental mechanism of trap-assisted row hammer effect and offers coherent explanations to many previous experiments.
- Explain the process of trap charge capture and emission clearly by a trapped charge density heat map changing with time points combined with potential waveform of victim node.

## Weaknesses

- Only explored ***trap-assisted*** row hammer effect, but did not explain why row hammer effect is trap-assisted clearly.
- Results can only explain trap-assisted row hammer effect happened in a data pattern that the victim cell and the aggressor cell have different value.
- Did not explain the non-monotonic behavior with temperature clearly.

## Paper presentation

- Blurry pictures.
- The structure of the article is somewhat illogical.

## Thoughts
- Design simulations to explain trap-assisted row hammer effect when the victim cell and the aggressor cell have the same value, by placing single trap at more positions. Actually there are four data-pairs to explore: 1/1, 0/0, 0/1, 1/0.

## Takeaways and questions

This paper is not easy to understand for me, since I don't quite know the terminologies of semiconductor research and the process of TCAD simulation. I had to read the paper for several times and search for relative explanations and finally catched the main line. To avoid forget it I record some important points here.

***TCAD*** is a branch of electronic design automation that models semiconductor fabrication and semiconductor device operation. The modeling of the fabrication is termed Process TCAD, while the modeling of the device operation is termed Device TCAD.

***Trap-Assisted*** is one of the main ideas of this paper. A “trap” is a defect in a semiconductor which creates a “deep” energy well where charge carriers (electrons or holes) can become “trapped”, either temporarily or permanently. This causes a charge to appear in the material which can alter its electrical properties.

Previous RowHammer papers I’ve read all describe RowHammer at the level of electronic element, so I just thought RowHammer as a circuit level defect. But this paper shows me that RowHammer is a complex microelectronic process. The 3D TCAD model leaves a deep impression on me, and now I have a clear understanding of DRAM cells’ underlying structure.
