# PSXCPU Cells Library

PSX CPU is based on standard cells.

PSX CPU built on CMOS technology. P and N channel transistors differ in size (P-doping is 1.3-1.5 times thicker). Diffusion is shown in yellow, and the polysilicon as purple. P-type diffusion is usually thickier rather diffusion of N-type, and is closer to the power supply. Accordingly, the diffusion of N-type is usually close to the ground.

Standard cells are placed by rows. Connections between cells are made using two layers of metal. (Channel Router)

Thus M1 runs parallel to the rows of cells, and M2 (the top layer) - perpendicularly.

Quite often automatic router place route directly on the area of the cell (via M1), if there exist free space for it:

![row_m1](/imgstore/cells/row_m1.jpg)

Ground and power supply are also by M1.

Cells under a microscope look like this:

![Cells](/imgstore/cells/Cells.jpg)

Standard cell patterns may be rotated by 180<sup>o</sup>, as the power and ground in alternating rows changes, due to the fact that the cells are stacked zigzag.

In some cases, the cell is also mirrored relative to a vertical axis. It seems that fitter tool has a feedback from wire router. Wire routrer (if it is more convenient) can give fitter signal flip cell to wire it easier to get to its input/outputs.

## Cells Addressing

To determine the connection between the two modules it is necessary to develop an addressing mechanism on the topology of the processor.

As we have already decided before - the processor is divided into 4 big parts: 00, 01, 02, 03.

Therefore addressing between cells will be based on these 4 parts.

The rows of standard cells will be numbered from left to right, inside each of the parts, starting from `00`.

Inside the rows, cells will be numbered from top to bottom, starting with `00`.

The number of rows in each part:

![Psxcpu-cell-rows](/imgstore/cells/Psxcpu-cell-rows.jpg)

- Part 00: 82 rows
- Part 01: 81 rows
- Part 02: 66 rows (counting from 16)
- Part 03: 81 rows

## Cells Map

In addition to standard NAND, NOR, and so forth, modern logic includes such elements as AO, AOI, OA and the OAI. This variation of And / Or operation with an additional result inversion (I). examples:
- 22-AOI: Y = ~ (AB | CD)
- 33-OA: Y = ( (A|B|C) & (D|E|F) )

That is, the first letter indicates what operation is used within the groups, and the second letter indicates what operation is used between groups. Before operation, the number of items placed into each group.

For convenience we have divided the types of categories and assigned them a cell color:

![Cell_types](/imgstore/cells/Cell_types.jpg)

At the moment, we made the identification of almost all cells. PSX CPU contains about 37,600 cells of 150 different types.

Cells Heatmap:

![Psxcpu_cells_map_sm](/imgstore/cells/Psxcpu_cells_map_sm.jpg)

## Cells By Category

Further description of the cell is divided into categories for easier reference.

:warning: The topology and cell schematics were obtained at different times and in different sequences. Sometimes, for example, a cell was found, and then its strengthened version. But it turned out that there is even a "weaker" version of the first cell, so the designations of their "power" (numbers after the name) are quite chaotic.

:warning: Some cells do not know how to call it at all, most of them are placed in the category of "composite".

Transistor schematic sources for all cells can be found here: https://drive.google.com/drive/u/2/folders/1ZomLORsXA5cFluM0va30vhQP0l_kxcaR

### Inverters

The more transistors connected in _parallel_ in an inverter, the greater its drive strength.

The more transistors connected in _series_, the longer the propagation delay.

#### NOT - Mini inverter

![Not](/imgstore/cells/Not.jpg)

We found this inverter after its larger counterpart (NOT1), so NOT had to be renamed NOT1, and the new NOT was this small one.

#### NOT1 - Inverter

![NOT1](/imgstore/cells/NOT1.jpg)

Nothing special.

#### NOT2 - Inverter, 2 pairs of transistors

![Not2](/imgstore/cells/Not2.jpg)

:warning: Topologically very similar to NAND2X:

![Nand2x](/imgstore/cells/Nand2x.jpg)

So be very careful not to mess up. The NAND2X is shorter than the NOT2, and also the legs on the right side go at different angles.

