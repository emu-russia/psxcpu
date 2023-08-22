# http://board.psxdev.ru/topic/9/

## nocash

MDEC Decompression goes through three steps
1)  RLE decompression
2)  IDCT conversion
3)  Y-to-mono (or YUV-to-RGB) conversion

RLE is quite simple. Although, I've just discovered that it contains some hidden features: rounding, saturation and special quant table disable feature. Anyways, I am quite sure that I've figured out the exact RLE output. Ie. I am SURE that I know what exact values get passed to the IDCT section.

Y-to-mono is very simple: 8bit saturation, and optional signed-to-unsigned conversion. The only special case is that the saturation bugs when the input value exceeds 9bit range.

IDCT is not simple. I've spent a couple of days on running hardware tests, but I am absolutely unable to reproduce this step without rounding errors.
Basically it's doing two 8x8 matrix multiplications:
  TempMatrix = DiagonallyMirrored.InputMatrix * ScaleTableMatrix
  OutputMatrix = DiagonallyMirrored.TempMatrix * ScaleTableMatrix
InputMatrix is the RLE decoded data (signed 11bit values).
TempMatrix contains some temporary result (signed numbers with unknown amount of bits)
OutputMatrix is the Y data for Y-to-Mono conversion (signed 9bit values or bigger).
ScaleTableMatrix contains some signed 13bit values (which be assigned as an array of 16bit values via an MDEC command; the hardware uses only the upper 13bit of those 16bit values).
And "Diagonally.Mirrored" means that matrix is multiplication is done "column-by-column" (instead of the usual row-by-column).

Unfortunatley, the matrix multiplications include several multiplications, and additions. The hardware may strip fractional parts after the multiplications, and/or after ther additions, possibly stripping different amounts of bits in the TempMatrix and OutputMatrix steps, and possibly stripping some final fraction before passing the result to the Y-to-Mono section.
At least in some cases, the result is rounded UP before stripping fractions. And at least in same cases, that rounding is done only if the fractions are BIGGER than 0.5.
Other than that, I didn't find out anything useful, everytime that I've found a good formula for some test values... it did turn out to fail on other test values.

