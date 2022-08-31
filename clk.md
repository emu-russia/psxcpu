# Main Clock

To the right of the crosshair is one thick wire, which is the `mclk` signal distribution (we think so).

At the top and bottom are two special blocks, obviously designed to distribute the amplified CLK across the entire surface of the chip.

![mclk_distrib](/imgstore/mclk_distrib.jpg)

In LSI chips, the CLK layout is usually called the `Clock Tree`: multiple clusters of DFF and other `always (@CLK)` sensitive blocks are symmetrically connected to a common CLK wire to minimize the Jitter effect.

![mclk_closeup](/imgstore/mclk_closeup.jpg)
