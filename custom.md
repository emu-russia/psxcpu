# Custom Memory Blocks

This section is about the different memory blocks inside the PSXCPU.

According to the available documents of the mid-90's - such large "cells" were called `Megacells`. LSI Logic EDA (MDE) could use them along with regular cells.

:warning: The section needs to be re-checked and cleared of bitard-punk vocabulary.

At the edges of the CPU there are many such blocks.
The blocks are numbered according to the part of the CPU in which they are located to make them easy to identify.

![Cpu_units](/imgstore/custom/Cpu_units.jpg)

There are a total of 16 blocks on the chip, which can be divided into 3 types. All blocks of the same type have the same structure, similar blocks and differ only in the number of rows and columns.

- Constant Table Memory:
	- Unit-00: ScaleTableMatrix 8x8 (32 registers per 26 bits) used as 64 records per 13bits.
	- Unit-01: IDCT intermediate result (13 vertical, 16 horizontal, 16 x 13-bit words, separate IO)
	- Unit-02 (56 vertical, 8 horizontal)
	- Unit-03
	- Unit-04 (32 vertical, 16+32+16 horizontal)
	- Unit-05 (24 vertical, 16+32+16 horizontal)
	- Unit-20 (16 vertical, 16+32+16 horizontal)
- Dual port registers:
	- Unit-11 (mdec registers?) 32 32-bit dual port
	- Unit-21 (cpu registers?) 32 32-bit dual port
	- Unit-22 (GTE registers?) 16 16-bit dual port
	- Unit-23 (GTE registers?) 16 16-bit dual port
	- Unit-24 (GTE registers?) 16 16-bit dual port
	- Unit-25 (GTE registers?) 16 16-bit dual port
- SRAM:
	- Unit-10 (mdec output RGB macroblock(?) 768 bytes)(24x8 vertical, 32 horizontal)
	- Unit-26 (data ScratchPad 1024 bytes)(32x8 vertical, 32 horizontal)
	- Unit-30 (instruction cache 4096 bytes)(32x8 vertical, 128 horizontal)

## Constant Table Memory

Blocks of this type store tables with constants or temporary results of calculations.

![Cpu_unit_type1](/imgstore/custom/Cpu_unit_type1.jpg)

Typical elements of these units are the same, differing only in the way they are connected to each other (and, accordingly, the wiring of the metal).

### Cell

![Unit00_cell](/imgstore/custom/Unit00_cell.jpg)

Normal bi-stable latch based on two inverters and control transistors:

- IE (input enable): Enables write to latch. Output Enable is not provided.
- row: row select is used instead of Output Enable, which selects a range of cells to read/write.
- in: input value
- out: output value

Note: the value on the latch is stored in inverted form (more convenient).

### 2-to-4 decoders

![Unit00_decoder1](/imgstore/custom/Unit00_decoder1.jpg)

This part of the decoder receives the input index bits, which are then combined in a different sequence in ANDs circuits, for the final row selection.

### ANDs (second stage of decoding)

![Unit00_decoder2](/imgstore/custom/Unit00_decoder2.jpg)

These two steps together form a higher bit decoder (for example, Unit-00 results in a 5-in-32 decoder).

### IO TriState Buffers

![Unit00_tristate](/imgstore/custom/Unit00_tristate.jpg)

Common tri-state buffers controlled by the control signals OE (output enable) and IE (input enable).

If OE or IE is 0, the I/Os are "off" (Z), otherwise the I/Os are connected to the selected cell for data exchange.

### IO Enable

![Unit00_io_enable](/imgstore/custom/Unit00_io_enable.jpg)

Младшие 4 разряда tri-state буферов имеют альтернативную топологию (разводку металла), которая добавляет туда схему усиления входных сигналов OE / IE.

Входные управляющие сигналы OE/IE приходят на 0й разряд буферов, при этом OE обычно подключен к питанию (OE=1, то есть выход всегда открыт).