Is there anything on the decapped chip that could help on that problems? For example, knowing the width of the multiplication results would be great!
Did you already discover an MDEC section the chip? I hope there is some sort of an MDEC section (without the MDEC stuff being randomly scattered across the chip).
Some characteristic thing for identifying the MDEC hardware should be:
Several arrays with 64 entries (11bit rle output, 8bit quanttable, 13bit scaletable, and Nbit Y, Cr, Cb buffers).
Nbit * 13bit multiplication unit for the scaletable multiplication (since it's a matrix multiplication, there might be 8 such multipliers to speed up things).

## org

We are still processing data. None of CPU internal parts are located yet roll

But we found some adder network, which looks like multiplier (or couple of multipliers). Not sure which one, and even not sure about its size smile

We need to vectorize more data and discover more standard cells (basically we found only D-triggers, full adder, half adder and multiplexer smile)

"Second american decaper" should capture better quality CPU images soon, maybe things go better.

Current standard cells progress (known cells, 7) : http://wiki.psxdev.ru/index.php/CPU_CELLS

Standard cells library (found, 32 ... and growing up) : http://psxdev.ru/files/cells.svg

## org

We suspect that we have found an area where MDEC decoding is taking place.

http://psxdev.ru/files/IC103/svg/c0018_r0023.svg

Need to investigate more.

## org

Looks like we found MDEC matrix multiplication :

http://wiki.psxdev.ru/images/6/6a/Circuit002_logic.png

## nocash

Cool!

So the multiplier output would be... 10bits? (The arrows pointing upwards on the of the image).

What are the small blue rectangles? Signals that you haven't figured out where they come from?

LSB is on left of the image, right? (the .png file is a bit too small to read text in it, but I'd guess LSB=left from the wires/arrows).

Is there only one multiplier? Or 8 multipliers (for multiplying a whole matrix column at once)? Just for curiosity, wouldn't matter too much if they've used parallelism or not. More important would be knowing if there's rounding in the multiplaction result and/or in the following sum-up additions.

And is there a second matrix multiplier unit (for the second part of the Temp=RLE*Scaletable (first part), and then RESULT=TEMP*Scaletable (second part) multiplication)?

## Akari

```
nocash wrote:
Cool!

So the multiplier output would be... 10bits? (The arrows pointing upwards on the of the image).

What are the small blue rectangles? Signals that you haven't figured out where they come from?

LSB is on left of the image, right? (the .png file is a bit too small to read text in it, but I'd guess LSB=left from the wires/arrows).

Is there only one multiplier? Or 8 multipliers (for multiplying a whole matrix column at once)? Just for curiosity, wouldn't matter too much if they've used parallelism or not. More important would be knowing if there's rounding in the multiplaction result and/or in the following sum-up additions.

And is there a second matrix multiplier unit (for the second part of the Temp=RLE*Scaletable (first part), and then RESULT=TEMP*Scaletable (second part) multiplication)?
```

We don't know for sure if this is really part of IDCT conversion, but it looks like it.
It has 2 inputs 13bits and 11bits and has 10bits output.

Scheme above updated so you can see a bit of progress. If you need i can send you original odt file.

I don't see any second part or something like this. Only strange 8bit that are go from 11bits data on the right. I don't know what can this be.

http://wiki.psxdev.ru/index.php/CPU_CIRCUIT_002

## nocash

```
Akari wrote:
We don't know for sure if this is really part of IDCT conversion, but it looks like it.
It has 2 inputs 13bits and 11bits and has 10bits output.
```

Looks right, 13bit Scaletable, 11bit RLE input in first pass.
And, as it seems now, also 11bit input in second pass, and 10bit output in both passes (which is very good to know, thanks!).

```
Akari wrote:
Only strange 8bit that are go from 11bits data on the right. I don't know what can this be.
```

I have an idea, think of it as three stages:

Stage 1: 11bit input from RLE unit (input to 1st matrix multiplication).

Stage 2: 11bit input from Stage 1 (eight 10bit values summed together, and then somehow squeezed into 10bits) (input to 2nd matrix multiplication).

Stage 3: 11bit input from Stage 2 (summed as above), and picking 8bits of the 11bit values, and passing that 8bits to the YUV-to-RGB unit (or Y-to-MONO unit) (instead of using the 11bit values as input to another, 3rd, matrix multiplication).

## nocash

Uhhhh. But meanwhile... you have changed the circuit picture?
And it is now doing something with 13 of 17 bits (instead 8 of 11 bits)?
That would smash my idea : - )

## Akari

```
nocash wrote:
Uhhhh. But meanwhile... you have changed the circuit picture?
And it is now doing something with 13 of 17 bits (instead 8 of 11 bits)?
That would smash my idea : - )
```

I updated picture with lastest info (now all triggers used) - now this input has 17 bits.
Bottom input still 13 bits.

Left part was also updated. There are some carry calculations. I still can't understand what are bone during calculations. Some strange manipulations without strict pattern.

## nocash

Okay, as far as I understand, there is something for fractional bits / rounding in upper-left?

The actual result seems to be only 10bits wide, but after summing up 8 values, it might become 13bit wide (yet another 13bit value).
Or would it be possible that the result is bigger than 10bits, in case you haven't found all outputs yet?

For the inputs, I am not sure which is which:
On the right is 17bits with something special on a 13bit portion.
On the bottom is 13bits with something special on a 11bit portion.
So both right & bottom inputs have some "13bit" stuff in there.

Best guess might be that the bottom input can be 11bit from RLE, or 13bit from previous multiplication (eight 10bit values summed).
What are think pink symbols in lower-left? If they are allowing to force the lower 2bits to zero, then they could expand the 11bit RLE values to 13bit?

The input on the right side is strange. If it is the scaletable then it should be only 13bits wide. Are you sure that the 13bits on the right side are outputs, not inputs?
For MDEC, I've no clue why there could be a 17bit input. Ah, unless, it's a smaller value sign-expanded to 17bits. Would that make sense? I don't understand the multiplier logic & don't know if it needs sign-expansion.

Another idea would be that they might re-use the multiplier for the YUV-to-RGB conversion (which requires multiplication by some constants; though I doubt that it'd be required to 17bit resolution in that stage).

EDIT: If the multiplier is re-used for different purposes - maybe the 17bit input is intended for smaller values with optional shift-amounts?
Like "17bit = (13bit shl 4)" for scaletable calculation, and "17bit = whatever smaller value" for YUV calculation.
That "shl 4" might be useful for getting the desired amount of fractional bits in the result, just an idea.

EDIT2: What is the small blue box in lower right of the image? Does that allow to disable all inputs on right side? Or is it the sign and allows to negate (or invert) the right input? When trying to figure out the rounding, I was having the feeling that the MDEC might "negate" negative values by a "NOT" operation instead of a real "NEG" operation.

## Akari

Newest foundings.

1) Scaletable matrix stored in UNIT 00. It's stored as 32 records 26 bits each. And later through multiplexer upper or lower 13 bits selected (lower part of sceme).

2) Output of all this calculations is 17 bit and all of them are going back to calculations again (result input is on the right part of sceme)

3) 13 bits of output is stored in UNIT 01. This is the only output of all those calculations. UNIT 01 can store 16 such outputs.

4) Other input to sceme are 6 bit of something.

5) UNIT 00 has ine more output to some other part. So scaletable matrix used somewhere else.

http://wiki.psxdev.ru/images/thumb/4/4f/Circuit002_logic.jpg/800px-Circuit002_logic.jpg

Full version are here http://wiki.psxdev.ru/images/4/4f/Circuit002_logic.jpg

## nocash

Cool. Looks as if you are finding more details every day!

```
Akari wrote:
1) Scaletable matrix stored in UNIT 00. It's stored as 32 records 26 bits each. And later through multiplexer upper or lower 13 bits selected (lower part of sceme).
```