#### NOT3 - Inverter, 3 pairs of transistors

![Not3](/imgstore/cells/Not3.jpg)

The M1 wiring allows you to do alternate routing from above. Also the input can come from M2.

#### NOT4 - Inverter, 4 pairs of transistors

![Not4](/imgstore/cells/Not4.jpg)

#### NOT8 - Strong inverter, 8 pairs of transistors

![NOT8](/imgstore/cells/NOT8.jpg)

These inverters are easy to spot by the rather impressive metal shield (this is the output):

![NOT8_M1](/imgstore/cells/NOT8_M1.jpg)

A feature of the topology is that input and output can go to M2, and can alternate paths go to M1.

Alternate routing through M1. Additional M1 is shown in blue, when input/output goes to M2 - this additional M1 is not present.

![NOT8_alternate](/imgstore/cells/NOT8_alternate.jpg)

#### NOT12 - 12 pairs of transistors

![Not12](/imgstore/cells/Not12.jpg)

It's hard to mistake this cell for something else :smiley:

### Buffers

The more transistors connected in _parallel_ in a buffer, the greater its drive strength.

The more transistors connected in _series_ in the buffer, the longer its propagation delay.

#### BUF2X - Buffer regular, 2X

![Buf2x_circuit](/imgstore/cells/Buf2x_circuit.jpg)

#### BUF3X - Buffer regular, 4 pairs of transistors

![Buf3x_circuit](/imgstore/cells/Buf3x_circuit.jpg)

#### BUF3X2 - Buffer regular, 4 pairs of transistors, reinforced diffusion

![Buf3x2_circuit](/imgstore/cells/Buf3x2_circuit.jpg)

#### BUF4X - Double buffer

The double buffer differs from the regular buffer in that it does out = not(not(not(in)))), while the regular buffer does just out = not(not(in)).

Hence the propagation delay of the double buffer will be longer than that of the regular buffer.

![BUF4X](/imgstore/cells/BUF4X.jpg)

:warning: The top of this cell is very similar to NOR2X:

![Nor2x](/imgstore/cells/Nor2x.jpg)

Don't mix it up!

#### BUF5X - Double buffer, 4 pairs of transistors

![BUF5X](/imgstore/cells/BUF5X.jpg)

#### BUF6X

![BUF6X](/imgstore/cells/BUF6X.jpg)

### Multiplexers

There will be all sorts of multiplexers.

#### MUX

![MUX](/imgstore/cells/MUX.jpg)

#### MUX1

![MUX1](/imgstore/cells/MUX1.jpg)

#### MUX2X - Regular multiplexer

The multiplexer implements the if-else construct in the hardware version. If a = 0, the output will be b, otherwise c.

To simplify the notation, the 2-in-1 multiplexer will be referred to simply as MUX in the schematics (higher bit multiplexers are quite rare).

![MUX2X](/imgstore/cells/MUX2X.jpg)

|abc | x|
|---|---|
|000 | 0|
|001 | 1|
|010 | 0|
|011 | 1|
|100 | 0|
|101 | 0|
|110 | 1|
|111 | 1|

Function: `x = a ? b : c`

In the classic version, input a is called s (select), and inputs c and b are i0 and i1, respectively. That is, if select = 0, we select the 0th input (i0), and the value select = 1 selects the 1st input (i1). So the classical truth table looks a little different. We haven't decided yet which variant of inputs to use, but most likely it will be the classical variant:

```
x = (s == 0) ? i0 : i1;
```

In the picture you can see that on the side there is some left wire which is not connected to anything. The point is that it is used for alternative routing:
- Input a / s (select) can come either from M2 or through M1 via this alternate route
- This route can be used simply to route another routing, that is, M1 goes through the entire cell without affecting it in any way.

The output from the multiplexer is additionally loaded with a paired push/pull inverter, which means that this cell gives extra power reserve at the output so that long hoses can be connected to it.

#### MUX3X

There is also an "strengthened" version of the Multiplexer:

![MUX3X](/imgstore/cells/MUX3X.jpg)

#### IMUX1

![IMUX1](/imgstore/cells/IMUX1.jpg)

