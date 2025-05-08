# Main Clock

To the right of the crosshair is one thick wire, which is the `mclk` signal distribution (we think so).

At the top and bottom are two special blocks, designed to distribute the amplified CLK across the entire surface of the chip.

![mclk_distrib](/imgstore/mclk_distrib.jpg)

Top mclk super buffer:

|Poly|M1|M2|
|---|---|---|
|![mclk_top_poly](/imgstore/custom/mclk_top_poly.jpg)|![mclk_top_m1](/imgstore/custom/mclk_top_m1.jpg) ![mclk_top_m1(2)](/imgstore/custom/mclk_top_m1(2).jpg)|![mclk_top_m2](/imgstore/custom/mclk_top_m2.jpg)|

Bottom mclk super buffer (symmetrical to the upper one):

|Poly|M1|M2|
|---|---|---|
|TBD|![mclk_bot_m1](/imgstore/custom/mclk_bot_m1.jpg)|![mclk_bot_m2](/imgstore/custom/mclk_bot_m2.jpg)|

![mclk](/imgstore/mclk.png)

The outputs from the two super buffers are short-circuited and feed the main mclk rail.

In large scale chips, the CLK layout is usually called the `Clock Tree`: multiple clusters of DFF and other `always (@CLK)` sensitive blocks are symmetrically connected to a common CLK wire to minimize the Jitter effect.

![mclk_closeup](/imgstore/mclk_closeup.jpg)
