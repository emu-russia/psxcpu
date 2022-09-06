# Netlist

This section is about restoring netlist connections of standard cells, custom units and PSXCPU terminals.

## Master Dataset

As a reference dataset we should choose a more or less complete image of the chip, which will be used to combine the standard cells and the netlist separately.

Let's choose this one: https://drive.google.com/drive/u/1/folders/1gm7yAS0Q0RMlnaueThbbUc0msHZU09Ft

## Method

A semi-automatic approach is used for wire reconstruction:

![method](/imgstore/netlist/method.png)

- Drawing all the wires by hand is not physically possible, or it would take a gazillion hours.
- Instead, the outermost vias of the wire are drawn and automatically joined as a wire by a script
- If the wire has "dog legs", only the "patch" (single segment) is put on the preset
- If a wire has intermediate vias, they are marked as `x`, so that the wire recovery process is not interrupted on these vias

## Progress

Not much so far.

![progress](/imgstore/netlist/progress.png)