### Unit-00 ScaleTableMatrix

![Unit00_overview](/imgstore/custom/Unit00_overview.jpg)

It is organized as 32 rows of 26 cells. The external circuits split the 26 bits into two 13-bit halves, as a result, the Scale Table matrix values are stored as 64 13-bit words.

The 5th bit of the row index is not used (connected to ground), because we only have 32 rows of cells addressed (index bits 0-4).

An additional element of this unit is the division of the cells into upper and lower halves (16 rows each), with the addition of additional logic to select the half, based on the 5th bit of the index:

![Unit00_gap](/imgstore/custom/Unit00_gap.jpg)

The circuit combines the 5th bit and the input enable control signal, to "connect" the top or bottom half. Output enable control, however, is not provided and is only controlled by the decoder (row select).

Output enable Unit-00 is always on (connected to the power supply), i.e. the outputs are always loaded with the last stored value.

Ports (**look carefully at the inputs/outputs alternating**):

![Unit00_pads](/imgstore/custom/Unit00_pads.jpg)

### Unit-01: Intermediate result of IDCT calculations

![Unit01_overview](/imgstore/custom/Unit01_overview.jpg)

The block consists of typical components: cells, tri-state buffers and 2 4-in-16 decoders.

The IO enable circuit is based in a typical way on alternate routing of the first 4 buffers (bits 0-3).

Features:

- Cells have split I/O. That is, one cell can be selected for input and another for output. This is needed to arrange distributed IDCT multiplication in 2 passes. While one pass uses one intermediate result, the second pass uses the other.
- Accordingly, for this purpose, instead of feeding one "row" wire, 2 wires are fed to each cell: "row_in" and "row_out"
- Also, instead of one decoder, 2 are used: one to select the input cell, the second to select the output cell.

Cell:

![Unit01_cell](/imgstore/custom/Unit01_cell.jpg)

Terminals (**be careful again, inputs/outputs alternate in/out -> out/in**):

![Unit01_pads](/imgstore/custom/Unit01_pads.jpg)

## Dual port registers

Allow you to simultaneously write to two different registers, as well as output the value of any two registers to 2 different outputs, in a single step.

![Cpu_unit_type2](/imgstore/custom/Cpu_unit_type2.jpg)

### 16-bit dual port registers

![Unit24_overview](/imgstore/custom/Unit24_overview.jpg)

- The main part is occupied by cells organized as a 16x16 matrix. One row of cells represents a single register. Bit numbering is from right to left for data lines and from left to right for index lines.
- On the upper right side are 8 2-in-4 decoders. They turn 16 inputs into 32 outputs, and the inputs are decoded by 2 nearby bits. These 32 outputs are then fed into a 4x16 array of AND operations, with each of the 16 lanes giving out 4 outputs to the left. Together, these circuits form a 4-in-16 decoder, for row selection.
- At the top left are the I/Os from the registers.
- Up in the middle is the IO enable circuit for the buffers, just below is the Input enable safety circuit for the cells.
- Output enable for the cells is not provided.

### Cell

![Unit24_cell_tran](/imgstore/custom/Unit24_cell_tran.jpg)

Each cell has 4 wires at the top (1-4) and 4 at the side (5-8). There are a total of 16 cells vertically and 16 horizontally.

![Unit24_cell](/imgstore/custom/Unit24_cell.jpg)

A special feature of the registers is that they have a selective input (2-on-1) and a selective output (1-on-2), for fast copying of values.

When I drew the schematics there was a little confusion in the numbering. So here is the human notation of the inputs/outputs, so as not to get confused:

- 1: Output 2
- 2: Output 1
- 3: Input 2
- 4: Input 1
- 5: Input 1 enable
- 6: Input 2 enable
- 7: Output 1 enable
- 8: Output 2 enable

We move from these names.

### IO Enable

Drives amplified input/output enable signals through the internals of the unit.

![Unit24_io_enable](/imgstore/custom/Unit24_io_enable.jpg)

