# Haowen Liu (2020-10-14)

# Paper information

- Title: Flipping Bits in Memory Without Accessing Them: An Experimental Study of DRAM Disturbance Errors
- Authors: Yoongu Kim, Ross Daly, Jeremie Kim, Chris Fallin, Ji Hye Lee, Donghyuk Lee, Chris Wilkerson, Konrad Lai, Onur Mutlu
- Venue: ISCA 2014
- Keyword: DRAM vulnerability, disturbance error characterization, disturbance error mitigation

# Paper content

## Summary

This paper was the first to expose the phenomenon of ***Disturbance Errors***, a kind of unexpected *bit-flips*, in modern commodity DRAM and other memory/storage tochnologies. This phenomenon has become more and more obvious with the DRAM process technology scaling down to smaller dimensions. Malicious attackers can exploit it to break the ***memory isolation*** ***security principle*** to cast great threat on computer system.

Disturbance Error is mainly caused by voltage fluctuations on an wordline, which injects noise into an adjacent wordline through **electromagnetic coupling**, **bridges**, and/or **hot-carrier injection**. *Repeated toggling of a DRAM row’s wordline* stresses inter-cell coupling effects that accelerate charge leakage from nearby rows. Such a cell **loses too much charge** before *refresh*, it experiences a disturbance error.

This paper carried out detailed experiments to explore the **properties** of disturbance errors in all aspects. Here are some important results.

- DRAM disturbance errors are caused by **the repeated opening/closing of a row**, *not* by column reads or writes.
- Disturbance Errors are widespread.
- **Access Pattern Dependence**: the shorter RI (refresh interval), the fewer errors; the longer AI (activation interval), the fewer errors; the fewer activations (RI/AI), the fewer errors.
- **Address Correlation**: an aggressor and its victims are likely to have consecutive row-addresses.
- **Data Pattern Dependence**: Except for rare exceptions, every other victim cell had an error in **just a single preferred direction** (**leakage**).
- Sensitivity Results: Errors are Mostly **Repeatable**; Victim Cells $\ne$ Weak Cells; Not Strongly Affected by Temperature (but affected).

This paper also proposed some mitigations against disturbance error and discuss their feasibility. The most recommended one is ***PARA*** (probability-triggering-refresh mechanism).

A user-level program can cause disturbance errors on pages belonging to other programs by simply generating many DRAM accesses. Because the DRAM process technology is still scaling down to smaller dimensions, this phenomenon is becoming so widespread that it’ve cast tremendous thread on security of lots of hardwares and softwares.

## Strengths

- Carried out detailed experiments on a broad range of real commodity DRAM in rigorous variable-controlling approach. Persuasive.
- Built an efficient experimental platform.

## Weaknesses

- For PARA method, it's inconvenient to find the appropriate refresh probability, since it's a constant that needs to be carefully constructed. And for the same reason it's hard to scale with the increasing disturbance error vulnerability of DRAM (not universal).

## Paper presentation

Explored ***properties*** of disturbance errors in all aspects, in a very short length (***concise writing***). 

## Thoughts

- Design and carry out experiments in variable-controlling approach to explore the correlation between DRAM age and disturbance-error-vulnerability. The aging of DRAM cells may change the physical property of capacitor, influencing the charging and leaking process. And this property may be exploited by malicious attckers. So we can also do some researches on system program behavior (frequent access to a physical address results in accelerated aging, and more vulnerable to RowHammer).
- Explore the physical cause of disturbance error, build a physical model to describe it and work out the mitigation methodology from circuit level based on the conclusion.
- To exploit RowHammer, a thorough understanding of operating system and program behavior is necessary.

## Takeaways and questions

There may be a truth — the simpler and more basic (generally come from principles directly), the more widespread, and the more valuable. Actually, there has been already a ***shift of mindset*** among mainstream security researchers: ***general-purpose hardware is fallible (in a very widespread manner) and its problems are actually exploitable***. We should pay more attention to hardware failures and mechanism defects.

Here I have some questions.

- If a DRAM row/bit is more usually used (times of access, charging state time, etc), is it more vulnerable to RowHammer? Or the less used ones are more vulnerable? Are there researches on the correlation between DRAM age and RowHammer-vulnerability?
- What results in victim cell faults in 2 directions?
- The probability-triggering mechanism of ***PARA*** might be inspired by ***CSMA/CD***, or actually it’s a common method in security research?
- How to exploit disturbance error in reality attack? Though the phenomenon seems very dangerous, maybe it's hard to exploit, because of the complex mechanisms of memory allocation.
- Can disturbance error be used in a less agressive way, not as a method of attack, but as a technique in a security mechanism or other field?