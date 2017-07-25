---
layout: post
title: From NAND to ALU in 4 steps
date: 2017-07-24
---
<center><img alt="" src="/images/ALU-diagram.png" width="250" /></center>

<p>In&nbsp;<a href="https://iiezhachenko.github.io/NAND-as-corner-stone-of-digital-engineering/">NAND as a corner stone of digital engineering</a>&nbsp;post, I wrote about NAND&#39;s role. Let&#39;s dive a bit deeper into the topic and see how simplified ALU can be built&nbsp;literally from NAND gates.</p>

<p>That post inspired by&nbsp;<a href="https://www.coursera.org/learn/build-a-computer">NAND to Tetris</a>&nbsp;course on Coursera. I took code snippets and chips specifications from practical assignments accomplished by me during the course. You can find HDL testing tool can <a href="http://nand2tetris.org/software.php">here</a>. Download and unzip the archive, then start &quot;tools\HardwareSimulator&quot;. HDL source files and associated tests for HardwareSimulator are available in my&nbsp;<a href="https://github.com/iiezhachenko/cs-study/tree/master/core-cs/nand2tetris">GitHub repo</a>.</p>

<h2 name="objective">Objective</h2>

<p>We are going to build simple ALU having only NAND logic gate as a starting point. Step-by-step we will create new chips and use them as building blocks for more sophisticated chips.</p>

<h3 name="alu-spec">ALU Specification</h3>

<center><img alt="" src="/images/ALU-diagram.png" width="500" /></center>

<table cellpadding="5px" style="margin-left: auto; margin-right: auto; width: 340px; margin-top: 30px;">
	<caption>ALU Chip Interface</caption>
	<tbody>
		<tr style="height: 21px;">
			<th style="height: 21px; width: 25.5px;">Pin</th>
			<th style="height: 21px; width: 70.5px;">Bus Width</th>
			<th style="height: 21px; width: 243px;">Function</th>
		</tr>
		<tr style="height: 21px;">
			<th colspan="3" style="height: 21px; width: 339px;">Input</th>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">x</td>
			<td style="height: 21px; width: 70.5px;">16 bits</td>
			<td style="height: 21px; width: 243px;">Input</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">y</td>
			<td style="height: 21px; width: 70.5px;">16 bits</td>
			<td style="height: 21px; width: 243px;">Input</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">zx</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Zero the X input if 1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">nx</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Negate the X input if 1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">zy</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Zero the Y input if 1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">ny</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Negate the Y input if 1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">f</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Compute out = x + y (if 1) or x &amp; y (if 0)</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">no</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">Negate the out output if 1</td>
		</tr>
		<tr style="height: 21px;">
			<th colspan="3" style="height: 21px; width: 25.5px;">Output</th>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">out</td>
			<td style="height: 21px; width: 70.5px;">16 bits</td>
			<td style="height: 21px; width: 243px;">Output</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">zr</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">1 if (out == 0), 0 otherwise</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px; width: 25.5px;">ng</td>
			<td style="height: 21px; width: 70.5px;">1 bit</td>
			<td style="height: 21px; width: 243px;">1 if (out &lt; 0), 0 otherwise</td>
		</tr>
	</tbody>
</table>

<table cellpadding="5px" style="margin-left: auto; margin-right: auto; width: 340px; margin-top: 30px;">
	<caption>ALU Chip Specification</caption>
	<tbody>
		<tr style="height: 21px;">
			<th style="height: 21px;">zx</th>
			<th style="height: 21px;">nx</th>
			<th style="height: 21px;">zy</th>
			<th style="height: 21px;">ny</th>
			<th style="height: 21px;">f</th>
			<th style="height: 21px;">no</th>
			<th style="height: 21px;">out=</th>
		</tr>
		<tr style="height: 21px;">
			<th style="height: 21px;">if zx then x=0</th>
			<th style="height: 21px;">if nx then x=!x</th>
			<th style="height: 21px;">if zy then y=0</th>
			<th style="height: 21px;">if ny then y=!y</th>
			<th style="height: 21px;">if f then out=x+y else out=x&amp;y</th>
			<th style="height: 21px;">if no then out=!out</th>
			<th style="height: 21px;">f(x,y)=</th>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">-1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">x</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">!x</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">!y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">-x</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">-y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">x+1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">y+1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">x-1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">y-1</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">x+y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">x-y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">y-x</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">x&amp;y</td>
		</tr>
		<tr style="height: 21px;">
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">0</td>
			<td style="height: 21px;">1</td>
			<td style="height: 21px;">x|y</td>
		</tr>
	</tbody>