#### IMUX - Inverting multiplexer

It differs from the regular one in that it inverts the inputs.

![IMUX](/imgstore/cells/IMUX.jpg)

Truth Table:

|abc | out|
|---|---|
|000 | 1|
|001 | 1|
|010 | 0|
|011 | 0|
|100 | 1|
|101 | 0|
|110 | 1|
|111 | 0|

The lower terminal `c` has a special pad to alternatively come from M2. But more often the input comes from M1 from above or below.

The input `b` always comes from M2.

By the way the inputs `b` and `c` go to the diffusion.

#### IMUX2X

There is also a more powerful version, with a reinforced inverter on the output:

![IMUX2X](/imgstore/cells/IMUX2X.jpg)

#### IMUX3X

![IMUX3X](/imgstore/cells/IMUX3X.jpg)

#### 3-MUX - 3-in-1 Multiplexer

![3-mux](/imgstore/cells/3-mux.jpg)

If input s1 == 1, the output is in2.

Otherwise (s1 == 0) the following condition is checked:
- If s0 == 0 the output goes in0.
- If s0 == 1, the output is in1.

Or shorter:

|sel|out =|
|---|---|
|00|in0|
|01|in1|
|1X|in2|

#### 3-MUX3X

![3-MUX3X](/imgstore/cells/3-MUX3X.jpg)

#### 4-MUX - 4-in-1 Multiplexer

![4-MUX](/imgstore/cells/4-MUX.jpg)

Selects one wire out of 4.

|s1 |s0 | which input to apply to the output|
|---|---|---|
|0  |0  | 0|
|0  |1  | 1|
|1  |0  | 2|
|1  |1  | 3|

- Sometimes the 0-3 input pins are attached to ground/power (i.e. hardcore set to 0/1).
- There is room on the left and right for an M1 pass-through

#### 4-MUX3X

![4-MUX3X](/imgstore/cells/4-MUX3X.jpg)

#### IMUXR1 - Reduced inverting multiplexer

Sometimes there are special multiplexers with the SEL input implemented as two ports:

![IMUXR1](/imgstore/cells/IMUXR1.jpg)

It is assumed that sel1 != sel2 (otherwise the circuit will work unpredictably).

This layout is called Dual-Pass Logic ("dual rails"):

![DPL](/imgstore/cells/DPL.jpg)

As indicated, such a MUX cannot output a heavy load signal, so its use is limited to the local cell domain.

#### IMUXR2

![IMUXR2](/imgstore/cells/IMUXR2.jpg)

### Logic elements OR

#### OR, OR2X, OR3X, OR4X

|OR|OR2X|OR3X|OR4X|
|---|---|---|---|
|![Or](/imgstore/cells/Or.jpg)|![Or2x](/imgstore/cells/Or2x.jpg)|![Or3x](/imgstore/cells/Or3x.jpg)|![OR4X](/imgstore/cells/OR4X.jpg)|

#### 3-OR, 3-OR2X, 3-OR3X, 3-OR4X

|3-OR|3-OR2X|3-OR3X|3-OR4X|
|---|---|---|---|
|![3-or](/imgstore/cells/3-or.jpg)|![3-or2x](/imgstore/cells/3-or2x.jpg)|![3-OR3X](/imgstore/cells/3-OR3X.jpg)|![3-OR4X](/imgstore/cells/3-OR4X.jpg)|

### Logic elements NOR

#### NOR, NOR2X, NOR3X, NOR4X

|NOR|NOR2X|NOR3X|NOR4X|
|---|---|---|---|
|![Nor](/imgstore/cells/Nor.jpg)|![Nor2x](/imgstore/cells/Nor2x.jpg)|![Nor3x](/imgstore/cells/Nor3x.jpg)|![NOR4X](/imgstore/cells/NOR4X.jpg)|

#### 3-NOR, 3-NOR2X, 3-NOR4X, 3-NOR6X

|3-NOR|3-NOR2X|3-NOR4X|3-NOR6X|
|---|---|---|---|
|![3-nor](/imgstore/cells/3-nor.jpg)|![3-nor2x](/imgstore/cells/3-nor2x.jpg)|![3-nor4x](/imgstore/cells/3-nor4x.png)|![3-NOR6X](/imgstore/cells/3-NOR6X.jpg)|