Sounds fine, 64x13bit should be right (even when it's internally organized as 32x26bit for whatever reason).

```
Akari wrote:
2) Output of all this calculations is 17 bit and all of them are going back to calculations again (result input is on the right part of sceme)
```

That feedback could be two things: Either the summing-up part (when multiplying & summing 8 entries from two matrix columns; in that case the feedback would be passed to the sum/addition hardware, not to the actual multiplier). Or, it could be the 2nd pass (when the whole matrix-by-matrix multiplication is done another time).

```
Akari wrote:
3) 13 bits of output is stored in UNIT 01. This is the only output of all those calculations. UNIT 01 can store 16 such outputs.
```

Stores 16 outputs? Are you sure? For a matrix column it should store 8 values. And for a whole matrix it should store 64 values.

```
Akari wrote:
4) Other input to sceme are 6 bit of something.
```

That sounds a bit too less (in case you are talking about second multiplier input).

```
Akari wrote:
5) UNIT 00 has ine more output to some other part. So scaletable matrix used somewhere else.
```

Theoretically the scaletable shouldn't be used anywhere else. Unless there are two matrix multiplier units (for pass1 and pass2). Or maybe there is some test function that allows to read the scaletable content.

Btw. don't know if you already have this in mind: The matrix multiplier should be most likely working like this:

```
src=blk, dst=temp_buffer
  for pass=0 to 1
    for x=0 to 7
      for y=0 to 7
        sum=0
        for z=0 to 7
          sum=sum+src[y+z*8]*(scaletable[x+z*8]/8)
        next z
        dst[x+y*8]=(sum+0fffh)/2000h      ;<-- or somehow different rounding in this place and/or other places
      next y
    next x
    swap(src,dst)          ;<-- or maybe actual HW uses another destination buffer in 2nd pass instead of swapping src/dst
  next pass
```

I don't understand the decapped circuit too well... as far as I understand, it does seem to have a shift-register for the multiplication, so I guess it's using the good old "shift-and-add" mechanism to do the multiplication (?) and aside from that, there should be another addition mechanism for the "sum" calculation - or, wait, it might be also using the "sum" value as initial value in the "shift-and-add" part; so maybe there is only one addition unit. Hope you'll understand the circuit better than me : - )

## org

It seems there is another such monster circuit (right near this one), which do more calculations smile

We suppose both circuits using Wallace/Dadda trees to speedup calculations (you can see it as FA/HA adders network)

## org

The second circuit is the same as first one and do second IDCT pass, as expected smile

Meanwhile I reversed ScaleTableMatrix memory buffer:

http://wiki.psxdev.ru/images/8/82/Unit00_overview.jpg

## Akari

```
nocash wrote:
That feedback could be two things: Either the summing-up part (when multiplying & summing 8 entries from two matrix columns; in that case the feedback would be passed to the sum/addition hardware, not to the actual multiplier). Or, it could be the 2nd pass (when the whole matrix-by-matrix multiplication is done another time).

nocash wrote:
Stores 16 outputs? Are you sure? For a matrix column it should store 8 values. And for a whole matrix it should store 64 values.
```

Looks like multiplication and summing with previous result is done in one pass through one network of different adders.

There are two identical sum_multipliers for pass 1 and pass 2. Looks like it done matrix multiplication "column-by-column" to speedup the multiplication process. Pass 2 can be calculated in paralel as soon as first 8 sums are calculated. Not need to wait until first two matrix multiplication is completed. And it takes only 8 records with 13 bits each to store, instead of 64x13.

unit 1 looks like dualport buffer. It has 16 records 13bits each. While first 8 records is used in pass2 - second 8 records are calculated. After second records calculated first 8 are not needed anymore and we can store next result here while using 2 set to pass2.

```
nocash wrote:
Akari wrote:
4) Other input to sceme are 6 bit of something.

That sounds a bit too less (in case you are talking about second multiplier input).
```

Input is 12 bit smile

```
nocash wrote:
Btw. don't know if you already have this in mind: The matrix multiplier should be most likely working like this:

  src=blk, dst=temp_buffer
  for pass=0 to 1
    for x=0 to 7
      for y=0 to 7
        sum=0
        for z=0 to 7
          sum=sum+src[y+z*8]*(scaletable[x+z*8]/8)
        next z
        dst[x+y*8]=(sum+0fffh)/2000h      ;<-- or somehow different rounding in this place and/or other places
      next y
    next x
    swap(src,dst)          ;<-- or maybe actual HW uses another destination buffer in 2nd pass instead of swapping src/dst
  next pass
I don't understand the decapped circuit too well... as far as I understand, it does seem to have a shift-register for the multiplication, so I guess it's using the good old "shift-and-add" mechanism to do the multiplication (?) and aside from that, there should be another addition mechanism for the "sum" calculation - or, wait, it might be also using the "sum" value as initial value in the "shift-and-add" part; so maybe there is only one addition unit. Hope you'll understand the circuit better than me : - )
```

I can give you some info to test:
- Pass 1 has 13 bit scaletable input and 12 bit of RLE input. While caculating sum - 17 bit result is used. When summing is done - only upper 13 bit of result is stored.
- Pass 2 uses 13 bit of pass 1 result and upper 12 bit of scaletable matrix. While caculating sum - 17 bit result is used. When summing is done - only upper 10 bit of result is passed to next stage.

I look at the next stage right now. It looks like some futher rounding is done. I see only 7 bit output.

## org

We found some circuit that makes the conversion on the output result of IDCT:

http://wiki.psxdev.ru/images/3/32/002_conv.png

It takes 10 bit as input and 8 bit as output :

https://code.google.com/p/psxdev/source … ersion.txt

EDIT!  Signed division-by-2 with clamping smile

## Akari

```
Akari wrote:
org wrote:
We found some circuit that makes the conversion on the output result of IDCT. It takes 10 bit as input and 8 bit as output.

Solved!!!

This is division by 2 with rounding up. Result is clamped 127, -128.

For example

1    1
2    1
3    2
4    2
5    3
6    3
7    4
8    4

251    126
252    126
253    127
254    127
255    127
256    127
257    127
258    127
259    127
260    127

509    127
510    127
511    127
-512    -128
-511    -128
-510    -128
-509    -128
-508    -128
-507    -128

-10    -5
-9    -4
-8    -4
-7    -3
-6    -3
-5    -2
-4    -2
-3    -1
-2    -1
-1    0
```

## org

Martin, this is why you wasn't able to reproduce results smile Signed division-by-2 is a bit tricky)

## nocash

I really hope that I could get signed-div-2 implemented ; - )