</table>

<h2>Stage I. Building the foundation.</h2>

<p>Let&#39;s start with building base logic gates from NAND. These gates are: NOT, AND, OR, XOR, and Multiplexor (MUX).</p>

<ul>
	<li>NOT - Accepts one input and returns the negated value.</li>
	<li>AND - Accepts two input values and returns 1 only if both arguments are 1.</li>
	<li>OR -&nbsp;Accepts two input values and returns 1 if at least one argument is 1.</li>
	<li>XOR -&nbsp;Accepts two input values and returns 1 when only one argument&nbsp;is&nbsp;1.</li>
	<li>MUX- Accepts three inputs (two values and one selector). Returns first value if the selector is 0, returns second value otherwise.</li>
</ul>

<h3>Building NOT</h3>

<p>The&nbsp;NOT gate is quite simple. We know&nbsp;that NAND returns 0 only if both inputs are 1. If we send the same signal to both inputs simultaneously, we will get 0 when sent 1 and 1 when sent 0.</p>

<p>NOT&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* Not gate:<br />
&nbsp;* out = not in<br />
&nbsp;*/</code></p>

<p><code>CHIP Not {<br />
&nbsp; &nbsp; IN in;<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Nand (a=in, b=in, out=out);<br />
}</code></p>

<h3>Building AND</h3>

<p>NAND is equivalent to negated AND.</p>

<p>AND&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* And gate:&nbsp;<br />
&nbsp;* out = 1 if (a == 1 and b == 1)<br />
&nbsp;* &nbsp; &nbsp; &nbsp; 0 otherwise<br />
&nbsp;*/</code></p>

<p><code>CHIP And {<br />
&nbsp; &nbsp; IN a, b;<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Nand(a=a, b=b, out=nandOut);<br />
&nbsp; &nbsp; Not(in=nandOut, out=out);<br />
}</code></p>

<h3>Building OR</h3>

<p>To build OR let&#39;s refer to De Morgan&#39;s theorem. It says that:</p>

<ul>
	<li>NOT(x AND y) = NOT(x) OR NOT(y)</li>
	<li>NOT(x OR y) = NOT(x) AND NOT(y)</li>
</ul>

<p>We already have NOT and AND gates. Now we can create OR gate.</p>

<p>OR&#39;s HDL Will be:</p>

<p><code>&nbsp;/**<br />
&nbsp;* Or gate:<br />
&nbsp;* out = 1 if (a == 1 or b == 1)<br />
&nbsp;* &nbsp; &nbsp; &nbsp; 0 otherwise<br />
&nbsp;*/</code></p>

<p><code>CHIP Or {<br />
&nbsp; &nbsp; IN a, b;<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Not(in=a, out=notA);<br />
&nbsp; &nbsp; Not(in=b, out=notB);<br />
&nbsp; &nbsp; And(a=notA, b=notB, out=andNotANotB);<br />
&nbsp; &nbsp; Not(in=andNotANotB, out=out);<br />
}</code></p>

<h3>Building XOR</h3>

<p>XOR is a bit trickier than OR. It should return 0&nbsp;when both inputs have the same value. We can create it using NOT, AND, OR chips we built previously.</p>

<p>XOR&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* Exclusive-or gate:<br />
&nbsp;* out = not (a == b)<br />
&nbsp;*/</code></p>

<p><code>CHIP Xor {<br />
&nbsp; &nbsp; IN a, b;<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Not(in=a, out=notA);<br />
&nbsp; &nbsp; Not(in=b, out=notB);<br />
&nbsp; &nbsp; And(a=notA, b=b, out=andNotAB);<br />
&nbsp; &nbsp; And(a=a, b=notB, out=andANotB);<br />
&nbsp; &nbsp; Or(a=andANotB, b=andNotAB, out=out);<br />
}</code></p>

<h3>Building Multiplexor</h3>

