# GTE

This section contains a description of the schematics of the GTE. As with the rest of the wiki, we don't focus on the software models; there is already enough documentation for that.

## GTE Overview

To summarize the main points:

- GTE (Geometry Transformation Engine) is a custom COP2 coprocessor which performs geometric calculations on vectors and matrices
- It contains 32 data registers and 32 control registers
- GTE instructions are executed in multiple clock cycles and there is an interlock when you try to read a register that is used in the computation.
- As long as the application doesn't touch the registers being used GTE instruction is executed in parallel to the regular instructions.
- All calculations are performed on fixed point numbers.