```
Akari wrote:
- Pass 1 has 13 bit scaletable input and 12 bit of RLE input. While caculating sum - 17 bit result is used. When summing is done - only upper 13 bit of result is stored.
- Pass 2 uses 13 bit of pass 1 result and upper 12 bit of scaletable matrix. While caculating sum - 17 bit result is used. When summing is done - only upper 10 bit of result is passed to next stage.
```

Pass 2 is using only 12bit scaletable, not 13bit??? That might explain some of my rounding errors. I would have NEVER imagined that it might use only 12bits there!

I am unsure which 17bits of the multiplication result are used. Theoretically, 12bit*13bit would give 25bit result. But for signed numbers it could be squeezed into 24bit (except that -800h*-1000h would overflow). And after summing up 8 values, the result might grow by factor 8, so it might be needed to be 28bits wide instead of just 25bit. If the result could be 24..28 bits wide - how many LSBs should be removed to get the 17bit value?

Assuming that the result is only 24bits wide, then the overall formula should remove fractional bits like so:

```
pass 1, after multiplication:  strip 7bit (to reduce result from 24bit to 17bit)      ;\
  pass 1, after summing:         strip 4bit (to reduce sum from 17bit to 13bit)         ; is there any rounding
  pass 2, before multiplication: strip 1bit (to reduce scaletable from 13bit to 12bit)  ; done in these steps?
  pass 2, after multiplication:  strip 7bit (to reduce result from 24bit to 17bit)      ;
  pass 2, after summing:         strip 7bit (to reduce sum from 17bit to 10bit)         ;/
  after pass 2:                  strip 1bit (signed div2)                               ;<-- with rounding-up
  ------------------------------------------------------------------
  total                         strip 27bits
```

Looks _almost_ right. In my pseudo source code, I've divded the result by 2000h (=stripped 13bit) in each pass, aka stripped 26bit in total.
When stripping 27bits the result would be too small... but wait, you have said that the input from RLE unit is 12bits? I was thinking that RLE output is signed 11bit. But if it's 12bit, then stripping 27bits on the final result would be just right.

Though I was quite sure about RLE being 11bits, if there is an extra fractional bit, then it didn't seem to affect my hardware test results. You don't happen to see some 11bit-to-12bit expansion on the pass 1 input (ie. something that creates an extra fractional bit, or an extra sign bit)?

## Akari

```
nocash wrote:
I really hope that I could get signed-div-2 implemented ; - )
```

Not just "signed-div-2", but "signed-div-2 with clamping" ie

```
if( result < -128 )
{
    result = -128;
}
else if ( result > 127 )
{
    result = 127;
}
```