<p>Multiplexor allows making&nbsp;decisions based on input signal. It is similar to IF-THEN-ELSE construction in programming languages.</p>

<p>MUX&#39;s HDL Will be:</p>

<p><code>/**&nbsp;<br />
&nbsp;* Multiplexor:<br />
&nbsp;* out = a if sel == 0<br />
&nbsp;* &nbsp; &nbsp; &nbsp; b otherwise<br />
&nbsp;*/</code></p>

<p><code>CHIP Mux {<br />
&nbsp; &nbsp; IN a, b, sel;<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Not(in=a, out=notA);<br />
&nbsp; &nbsp; And(a=b, b=sel, out=andBSel);<br />
&nbsp; &nbsp; And(a=notA, b=andBSel, out=andNotA-andBSel);<br />
&nbsp; &nbsp; Or(a=b, b=sel, out=orBSel);<br />
&nbsp; &nbsp; Not(in=orBSel, out=notOrBSel);<br />
&nbsp; &nbsp; And(a=a, b=notOrBSel, out=andA-notOrBSel);<br />
&nbsp; &nbsp; And(a=a, b=b, out=andAB);<br />
&nbsp; &nbsp; Or(a=andNotA-andBSel, b=andA-notOrBSel, out=orAndNotA-andBSel--andA-notOrBSel);<br />
&nbsp; &nbsp; Or(a=andAB, b=orAndNotA-andBSel--andA-notOrBSel, out=out);<br />
}</code></p>

<h2>Stage II. Pack bits into buses.</h2>

<p>We already know, how to build basic logic gates. Let&#39;s build few more complex solutions:</p>

<ul>
	<li>And16 - 16-bit version of AND. Just pack 16 AND chips into one and connect to the 16-bit bus.</li>
	<li>Not16 - same as And16, but for NOT.</li>
	<li>Mux16 - 16 multiplexors connected to the single selector and a 16-bit bus.</li>
	<li>Or8Way - accepts eight inputs and returns 1 if at least one of them is 1.</li>
</ul>

<h3>Building And16</h3>

<p>Pack 16 AND chips into one and connect to 16-bit input and output buses.</p>

<p>And16&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* 16-bit bitwise And:<br />
&nbsp;* for i = 0..15: out[i] = (a[i] and b[i])<br />
&nbsp;*/</code></p>

<p><code>CHIP And16 {<br />
&nbsp; &nbsp; IN a[16], b[16];<br />
&nbsp; &nbsp; OUT out[16];</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; And(a=a[0], b=b[0], out=out[0]);<br />
&nbsp; &nbsp; And(a=a[1], b=b[1], out=out[1]);<br />
&nbsp; &nbsp; And(a=a[2], b=b[2], out=out[2]);<br />
&nbsp; &nbsp; And(a=a[3], b=b[3], out=out[3]);<br />
&nbsp; &nbsp; And(a=a[4], b=b[4], out=out[4]);<br />
&nbsp; &nbsp; And(a=a[5], b=b[5], out=out[5]);<br />
&nbsp; &nbsp; And(a=a[6], b=b[6], out=out[6]);<br />
&nbsp; &nbsp; And(a=a[7], b=b[7], out=out[7]);<br />
&nbsp; &nbsp; And(a=a[8], b=b[8], out=out[8]);<br />
&nbsp; &nbsp; And(a=a[9], b=b[9], out=out[9]);<br />
&nbsp; &nbsp; And(a=a[10], b=b[10], out=out[10]);<br />
&nbsp; &nbsp; And(a=a[11], b=b[11], out=out[11]);<br />
&nbsp; &nbsp; And(a=a[12], b=b[12], out=out[12]);<br />
&nbsp; &nbsp; And(a=a[13], b=b[13], out=out[13]);<br />
&nbsp; &nbsp; And(a=a[14], b=b[14], out=out[14]);<br />
&nbsp; &nbsp; And(a=a[15], b=b[15], out=out[15]);<br />
}</code></p>

<h3>Building Not16</h3>

<p>Pack 16 NOT chips into one and connect to 16-bit input and output buses.</p>

<p>Not16&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* 16-bit Not:<br />
&nbsp;* for i=0..15: out[i] = not in[i]<br />
&nbsp;*/</code></p>

