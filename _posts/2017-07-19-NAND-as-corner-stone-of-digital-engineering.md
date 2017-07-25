---
layout: post
title: NAND as a corner stone of digital engineering
date: 2017-07-19
---

<center><img align="center" src="/images/nand-gate.png" width="250"></center>
NAND logic gate can be considered starting point in digital electronics. Every other logic element can be built using NAND gates only.

NAND (Negative-AND) returns False only if all inputs are True.


#### Truth Table for NAND
<table>
	<tr>
		<th class="truth-table">a</th>
		<th class="truth-table">b</th>
		<th class="truth-table">out</th>
	</tr>
	<tr>
		<td class="truth-table">0</td>
		<td class="truth-table">0</td>
		<td class="truth-table">1</td>
	</tr>
	<tr>
		<td class="truth-table">1</td>
		<td class="truth-table">0</td>
		<td class="truth-table">1</td>
	</tr>
	<tr>
		<td class="truth-table">0</td>
		<td class="truth-table">1</td>
		<td class="truth-table">1</td>
	</tr>
	<tr>
		<td class="truth-table">1</td>
		<td class="truth-table">1</td>
		<td class="truth-table">0</td>
	</tr>
</table>

## Building basic logic gates from NAND

### NOT
NOT gate inverts input signal. It will return False when the input is True. To build NOT gate from NAND, we need to connect the same signal source to both NAND input pins.

<center>
	<table>
		<tr>
			<th>Boolean Definition</th>
			<th colspan="2">Truth Table</th>
			<th>HDL Code</th>
		</tr>
		<tr>
			<td rowspan="3">
				a NAND a
			</td>
			<th class="truth-table">in</th><th class="truth-table">out</th>
			<td rowspan="3">
			<pre>
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    Nand (a=in, b=in, out=out);
}
			</pre>				
			</td>
		</tr>
		<tr><td class="truth-table">0</td><td class="truth-table">1</td></tr>
		<tr><td class="truth-table">1</td><td class="truth-table">0</td></tr>
	</table>
</center>

### AND
AND gate is opposite to NAND. Connect NAND to NOT - profit!
<center>
	<table>
		<tr>
			<th>Boolean Definition</th>
			<th colspan="3">Truth Table</th>
			<th>HDL Code</th>
		</tr>
		<tr>
			<td rowspan="5">
				NOT(a NAND b)
			</td>
			<th class="truth-table">a</th><th class="truth-table">b</th><th class="truth-table">out</th>
			<td rowspan="5">
			<pre>
CHIP And {
    IN a, b;
    OUT out;

    PARTS:
    Nand(a=a, b=b, out=nandOut);
    Not(in=nandOut, out=out);
}
			</pre>				
			</td>
		</tr>
		<tr><td class="truth-table">0</td><td class="truth-table">0</td><td class="truth-table">0</td></tr>
		<tr><td class="truth-table">1</td><td class="truth-table">0</td><td class="truth-table">0</td></tr>
		<tr><td class="truth-table">0</td><td class="truth-table">1</td><td class="truth-table">0</td></tr>
		<tr><td class="truth-table">1</td><td class="truth-table">1</td><td class="truth-table">1</td></tr>
	</table>
</center> 

### OR
OR will return True if at least one of inputs is True.

According to De Morgan's Theorem:
NOT(a) AND NOT(b) = NOT(a OR b)
<center>
	<table>
		<tr>
			<th>Boolean Definition</th>
			<th colspan="3">Truth Table</th>
			<th>HDL Code</th>
		</tr>
		<tr>
			<td rowspan="5">
				NOT(NOT(a) AND NOT(b))
			</td>
			<th class="truth-table">a</th><th class="truth-table">b</th><th class="truth-table">out</th>
			<td rowspan="5">
			<pre>
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
    Not(in=a, out=notA);
    Not(in=b, out=notB);
    And(a=notA, b=notB, out=andNotANotB);
    Not(in=andNotANotB, out=out);
}
			</pre>				
			</td>
		</tr>
		<tr><td class="truth-table">0</td><td class="truth-table">0</td><td class="truth-table">0</td></tr>
		<tr><td class="truth-table">1</td><td class="truth-table">0</td><td class="truth-table">1</td></tr>
		<tr><td class="truth-table">0</td><td class="truth-table">1</td><td class="truth-table">1</td></tr>
		<tr><td class="truth-table">1</td><td class="truth-table">1</td><td class="truth-table">1</td></tr>
	</table>
</center>

#### Gates Diagrams
<img src="/images/not-and-or-from-nand.png">

**With NOT, AND, OR on the table we can build literally any logic element.**