#### 5-NOR

![5-nor](/imgstore/cells/5-nor.jpg)

#### 6-NOR, 6-NOR3X

|6-NOR|6-NOR3X|
|---|---|
|![6-nor](/imgstore/cells/6-nor.jpg)|![6-NOR3X](/imgstore/cells/6-NOR3X.jpg)|

### Logic elements AND

#### AND1, AND, AND2X, AND3X

|AND1|AND|AND2X|AND3X|
|---|---|---|---|
|![AND1](/imgstore/cells/AND1.jpg)|![And](/imgstore/cells/And.jpg)|![AND2X](/imgstore/cells/AND2X.jpg)|![AND3X](/imgstore/cells/AND3X.jpg)|

#### 3-AND, 3-AND2X, 3-AND3X, 3-AND4X

|3-AND|3-AND2X|3-AND3X|3-AND4X|
|---|---|---|---|
|![3-and](/imgstore/cells/3-and.jpg)|![3-AND2X](/imgstore/cells/3-AND2X.jpg)|![3-AND3X](/imgstore/cells/3-AND3X.jpg)|![3-AND4X](/imgstore/cells/3-AND4X.jpg)|

### Logic elements NAND

#### NAND, NAND2X, NAND3X, NAND4X

|NAND|NAND2X|NAND3X|NAND4X|
|---|---|---|---|
|![Nand](/imgstore/cells/Nand.jpg)|![Nand2x](/imgstore/cells/Nand2x.jpg)|![Nand3x](/imgstore/cells/Nand3x.jpg)|![Nand4x](/imgstore/cells/Nand4x.jpg)|

#### 3-NAND, 3-NAND2X, 3-NAND4X, 3-NAND5X

|3-NAND|3-NAND2X|3-NAND4X|3-NAND5X|
|---|---|---|---|
|![3-nand](/imgstore/cells/3-nand.jpg)|![3-nand2x](/imgstore/cells/3-nand2x.jpg)|![3-nand4x](/imgstore/cells/3-nand4x.jpg)|![3-NAND5X](/imgstore/cells/3-NAND5X.jpg)|

#### 5-NAND, 5-NAND3X

|5-NAND|5-NAND3X|
|---|---|
|![5-nand](/imgstore/cells/5-nand.jpg)|![5-NAND3X](/imgstore/cells/5-NAND3X.jpg)|

#### 6-NAND, 6-NAND3X

|6-NAND|6-NAND3X|
|---|---|
|![6-nand](/imgstore/cells/6-nand.jpg)|![6-nand3x](/imgstore/cells/6-nand3x.png)|

### Logic elements XOR/XNOR

#### XOR, XOR2, XOR3

|XOR|XOR2|XOR3|
|---|---|---|
|![XOR](/imgstore/cells/XOR.jpg)|![XOR2](/imgstore/cells/XOR2.jpg)|![XOR3](/imgstore/cells/XOR3.jpg)|

#### XNOR1, XNOR, XNOR2, XNOR3

|XNOR1|XNOR|XNOR2|XNOR3|
|---|---|---|---|
|![XNOR1](/imgstore/cells/XNOR1.jpg)|![Xnor](/imgstore/cells/Xnor.jpg)|![XNOR2](/imgstore/cells/XNOR2.jpg)|![XNOR3](/imgstore/cells/XNOR3.jpg)|

### Composite logic (AO, OA, NAND+NOR etc)

#### 12-OAI

![12oai](/imgstore/cells/12oai.jpg)

y = ~ (a & (b|c));

#### 12-AOI

![12-aoi](/imgstore/cells/12-aoi.png)

y = ~ (a | (b&c));

#### 22-OAI

![22oai](/imgstore/cells/22oai.jpg)

22-OAI implements the function Y = NAND(a|b, c|d).

#### 22-AOI

![22aoi](/imgstore/cells/22aoi.jpg)

Implement function Y = NOR(a&b, c&d) (2-2 AND/NOR MUX).

#### 31-AOI