<p><code>CHIP Not16 {<br />
&nbsp; &nbsp; IN in[16];<br />
&nbsp; &nbsp; OUT out[16];</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Not(in=in[0], out=out[0]);<br />
&nbsp; &nbsp; Not(in=in[1], out=out[1]);<br />
&nbsp; &nbsp; Not(in=in[2], out=out[2]);<br />
&nbsp; &nbsp; Not(in=in[3], out=out[3]);<br />
&nbsp; &nbsp; Not(in=in[4], out=out[4]);<br />
&nbsp; &nbsp; Not(in=in[5], out=out[5]);<br />
&nbsp; &nbsp; Not(in=in[6], out=out[6]);<br />
&nbsp; &nbsp; Not(in=in[7], out=out[7]);<br />
&nbsp; &nbsp; Not(in=in[8], out=out[8]);<br />
&nbsp; &nbsp; Not(in=in[9], out=out[9]);<br />
&nbsp; &nbsp; Not(in=in[10], out=out[10]);<br />
&nbsp; &nbsp; Not(in=in[11], out=out[11]);<br />
&nbsp; &nbsp; Not(in=in[12], out=out[12]);<br />
&nbsp; &nbsp; Not(in=in[13], out=out[13]);<br />
&nbsp; &nbsp; Not(in=in[14], out=out[14]);<br />
&nbsp; &nbsp; Not(in=in[15], out=out[15]);<br />
}</code></p>

<h3>Building Mux16</h3>

<p>Pack 16 MUX chips into one and connect to 16-bit input and output buses. Connect all of them to a single selector input.</p>

<p>Mux16&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* 16-bit multiplexor:&nbsp;<br />
&nbsp;* for i = 0..15 out[i] = a[i] if sel == 0&nbsp;<br />
&nbsp;* &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;b[i] if sel == 1<br />
&nbsp;*/</code></p>

<p><code>CHIP Mux16 {<br />
&nbsp; &nbsp; IN a[16], b[16], sel;<br />
&nbsp; &nbsp; OUT out[16];</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Mux(a=a[0], b=b[0], sel=sel, out=out[0]);<br />
&nbsp; &nbsp; Mux(a=a[1], b=b[1], sel=sel, out=out[1]);<br />
&nbsp; &nbsp; Mux(a=a[2], b=b[2], sel=sel, out=out[2]);<br />
&nbsp; &nbsp; Mux(a=a[3], b=b[3], sel=sel, out=out[3]);<br />
&nbsp; &nbsp; Mux(a=a[4], b=b[4], sel=sel, out=out[4]);<br />
&nbsp; &nbsp; Mux(a=a[5], b=b[5], sel=sel, out=out[5]);<br />
&nbsp; &nbsp; Mux(a=a[6], b=b[6], sel=sel, out=out[6]);<br />
&nbsp; &nbsp; Mux(a=a[7], b=b[7], sel=sel, out=out[7]);<br />
&nbsp; &nbsp; Mux(a=a[8], b=b[8], sel=sel, out=out[8]);<br />
&nbsp; &nbsp; Mux(a=a[9], b=b[9], sel=sel, out=out[9]);<br />
&nbsp; &nbsp; Mux(a=a[10], b=b[10], sel=sel, out=out[10]);<br />
&nbsp; &nbsp; Mux(a=a[11], b=b[11], sel=sel, out=out[11]);<br />
&nbsp; &nbsp; Mux(a=a[12], b=b[12], sel=sel, out=out[12]);<br />
&nbsp; &nbsp; Mux(a=a[13], b=b[13], sel=sel, out=out[13]);<br />
&nbsp; &nbsp; Mux(a=a[14], b=b[14], sel=sel, out=out[14]);<br />
&nbsp; &nbsp; Mux(a=a[15], b=b[15], sel=sel, out=out[15]);<br />
}</code></p>

<h3>Building Or8Way</h3>

<p>To build this chip, we will take pairs of inputs and process them with OR chips. Then use pairs of results and do the same. Rinse and repeat.</p>

<p>Or8Way&#39;s HDL Will be:</p>