```
nocash wrote:
Akari wrote:
- Pass 1 has 13 bit scaletable input and 12 bit of RLE input. While caculating sum - 17 bit result is used. When summing is done - only upper 13 bit of result is stored.
- Pass 2 uses 13 bit of pass 1 result and upper 12 bit of scaletable matrix. While caculating sum - 17 bit result is used. When summing is done - only upper 10 bit of result is passed to next stage.

Pass 2 is using only 12bit scaletable, not 13bit??? That might explain some of my rounding errors. I would have NEVER imagined that it might use only 12bits there!

I am unsure which 17bits of the multiplication result are used. Theoretically, 12bit*13bit would give 25bit result. But for signed numbers it could be squeezed into 24bit (except that -800h*-1000h would overflow). And after summing up 8 values, the result might grow by factor 8, so it might be needed to be 28bits wide instead of just 25bit. If the result could be 24..28 bits wide - how many LSBs should be removed to get the 17bit value?

Assuming that the result is only 24bits wide, then the overall formula should remove fractional bits like so:

  pass 1, after multiplication:  strip 7bit (to reduce result from 24bit to 17bit)      ;\
  pass 1, after summing:         strip 4bit (to reduce sum from 17bit to 13bit)         ; is there any rounding
  pass 1, before multiplication: strip 1bit (to reduce scaletable from 13bit to 12bit)  ; done in these steps?
  pass 2, after multiplication:  strip 7bit (to reduce result from 24bit to 17bit)      ;
  pass 2, after summing:         strip 7bit (to reduce sum from 17bit to 10bit)         ;/
  after pass 2:                  strip 1bit (signed div2)                               ;<-- with rounding-up
  ------------------------------------------------------------------
  total                         strip 27bits
Looks almost right. In my pseudo source code, I've divded the result by 2000h (=stripped 13bit) in each pass, aka stripped 26bit in total.
When stripping 27bits the result would be too small... but wait, you have said that the input from RLE unit is 12bits? I was thinking that RLE output is signed 11bit. But if it's 12bit, then stripping 27bits on the final result would be just right.

Though I was quite sure about RLE being 11bits, if there is an extra fractional bit, then it didn't seem to affect my hardware test results. You don't happen to see some 11bit-to-12bit expansion on the pass 1 input (ie. something that creates an extra fractional bit, or an extra sign bit)?
```

Nope just 12 wires that come from far far away smile
I don't know if this is RLE result or some other things, but it is 12 bit for sure.

And after pass 2 division by 2 - strips 2 bits (but with clamping). Overall result is 8 bit: 7 value + 1 sign.

During multiplication all bits after 17 only carry is passed to next stage. You can see it in left part. Only data for upper 17 bits are go to D-triggers and futher. We don't know yet how reducing to 17 bit is working. It seems like calculation is done as sum of 8 numbers. Six of them are 13 bit of scaletable data (in 1st pass), 7th data from RLE, 8th is 17 bits of previous result. Six 13bits are formed through some calculations from 12 RLE inputs.

We don't see any rounding during this calculations.

We test multiplication and sum soon and can give you more precise data )

## org

```
f( result < -128 )
{
    result = -128;
}
else if ( result > 127 )
{
    result = 127;
}
```

This would be :

```
result = min ( 127, max ( result, -128) );
```

smile

## nocash