Unit22-25 has OE1=OE2=1 (connected to the power supply), so both outputs are used.

### Register IO buffers

They are used mainly to amplify the output signal and also as a tri-state buffer to enable/disable the exchange between certain register inputs and outputs.

The input receives 4 control signals coming from the IO Enable circuit:

- IE1, IE2: input enable. If IE=0, the corresponding input goes into a tri-state (off).
- OE1, OE2: output enable. If OE=0, then the corresponding output goes to tri-state (off).

![Unit24_reg_inputs_outputs](/imgstore/custom/Unit24_reg_inputs_outputs.jpg)

### 2-to-4 decoders

![Unit24_decoder_2-to-4](/imgstore/custom/Unit24_decoder_2-to-4.jpg)

Part of the decoder.

### ANDs

![Unit24_ands](/imgstore/custom/Unit24_ands.jpg)

The next step is to decode a register index, a 4x16 array of AND operations.

The input is 32 lines from the 2-by-4 decoders above. These lines loop through the array of AND elements and selectively connect to the AND inputs.

The output of each AND line has 4 outputs, two of which go directly to the cells, and two more go to the Input Enable circuit.

The ANDs and 2-on-4 circuits together make up the 4-on-16 row decoder:

![Unit24_decoder](/imgstore/custom/Unit24_decoder.jpg)

### Input enable

The 2 outputs from the AND circuits go to this circuit where an additional AND operation is performed between the input wires and IE1/IE2.

If IE1/IE2=0, then so output 5/6 = 0 and the cell input is overlapped.

![Unit24_input_enable](/imgstore/custom/Unit24_input_enable.jpg)

There is a small flaw here: the circuit has control of transistors 5/6 of the memory cell (IE1/IE2), but no control of transistors 7/8 (which open the outputs). This is not necessary though, because the I/Os are disabled in the Register IO buffers circuit anyway. In fact, this Input enable scheme is not needed either, but the developers apparently over-insured.

### Logic

![Dualport16](/imgstore/custom/Dualport16.jpg)

I am not sure only about the numbering of the output bits. Most likely (by analogy with other units) the numbering of bits goes from right to left.
That is, where in the picture it says Bit0 is actually Bit31, it's not easy to redo the picture.

## SRAM

Blocks of this type are static SRAM used as a cache for various data.

![Cpu_unit_type3](/imgstore/custom/Cpu_unit_type3.jpg)

The data "cache" (ScratchPad) has 18 control lines (among them are address bus lines somewhere) as well as 32x2 Data bus lines.

The instruction cache has 17 control lines (2 of them are not used) and 32x2 data bus (instruction).

The peculiarity of data buses in the caches is that their lines are paired for each bit (circles show the places where the lines join into one):

![DCache_data_lines](/imgstore/custom/DCache_data_lines.jpg)

![ICache_data_lines](/imgstore/custom/ICache_data_lines.jpg)

One line works for input, the other for output (the data bus is bidirectional). This arrangement is probably due to the fact that before implementing the circuit in silicon it was tested in a simulator, in which it is easier to make unidirectional inputs and outputs.

Functionally, the blocks consist of:

- Row decoder
- Column decoder
- Control logic
- Address line input buffers
- Data bus buffers
- Memory Cell Array

### SRAM Cell

The memory cell is a conventional CMOS SRAM-cell, based on 6 transistors.

![Sram_cell](/imgstore/custom/Sram_cell.jpg)

![Sram_cell_trans](/imgstore/custom/Sram_cell_trans.jpg)

The memory element is a Flip-flop based on paired inverters whose inputs are connected to the column line via a row line tri-state buffer.

When row = 0 the cell is in bistable state and stores the charge.

When row = 1, the entire row of cells "opens", but only the one whose column line does NOT have a Z value (is NOT disconnected) changes the value.

The cell access is controlled by a special Row/Column decoder, which generates row/column lines based on the input address bus.