![31-AOI](/imgstore/cells/31-AOI.jpg)

```
abcd
0000	1
0001    0
0010    1
0011    0
0100    1
0101    0
0110    1
0111	0
1000    1
1001    0
1010    1
1011    0
1100    1
1101    0
1110	0    
1111	0

nand(a,b,c) & ~d
```

#### NOR & NAND (211-AOI)

|![Nor_nand](/imgstore/cells/Nor_nand.jpg)|![NOR_NAND_logisim](/cells/cells_circuits/NOR_NAND_logisim.jpg)|
|---|---|

#### NAND \| NOR (211-OAI)

|![Nand_or_nor](/imgstore/cells/Nand_or_nor.jpg)|![NAND_NOR_logisim](/cells/cells_circuits/NAND_NOR_logisim.jpg)|
|---|---|

#### NAND & XOR

![Nand_and_xor](/imgstore/cells/Nand_and_xor.jpg)

Used in counters, as an XOR element.

At the top is the NAND(a,b) operation. When a and b = 1, the output is 0. Otherwise, the output comes from the lower half.

The lower half is the XOR(c,d) operation

![Counter2](/imgstore/cells/Counter2.jpg)

#### NOR \| AND

![Nor_or_and](/imgstore/cells/Nor_or_and.png)

Used in counters, as an XNOR element.

![Counter1](/imgstore/cells/Counter1.jpg)

### Latches

There will be all kinds of level-triggered items.

#### D Latches

![Dlatches](/imgstore/cells/Dlatches.jpg)

/DLATCH remembers the value when CLK=0, DLATCH remembers the value when CLK=1.

If the input is "z" (disconnected) the latch does not change its value. This way you can economize and not make a separate Enable input.
These kinds of latches are also called Transparent Latches.

Transistor circuit on the example of /DLATCH:

![D1](/imgstore/cells/D1.jpg)

Flow by example /DLATCH:

![Ndlatch_flow](/imgstore/cells/Ndlatch_flow.jpg)

When CLK=0 the old value is cut off and the new value from input in goes to the latch. This is where it is stored.

At CLK=1 the value on the latch circulates back and forth and goes to the output.

The output, by the way, is not inverted (Q=in).

The latch at CLK=1 differs simply in the arrangement of the side legs.

![NDLATCH_logisim](/imgstore/cells/NDLATCH_logisim.jpg)

### DFFs

Edge-triggered circuits are not triggered by level, but by a level change from 0 to 1 (rising edge, posedge in Verilog) or from 1 to 0 (falling edge, negedge in Verilog).

![Edges](/imgstore/cells/Edges.jpg)

The point of this is to "open" the trigger only in the first or last part of full cycle. That is, we can ideally multiply the frequency if we use two triggers, one of which is set to the rising edge and the other to the falling edge.

If the input is "z" (disconnected), the trigger does not change its value. This way we can economize and not make a separate Enable input.

Or we can do two actions in 1 half-cycle at once, again using this trick. We put the first action on the falling edge trigger and the second action on the rising edge trigger. This is how DDR memory works.

Sequiental logic differs from the combinatorial logic in that it can remember some states (in this case the triggers remember the input value).

The family of triggers on the rising edge looks like this (most of them are rather large cells consisting of several parts):

![DFFs](/imgstore/cells/DFFs.jpg)

The design of modern DFFs:

![Modern_dff](/imgstore/cells/Modern_dff.jpg)

Master flip-flop opens first because CLK=0 is set to PMOS. The Slave opens second.

The standard PSXCPU cells use a slightly different design. Instead of FF on inverter+MOSFET pair, inverters+MUX are used.

If at CLK=0 the first MUX opens to receive D, it posedge DFF. If at CLK=0 the second MUX opens and the first one stays closed, it is negedge DFF.

I don't really understand how it works, but most likely the trick is propagation delay. It is not necessary to know this magic, we will start from the fact that if the DFF input goes to a MUX, which opens at CLK=0 by a P-type MOSFET, it is a posedge DFF.

#### DFF triggered on a rising edge (posedge, CLK 0->1 change)