<p><code>CHIP Or8Way {<br />
&nbsp; &nbsp; IN in[8];<br />
&nbsp; &nbsp; OUT out;</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Or(a=in[0], b=in[1], out=out1);<br />
&nbsp; &nbsp; Or(a=in[2], b=in[3], out=out2);<br />
&nbsp; &nbsp; Or(a=in[4], b=in[5], out=out3);<br />
&nbsp; &nbsp; Or(a=in[6], b=in[7], out=out4);<br />
&nbsp; &nbsp; Or(a=out1, b=out2, out=outSemi1);<br />
&nbsp; &nbsp; Or(a=out3, b=out4, out=outSemi2);<br />
&nbsp; &nbsp; Or(a=outSemi1, b=outSemi2, out=out);<br />
}</code></p>

<h2>Stage III. Add Math chips.</h2>

<p>At that stage, we are ready to build chips which perform Math operations.&nbsp;<a href="https://www.coursera.org/learn/build-a-computer/lecture/zY2v8/unit-2-1-binary-numbers">Binary numbers</a>,&nbsp;<a href="https://www.coursera.org/learn/build-a-computer/lecture/tywAA/unit-2-2-binary-addition">binary addition</a>, and&nbsp;<a href="https://www.coursera.org/learn/build-a-computer/lecture/seM6y/unit-2-3-negative-numbers">binary negative numbers</a>&nbsp;are explained in linked videos.</p>

<ul>
	<li>HalfAdder - accepts two inputs, adds them, returns resulting sum bit and a carry (used to increment next bit in multi-bit numbers)</li>
	<li>FullAdder - receives three inputs, adds them, returns resulting sum bit and a carry. This chip is similar to HalfAdder, but it also adds input carry besides two input bits.</li>
	<li>Add16 - a chain of HalfAdder and Fulladders, that pass carry one to another.</li>
	<li>Inc16 - 16-bit incrementer. Will add 1 to the 16-bit input value and return the resulting sum.</li>
</ul>

<h3>Building HalfAdder</h3>

<p>According to rules of binary addition resulting bit will be 0, if inputs are 1,1 or 0,0. It will be 1 when inputs are 0,1 or 1,0.&nbsp;</p>

<p>HalfAdder&#39;s HDL Will be:</p>

<div><code>/**</code></div>

<div><code>&nbsp;* Computes the sum of two bits.</code></div>

<div><code>&nbsp;*/</code></div>

<div>&nbsp;</div>

<div><code>CHIP HalfAdder {</code></div>

<div><code>&nbsp; &nbsp; IN a, b; &nbsp; &nbsp;// 1-bit inputs</code></div>

<div><code>&nbsp; &nbsp; OUT sum, &nbsp; &nbsp;// Right bit of a + b&nbsp;</code></div>

<div><code>&nbsp; &nbsp; &nbsp; &nbsp; carry; &nbsp;// Left bit of a + b</code></div>

<div>&nbsp;</div>

<div><code>&nbsp; &nbsp; PARTS:</code></div>

<div><code>&nbsp; &nbsp; Xor(a=a, b=b, out=sum);</code></div>

<div><code>&nbsp; &nbsp; And(a=a, b=b, out=carry);</code></div>

<div><code>}</code></div>

<div>&nbsp;</div>

<h3>Building FullAdder</h3>

<p>As you may guess, we can make a FullAdder from two HalfAdders.</p>

<p>FullAdder&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* Computes the sum of three bits.<br />
&nbsp;*/</code></p>

<p><code>CHIP FullAdder {<br />
&nbsp; &nbsp; IN a, b, c; &nbsp;// 1-bit inputs<br />
&nbsp; &nbsp; OUT sum, &nbsp; &nbsp; // Right bit of a + b + c<br />
&nbsp; &nbsp; &nbsp; &nbsp; carry; &nbsp; // Left bit of a + b + c</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; HalfAdder(a=a, b=b, sum=sumAB, carry=carryAB);<br />
&nbsp; &nbsp; HalfAdder(a=sumAB, b=c, sum=sum, carry=carryABC);<br />
&nbsp; &nbsp; Or(a=carryAB, b=carryABC, out=carry);<br />
}</code></p>

<h3>Building Add16</h3>

<p>Add16 is a 16-bit chip which performs addition of two 16-bit numbers. It is a chain of Half and Full Adders.</p>

<p>Add16&#39;s HDL Will be:</p>

