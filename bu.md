# Bus Interface Unit

Прежде чем изучать управляющие сигналы, нужно отметить, что CPU работает с двумя шинами, которыми управляет bus unit (B/U):

- Main bus: проходит внутри процессора, а также соединяется с [[CPU-RAM|DRAM]] и [[GPU]]
- Sub bus: на этой шине располагаются все устройства подсистемы (ROM BIOS, [[SPU]], [[CD-ROM]]) и параллельный порт (который на самом деле тоже является своего рода внешним устройством)
- /SWR0, /SWR1: Sub bus write enable. SWR0 предназначен как для внутренних девайсов Sub bus, так и для PIO, а вот SWR1 подается почему-то эксклюзивно только на PIO.
- /SRD: Sub bus read enable.

<br>
'''PIO''':
* /CS0 : Выбрать PIO на Sub bus. Одновременно на шину может быть подключен только один девайс, во избежание конфликта шин. Этим управляют контакты группы "CS" (chip select).
* DACK5 / DREQ5 : PIO DMA
* /INTIN10 : контакт совмещен с контроллерами/картами памяти и PIO (общий сигнал прерывания).
<br>
'''SIO''':
* RXD1, TXD1, /DSR1, /DTR1, /CTS1, /RTS1 : классический последовательный интерфейс
<br>
'''Контроллеры/карты памяти''': представляют собой вариацию SIO
* /SCK0 : тайминг для контроллеров (строб?)
* RXD0, TXD0, /DSR0, /DTR0A, /DTR0B : последовательный интерфейс. DTR имеет два контакта, поскольку у нас два порта для контроллеров/карт памяти (Port A и Port B)<br>
[[File:Front jack.jpg|300px]]
<br>
'''DRAM''': в более новых материнках оперативная память представляет собой один чип (IC106), но раньше их было больше (4). Мы будем ориентироваться на более новый вариант, потому что это удобно.
* DD : шина данных 32-бит
* DA : адресная шина 10-бит
* /DWE : write enable
* /DRAS0, /DCAS0, /DCAS1, /DCAS2, /DCAS3 : рефреш
<br>
'''ROM BIOS''': ROM подсоединен к Sub bus.
* /CS2 : Подключить ROM к шине Sub bus. Непосредственно обмен данным (OE) включается, если активен сигнал read enable (/SRD). Ну это понятно, ROM можно только читать :)
<br>
'''[[GPU]]''':
* VD : шина данных 32-бит, подключена к Main bus
* VA2 : адресная шина, 1-бит :) Дело в том, что у GPU всего-лишь два 32-разрядных регистра (GP0/GP1), поэтому для их адресации достаточно одной адресной линии.
* /VRD, /VWR : read/write enable
* /CS7 : Выбрать GPU
* DREQ2, DACK2 : GPU DMA. Когда для передачи данных используется контроллер прямого доступа к памяти, графический 
процессор посылает сигнал запроса на захват шины, устанавливая низкий логический уровень на контакте GPUDREQ. Получив такой запрос, процессор освобождает шины, извещая об этом установкой низкого логического уровня на выходе GPUDACK.
* /INTIN0 : сигнал прерывания GPU VBLANK
* TCLK0 : выходит с контакта GPU PCK (pixel clock), может использоваться в качестве Root Counter 0 (счетчик точек)
* TCLK1 : выходит с контакта GPU HBLANK, может использоваться в качестве Root Counter 1 (счетчик горизонтальных линий)
* /INTIN1 : общее прерывание GPU (может быть вызвано отправкой специальной команды в GPU, но насколько мне известно в играх не используется)
<br>
'''[[CD-ROM]]''': 
* /CS5 : выбрать CD-контроллер
* /INTIN2 : прерывание от CD-контроллера
<br>
'''[[SPU]]''':
* /CS4 : выбрать SPU
* /INTIN9 : прерывание от SPU
* DACK4, /DREQ4 : SPU DMA
<br>
'''Тайминг и сброс CPU''':
* CRYSTALP : входной CLK, 67.73 MHz
* SYSCLK0 : CLK на вход GPU (33.3 MHz)
* SYSCLK1 : CLK на девайсы Sub bus (33.3 MHz)
* DSYSCLK : CLK * 2 на вход GPU (66.67 MHz NTSC, 64.5 MHz PAL)
* /EXT RESET : сброс (приходит с общего сигнала RES3.3 от блока питания)
[[File:CPU clk.jpg]]
<br>