|![DFF](/imgstore/cells/DFF.jpg)|![DFF_logisim](/imgstore/cells/DFF_logisim.jpg)|
|---|---|

#### DFF on the rising edge, with reset

Taking DFF as a basis, the developers began to produce its modifications.

One of them is a DFF with an additional reset input (with the input in inverse logic, as it is usually done for reset signals).

![DFFR_circuit](/imgstore/cells/DFFR_circuit.jpg)

- If /res (in2) is zero (reset), the output will always be 0
- Otherwise the circuit works like a regular DFF, setting the value on a rising edge

The following diagram does not reflect the fine mechanics of delayed multiplexers:

![DFFR_logic](/imgstore/cells/DFFR_logic.png)

(The schematic is given to get a rough idea of which logic elements are used)

DFF with reset are used e.g. in counters and /RES is used when you want to reset the counter.

#### DFF on rising edge, selectable by MUX, with reset

This variant of the cell is hybrid: instead of the usual input (D) it has a multiplexed input.

Most likely, the combination of MUX + DFF was so common that the developers decided to add a hybrid cell.
This DFF is also sometimes called TFF and is used in the JTAG Boundary Scan mechanism: one of the multiplexer inputs is used in "operation" mode and the other is used for testing and sequential value loading.

The circuit is not very different from the DFFR.

|Topology|Transistor circuit|
|---|---|
|![DFFR_MUX_topo](/imgstore/cells/DFFR_MUX_topo.jpg)|![DFFR_MUX_trans](/imgstore/cells/DFFR_MUX_trans.jpg)|

Logic circuit (again, "slow" MUXes are not taken into account here):

![DFFR_MUX_logic](/imgstore/cells/DFFR_MUX_logic.jpg)

#### DFF on the falling edge (negedge)

![NDFF_trans](/imgstore/cells/NDFF_trans.jpg)

As you can see, when CLK=0, the lower MUX (where input D comes in) stays closed and the upper one opens. As it was already said above - such magic forms negedge DFF.

### Cells for the multiplier

#### Full Adder

![Full_adder](/imgstore/cells/Full_adder.jpg)

```
in1 = a
in2 = b
in3 = carry_in
out1 = carry_out
out2 = sum
```

#### Half Adder

![Half_adder](/imgstore/cells/Half_adder.jpg)

```
in1 = a
in2 = b
out1 = carry_out
out2 = sum
```

#### Carry Adder

This item is similar to Full Adder in functionality, except that its Carry In always equals 1.

![Xnor_or](/imgstore/cells/Xnor_or.jpg)

```
out1 = XNOR (a,b) = sum
out2 = OR (a,b) = carry out
```

#### WS1, WS2 - Weighted Sum

|WS1|WS2|
|---|---|
|![WS1_circuit](/imgstore/cells/WS1_circuit.jpg)|![WS2_circuit](/imgstore/cells/WS2_circuit.jpg)|

The purpose of these cells is not completely clear, but they are usually in the last stages of multipliers. So most likely they calculate the weighted sum of intermediate results.

Therefore, the designation of them would be "weighted sums" (WS).

- At the top is an inverting multiplexer (IMUX)
- Just below two amplifying inverters for out2/out3 outputs
- Still below are three multiplexers, two are used for the out2/out3 inputs and another one for the output IMUX
- At the very bottom is the selector operation XNOR

Logic circuits:

![WS_logic](/imgstore/cells/WS_logic.jpg)

The differences between the two cells are insignificant:
- The inverter after the in2/in3 multiplexer is repositioned
- Direct/inverted input in5 for out2/out3

The XNOR selective operation defines 2 circuit behaviors.

WS1:

```
x = XNOR (in4, in5)

if (x = 0):
out1 = !MUX (in1, in2, in3);
out2 = !in2;
out3 = !in3;

if (x = 1):
out1 = MUX (in1, in2, in3);
out2 = out3 = !in5;
```

WS2:

```
x = XNOR(in4, in5);

if (x = 0):
out1 = MUX (in1, in2, in3);
out2 = !in2
out3 = !in3

if (x = 1):
out1 = !MUX (in1, in2, in3);
out2 = out3 = in5
```

