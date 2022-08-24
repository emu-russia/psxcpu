# PSXCPU Cells Library

PSX CPU is based on standard cells.

Standard cells are placed by rows. Connections between cells are made using two layers of metal. (Channel Router)

Thus M1 runs parallel to the rows of cells, and M2 (the top layer) - perpendicularly.

Quite often automatic router place route directly on the area of the cell, if there exist free space for it:

![row_m1.jpg](/imgstore/cells/row_m1.jpg)

Ground and power supply are also by M1.

M1 is also sometimes used for the connection between adjacent cells, just inside the row of cells, without going beyond its boundaries.

Cells under a microscope look like this:

![Cells.jpg](/imgstore/cells/Cells.jpg)

Standard cell patterns may be rotated by 180<sup>o</sup>, as the power and ground in alternating rows changes, due to the fact that the cells are stacked zigzag.

In some cases, the cell is also mirrored relative to a vertical axis. It seems that fitter tool has a feedback from wire router. Wire routrer (if it is more convenient) can give fitter signal flip cell to wire it easier to get to its input/outputs.

## PSX CPU Standard cell library

PSX CPU built on CMOS technology. P and N channel transistors differ in size (P-doping is 1.3-1.5 times thicker). Diffusion is shown in yellow, and the polysilicon as purple. P-type diffusion is usually thickier rather diffusion of N-type, and is closer to the power supply. Accordingly, the diffusion of N-type is usually close to the ground.

In addition to standard NAND, NOR, and so forth, modern logic includes such elements as AO, AOI, OA and the OAI. This variation of And / Or operation with an additional result inversion (I). examples:
- 22-AOI: Y = ~ (AB | CD)
- 33-OA: Y = ( (A|B|C) & (D|E|F) )

That is, the first letter indicates what operation is used within the groups, and the second letter indicates what operation is used between groups. Before operation, the number of items placed into each group.

For convenience we have divided the types of categories and assigned them a cell color:

![Cell_types.jpg](/imgstore/cells/Cell_types.jpg)

At the moment, we made the identification of almost all cells. PSX CPU contains about 37,600 cells of 150 different types.

Cells Heatmap:

![Psxcpu_cells_map_sm.jpg](/imgstore/cells/Psxcpu_cells_map_sm.jpg)

## Cells By Category

Further description of the cell is divided into categories for easier reference.

### Inverters

### Buffers

### Multiplexers

### Logic elements OR

### Logic elements NOR

### Logic elements AND

### Logic elements NAND

### Logic elements XOR/XNOR

### Composite logic (AO, OA, NAND+NOR etc)

### Triggers (synchronous memory elements)

### Latches (asynchronous memory elements)

### Cells for the multiplier

### Cells for use with buses (Bus keeper, Tri-states)

## Lambda cell parameters

Each standard cell library have so-called lambda parameters. They define in units of cells ratio of areas: how much place will take P-diffusion, how much takes the gap between the P- and N- diffusion regions, what size of wires and how they can pass through the cell when installing routing and so forth.

The unit of lambda adopted thickness of the thin element, in our case - is the thickness of polysilicon tracks.

Calculate lambda parameters of some cell.

First, we find the thickness of the polysilicon, as the average sum:

![Lambda_avg_poly.jpg](/imgstore/cells/Lambda_avg_poly.jpg)

Then we measure the width of the different parts of the cell and divide by the thickness of the polysilicon. We get the lambda values:

![Lamda_parts.jpg](/imgstore/cells/Lamda_parts.jpg)

- The width of the P-diffusion 13 lambda
- The width of the gap 7 lambda
- The width of the N-diffusion 11 lambda

Lambda parameters allow you to accurately describe the cell and can be used for example when zooming cells in specific cell research programs.

:warning: Does this section even make sense? Anyone who studies standard cells first learns about the lambda parameter.

## Pockets

PSXCPU uses P-pockets. For a long time it was not clear at all whether they are there (obviously they should be). But they are practically not visible and appear only when lapping the chip:

![pocket.jpg](/imgstore/cells/pocket.jpg)

On the domestic side this knowledge is of little use when studying the cells (it is already clear where the P-MOS FETs are), but when studying [Custom Blocks](custom.md) it is very important, because there is a whole mess of N and P. Somewhere you can guess, but there are places where without visible pockets is very difficult.
