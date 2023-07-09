# PSXCPU Terminals

The processor has very many terminals - 208. This is due to the large number of buses.

There are even more inside because the VDD/VSS from one pin come to 2 paired pads at once.

## Power Distribution & Bonding

The power and ground wraps around the edges of the processor and also crosses it crosswise, so we call the central area "the crosshair".

Slightly to the right of the crosshair is the vertical Main CLK distribution across the entire chip.

![IC103_contacts](/imgstore/pads/IC103_contacts.jpg)

## Pinout

![PSXCPU_pads_service_manual](/imgstore/pads/PSXCPU_pads_service_manual.jpg)

## Signals

|Signal/Group|Direction|Description|
|---|---|---|
|**CPU Timing and Reset**|||
|CRYSTALN|N.C.|input CLK (N)|
|CRYSTALP|input|input CLK (P), 67.73 MHz <br/>![CPU_clk](/imgstore/pads/CPU_clk.jpg)|
|SYSCLK0|output|CLK to GPU input (33.3 MHz)|
|SYSCLK1|output|CLK on Sub bus devices (33.3 MHz)|
|DSYSCLK|output|CLK \* 2 to GPU input (66.67 MHz NTSC, 64.5 MHz PAL)|
|/EXT_RESET|input(?)|reset (comes from the common signal RES3.3 from the power supply)|
|**PIO**|||
|/CS0|output|Select PIO on the sub bus. Only one device can be connected to the bus at a time to avoid bus conflicts. This is controlled by the signals in the "CS" (chip select) group.|
|DREQ5|input|PIO DMA|
|DACK5|output|PIO DMA| 
|/INTIN10| |combined with controllers/memory cards and PIO (common interrupt signal)|
|**SIO**|||
|RXD1| | |
|TXD1| | |
|/DSR1| | |
|/DTR1| | |
|/CTS1| | |
|/RTS1| |conventional serial interface|
|**Controllers/memory cards**|||
|/SCK0| |timing for controllers (?)|
|RXD0| | |
|TXD0| | |
|/DSR0| | |
|/DTR0A| | |
|/DTR0B| |serial interface. DTR has two pins because we have two ports for controllers/memory cards (Port A and Port B); Represents a variation of the SIO. <br/>![Front_jack](/imgstore/pads/Front_jack.jpg)|
|**DRAM**|||
|DD|inout|32-bit DRAM data bus. Designed for data transfer between CPU and DRAM|
|DA|output|13 bit DRAM Address bus. The CPU sets the address to access the DRAM; <br/>![DA0_routing](/imgstore/pads/DA0_routing.png)|
|/DWE|output|0: DRAM write enable|
|/DRAS0|output| |
|/DRAS1|output| |
|/DCAS0|output| |
|/DCAS1|output| |
|/DCAS2|output| |
|/DCAS3|output|refresh|
|**ROM BIOS + Sub Bus**|||
|/CS2|output|Connect the ROM to the Sub bus. Direct data exchange (OE) is enabled if the read enable signal (/SRD) is active|
|/SWR0, /SWR1|output|Sub bus write enable. SWR0 is for internal devices, as well as for PIO, but SWR1 is exclusive to PIO for some reason|
|/SRD|output|Sub bus read enable. The ROM is connected to the sub bus.|
|SA|output|24-bit Sub address bus|
|SD|inout|16-bit Sub data bus|
|**GPU Interface**|||
|VD|inout|32-bit data bus, connected to Main bus|
|VA2|output|address bus, 1-bit :) The thing is that the GPU only has two 32-bit registers (GP0/GP1), therefore one address line is enough for addressing them|
|/VRD|output|0: Video read enable|
|/VWR|output|0: Video write enable|
|/CS7|output|Select GPU|
|DREQ2|input|GPU DMA|
|DACK2|output|GPU DMA. When a direct memory access controller is used to transfer data, the GPU sends a bus-capture request signal by setting the logic low on the GPUDREQ pin. Upon receiving such a request, the processor releases the buses, signaling this by setting a low logic level on the GPUDACK output|
|/INTIN0|input|GPU VBLANK interrupt signal|
|TCLK0|input|comes out of the GPU PCK (pixel clock) pin, can be used as Root Counter 0 (point counter)|
|TCLK1|input|comes out from GPU HBLANK pin, can be used as Root Counter 1 (horizontal line counter)|
|/INTIN1|input|general interrupt of the GPU (can be caused by sending a special command to the GPU, but as far as I know is not used in games)|
|**CD-Controller Interface**|||
|/CS5|output|Select CD-controller|
|/INTIN2|input|interrupt from CD-controller|
|**SPU Interface**|||
|/CS4|output|Select SPU|
|/INTIN9|input|interrupt from SPU|
|/DREQ4|input|SPU DMA|
|DACK4|output|SPU DMA|
|**Test/Misc**|||
|/CSHTST| |Unknown|
|/RC_NET| |Unknown|

## Terminal Schematics

Below is a schematic of all the terminal designs.

:warning: Put the section in order, deassociate the terminal from the names (DD/DA), assemble all the topology of all the terminal designs.

### Output Terminal

The pin has 1 wire (the actual address bit).

The pin is simple. First the DA is fed to the push-pull inverter chain, for pre-amplification:

![DA1](/imgstore/pads/DA1.jpg)

Then the value in inverted form is fed to the power inverter, which goes straight to the pin.

![DA2](/imgstore/pads/DA2.jpg)

The picture shows only one half of the "comb" of the inverter, the other half didn't make it into the slides the decapper did. The approximate design of the inverter is the same as the DD bus pin, except that there is no input wire and no ESD.

### Inout + TriState Terminal

The DD bus inner pins are routed to 6 ports:

![DD_pad](/imgstore/pads/DD_pad.jpg)

- WR=1 when write cycle (CPU outputs data outward)
- RD=1 when reading loop (CPU gets data outward). I'm not quite sure what it is RD, it could be for example the RES=1 reset signal or something else that keeps the bus from working outward. Anyway, the bus only works outward when the combination ab = 10.
- CPU/DD(n): data to output to the bus
- DD(n)/CPU: read data
- e/f: the purpose is not yet clear. These lines connect the pins one by one (F comes from the previous pin and E goes to the F input of the next one). The very first F and the very last E go somewhere far into the depths of the CPU. Probably some kind of dirty circuit, to check the input data for 0.

Note: not all transistors are used in the amplifier "comb" at the output, most are disconnected. Also, the input hose connects to some demonic pieces of diffusion with the transistors disconnected, most likely this is static protection (ESD). Another ESD element is an inverter with no output (diode?)
