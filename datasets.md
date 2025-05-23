# CXD8530CQ Source Data Sets

## Raw

This section contains raw datasets, straight from the microscope (usually over-100500 slides).

Initial datasets are done by:
- The first dataset was made by Mikhail @zeptobars (M2, poly at 40x). Acid etching was used to obtain the dataset on poly, so the photos contain a lot of debris (bits of wires, dirt), but nevertheless all the necessary information can be extracted from it.
- The second set of M2 and M1 datasets was made by an anonymous decaper

Datasets:

- M2, fine focus, a little bit of M1 is visible (by @zeptobars): http://psxdev.ru/download
- M2, bad focus (but M1 is visible): https://drive.google.com/file/d/1PQ1_PY1s871K0953p8fsvvdBlgYIJ0lY/view?usp=sharing
- M2, good focus (but can't see M1): https://drive.google.com/file/d/1QtGmelIh61QB_D9XsH0mtG8ihhqs0mwi/view?usp=sharing
- M1, first lapping session, 20x (practically useless): https://drive.google.com/file/d/1mpvKIXQzu-QPdkQ9XsWjDienH57-AmVj/view?usp=sharing
- M1, first lapping session, 50x: https://drive.google.com/file/d/1D3nZWLGd1eeeYGkYUtsVy9-yvPFNJW43/view?usp=sharing
- M1, second lapping session, 50x: https://drive.google.com/file/d/1dDq4yHg06ymJxQr2D6T92aebb-Ppf0oI/view?usp=sharing
- M1, third lapping session, 50x: https://drive.google.com/file/d/1n-nlIoNrGA6GH2iUMan6el_Z8MQ5GM9d/view?usp=sharing
- Active area (poly), 40x, lots of garbage (by @zeptobars): https://drive.google.com/file/d/1RY8jimW_SvzkweqpLAbkgV9KUVVg6Gvs/view?usp=sharing

## Cooked

This section contains more or less processed datasets.

### M2 Fused

A stitched M2 that can be used to rebuild a netlist.

![m2_Fused_sm](/imgstore/m2_Fused_sm.jpg)

https://drive.google.com/drive/u/1/folders/1gm7yAS0Q0RMlnaueThbbUc0msHZU09Ft

### M1 Lap1 Fused

|20X|50X|
|---|---|
|![lap1_20x_Fused_sm](/imgstore/lap1_20x_Fused_sm.jpg)|![lap1_Fused_sm](/imgstore/lap1_Fused_sm.jpg)|

https://drive.google.com/drive/u/1/folders/1BWaV7qeUzVNYJ-a94BVtwIwGwX7uTTBI

### M1 Lap2 Fused

![lap2_Fused_sm](/imgstore/lap2_Fused_sm.jpg)

https://drive.google.com/drive/u/1/folders/1Bs6g3hyMySYPuSzomyJ62MnTsNKNFTE7

### Cell Interconnects

Within the same row, cells are often directly connected to each other using M1.

![row_m1](/imgstore/cells/row_m1.jpg)

To make it easier to navigate, all the rows of cells with M1 were stitched together. We got such long "sausages".

https://drive.google.com/drive/u/1/folders/1RRScSK1xoU807RtxnDKJNyQKCcT8BQb3

### Missing M1

Missing parts of M1 that, for some reason, did not make it into the original M1 datasets.

For the most part, this applies to the right side of the processor (3 right rows of cells), which immediately lapped down to "meat".

![lapping_disaster](/imgstore/lapping_disaster.jpg)

https://drive.google.com/drive/u/2/folders/1oexmDc3mREyYFzV5wCQ7gH5R2jsMz9bk

### Poly Stitched

Stitched areas with standard cells.

![poly_stitched](/imgstore/poly_stitched.jpg)

https://drive.google.com/drive/u/2/folders/1fDKUR1nrEk5RCaYHKYP0jsDJLMzKCZ7X

### active_sequenced_images

These pictures were used to get a list of cells in the folder `/cells/active_sequenced` (for WRK files).

In essence, it is a chopped up dataset `PolyStitched`.

![legend](/cells/active_sequenced/legend.jpg)

https://drive.google.com/drive/u/1/folders/1ytoTS_vl4F2xwJpaPSVvHei9QjYtd-Dr

### DCache (ScratchPad)

DCache Topology.

|![DCache_m2_Fused_small](/imgstore/custom/DCache_m2_Fused_small.jpg)|
|---|
|![DCache_poly_Fused_small](/imgstore/custom/DCache_poly_Fused_small.jpg)|

https://drive.google.com/drive/u/1/folders/1-4UZaXHdw9YEB1l71jSo1c_1_948NUOa

TBD: You can't see the poly in the upper right corner, so you'll have to take additional photos.