<p><code>CHIP Add16 {<br />
&nbsp; &nbsp; IN a[16], b[16];<br />
&nbsp; &nbsp; OUT out[16];</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; HalfAdder(a=a[0], b=b[0], sum=out[0], carry=carry0);<br />
&nbsp; &nbsp; FullAdder(a=a[1], b=b[1], c=carry0, sum=out[1], carry=carry1);<br />
&nbsp; &nbsp; FullAdder(a=a[2], b=b[2], c=carry1, sum=out[2], carry=carry2);<br />
&nbsp; &nbsp; FullAdder(a=a[3], b=b[3], c=carry2, sum=out[3], carry=carry3);<br />
&nbsp; &nbsp; FullAdder(a=a[4], b=b[4], c=carry3, sum=out[4], carry=carry4);<br />
&nbsp; &nbsp; FullAdder(a=a[5], b=b[5], c=carry4, sum=out[5], carry=carry5);<br />
&nbsp; &nbsp; FullAdder(a=a[6], b=b[6], c=carry5, sum=out[6], carry=carry6);<br />
&nbsp; &nbsp; FullAdder(a=a[7], b=b[7], c=carry6, sum=out[7], carry=carry7);<br />
&nbsp; &nbsp; FullAdder(a=a[8], b=b[8], c=carry7, sum=out[8], carry=carry8);<br />
&nbsp; &nbsp; FullAdder(a=a[9], b=b[9], c=carry8, sum=out[9], carry=carry9);<br />
&nbsp; &nbsp; FullAdder(a=a[10], b=b[10], c=carry9, sum=out[10], carry=carry10);<br />
&nbsp; &nbsp; FullAdder(a=a[11], b=b[11], c=carry10, sum=out[11], carry=carry11);<br />
&nbsp; &nbsp; FullAdder(a=a[12], b=b[12], c=carry11, sum=out[12], carry=carry12);<br />
&nbsp; &nbsp; FullAdder(a=a[13], b=b[13], c=carry12, sum=out[13], carry=carry13);<br />
&nbsp; &nbsp; FullAdder(a=a[14], b=b[14], c=carry13, sum=out[14], carry=carry14);<br />
&nbsp; &nbsp; FullAdder(a=a[15], b=b[15], c=carry14, sum=out[15], carry=carry);<br />
}</code></p>

<h3>Building Inc16</h3>

<p>The tricky part here is to obtain zero and 16-bit 1. We can get zero by negating input value, and then perform AND with original and negated values as inputs. Because all bits are opposite after negation, resulting value will be zero.</p>
<p>Now we need 1. Negate any bit from a bus and XOR original bit value with negated one. These bits have different values after negation, so resulting bit will be 1. Now connect XOR&#39;ed bit as last bit of 16-bit zero to get 16-bit 1.</p>
<p>Add input 16-bit value with 16-bit 1 obtained earlier. Done.</p>

<p>Inc16&#39;s HDL Will be:</p>

<p><code>/**<br />
&nbsp;* 16-bit incrementer:<br />
&nbsp;* out = in + 1 (arithmetic addition)<br />
&nbsp;*/</code></p>

<p><code>CHIP Inc16 {<br />
&nbsp; &nbsp; IN in[16];<br />
&nbsp; &nbsp; OUT out[16];</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; Not16(in=in, out=notIn16);<br />
&nbsp; &nbsp; And16(a=in, b=notIn16, out[1..15]=zero15);<br />
&nbsp; &nbsp; Not(in=in[0], out=notIn0);<br />
&nbsp; &nbsp; Xor(a=in[0], b=notIn0, out=one1);<br />
&nbsp; &nbsp; Add16(a[0]=one1, a[1..15]=zero15, b=in, out=out);<br />
}</code></p>

<h2>Stage IV. ALU</h2>

<p>We built all parts, needed to construct ALU. Despite intimidating spec, it is quite simple, when components which we made previously are available.</p>

<p>The algorithm is simple:</p>

<ol>
	<li>Get the Zero</li>
	<li>Decide whether to return zero or input value</li>
	<li>Decide whether to negate or not value obtained in step 2</li>
	<li>Decide whether to sum arguments or perform AND</li>
	<li>Decide whether to negate or not value obtained in step 4</li>
	<li>Return output.</li>
	<li>Return NG flag</li>
	<li>Return ZR flag</li>