Yes, yes, I already knew about that final min/max clamping (in the psx-spx doc, I did have it in the Y-to-MONO section) (and as you've found it right after IDCT pass 2, same clamping should be probably occurring right at the begin of (or prior to) the YUV-to-RGB section).

## nocash

I have changed my RLE code to output 12bit (one more fractional bit as in my old code with 11bits). And removed the +4 rounding in my RLE code, and removed the +0FFFh rounding in my IDCT code (so the only remaining rounding is that in the final signed-div-2 stage that you've discovered).
And then I've changed my IDCT code to remove fractional bits in the various stages (as listed in my earlier post above).

With qscale=0, the test results are looking almost perfect: Tested some hundreds of calculations, and got only one error (occurs when RLE=1FFh, qscale=0, scaletable=-8000h, the Y-to-MONO result should be +7Fh, but there seems to be a signoverflow somewhere that gets me -80h, I'll have to look more into that (yes, I am doing that clamping, it's only this special case where the clamping doesn't work)). Anyways, the results are MUCH better than in my earlier tests (which had around a dozen of errors). Knowing the places where to remove fractions, and knowing that scaletable is only 12bit in 2nd pass has been of great help - many thanks for that findings!

And with qscale>0, the test results are still as glitchy as in my earlier tests. Well, I did almost expect that there would be some problems approaching when changing some test parameters : - )

The problem is that some of my calculated results are 1 bigger as on real hardware. The final signed-div-2 rounding is hopefully correct (I am doing that as x=(x+1)/2). And I am not rounding-up anything elsewhere, so there's probably one more place where the hardware is rounding-down something (by stripping fractional bits). Most likely in this part of the RLE section:
   val = 10bitRLEdata * 6bitQscale * 8bitQuanttable
I guess the hardware might drop some fractional bits after the first of the two multiplications (and of course, in that case the multiply-order would be also important; which might be different as shown above).

## nocash

ixed the one error that occured with qscale=0. I was rounding up the 10bit result (+1), and then masking it to 10bit size (AND 3FFh). Which was the wrong around (value +1FFh with +1 added for rounding did exceed signed 10bits, so I should have done the masking before rounding) (as you have correctly listed it in the https://code.google.com/p/psxdev/source/browse/trunk/CIRCUIT_002/conversion.txt logic table).

Results for qscale=0 appear to be perfect now. I'll need to do some tests on removing fractional bits here or there in the RLE section for qscale>0. And, for colored images, the YUV-to-RGB part does still lack some details about fractions/rounding, particulary in the `G=(-0.3437*B)+(-0.7143*R) and R=(1.402*R) and B=(1.772*B)` calculations.

## org

Color space conversion is still a hidden gem. But we found 768 bytes SRAM buffer, which is supposed to be 16x16 RGB output macroblock FIFO.

## nocash

```
org wrote:
we found 768 bytes SRAM buffer, which is supposed to be 16x16 RGB output macroblock FIFO.
```

Sounds good. There should be also something for 64-byte mono output (might be using a fragment of the RGB buffer).
And there should be two 64-byte color buffers (for the low-resolution Cr and Cb data blocks), and maybe one (or four) 64-byte mono buffers (for the hi-resolution Y data blocks).
And a 64x12bit buffer for the decompressed RLE data, and probably some FIFO for incoming uncompressed RLE data.

About my attempts to reproduce the MDEC decompression formula: Grumph. Yesterday, I've changed some test parameters, and that has smashed everything, and I am now getting huge errors for both qscale=0 and qscale>0. The old test used only one non-zero RLE value. The new test used 32 non-zero RLE values (still a very simple test case with all 32 values set to the same value, and with the whole scaletable also being filled by a single value - but even that simple setup is too complex for my poor formula).

## nocash

```
Akari wrote:
We test multiplication and sum soon and can give you more precise data )
```

You mean you are (almost) ready to simulate the circuit? That would be great!
Knowing the 17bit result for some simple multiplications would be neat, something like this:
100h*100h (for knowing how many fractional bits of the result get stripped).
7FFh*001h (for knowning if there's something rounded-up).
One multiplication (without summing) would be enough, ie. with the incoming sum being forced to zero via the AND gates at the right side of the circuit.

Currently, I am assuming that the whole multiplication result is 24bit, so I am stripping lower 7bit to make it a 17bit value. And then I am 'masking' it to 17bit size (or actually I am sign-expanding it from 17bit to 32bits; since my test program is using 32bit maths). However, that 'masking' is somehow smashing the final output. The output is better when allowing the values to exceed 17bits (which probably means that one should strip more than 7 lower bits, so that the more significant bits fit into the 17bit space).
Stripping more than 7 lower bits (=rounding down further) might also fix the rounding issue where I got too large results.

## Akari

```
nocash wrote:
You mean you are (almost) ready to simulate the circuit? That would be great!
```

Maybe Org do simulate multiplication when he has time. It is possible now.

By the way looks like I found IDCT logic. There are few counters that activate address selectors on units and related to CLK signal. More info soon )

http://wiki.psxdev.ru/images/6/61/Circuit002_logic4.jpg

## org

```
Maybe Org do simulate multiplication when he has time. It is possible now.
```

This circuit is way heck too tough ))) But I'll find the strength to :=)

## org

Well.. I did it smile

Whole IDCT pass1 :

http://wiki.psxdev.ru/images/7/74/IDCT_pass1.png

MUX controls:
http://wiki.psxdev.ru/images/b/b4/IDCT_MUX_CONTROL.png

MUXs:
http://wiki.psxdev.ru/images/3/31/IDCT_MUXs.png

And most complicated part, multiplier FA/HA tree:
http://psxdev.ru/files/IC103/IDCT_TREE.png

I just toggle IDCT_CLK and it is working, or kinda of )))) I experimented with inputs and it show something smile Need to check it again and fix possible errors.

PS. So called "MUXs" block looks like barrel shifter, but I'm not sure)

To see high resolution images open it as URLs.

## nocash

Okay...
Lower image looks like having all bits zero.
Upper image shows 111111110101 * 0000000000001 = 01010101010101111.
Hope that isn't the actual/final result : - )

## org

This stuff is really weird)) Can break your brain smile

## nocash

How many inputs does have that circuit?
The two factors, the old result (for summing up), and the AND_FLAG and IDCT_CLK, and... are there more inputs?

Do you already know how many CLK's it would take to do one single multiplication?
If it takes more than one CLK, then there should be some counter for the separate steps, either inside of the circuit or somewhere externally. And whereever that counter might be located, there should be probably some input to reset that counter?

## org

It takes single clock to stabilize (I mean IDCT_CLK is changed from 0 to 1 only once, to make result valid).

But if you enable AND_FLAG whole circuit enable some kind of counter mode )

Surely there are some errors in connections ))

```
How many inputs does have that circuit?
```

- 12 bit from the left are input RLE result
- 13 bit from the bottom are input from scale table matrix
- 17 bit output from the top are result of pass1
- IDCT_CLK to toggle
- AND_FLAG wtf 

## org

BTW you can experiment with it by yourself. Circuit is uploaded to SVN:

https://code.google.com/p/psxdev/source/browse/trunk/CIRCUIT_002/IDCT.circ

You need Logisim : http://ozark.hendrix.edu/~burch/logisim/

## nocash

Only one CLK per multiplication?
The "111111110101 * 0000000000001 = 01010101010101111" stuff looked like incomplete result, as if it were having multiplied only each 2nd bit, so I was thinking that it might multiply the other bits in a second CLK cycle (and add them to result from first cycle).
Using "111111110101" (=negative) as test value looks more complex than needed, I would start with simple positive numbers.

