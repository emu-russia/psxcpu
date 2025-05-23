# Bus Interface Unit

:warning: According to the old architecture photos there are two bus modules inside PSXCPU - one for external buses and one for internal buses and cache. The suspicion that the module for handling internal buses and cache is actually COP0. But so far it is not very clear, so I will leave this note here.

16- and 32- bit bus controller.

The CPU has two buses which are controlled by the bus unit (B/U):
- Main bus: runs inside the CPU and also connects to the DRAM and GPU
- Sub bus: on this bus all the subsystem devices (ROM BIOS, SPU, CD-ROM) and the parallel port (which is in fact also a kind of external device) are located.

## Hardware Registers

Mapping registers inside the CPU: 0x1F801000-0x1F80????

There must be a decoder somewhere.

|Register/group|Description|
|---|---|
|Bus Unit control| |
|0x1f801000| |
|0x1f801004| |
|0x1f801008| |
|0x1f80100c| |
|0x1f801010| |
|0x1f801014|spu_delay|
|0x1f801018|dv5_delay|
|0x1f80101c| |
|0x1f801020|com_delay|
|0x1f801060|ram_size|
|SIO0| |
|0x1f801040|sio0_data|
|0x1f801044|sio0_status|
|0x1f801048|sio0_mode|
|0x1f80104a|sio0_control|
|0x1f80104e|sio0_baud|
|SIO1| |
|0x1f801050|sio1_data|
|0x1f801054|sio1_status|
|0x1F801058|sio1_mode|
|0x1f80105a|sio1_control|
|0x1f80105e|sio1_baud|
|INTC| |
|0x1f801070|int_reg|
|0x1f801074|int_mask|
|DMAC| |
|0x1f801080|mdec_dma0_madr|
|0x1f801084|mdec_dma0_bcr|
|0x1f801088|mdec_dma0_chcr|
|0x1f801090|mdec_dma1_madr|
|0x1f801094|mdec_dma1_bcr|
|0x1f801098|mdec_dma1_chcr|
|0x1f8010a0|gpu_dma_madr|
|0x1f8010a4|gpu_dma_bcr|
|0x1f8010a8|gpu_dma_chcr|
|0x1f8010b0|cd_dma_madr|
|0x1f8010b4|cd_dma_bcr|
|0x1f8010b8|cd_dma_chcr|
|0x1f8010c0|spu_dma_madr|
|0x1f8010c4|spu_dma_bcr|
|0x1f8010c8|spu_dma_chcr|
|0x1f8010d0|dma5_madr|
|0x1f8010d4|dma5_bcr|
|0x1f8010d8|dma5_chcr|
|0x1f8010e0|dma6_madr|
|0x1f8010e4|dma6_bcr|
|0x1f8010e8|dma6_chcr|
|0x1f8010f0|dma_pcr|
|0x1f8010f4|dma_icr|
|ROOT COUNTERS| |
|0x1f801100|t0_count|
|0x1f801104|t0_mode|
|0x1f801108|t0_target|
|0x1f801110|t1_count|
|0x1f801114|t1_mode|
|0x1f801118|t1_target|
|0x1f801120|t2_count|
|0x1f801124|t2_mode|
|0x1f801128|t2_target|
|CDROM| |
|0x1f801800|cdrom_reg0|
|0x1f801801|cdrom_reg1|
|0x1f801802|cdrom_reg2|
|0x1f801803|cdrom_reg3|
|GPU| |
|0x1f801810|gpu_reg0|
|0x1f801814|gpu_reg1|
|MDEC| |
|0x1f801820|mdec_reg0|
|0x1f801824|mdec_reg1|
|SPU| |
|0x1f801c00-0x1f801dff| |
|DEBUG ???| |
|0x1f802030|int_2000|
|0x1f802040|dip_switches|
