# Main Clock

To the right of the crosshair is one thick wire, which is the `mclk` signal distribution (we think so).

At the top and bottom are two special blocks, obviously designed to distribute the amplified CLK across the entire surface of the chip.

![mclk_distrib](/imgstore/mclk_distrib.jpg)

Top mclk special block:

|Poly|M1|M2|
|---|---|---|
|![mclk_top_poly](/imgstore/custom/mclk_top_poly.jpg)|![mclk_top_m1](/imgstore/custom/mclk_top_m1.jpg) ![mclk_top_m1(2)](/imgstore/custom/mclk_top_m1(2).jpg)|TBD|

Bottom mclk special block (presumably symmetrical to the upper one, but it is necessary to compare Datasets):

|Poly|M1|M2|
|---|---|---|
|TBD|![mclk_bot_m1](/imgstore/custom/mclk_bot_m1.jpg)|TBD|

In large scale chips, the CLK layout is usually called the `Clock Tree`: multiple clusters of DFF and other `always (@CLK)` sensitive blocks are symmetrically connected to a common CLK wire to minimize the Jitter effect.

![mclk_closeup](/imgstore/mclk_closeup.jpg)