What do you mean by "if you enable AND_FLAG whole circuit enable some kind of counter mode"?
The main purpose of the AND_FLAG should be initializing the result to ZERO, so eight multiplications will give you this:
  ZERO + Result#1 + Result#2 + ... + Result#8
Which, well, yes, that "summing up" could be called "counting up", in case you meant that.

Or if there's some other counter bound to AND_FLAG, that might indicate that multiplication would need more than one CLK. Ie. the circuit might use AND_FLAG for setting result to ZERO, and also for initializing some CLK counter (that would be needed only for the first initial multiplication assuming that that counter would wrap to zero automatically after each multiplication). Only, I can't see any such CLK counter bound to AND_FLAG in the circuit.

I probably don't have time & hardware resources for installing logisim (and java).

## Akari

```
nocash wrote:
Only one CLK per multiplication?
The "111111110101 * 0000000000001 = 01010101010101111" stuff looked like incomplete result, as if it were having multiplied only each 2nd bit, so I was thinking that it might multiply the other bits in a second CLK cycle (and add them to result from first cycle).
Using "111111110101" (=negative) as test value looks more complex than needed, I would start with simple positive numbers.
```

I fixed a lot of bugs in Circuit. It do some calculations but I think there are still a lot of bugs.

multiplication 111111110101 * 0000000000001 now = 11111111111111110

000000000000 * 0000000000000 = 00000000000000000
000001000101 * 0000010100000 = 00000000001010101

By the way, can you tell me how many fractional bits in rle result and in scaletable matrix?

## nocash

```
Akari wrote:
By the way, can you tell me how many fractional bits in rle result and in scaletable matrix?
```

I could... the scaletable consists of JPEG constants (1.000, 1.387, etc.) multiplied by sqrt(2) multiplied by 4000h, so it should have 14bit fractional part. For the RLE numbers I've no clue if they have some nominal number of fractional bits.

But technically... the only that does really matter is how many fractional bits are stripped from the result. As said here, http://board.psxdev.ru/post/57/#p57 the full result should be 24bit..28bit wide (24bit would be minimum, and 28bit would be safe for avoiding overflows),  so to get that down to 17bits, the 24bit..28bit result would be right-shifted by 7..11 bits.

In so far, to avoid rounding issues, it may be best to start with values where you know that the lower 11bits of result are all zero, so you can still see the 'intact' result even if some or all of those 11bits get removed. I would start with some simple stuff, set only one bit each factor, then check if and where you see the bit in result, and then try setting some more bits in one (or both) factors...

## nocash

```
org wrote:
PS. So called "MUXs" block looks like barrel shifter, but I'm not sure)
```

What would that shifter be good for? If the multiplication really passes in a single CLK cycle then I couldn't imagine how/why it could use shifting, unless it's done on the two CLK edges.
I don't understand the circuit at all, but I still think it's hard to believe that it could finish multiplication in a single CLK.

## Akari

```
nocash wrote:
org wrote:
PS. So called "MUXs" block looks like barrel shifter, but I'm not sure)

What would that shifter be good for? If the multiplication really passes in a single CLK cycle then I couldn't imagine how/why it could use shifting, unless it's done on the two CLK edges.
I don't understand the circuit at all, but I still think it's hard to believe that it could finish multiplication in a single CLK.
```

It looks like it done one multiply operation per clock. And it will take 8 clocks to finish multiplication of one number in matrix.

Launch logisim and try to look for yourself. It's real funny to try to guess what is going on there ) Like a puzzle solving.

## AmiDog

```
nocash wrote:
And, for colored images, the YUV-to-RGB part does still lack some details about fractions/rounding, particulary in the G=(-0.3437*B)+(-0.7143*R) and R=(1.402*R) and B=(1.772*B) calculations.
```

The MDEC seems to be using fixed point math with a 12-bit fractional part, just as the GTE and GPU does in places.

Using these coefficients:

RCr ( 5744)
GCr (-2928)
GCb (-1408)
BCb ( 7264)

or in other words:

Red   = Y + 1.4023 * Cr
Green = Y - 0.3437 * Cb - 0.7148 * Cr
Blue  = Y + 1.7734 * Cb

I've been able to verify all 16.8M combinations of Y/Cb/Cr and the resulting R/G/B values.

However, the MDEC seems to be using too few bits, causing "interesting" overflow/rounding issues for some edge cases, which needs to be handled.

## nocash

I haven't looked into that part yet since I am still having troubles to calculate the incoming Y,Cr,Cb values (I guess you don't know how get those 100% correct, too).

Anyways, good to know that the YUV-to-RGB stage appears to be simple, aside from that overflow/rounding issue. Is that BOTH happening overflows
(MSBs), and rounding (LSBs)?

Btw. that 12bit fractions (5744,-2928,-1408,7264) are all multiples of 4. So actually, they are having only 10bit fractions.

## LostTemplar

