# MDEC

MDEC is used to decode streaming video, usually stored in .STR files. But in general case MDEC can decode any images (pictures, textures etc.).

The decoding algorithm is uncomplicated and almost repeats JPEG. That's why we won't explain the basics and principles of DCT here.

Input data is divided into smaller portions called macroblocks. Therefore, the decoding process is done in "steps" by decoding macroblocks one by one.

Hardware MDEC performs RLE decoding, IDCT decoding and color-space conversion (YCbCr -> RGB). VLC decoding (Huffman) is done programmatically, by PsyQ SDK libpress library.

## Programming Interface

Two registers are available for the programmer: MDEC Data/Command (0x1f80_1820) and MDEC Status/Control (0x1f80_1824) as well as two DMA channels MDEC Input (DMA0) and MDEC Output (DMA1).

You can read more here: http://nocash.emubase.de/psx-spx.htm#macroblockdecodermdec

The PsqQ SDK contains a special library called `libpress` which contains all the basic APIs for decoding MDEC data streams.

## Data Formats

The format of the `input` bitstream data depends on the `output` data mode:
- If the output data format is set to 4bpp or 8bpp (monochrome), then MDEC interprets the input data as 8x8 luminance blocks (Y)
- If output data format is 15bpp or 24bpp (color), then input data is also interpreted as 8x8 blocks, but in this order: Cr, Cb, Y1, Y2, Y3, Y4. The color blocks are "stretched" to 16x16 blocks (when addressing, the index is simply multiplied by 2).

<pre>
   .-----.       .-----.       .-----.
   |     |       |     |       |Y1|Y2|
   | Cr  |   +   | Cb  |   +   |--+--|
   |     |       |     |       |Y3|Y4|
   '-----'       '-----'       '-----'
</pre>

The chromaticity is stretched because it is pointless to store separate blocks, because the human eye will not notice the difference anyway.

The output data format is set by the MDEC control register.

## MDEC Circuit

- RLE Decoding
- Uses Radix-4 Booth multiplication: http://www.geoffknagge.com/fyp/booth.shtml
- IDCT circuit. Multiplies result of RLE decoding and Scale Table Matrix (in 2 passes).

### Topology

![Circuit002_loc](/imgstore/mdec/Circuit002_loc.jpg)

![Circuit002_topo](/imgstore/mdec/Circuit002_topo.jpg)

![Circuit002_topo2](/imgstore/mdec/Circuit002_topo2.jpg)

![Circuit002_topo3](/imgstore/mdec/Circuit002_topo3.jpg)

### Logic

The circuit is an IDCT transform which is part of the MDEC decompression.

The conversion is performed in 2 passes:

#### Pass 1

The first pass multiplies the result of RLE decompression and the Scale Table Matrix stored in UNIT 00. It is stored as 32 entries of 26 bits each. After the output the data in pairs come to the multiplexers where it is chosen which 13 bits to use.

![Circuit002_logic](/imgstore/mdec/Circuit002_logic.jpg)

Inputs:
- RLE input: 12 bits
- Scale Table Matrix input: 13 bits
- Sum of previous calculation step: 17 bits

The circuit immediately multiplies the 2 inputs and sums the multiplication with the result of the previous calculation step. 17 bits of the result is fed back to the circuit.

At the end of the calculation the higher 13 bits of the result are stored in UNIT 01, which is a dual port memory. i.e. different values can be fed to its input and output.

#### Pass 2

On the second pass, 13 bits of the result of the first pass and the upper 12 bits of the Scale Table Matrix are multiplied.

![Circuit002_logic2](/imgstore/mdec/Circuit002_logic2.jpg)

Inputs:
- First pass result: 13 bits
- Scale Table Matrix input: upper 12 bits
- Sum of previous calculation step: 17 bits

The circuit multiplies the 2 inputs again and sums the multiplication with the result of the previous calculation step. 17 bits of the result are fed back to the circuit.

#### Division by 2

At the end of the calculation, the highest 10 bits of the result are sent to the sign division circuit by 2 with clamping -128, 127. The output is 8 bits with a sign.

![Circuit002_logic3](/imgstore/mdec/Circuit002_logic3.jpg)

![002_conv](/imgstore/mdec/002_conv.png)

#### IDCT Control

There are three independent counters with control outputs that are connected by different logical operands. The output lines control the trigger clocks in the first and second pass as well as the address lines of the UNITs 0/1.

![Circuit002_logic4](/imgstore/mdec/Circuit002_logic4.jpg)
