---
layout: post
title: NAND Logic Gate as a corner stone of digital engineering
date: 2017-07-19
---

NAND logic gate can be considered starting point in digital electronics. Every other logic element can be built using NAND gates only.

NAND (Negative-AND) returns False only if all inputs are True. On diagrams, it is depicted as shown below.

<img src="/images/nand-gate.png" width="250">

#### Truth Table for NAND

a | b | out
--- | --- | ---
0 | 0 | 1
1 | 0 | 1 
0 | 1 | 1 
1 | 1 | 0 

## Let's see how basic logic gates can be built from NAND.

### NOT
NOT gate inverts input signal. It will return False when the input is True. To build NOT gate from NAND, we need to connect the same signal source to both NAND input pins.
NOT(a) is equal to (a NAND a).
#### Truth Table for NOT

in | out 
--- | ---
0 | 1
1 | 0

### AND
AND gate is opposite to NAND. Connect NAND to NOT - profit!

#### Truth Table for AND

a | b | out
--- | --- | ---
0 | 0 | 0
1 | 0 | 0 
0 | 1 | 0 
1 | 1 | 1 

### OR
OR will return True if at least one of inputs is True.
According to De Morgan's Law, (NOT(a) AND NOT(b)) is equivalent to (NOT(a OR b)). With this is mind we can build a diagram.

#### Truth Table for OR

a | b | out
--- | --- | ---
0 | 0 | 0
1 | 0 | 1 
0 | 1 | 1 
1 | 1 | 1 

#### Gates Diagrams
<img src="/images/not-and-or-from-nand.png">

**With NOT, AND, OR on the table we can build literally any logic element.**