I THINK that I understood the multiplication algorithm that was used in the IDCT multiplier, and at least the bottom part of the circuit (MUX ARRAYS CONTROL and MUX ARRAYS, as well as the very bottom of the multiplication tree). I'll just write down my thoughts, so it might not be the best explanation. Also, I didn't yet think about fixed-point or negative numbers, so there might still be some aspects missing.

The algorithm leverages the fact that you can express every product of an N-bit number A and an M-bit number B as the sum of ceil(N/2) (possibly negative) terms of B. For example, assume that N=4 and M arbitrary. Then every possible product can be written as sum of N/2=2 terms:

```
0 * B = 0 * B
1 * B = 1 * B
2 * B = (4 - 2) * B
3 * B = (4 - 1) * B
4 * B = 4 * B
...
13 * B = (-4 + 1) * B,   + 16 * B is assumed
...
```

The circuit looks at bit triples at a distance of two bits each (i.e. they overlap by 1 bit) to achieve this; a -1th bit of 0 is assumed. Each 12-bit number has 12/2=6 of these triples that correspond to the the 12/2=6 14-bit outputs L0-L5 of MUXs. Each triple represents a factor that Ln gets multiplied with:

```
000 := 0
001 := 1
010 := 1
011 := 2
100 := -2
101 := -1
110 := -1
111 := "-0"
```

MUX ARRAYS CONTROL looks at the triples and multiplies the input by 2 (shifts it by 1) if necessary (Ln_D1,2) - that's why Ln is 14 bits long. If all bits are 0 or 1, the input is treated as -1 (all 1s). Apart from MUX ARRAYS CONTROL the 2nd bit is used to get the complement of the input (all triples with the 2nd bit = 1 have a negative factor; Ln_C1,2). This fits perfectly with the chart above. Observe that for the 2's complement, there's still a +1 missing; that one is in the multiplier tree: if for a triple, the associated factor is negative or zero (because then the input = -1), +1 is added. This is checked by the 12-OAIs. 

In the end, L0-L5 get summed up to get the final result; each Ln has the actual value (Ln << 2\*n). Here's an example for our 4-bit numbers:

```
13 * B = 1101b * B => assume implicit -1th bit: 1101.0
divide into overlapping triples:
L0:   010 := 1 * B
L1: 110   := (-1 * B)
=> ((L0 << 0) + (L1 << 2)) =  (1 - (1 << 2)) * B = (1 - 4) * B = -3 * B; +16 * B is assumed so = 13*B
```

However, I still don't know how the tree is working exactly (especially as there are still errors in the Logisim circuit). There's not enough levels to allow for all additions, so that's probably what the WS elements are for. The AND_FLAG input (as I think nocash has already mentioned) serves to initialize the accumulation operation if it is 0; if it is 1, the result of the multiplication is added to the result of the multiplication one cycle before. I think one multiplication needs 2? cycles, but being pipelined each cycle a result is output and accumulated.

## LostTemplar

In IDCT.circ, the DFF straight below RES00 doesn't get the clock; if you fix that, the circuit should work (I only tested a few cases, though)!

## nocash

Sounds good. Overlapping triples are a bit too abstract for my brain. I might have a rough (and possibly wrong) idea what you are talking about, but  I would prefer not to think too much about it :- )
Unless... are there any details important for emulation envolved? Like rounding errors in the multiplication result?

And very dumb question: Does anybody know yet how many LSBs get stripped from the multiplication results?
Ie. what are the results for 256\*256, in first and second multiplier sections?
The results should be right-shifted - so they must be something else than 65536.

NB. Some more info on the MDEC including RLE section was posted here: http://www.psxdev.net/forum/viewtopic.p … &t=551

## LostTemplar

As far as I understand it works as follows (someone please correct me if I'm wrong):

The circuit multiplies a 13- by a 12-bit number, both signed. Technically, the result is a 25-bit number; however, the circuit drops a) bits 0-6 and b) bit 25 (the carry into the 25th bit, to be precise). That means that internally a 17-bit number is used to accumulate further products. The overall result, then, is once again stripped of bits 0-3 (i.e. bits 7-10 in the original 25-bit number) to leave us with a 13-bit number that is used as the input of the second pass (another instance of the multiplier).

I haven't gotten to it yet, but judging from the circuit schematics, in the very end 8 bits of 17-bit result of the second pass are selected and divided by 2 (with clamping) to arrive at an 8-bit number.

Your example (256\*256) would result in the "25-bit" number 65536. Then, bits 0-6 are dropped and you get 512.

If you multiply for instance 257\*256 you get 65792 (0x10100). Now drop bits 0-6 to get 514 -- this number is used for accumulation. However, if it were to be passed to the second stage, an additional 4 bits are dropped and the result is 32, which is then multiplied by 12 bits from the scale matrix.

As a side note, the second pass probably uses 13 bits from the previous result and 12 bits from the scale matrix because a) they wanted to use the same circuit design again and b) just a guess, but the additional bit was more accurate/helpful when used for the previous result, not the scale matrix.