First met in the matrix multiplication circuit [MDEC IDCT](mdec.md), where they form a chain:

![WS_circuit](/imgstore/cells/WS_circuit.jpg)

- WS1 and WS2 are alternately interleaved to organize inverted carry-chain (a trick to reduce propagation delay)
- The inputs in4/in5 are fed ...

#### MUX_ARRAY - MUX Array

:warning: The name of this cell has undergone many variations, originally we decided to call it "Pipeline", but then we simplified it to "MUX Array".

These cells are at the initial stages of the multipliers. The cell consists of 4 parts, each part is 2 connected multiplexers.

The input multiplexer by signal C selects between the two inputs, and the output multiplexer by signal D outputs outside either the previous value or the current value (i.e. organizes a kind of shift chain by 1 bit).

![Pipeline](/imgstore/cells/Pipeline.jpg)

Generalized pipeline of operations, to divide by clock the sequence of actions (4-stage). It can form longer chains.

The cell consists of 4 stages, each stage has two inputs (1,2) and one output (3). The circuit is controlled by the control signals C1/C2 and D1/D2 (whether they are interconnected or not is unknown).

The circuit also has a "start" input - in (to start the pipeline) and a "finish" input (signaling that the pipeline has stopped) - out.

Each stage is based on 2 multiplexers:

|![Pipeline_C1_C2_flow](/imgstore/cells/Pipeline_C1_C2_flow.jpg)|![Pipeline_D1_D2_flow](/imgstore/cells/Pipeline_D1_D2_flow.jpg)|
|---|---|

Control signals C1/C2 control inputs 1 and 2. The control signals D1/D2 select what to feed to output 3.

When C1 = 1, the value from contact 1 is fed to the current stage input. When C2 = 1, the value from pin 2 is fed to the current stage input.

When D1 = 1, the selected input from pin 1/2 of the current stage goes to output 3.
When D2 = 1, the output from the previous stage goes to output 3.

The idea is that C1/C2 and D1/D2 should be consistent and represent the same clock signal (CLK). But so far this has not been confirmed.
What is certain is that C1 != C2 and D1 != D2 (they cannot be 0 0 or 1 1 at the same time).

In general, the logic of the whole "sausage" will look as follows:

![Pipeline_logic](/imgstore/cells/Pipeline_logic.jpg)

:warning: Logic output 3 from each step is inverted with respect to inputs 1/2 or the value from the previous step.

The output signal `out` (stop) is additionally pulled up by a small buffer.

It makes no sense to look for any special logic here, as well as to look for analogues in standard multiplier circuits.

Along with the Weight sum cells it is just a "helper cell" which replaces the 8 multiplexers, to optimize the on-chip space.

### Cells for use with buses (Bus keeper, Tri-states)

#### BUS_KEEPER - Bus Keeper

|![BUS_KEEPER_1](/imgstore/cells/poly/BUS_KEEPER_1.jpg) ![BUS_KEEPER_2](/imgstore/cells/poly/BUS_KEEPER_2.jpg)|![BUS_KEEPER](/imgstore/cells/m1/BUS_KEEPER.jpg)|
|---|---|

BUS keeper is to keep last state on the bus, basically, it's flipflop constructed by two inverter, but its output drive ability is limited. its output is connected on bus. in normal operation, it take no effect on normal logic level, but when bus is into tristate, the last logic level is keeped by bus keeper to prevent bus from floating.

![bus_keeper](/cells/cells_circuits/bus_keeper.png)

#### TRISTATE2

![TRISTATE2](/imgstore/cells/TRISTATE2.jpg)

#### TRISTATE3

![TRISTATE3](/imgstore/cells/TRISTATE3.jpg)

## Pockets

PSXCPU uses N-pockets for P-diffusion. For a long time it was not clear at all whether they are there (obviously they should be). But they are practically not visible and appear only when lapping the chip:

![pocket](/imgstore/cells/pocket.jpg)

On the domestic side this knowledge is of little use when studying the cells (it is already clear where the P-MOS FETs are), but when studying [Custom Blocks](custom.md) it is very important, because there is a whole mess of N and P. Somewhere you can guess, but there are places where without visible pockets is very difficult.