== Hardware registers ==

Маппинг регистров внутри CPU: 0x1F801000-0x1F80????

===Bus Unit control===
* 0x1f801000
* 0x1f801004
* 0x1f801008
* 0x1f80100c
* 0x1f801010
* 0x1f801014	- spu_delay
* 0x1f801018	- dv5_delay
* 0x1f80101c
* 0x1f801020	- com_delay
* 0x1f801060	- ram_size

===SIO0===
* 0x1f801040	- sio0_data
* 0x1f801044	- sio0_status
* 0x1f801048	- sio0_mode
* 0x1f80104a	- sio0_control
* 0x1f80104e	- sio0_baud

===SIO1===
* 0x1f801050	- sio1_data
* 0x1f801054	- sio1_status
* 0x1F801058	- sio1_mode
* 0x1f80105a	- sio1_control
* 0x1f80105e	- sio1_baud

===INTC===
* 0x1f801070	- int_reg
* 0x1f801074	- int_mask

===DMAC===
* 0x1f801080	- mdec_dma0_madr
* 0x1f801084	- mdec_dma0_bcr
* 0x1f801088	- mdec_dma0_chcr
* 0x1f801090	- mdec_dma1_madr
* 0x1f801094	- mdec_dma1_bcr
* 0x1f801098	- mdec_dma1_chcr
* 0x1f8010a0	- gpu_dma_madr
* 0x1f8010a4	- gpu_dma_bcr
* 0x1f8010a8	- gpu_dma_chcr
* 0x1f8010b0	- cd_dma_madr
* 0x1f8010b4	- cd_dma_bcr
* 0x1f8010b8	- cd_dma_chcr
* 0x1f8010c0	- spu_dma_madr
* 0x1f8010c4	- spu_dma_bcr
* 0x1f8010c8	- spu_dma_chcr
* 0x1f8010d0	- dma5_madr
* 0x1f8010d4	- dma5_bcr
* 0x1f8010d8	- dma5_chcr
* 0x1f8010e0	- dma6_madr
* 0x1f8010e4	- dma6_bcr
* 0x1f8010e8	- dma6_chcr
* 0x1f8010f0	- dma_pcr
* 0x1f8010f4	- dma_icr

===ROOT COUNTERS===
* 0x1f801100	- t0_count
* 0x1f801104	- t0_mode
* 0x1f801108	- t0_target
* 0x1f801110	- t1_count
* 0x1f801114	- t1_mode
* 0x1f801118	- t1_target
* 0x1f801120	- t2_count
* 0x1f801124	- t2_mode
* 0x1f801128	- t2_target
* 0x1f801130	- t3_count?
* 0x1f801134	- t3_mode?
* 0x1f801138	- t3_target?

===CDROM (outside)===
* 0x1f801800	- cdrom_reg0
* 0x1f801801	- cdrom_reg1
* 0x1f801802	- cdrom_reg2
* 0x1f801803	- cdrom_reg3

===GPU (outside)===
* 0x1f801810	- gpu_reg0
* 0x1f801814	- gpu_reg1

===[[MDEC]]===
* 0x1f801820	- mdec_reg0
* 0x1f801824	- mdec_reg1

===SPU (outside) ===
* (0x1f801c00-0x1f801dff)

===DEBUG ???===
* 0x1f802030	- int_2000
* 0x1f802040	- dip_switches
