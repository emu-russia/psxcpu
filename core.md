# CW33300 Integrated Core

The CPU includes the LSI CoreWare CW33300 core. The core is compatible with the R3000 processor instruction set.

Naturally, it is evenly spread over the chip, along with other components :smiley:

It is known (from tests and hacker docs) that coprocessor 0 has a simplified MMU, with the virtual address being converted to a physical address simply by zeroing out the three highest bits.

![R3000Core](/imgstore/core/R3000Core.jpg)

## I-Cache

- I-Cache is 4k long
- Divided into 16-byte "lines"
- 4 instruction per line 
- Each line has a "tag" indicating which physical address is cached
- Instruction reads check tag to see if code is in cache
- Code position in I-cache depends on low-order 12-bits of address
- Code may wrap from end of cache to beginning
- High nybble of instruction address controls if code is cacheable
- Instruction reads from cache take 1 tick

If instruction not in cache, the appropriate cache line is burst filled from RAM:
- Takes 4-7 ticks to read from RAM
- Plus 1 tick to transfer from cache to R3000
- Reads entire line in 1 burst

Code may wrap from end of cache to beginning withot penalty. Execution continues at start of cache.
- Memory Addresses: 0x80051F34 ... 0x80052233
- Cache Addresses: 0xF34 ... 0xFFF -> wrap -> 0x000 ... 0x233

High nybble of instruction address controls if code is cacheable:
- 0x8 & 0x0 - Cacheable
- 0xA - Not Cacheable

## D-Cache

- D-Cache (aka Scratchpad) is 1K long
- Memory mapped : address range 0x1F800000 ... 0x1F8003FF
- 1 tick read/write access.
- RAM writes normally 3 to 6 ticks
- RAM reads normally 2 to 6 ticks
- Cannot be used for DMA transfers

## Memory Page Faults

Memory pages are 1k long, on 1k boundaries:
- 0x000 ... 0x3FF, 0x400 ... 0x7FF etc.

Page faults occur when you read or write a different page from the previous read / or write operation.
Burst reads to I-cache can cause page fault.
D-cache reads & writes don't cause page fault.

Page faults slow down RAM Reading access:
- 1st read from particular page in RAM takes 5 ticks: 4 ticks for RAM to R3000 R-buffer, 1 tick R-buffer to CPU
- 2nd & subsequent reads take just 2 ticks : 1 tick for RAM to R3000 R-buffer, 1 tick R-buffer to CPU.
- True for both data reads and I-cache burst read.

Page faults slow down RAM Writing access:
- 1st write to a particular page in RAM takes 5 ticks: 1 tick for CPU to R3000 W-buffer, 4 ticks W-buffer to RAM
- 2nd & subsequent writes take just 3 ticks: 1 tick for CPU to R3000 W-buffer, 2 ticks W-buffer to RAM

R3000 CPU has "Write-buffer" between registers and main RAM. W-buffer
is four step 32bit length fifo. It takes one cycle to write to
W-buffer. But if there are no free register on W-buffer, CPU must
flush W-buffer, write ALL the data on W-buffer to main RAM.
It takes one or four cycles to write a data on W-buffer to main
RAM. If two or more write operations are done continuously, the first
operation takes four cycles and the second and later operations take
only one cycle.

And main RAM has 1KByte "pages". Any write operation on new page takes
four cycles. And programmer cannot control or detect the W-buffer
flush timing, and cannot know the status of it. R3000 has
Bus-Snoop-Mechanism and I-cache. You cannot predict the start of
flushing of W-buffer, even if you know the complete assembler codes.
Each step of W-buffer is assigned to one store instruction. So if
four Store-Byte-Instructions are executed, W-buffer becomes full.