</ol>

<p>ALU&#39;s HDL Will be:</p>

<p><code>CHIP ALU {<br />
&nbsp; &nbsp; IN &nbsp;<br />
&nbsp; &nbsp; &nbsp; &nbsp; x[16], y[16], &nbsp;// 16-bit inputs &nbsp; &nbsp; &nbsp; &nbsp;<br />
&nbsp; &nbsp; &nbsp; &nbsp; zx, // zero the x input?<br />
&nbsp; &nbsp; &nbsp; &nbsp; nx, // negate the x input?<br />
&nbsp; &nbsp; &nbsp; &nbsp; zy, // zero the y input?<br />
&nbsp; &nbsp; &nbsp; &nbsp; ny, // negate the y input?<br />
&nbsp; &nbsp; &nbsp; &nbsp; f, &nbsp;// compute out = x + y (if 1) or x &amp; y (if 0)<br />
&nbsp; &nbsp; &nbsp; &nbsp; no; // negate the out output?</code></p>

<p><code>&nbsp; &nbsp; OUT&nbsp;<br />
&nbsp; &nbsp; &nbsp; &nbsp; out[16], // 16-bit output<br />
&nbsp; &nbsp; &nbsp; &nbsp; zr, // 1 if (out == 0), 0 otherwise<br />
&nbsp; &nbsp; &nbsp; &nbsp; ng; // 1 if (out &lt; 0), &nbsp;0 otherwise</code></p>

<p><code>&nbsp; &nbsp; PARTS:<br />
&nbsp; &nbsp; // Init get Zero value<br />
&nbsp; &nbsp; Not16(in=x, out=notXZ);<br />
&nbsp; &nbsp; And16(a=notXZ, b=x, out=zero, out[7..15]=zero715);</code></p>

<p><code>&nbsp; &nbsp; // Zero X or Y<br />
&nbsp; &nbsp; Mux16(a=x, b=zero, sel=zx, out=ZeroOrX);<br />
&nbsp; &nbsp; Mux16(a=y, b=zero, sel=zy, out=ZeroOrY);</code></p>

<p><code>&nbsp; &nbsp; // Negate X or Y<br />
&nbsp; &nbsp; Not16(in=ZeroOrX, out=notX);<br />
&nbsp; &nbsp; Not16(in=ZeroOrY, out=notY);<br />
&nbsp; &nbsp; Mux16(a=ZeroOrX, b=notX, sel=nx, out=finalX);<br />
&nbsp; &nbsp; Mux16(a=ZeroOrY, b=notY, sel=ny, out=finalY);</code></p>

<p><code>&nbsp; &nbsp; // Sum or And<br />
&nbsp; &nbsp; Add16(a=finalX, b=finalY, out=sumXY);<br />
&nbsp; &nbsp; And16(a=finalX, b=finalY, out=andXY);<br />
&nbsp; &nbsp; Mux16(a=andXY, b=sumXY, sel=f, out=operationXY);</code></p>

<p><code>&nbsp; &nbsp; // Negate OUT or not<br />
&nbsp; &nbsp; Not16(in=operationXY, out=notOperationXY);</code></p>

<p><code>&nbsp; &nbsp; // Return result and NG flag<br />
&nbsp; &nbsp; Mux16(a=operationXY, b=notOperationXY, sel=no, out=out, out[0..7]=isZeroIn07, out[8..15]=isZeroIn815, out[15]=ng);</code></p>

<p><code>&nbsp; &nbsp; // Return ZR flag<br />
&nbsp; &nbsp; Or8Way(in=isZeroIn07, out=or1);<br />
&nbsp; &nbsp; Or8Way(in=isZeroIn815, out=or2);<br />
&nbsp; &nbsp; Or(a=or1, b=or2, out=or3);<br />
&nbsp; &nbsp; Not(in=or3, out=zr);<br />
}</code></p>

<h2>Conclusion</h2>

<p>As appeared, even quite sophisticated concept can be split into simpler tasks and approached not as an intimidating challenge, but as a series of trivial tasks. Of course, we build a simplified ALU, but it was more than enough to demonstrate approach and tricks used.</p>
