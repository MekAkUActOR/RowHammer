# Haowen Liu (2020-10-14)

# Paper information
- Title: Flipping Bits in Memory Without Accessing Them: An Experimental Study of DRAM Disturbance Errors
- Authors: Yoongu Kim, Ross Daly, Jeremie Kim, Chris Fallin, Ji Hye Lee, Donghyuk Lee, Chris Wilkerson, Konrad Lai, Onur Mutlu
- Venue: ISCA 2014
- Keyword: DRAM vulnerability, disturbance error characterization, disturbance error mitigation

# Paper content
## Summary
This paper was the first to expose the phenomenon of ***Disturbance Errors***, a kind of *bit-flips*, in modern commodity DRAM and other memory/storage technologies. This phenomenon has become more and more obvious with the DRAM process technology scaling down to smaller dimensions. Malicious attackers can exploit it to break the *memory isolation* *security principle* to cast great threat on computer system.

Disturbance Error is mainly caused by voltage fluctuations on a wordline, which injects noise into an adjacent wordline through electromagnetic coupling, bridges, and/or hot-carrier injection. *Repeated toggling of a DRAM row’s wordline* stresses inter-cell coupling effects that accelerate charge leakage from nearby rows. When such a cell **loses too much charge** in a *refresh* interval, it experiences a disturbance error.

This paper carried out detailed experiments to explore the **properties** of disturbance errors in all aspects. Here are some important results.

- DRAM disturbance errors are caused by **the repeated opening/closing of a row**, *not* by column reads or writes.
- Disturbance Errors are widespread across DRAM types and vendors.
- Access Pattern Dependence: the shorter RI (refresh interval), the fewer errors; the longer AI (activation interval), the fewer errors; the fewer activations (RI/AI), the fewer errors.
- Address Correlation: an aggressor and its victims are likely to have consecutive row-addresses.
- Data Pattern Dependence: despite rare exceptions, every other victim cell (cell experiencing bit flip) had an error in **just a single preferred direction** (leakage direction). For example, one cell can only be flipped from $1$ to $0$ but cannot reverse.
- Sensitivity Results: errors are mostly **repeatable**; victim cells $\ne$ weak cells; not strongly affected by temperature (but affected).

This paper also proposed some mitigations against disturbance error and discuss their feasibility. The most recommended one is ***PARA*** (probability-triggering-refresh mechanism). *Every time a row is opened and closed, one of its adjacent rows is also opened (i.e., refreshed) with some low probability.*

A user-level program can cause disturbance errors on pages belonging to other programs by simply generating many DRAM accesses. Because the DRAM process technology is still scaling down to smaller dimensions, this phenomenon is becoming so widespread that it have cast tremendous thread on security of lots of hardwares and softwares.

## Strengths
- Carried out detailed experiments on a broad range of real commodity DRAM in rigorous variable-controlling approach. Persuasive.
- Built an efficient experimental platform.

## Weaknesses
- For PARA method, it's inconvenient to find the appropriate refresh probability, since it's a constant that needs to be carefully constructed. And for the same reason it's hard to scale with the increasing disturbance error vulnerability of DRAM (not universal).

## Paper presentation
Explored ***properties*** of disturbance errors in all aspects, in a very short length (***concise writing***). 

## Thoughts
- The aging of DRAM cells may change the physical property of capacitor and transistor, influence the charging and leaking process, and finally affect the vulnerability of cells to disturbance errors. And this property may be exploited by malicious attackers.
- To exploit RowHammer, a thorough understanding of operating system and program behavior is necessary.

## Takeaways and questions
There may be a truth — the simpler and more basic (generally come from principles directly), the more widespread, and the more valuable. Actually, there has been already a ***shift of mindset*** among mainstream security researchers: ***general-purpose hardware is fallible (in a very widespread manner) and its problems are actually exploitable***. We should pay more attention to hardware failures and mechanism defects.

Here I have several questions.

- If a DRAM row/bit is more usually used (times of access, charging state time, etc), is it more vulnerable to RowHammer? Or the less used ones are more vulnerable? Are there researches on the correlation between DRAM age and RowHammer-vulnerability?
- What results in victim cell faults in 2 directions?
- Is the probability-triggering mechanism of ***PARA*** a common method in security research?
- How to exploit disturbance error in reality attack? Though the phenomenon seems very dangerous, maybe it's hard to exploit, because of the complex mechanisms of memory allocation.
- Can disturbance error be used in a less aggressive way, not as a method of attack, but as a technique in a security scenario like *Physical Unclonable Function*, or other field?

## Q&As
**Q1:** Is "Disturbance Error" a proper noun?

**A1:** Yes, “disturbance error" is a proper noun in this paper, and the word "RowHammer” was proposed and defined by later works.

**Q2:** In third para of summary, does RI have anything to do with "Access Pattern"?

**A2:** Refresh interval affects the number of possible activations, thus affects access pattern if the activation interval is fixed.
