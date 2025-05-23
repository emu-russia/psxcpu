# PSXCPU

Reverse engineering of the SONY PlayStation CPU (CXD8530CQ).

:warning: The information was moved from the old wiki in Russian. Information may need to be clarified here and there.

:warning: A number of sections and repository contents may be redundant because the repository was built from different sources (site, old wiki, forum, etc.). Over time, everything will fall into place as it should.

## IC103 Overview

The CPU consists of the following components:

- Slightly modified [LSI CW33300 core](core.md)
- System coprocessor 0 ([COP0](cop0.md))
- System coprocessor 2 aka Geometry Transformation Engine ([GTE](gte.md))
- Motion Decoder aka [MDEC](mdec.md) (JPEG-like video decoder)
- DMA controller ([DMAC](dmac.md)) (7 channels)
- Interrupt controller (INTC)
- DRAM controller ([DRAMC](dramc.md))
- [Bus controller](biu.md) (interface for ROM BIOS / GPU)
- [SIO](sio.md) controller (RS-232-like serial interface), for two ports (SIO0 and SIO1)
- [PIO](pio.md) controller (expansion port for additional devices)
- [Root counters](rcnt.md) (3 hardware counters)
- Built-in instruction cache and data cache (data cache with direct access capability aka "ScratchPad")
- Dedicated "mini-caches" (R-buffer and W-buffer)

Microphotograph of a chip:

![Cpu_overview](/imgstore/Cpu_overview.jpg)

As you can see most of the chip is taken up by the "mess" of synthesized HDL logic (see [Cells](cells.md)), and at the edges there are various memory and registers (see [Custom Blocks](custom.md)).

From the available documents of the mid-90s we know that PSXCPU is most likely based on the LSI Logic framework called `CoreWare`. The MDE or C-MDE program was most likely used as the EDA.

## CPU Revisions

The CPU on all revisions of the motherboard is represented by one large chip with 208 pins, under the designation IC103.

|PCB|older PU-7(?)|PU-7|older PU-8|newer PU-8|PU-18|PU-20|PU-22|PU-23|PM-41|PM-41(2)|
|---|---|---|---|---|---|---|---|---|---|---|
|IC103| ??? |![CXD8530BQ_package](/imgstore/CXD8530BQ_package.jpg)|![8530BQ_PU8_package](/imgstore/8530BQ_PU8_package.jpg)|![8530CQ_package](/imgstore/8530CQ_package.jpg)|![CXD8606AQ_package](/imgstore/CXD8606AQ_package.jpg)|![CXD8606BQ_package](/imgstore/CXD8606BQ_package.jpg)|![8606BQ_PU22_package](/imgstore/8606BQ_PU22_package.jpg)|![8606BQ_PU23_package](/imgstore/8606BQ_PU23_package.jpg)|![8606BQ_PM41_package](/imgstore/8606BQ_PM41_package.jpg)|![CXD8606CQ_package](/imgstore/CXD8606CQ_package.jpg)|
||8530AQ?|8530BQ|8530BQ|8530CQ|8606AQ|8606BQ|8606BQ|8606BQ|8606BQ|8606CQ|
||???|L9A0025|L9A0025|L9A0048|L9A0082|L9B0082|L9B0082|L9B0082|L9B0082|L9A0182|

- The very first Japanese consoles (SCPH-1000 / PU-7) and old versions of PU-8 came with revision 90025.
- Then they were quickly replaced by newer consoles (there was some bug in MDEC) which already had the revision 90048 chip.
- In consoles since SCPH-5500 (PU-18) the 90082 revision of the chips were added. These chips were present in all latest PSX models, also in the first version of PSOne motherboards (PM-41).
- The latest versions of PSOne with PM-41(2) motherboards contained 90182 revision of the chip.
- EDIT: Turns out that the oldest revisions of the PU-7 contain some old version of the chip, most likely the one in the pictures from Ken Kutaragi below (3 layers of metal, obvious separation of modules into rectangular regions).

On this site we are examining the `90048` revision (which was in SCPH-1001). It is likely that the new revisions differ significantly in M1/M2 wiring as the new revision chip is "reassembled" from Verilog/VHDL. So, to trace other revisions means to re-trace the whole processor %)

You can find out the revision of the chip from the marking on the cover, removing unnecessary letters (e.g. L9A0048 means revision 90048). There must be some sense in the letters, but most likely it is related to the improvement of the technological process.

There is also a tattoo on the lower right corner of the chip. The revision of the chip is indicated in the first line:

![cpurev_90048](/imgstore/cpurev_90048.jpg)

This document by Ken Kutaragi (http://www.hotchips.org/wp-content/uploads/hc_archives/hc11/2_Mon/hc99.k1.kutaragi.pdf) shows the old architecture compared to the one we are studying, though the second picture is on the side :smiley:

![old_silicon](/imgstore/old_silicon.jpg)

From the picture you can conclude roughly the location of the main components (the location of MDEC, GTE and instruction cache are exactly the same).

Also there is information about number of transistors in final revision of CPU - 850K (2 layers Al, 350 nm). The old architecture contains 1000K transistors and 3 layers of metal.

## CPU Block Diagram

We will be guided by the picture from the PU-22 (SCPH-7500) service manual when the CPU was most fully connected to the other parts.

Starting with PU-23 (SCPH-9000) the parallel port (PIO) was taken away from it, and in PM-41 (PSOne) the serial port (SIO) was also taken away.

![CPU_Block](/imgstore/CPU_Block.jpg)
