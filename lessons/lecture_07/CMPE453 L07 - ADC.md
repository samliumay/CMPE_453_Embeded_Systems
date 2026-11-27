---
course: CMPE 453 Embedded Systems
lecture: 7
title: "ADC: the analog to digital converter"
tags: [cmpe453, lecture]
slides: lectures/lecture_7/Lecture7-ADC.pdf
---
# CMPE 453 · Lecture 7 · ADC: the analog to digital converter

> [!summary] In short
> - An ADC turns a voltage into a number. An $n$-bit ADC has $2^n$ steps. Step size $=V_{ref}/2^n$.
> - ATmega328P: 10 bits, so $\text{ADC}=V_{in}\cdot1024/V_{ref}$ (rounded down, 0 to 1023) and back $V_{in}\approx\text{ADC}\cdot V_{ref}/1024$.
> - It uses successive approximation: one comparison per bit, from the MSB down.
> - Channels ADC0 to ADC5 = PC0 to PC5 = A0 to A5. `ADMUX` selects the reference (`REFS1:0`), the alignment (`ADLAR`) and the channel (`MUX3:0`).
> - `ADCSRA`: `ADEN` switches the ADC on, `ADSC` starts a conversion and stays 1 until it is done, `ADPS2:0` set the clock prescaler.
> - The ADC clock must be 50 to 200 kHz. At 16 MHz only prescaler 128 fits: 125 kHz. One conversion = 13 ADC clock cycles = 104 µs (the first one 25 cycles = 200 µs).
> - Read `ADCL` first, then `ADCH`. In C, reading `ADC` does this for you.

## 1. Why an ADC, and where it sits (slides 2-3)
![[cmpe453_lecture_7_lecture7_adc_p002.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p003.jpg]]

**Notes.** A microcontroller must interact with the environment. Physical quantities are **analog**: temperature, pressure, distance, acceleration, force. Analog means the value can be anything in a range, not only 0 or 1. The microcontroller works in the **digital** domain: logic 1 and 0.

Three steps bring a physical quantity into the program:
1. **Sensor**: converts the physical quantity to a voltage.
2. **Signal conditioning**: scales the voltage so that it is inside the range the microcontroller accepts (amplify a small signal, divide a big one, remove noise).
3. **ADC** (analog to digital converter): converts the analog voltage to a digital value, a number.

The figure on slide 3 shows the chain: physical quantity, sensor, ADC, CPU, display. The label "digital voltage" after the ADC means a binary number on several wires, each wire at logic 0 or 1. The figure does not show step 2. Signal conditioning sits between the sensor and the ADC.

A digital input pin (lecture 4) answers only "high or low?". The ADC answers "how many volts?".

> [!question]- Check (slide 2): A pressure sensor gives 0 to 50 mV. The ADC accepts 0 to 5 V. Name the three steps for this sensor. What must step 2 do, with which number?
> Step 1, sensor: pressure becomes a voltage of 0 to 50 mV.
> Step 2, signal conditioning: the range must grow from 50 mV to 5 V. The amplifier gain is $5/0.05=100$. Check with half pressure: $0.025\cdot100=2.5$ V.
> Step 3, ADC: the 0 to 5 V voltage becomes a number.
> Without step 2 the ADC would only ever see the lowest 1 % of its range, so almost all of its steps would be wasted.

> [!question]- Check (slide 3): In the figure, which block is missing compared with the three steps of slide 2, and between which two blocks does it belong? On an Arduino Uno, which of the blocks ADC and CPU are inside the ATmega328P?
> **Signal conditioning** is missing. It belongs between the sensor and the ADC, on the wire labelled "analog voltage".
> Both the ADC and the CPU are inside the ATmega328P. The ADC is an on-chip peripheral, like the USART and the TWI. Only the sensor (and the display) are outside.

## 2. Resolution, step size, conversion time and V_ref (slides 4-5)
![[cmpe453_lecture_7_lecture7_adc_p004.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p005.jpg]]

**Notes.** The block diagram on slide 4 shows the pins of any ADC:
- $V_{in}$: the analog input, the voltage to measure.
- $V_{ref}$: the reference voltage. It sets the top of the measuring range.
- **Start Conversion**: a signal that tells the ADC to measure now.
- **D0 ... Dx**: the binary data output. An $n$-bit ADC has $n$ output lines, D0 (LSB) to D$(n-1)$ (MSB).

**Resolution** is the number of bits $n$. The output can take $2^n$ different values (0 to $2^n-1$). These are the **steps**.

**Step size** is the smallest change of the input that the ADC can see:

$$\text{step size}=\frac{V_{ref}}{2^n}$$

With $V_{ref}=5$ V (slide table): 8 bits give $5/256=19.53$ mV, 10 bits give $5/1024=4.88$ mV, 12 bits give $5/4096=1.22$ mV, 16 bits give $5/65536=0.076$ mV. More bits, smaller step, finer measurement.

**Conversion time** (slide 5) is the time the ADC needs to convert the analog input to the digital number. It depends on:
- the clock source connected to the ADC (faster clock, faster conversion),
- the conversion method (for example successive approximation, section 5),
- the technology of the chip: MOS or TTL.

**V_ref** (slide 5). $V_{ref}=V_{ref(+)}-V_{ref(-)}$. On the ATmega328P $V_{ref(-)}$ is GND, so $V_{ref}$ is simply the reference voltage. The input range is 0 to $V_{ref}$. The two tables show the idea: a smaller $V_{ref}$ gives a smaller step, but also a smaller range. For a 10-bit ADC, $V_{ref}=2.56$ V gives a step of exactly 2.5 mV and $V_{ref}=1.024$ V gives exactly 1 mV.

> [!warning] Slide error
> - Table 13-3, row 1.28 V: the slide writes "1/1024 = 1.25". It must be $1.28/1024=1.25$ mV. The result is right, the fraction is wrong.
> - Table 13-2 cuts digits off and does not round. It writes 11.71 and 3.90. Exact: $3/256=11.719$ mV (rounded 11.72) and $1/256=3.906$ mV (rounded 3.91). It also writes 15.62 for $4/256=15.625$ mV.
> - Table 13-1 (slide 4) writes 1.2 for 12 bits. This is not wrong, only short: $5/4096=1.22$ mV.

> [!info] Quantisation error (beyond the slides)
> The ADC has only $2^n$ output values, but the input can be any voltage. All voltages inside one step give the same number. The difference between the real voltage and the voltage that the number stands for is the **quantisation error**.
> - With the formula of the slides (divide, then cut off the fraction) the error is between 0 and one step.
> - Example, 10 bits and $V_{ref}=5$ V: every input from 2.5000 V up to just below 2.5049 V gives the code 512. The program cannot tell these inputs apart.
> - The largest code is $2^n-1$, not $2^n$. For 10 bits and 5 V the code 1023 stands for $1023\cdot5/1024=4.995$ V. So the top of the range is $V_{ref}$ minus one step.
> - The error can be made smaller only with more bits or with a smaller $V_{ref}$.

> [!info] Sampling (beyond the slides)
> An ADC does not watch the input all the time. It takes a **sample**: a small capacitor inside the chip (sample and hold) stores the input voltage for a moment, and this stored voltage is converted. While the conversion runs, changes of the input are not seen.
> The number of samples per second is the **sampling rate**. It cannot be higher than 1 / conversion time. To follow a signal you need more than two samples per period of its fastest part (the Nyquist rule).

> [!question]- Check (slide 4, block diagram): A 12-bit ADC has the pins of the block diagram. How many data output lines does it have, what are their names, and what is the smallest and the largest number it can give?
> 12 bits need **12 lines**: D0 (LSB) to D11 (MSB).
> Number of steps: $2^{12}=4096$. The numbers go from **0** to $4096-1=$ **4095**.

> [!question]- Check (slide 4, table): The table is for $V_{ref}=5$ V. Compute the step size for $V_{ref}=3.3$ V with 10 bits and with 12 bits.
> 10 bits: $2^{10}=1024$ steps. $3.3/1024=0.003223$ V $=$ **3.22 mV**.
> 12 bits: $2^{12}=4096$ steps. $3.3/4096=0.000806$ V $=$ **0.81 mV**.
> Two more bits make the step 4 times smaller, because $2^2=4$.

> [!question]- Check (slide 5, conversion time): An ADC needs 13 clock cycles for one conversion. How long is one conversion with a 200 kHz ADC clock and with a 50 kHz ADC clock? Which of the three factors on the slide did you just change?
> One clock cycle at 200 kHz: $1/200\,000=5$ µs. $13\cdot5=$ **65 µs**.
> One clock cycle at 50 kHz: $1/50\,000=20$ µs. $13\cdot20=$ **260 µs**.
> This is the first factor: the clock source connected to the ADC. The method (13 cycles) and the technology stayed the same.

> [!question]- Check (slide 5, V_ref): A sensor gives 0 to 1 V. You have a 10-bit ADC. Compare $V_{ref}=5$ V and $V_{ref}=1.024$ V: step size, and how many different codes the sensor can produce.
> $V_{ref}=5$ V: step $=5/1024=4.88$ mV. Largest code for 1 V: $1/0.0048828=204.8$, so 204. The sensor uses only about **205** of the 1024 codes.
> $V_{ref}=1.024$ V: step $=1.024/1024=$ **1 mV**. Largest code for 1 V: $1/0.001=1000$. The sensor uses about **1000** codes.
> The smaller reference measures the same sensor almost 5 times finer. The price: nothing above 1.024 V can be measured.

## 3. The digital output formula (slide 6)
![[cmpe453_lecture_7_lecture7_adc_p006.jpg]]

**Notes.** The output number is the input voltage counted in steps:

$$D_{out}=\frac{V_{in}}{\text{step size}}=\frac{V_{in}}{V_{ref}/2^n}=\frac{V_{in}\cdot2^n}{V_{ref}}$$

- $D_{out}$: the number on the data lines (decimal). Write it in binary to get the bits D$(n-1)$ ... D0.
- $V_{in}$: the input voltage. $V_{ref}$: the reference voltage. $n$: the number of bits.
- If the division does not give a whole number, the fraction is cut off (this is the quantisation error).

The inverse gives the voltage back from the number: $V_{in}\approx D_{out}\cdot\text{step size}$.

> [!example] The slide example, every step
> 8-bit ADC, $V_{ref}=2.56$ V. Step size $=2.56/256=0.01$ V $=10$ mV.
> (a) $V_{in}=1.7$ V: $D_{out}=1.7/0.01=170$. In binary: $170=128+32+8+2$ = `10101010` (D7 ... D0).
> (b) $V_{in}=2.1$ V: $D_{out}=2.1/0.01=210$. In binary: $210=128+64+16+2$ = `11010010`.

> [!question]- Check (slide 6): Same ADC (8 bits, $V_{ref}=2.56$ V). Compute D7 ... D0 for (a) $V_{in}=0.85$ V and (b) $V_{in}=1.234$ V. For (b), which voltage does the program compute back from the number?
> Step size $=2.56/256=10$ mV.
> (a) $0.85/0.01=85$. $85=64+16+4+1$ = **`01010101`**.
> (b) $1.234/0.01=123.4$. Cut off the fraction: 123. $123=64+32+16+8+2+1$ = **`01111011`**.
> Back: $123\cdot0.01=1.23$ V. The 4 mV that are lost are the quantisation error. It is smaller than one step (10 mV).

## 4. The analog input pins (slide 7)
![[cmpe453_lecture_7_lecture7_adc_p007.jpg]]

**Notes.** The slide shows the pin mapping of the ATmega328P (28-pin package, as on the Uno).

| ADC channel | Port pin | Arduino name | Chip pin |
|---|---|---|---|
| ADC0 | PC0 | A0 (analog input 0) | 23 |
| ADC1 | PC1 | A1 | 24 |
| ADC2 | PC2 | A2 | 25 |
| ADC3 | PC3 | A3 | 26 |
| ADC4 | PC4 | A4 | 27 |
| ADC5 | PC5 | A5 | 28 |

Two more pins belong to the ADC: **AVCC** (pin 20), the supply voltage of the analog part, and **AREF** (pin 21), the analog reference pin.

The four points on the slide:
- **Multiple input channels**: six pins can be measured.
- **Single digital output register**: there is only one ADC inside the chip and one result register. A switch (the multiplexer, MUX) connects **one** channel at a time to the ADC. Two channels are measured one after the other, never at the same moment.
- **SC and EOC**: the program must give a start conversion signal, and the ADC must tell when the conversion has ended. On the AVR both are the bit `ADSC` (section 9).
- **Analog $V_{in}$ to $n$-bit digital number**: here $n=10$.

> [!warning] Watch out
> PC4 and PC5 are also SDA and SCL of the TWI (lecture 6.2). If I2C is used, only ADC0 to ADC3 are free for analog inputs.

> [!question]- Check (slide 7): A sensor is connected to the Uno pin A4. Give the port pin, the ADC channel and the chip pin number. The same board also has an LM75 on I2C. What is the problem? Why can the chip not measure A0 and A1 at exactly the same time?
> A4 = **PC4** = **ADC4** = chip pin **27**.
> PC4 is also SDA. With the LM75 on the bus, the pin is used by I2C, so the sensor must move to one of A0 to A3.
> There is only one ADC and one output register. The multiplexer connects only one channel at a time. The program measures A0, then switches the channel, then measures A1.

## 5. Successive approximation (slides 8-31)
![[cmpe453_lecture_7_lecture7_adc_p008.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p009.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p010.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p011.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p012.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p013.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p014.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p015.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p016.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p017.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p018.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p019.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p020.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p021.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p022.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p023.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p024.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p025.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p026.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p027.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p028.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p029.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p030.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p031.jpg]]

**Notes.** The AVR has an on-chip ADC. It converts by **successive approximation**: it finds the number bit by bit, like guessing a number with "higher or lower" questions.

The parts in the figure (slide 9):
- **SAR** (successive approximation register): holds the current guess, an $n$-bit number. At the end it holds the result.
- **DAC** (digital to analog converter): turns the guess back into a voltage, $V_{DAC}=\text{SAR}\cdot\text{step size}$. In the figure this voltage is called Vout.
- **Comparator** (the triangle): compares $V_{in}$ with $V_{DAC}$ and gives one bit: is $V_{in}$ higher?
- **Control**: sets or clears the bits of the SAR after each comparison.

Example for $V_{DAC}$ (slide 9): step size 1 V, SAR = `1010` = 10, so $V_{DAC}=10\cdot1=10$ V. A 4-bit ADC with a step of 1 V has $V_{ref}=16\cdot1=16$ V.

The method, in the order the table on the slides uses it:
1. Start with the MSB: set SAR = `1000` (half of the range).
2. The DAC makes $V_{DAC}$ from the SAR. The comparator compares.
3. If $V_{in}>V_{DAC}$, the tested bit **stays 1**. If not, it is **cleared to 0**.
4. Set the next lower bit to 1 and go to 2. After the LSB (index 0) the conversion is finished.

Each comparison halves the range in which $V_{in}$ can be. An $n$-bit result needs exactly $n$ comparisons.

The slides 10 to 31 fill one table for $V_{in}=11.2$ V, step by step. The complete table (slide 31):

| Index (tested bit) | SAR tried | $V_{DAC}$ | Comparison | Decision | SAR after |
|---|---|---|---|---|---|
| 3 | `1000` | 8 V | $11.2>8$ | bit 3 stays 1 | `1000` |
| 2 | `1100` | 12 V | $11.2<12$ | bit 2 cleared | `1000` |
| 1 | `1010` | 10 V | $11.2>10$ | bit 1 stays 1 | `1010` |
| 0 | `1011` | 11 V | $11.2>11$ | bit 0 stays 1 | `1011` |

Result: SAR = `1011` = 11, which stands for 11 V. The real input was 11.2 V. The 0.2 V difference is the quantisation error.

How to read the slide table:
- The **x** marks the bit that is being decided. `x100` (slide 14) means: bit 3 is not decided yet, and bit 2 is already set to 1 for the next test.
- Column **Index** is the bit under test. Column **Index-1** is the bit that is set for the next round. In the last row Index-1 is −1: there is no next bit, so `101x` has nothing new to set.
- $V_{DAC}$ in each row belongs to the SAR value in the same row.
- The slides compare with $>$. If $V_{in}$ is exactly equal to $V_{DAC}$, this rule clears the bit, and the result is one lower than the formula $V_{in}/\text{step size}$ gives. A real converter keeps the bit in this case (beyond the slides). In the exercises the input is never exactly on a step.

> [!warning] Slide error
> The seven steps on the slides are not exact. Use the table.
> - Step 4 sets the next bit before step 5 compares. Read literally, the first comparison would use `1100` (12 V). The table compares with `1000` (8 V) first. The comparison in step 5 uses the $V_{DAC}$ from **before** step 4.
> - "Step 7: Go to Step 4" never stops. The loop ends when Index becomes smaller than 0, after 4 comparisons.
> - Check with a sum: the bits that stay 1 must add up to the result, $8+2+1=11$.

> [!info] Why not simply count up? (beyond the slides)
> A simple ADC could try 0, 1, 2, ... until $V_{DAC}$ passes $V_{in}$. For 10 bits this needs up to 1024 comparisons. Successive approximation always needs 10. This is the "data conversion method" factor of the conversion time on slide 5.
> It works only if $V_{in}$ does not change during the conversion. This is the job of the sample and hold capacitor from section 2.

> [!question]- Check (slide 8): The ATmega328P ADC has 10 bits. How many comparisons does one conversion need with successive approximation? How many would a converter need in the worst case if it tested the codes one by one from 0?
> Successive approximation: one comparison per bit, so **10**.
> One by one: up to $2^{10}=$ **1024**.
> The number of comparisons is fixed. It does not depend on the input voltage.

> [!question]- Check (slide 9): A 4-bit converter has a step size of 0.5 V. What is its $V_{ref}$? What is $V_{DAC}$ for SAR = `0110`, and for the first guess `1000`?
> $V_{ref}=2^4\cdot0.5=16\cdot0.5=$ **8 V**.
> `0110` = 6: $V_{DAC}=6\cdot0.5=$ **3 V**.
> `1000` = 8: $V_{DAC}=8\cdot0.5=$ **4 V**. This is exactly half of $V_{ref}$. The first comparison always asks "is the input in the upper half?".

> [!question]- Check (slides 10-12): The table starts with Index = 3, SAR = `1000`, $V_{in}=11.2$ V. Why does the SAR start at `1000` and not at `0000` or `1111`? Which column would change if the input were 6.7 V?
> `1000` is the middle of the range (8 V of 16 V). One comparison then removes half of all possible values. Starting at `0000` or `1111` would remove almost nothing.
> Index 3 is the MSB ($n-1=4-1=3$), the bit with the largest weight, so it is decided first.
> With 6.7 V only the column $V_{in}$ changes. The first guess is always `1000`, whatever the input is.

> [!question]- Check (slides 13-15): Do the first row for $V_{in}=6.7$ V (step 1 V). Which $V_{DAC}$ is compared, what goes in the Comment column, and what is bit 3?
> SAR = `1000`, so $V_{DAC}=8\cdot1=8$ V.
> $6.7<8$, so the comment is $V_{in}<V_{DAC}$.
> Bit 3 is **cleared to 0**. (On the slides, 11.2 V was above 8 V, so bit 3 stayed 1.)
> The slide notation before the decision is `x100`: bit 3 open, bit 2 already set for the next test.

> [!question]- Check (slides 16-17): On slides 16-17 the second row becomes `1100` and Index becomes 2. What would the second row show for $V_{in}=6.7$ V? What is its $V_{DAC}$?
> Bit 3 was cleared, and bit 2 is set for the test: SAR = **`0100`**, Index = 2.
> $V_{DAC}=4\cdot1=$ **4 V**.
> The slides show `1100` because there bit 3 stayed 1: $8+4=12$ V.

> [!question]- Check (slides 18-20): Continue with $V_{in}=6.7$ V: SAR = `0100`, $V_{DAC}=4$ V. What is the decision for bit 2, and which value is tried next? Compare with the slides, where `1100` gives 12 V.
> $6.7>4$: bit 2 **stays 1**.
> Next, bit 1 is set: SAR = **`0110`**, $V_{DAC}=6$ V.
> On the slides $11.2<12$, so bit 2 was cleared and the next value was `1010` (`1x10` with x = 0).

> [!question]- Check (slides 21-23): On slide 21 the SAR is `1010`, not `1110`. Why? Which numbers stand in the columns Index and Index-1 of this row?
> The test of bit 2 failed ($11.2<12$), so bit 2 was cleared. Then bit 1 was set: `1000` + `0010` = `1010`. `1110` would keep a bit that was already found to be too much.
> Index = 1 (bit 1 is tested now), Index-1 = 0 (bit 0 is set next).

> [!question]- Check (slides 24-26): $V_{in}=6.7$ V, third row: SAR = `0110`. Compute $V_{DAC}$, decide bit 1 and give the SAR for the last row.
> $V_{DAC}=6\cdot1=6$ V.
> $6.7>6$: bit 1 **stays 1**.
> Bit 0 is set for the last test: SAR = **`0111`**, $V_{DAC}=7$ V.

> [!question]- Check (slides 27-29): In the last row of the slides Index = 0 and Index-1 = −1. What does −1 mean? What does `101x` on slide 29 show?
> There is no bit −1. So no further bit is set, and this is the last comparison.
> `101x`: bits 3, 2, 1 are fixed (`101`), and only bit 0 is still open. The comparison of `1011` (11 V) with 11.2 V decides it.

> [!question]- Check (slides 30-31): Finish $V_{in}=6.7$ V: last row SAR = `0111`. Give the result, the voltage it stands for and the quantisation error. Compare the error with the slide example (11.2 V).
> $V_{DAC}=7$ V. $6.7<7$: bit 0 is **cleared**.
> Result: **`0110`** = 6, which stands for 6 V. Check with the formula: $6.7/1=6.7$, cut off: 6.
> Quantisation error: $6.7-6=0.7$ V. Slide example: $11.2-11=0.2$ V.
> Both are smaller than one step (1 V). The result is the largest code whose voltage is below $V_{in}$.

## 6. The ADC of the ATmega328P (slides 32-33)
![[cmpe453_lecture_7_lecture7_adc_p032.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p033.jpg]]

**Notes.** The facts on slide 32:
- **10-bit ADC**: results 0 to 1023.
- **6 analog inputs**: ADC0 to ADC5 (PC0 to PC5).
- The result is in two 8-bit registers: **`ADCL`** (low byte) and **`ADCH`** (high byte), 16 bits together. The slide writes "HSB"; it means the high (most significant) byte.
- The result has 10 bits, so 6 of the 16 bits are unused. You choose whether the unused bits are the upper ones (right-adjusted) or the lower ones (left-adjusted). The bit `ADLAR` selects this (section 7).
- $V_{ref}$ can be: **AVCC** (the analog supply, 5 V on the Uno), the **internal 1.1 V** reference, or an external voltage on the **AREF** pin.
- The conversion time depends on the crystal frequency ($F_{osc}$, 16 MHz on the Uno) and on the prescaler bits `ADPS2:0` (section 9).

The conversion formula of the datasheet, for 10 bits:

$$\text{ADC}=\frac{V_{in}\cdot1024}{V_{ref}}\qquad V_{in}\approx\frac{\text{ADC}\cdot V_{ref}}{1024}$$

In this course the result is cut off to a whole number, 0 to 1023. `0x000` is GND. `0x3FF` (1023) is $V_{ref}$ minus one step. (Beyond the slides: the datasheet gives only the formula and does not say how the fraction is handled. The real ADC also has an error of about 2 steps, so the last bit is not exact anyway.)

The block diagram (figure 7-1) is the successive approximation figure of section 5 with real names:
- **Input multiplexer**: selects one of ADC0 ... ADC5. Controlled by `ADMUX`.
- **Ref mux**: selects AVCC, 1.1 V or AREF as the reference of the DAC. Also controlled by `ADMUX`.
- **10-bit DAC** and **comparator**: make the guess voltage and compare it with the input.
- **Conversion logic**: the control and the SAR. It gets its clock from the **clock prescale** block.
- **`ADCSRA` / `ADCSRB`**: control and status. **`ADCH` / `ADCL`**: the result.

> [!example] The slide example, every step
> 10-bit ADC, $V_{ref}=2.56$ V. Step size $=2.56/1024=0.0025$ V $=2.5$ mV.
> (a) $V_{in}=0.2$ V: $D_{out}=0.2/0.0025=80$. $80=64+16$ = `1010000`. As D9 ... D0: `0001010000`.
> (b) $V_{in}=0$ V: $D_{out}=0$ = `0000000000`.
> The difference is 80 = `1010000`, a number of 7 bits. So a change of only 0.2 V moves the output by 80 steps.

> [!warning] Watch out
> $V_{in}$ must stay between GND and $V_{ref}$. A higher voltage only gives 1023. A voltage above the supply (or below GND) can damage the pin.

> [!question]- Check (slide 32): Follow one conversion in the block diagram. Which block selects the pin, which block selects the reference, which register starts the conversion and where is the result? Which blocks correspond to "SAR + Control" of slide 9?
> The **input multiplexer** selects the pin, the **ref mux** selects the reference. Both are set by `ADMUX` (multiplexer control).
> The conversion is started through `ADCSRA` (control and status). The result is in `ADCH` / `ADCL` (ADC data).
> "SAR + Control" is the block **conversion logic**. It sends the guess to the 10-bit DAC and gets the answer from the comparator.

> [!question]- Check (slide 33): (a) 10-bit ADC, $V_{ref}=2.56$ V, $V_{in}=0.6$ V: give D9 ... D0. (b) ATmega328P with $V_{ref}=5$ V, $V_{in}=3.2$ V: give the result in decimal and hex, and the voltage that the program computes back.
> (a) Step $=2.56/1024=2.5$ mV. $0.6/0.0025=240$. $240=128+64+32+16$ = `11110000`. As 10 bits: **`0011110000`**.
> (b) $\text{ADC}=3.2\cdot1024/5=655.36$. Cut off: **655** = `1010001111` = **`0x28F`**.
> Back: $655\cdot5/1024=3.198$ V. The error is about 1.8 mV, less than one step (4.88 mV).

## 7. ADMUX: reference, channel and alignment (slides 34-35)
![[cmpe453_lecture_7_lecture7_adc_p034.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p035.jpg]]

**Notes.** `ADMUX` is the ADC multiplexer selection register (address `0x7C`). All bits are 0 after reset.

| Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|
| Name | `REFS1` | `REFS0` | `ADLAR` | - | `MUX3` | `MUX2` | `MUX1` | `MUX0` |

**`REFS1:0`** select the reference voltage (slide 34):

| `REFS1` | `REFS0` | Reference |
|---|---|---|
| 0 | 0 | AREF pin, internal reference turned off (default) |
| 0 | 1 | AVCC, with an external capacitor at the AREF pin |
| 1 | 0 | reserved |
| 1 | 1 | internal 1.1 V reference, with an external capacitor at the AREF pin |

On the Uno, AVCC is connected to 5 V and the capacitor at AREF is already on the board. So `ADMUX |= (1 << REFS0);` gives $V_{ref}=5$ V. All slide programs use this line.

**`MUX3:0`** select the input channel (slide 35): `0000` = ADC0, `0001` = ADC1, `0010` = ADC2, `0011` = ADC3, `0100` = ADC4, `0101` = ADC5. The value is simply the channel number in binary. The default `0000` is ADC0 = PC0.

**`ADLAR`** (ADC left adjust result) selects where the 10 result bits D9 ... D0 lie in `ADCH:ADCL` (figure on slide 35):

| `ADLAR` | `ADCH` | `ADCL` | Name |
|---|---|---|---|
| 0 | 6 unused bits, then D9 D8 | D7 ... D0 | right-justified (default) |
| 1 | D9 ... D2 | D1 D0, then 6 unused bits | left-justified |

- `ADLAR = 0`: the 16-bit value `ADC` is directly the number 0 to 1023. Use this for the full 10 bits.
- `ADLAR = 1`: `ADCH` alone holds the top 8 bits. Use this when 8 bits are enough: read only `ADCH` (0 to 255). This is the "8-bit mode" of slide 39.

Bit 4 is not used. It always reads 0.

> [!warning] Watch out
> - With an external voltage connected to AREF, never select AVCC or the internal 1.1 V. The two voltages would be shorted together inside the chip (beyond the slides).
> - The slide table of channels ends with `0110` = ADC6. ADC6 and ADC7 exist only in the 32-pin surface-mount packages. The 28-pin chip on the Uno has only ADC0 to ADC5.
> - After changing the reference, the first conversion may be inaccurate. Throw it away (beyond the slides).

> [!info] The other MUX values (beyond the slides)
> The datasheet table goes on: `1000` = the internal temperature sensor (channel ADC8, used with the 1.1 V reference), `1110` = the internal 1.1 V voltage, `1111` = 0 V (GND). The other values are reserved.

> [!question]- Check (slide 34): You want the internal 1.1 V reference, channel ADC2, right-adjusted. Which bits of `ADMUX` are 1? Give the value in binary and hex, and one C line that sets it.
> Internal 1.1 V: `REFS1` = 1, `REFS0` = 1. Right-adjusted: `ADLAR` = 0. ADC2: `MUX3:0` = `0010`, so `MUX1` = 1.
> `ADMUX` = `1100 0010` = **`0xC2`**.
> `ADMUX = (1 << REFS1) | (1 << REFS0) | (1 << MUX1);`

> [!question]- Check (slide 35, channel): Give `ADMUX` for: reference AVCC, left-adjusted result, channel ADC5. Then decode `ADMUX = 0x43`.
> AVCC: `REFS1:0` = `01` (bit 6). Left-adjusted: `ADLAR` = 1 (bit 5). ADC5: `MUX3:0` = `0101`.
> `0100 0000` + `0010 0000` + `0000 0101` = `0110 0101` = **`0x65`**.
> `0x43` = `0100 0011`: `REFS1:0` = `01` (AVCC), `ADLAR` = 0 (right-adjusted), `MUX3:0` = `0011` = **ADC3** (PC3, A3).

> [!question]- Check (slide 35, ADLAR): A conversion gives the 10-bit result `0x2D7`. What do `ADCH` and `ADCL` contain with `ADLAR = 0` and with `ADLAR = 1`? What number does `ADCH` alone give in the second case?
> `0x2D7` = 727 = `10 1101 0111` (D9 ... D0).
> `ADLAR = 0`: `ADCH` = `000000 10` = **`0x02`**, `ADCL` = `1101 0111` = **`0xD7`**.
> `ADLAR = 1`: `ADCH` = D9 ... D2 = `1011 0101` = **`0xB5`**, `ADCL` = D1 D0 then six zeros = `1100 0000` = **`0xC0`**.
> `ADCH` alone = `0xB5` = 181. This is $727/4$ with the fraction cut off ($727\gg2=181$): the 8-bit version of the same measurement.

## 8. The ADC data register and the read order (slide 36)
![[cmpe453_lecture_7_lecture7_adc_p036.jpg]]

**Notes.** The result registers are `ADCL` (address `0x78`) and `ADCH` (address `0x79`). They are read-only. In the pictures the bits are called ADC9 ... ADC0 (the same as D9 ... D0).

- Upper picture, `ADLAR = 0`: `ADCH` holds ADC9 ADC8 in its bits 1 and 0, `ADCL` holds ADC7 ... ADC0.
- Lower picture: this is the case `ADLAR = 1` (the label is missing on the slide). `ADCH` holds ADC9 ... ADC2, `ADCL` holds ADC1 ADC0 in its bits 7 and 6.

**Read order.** When `ADCL` is read, the ADC data register is not updated until `ADCH` is read. The reason: the result is in two bytes. If a new conversion finished between the two reads, the low byte would come from the old result and the high byte from the new one. So reading `ADCL` locks both registers, and reading `ADCH` unlocks them.

Rules:
- Read **`ADCL` first, then `ADCH`**.
- If the result is left-adjusted and 8 bits are enough, read only `ADCH`.
- In avr-gcc the 16-bit name `ADC` reads both bytes in the correct order: `uint16_t v = ADC;`.

By hand it would be:

```c
uint8_t low  = ADCL;                 /* first: locks the result */
uint8_t high = ADCH;                 /* second: unlocks it */
uint16_t v = ((uint16_t)high << 8) | low;
```

> [!question]- Check (slide 36): (a) `ADLAR = 0`. A program reads `ADCL` = `0x9C` and then `ADCH` = `0x01`. What is the result, and which voltage is it with $V_{ref}=5$ V? (b) Another program reads only `ADCL` in every loop and never `ADCH`. What goes wrong?
> (a) Result = `(0x01 << 8) | 0x9C` = `0x19C` = $256+156=$ **412**.
> $V_{in}\approx412\cdot5/1024=$ **2.01 V**.
> (b) The first read of `ADCL` locks the data register. It is unlocked only by reading `ADCH`, and that never happens. New results are not stored, so the program sees the same old value forever. It also loses the two top bits of the result.

## 9. ADCSRA: the clock prescaler and the control bits (slides 37-38)
![[cmpe453_lecture_7_lecture7_adc_p037.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p038.jpg]]

**Notes.** `ADCSRA` is the ADC control and status register A (address `0x7A`). All bits are 0 after reset.

| Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|
| Name | `ADEN` | `ADSC` | `ADATE` | `ADIF` | `ADIE` | `ADPS2` | `ADPS1` | `ADPS0` |

**The ADC clock.** The successive approximation circuit needs its own clock. The ADC operates reliably between **50 kHz and 200 kHz**. The CPU clock is much faster, so a prescaler divides it:

$$f_{ADC}=\frac{f_{CPU}}{\text{division factor}}$$

| `ADPS2` | `ADPS1` | `ADPS0` | Division factor | $f_{ADC}$ at 16 MHz |
|---|---|---|---|---|
| 0 | 0 | 0 | 2 | 8 MHz |
| 0 | 0 | 1 | 2 | 8 MHz |
| 0 | 1 | 0 | 4 | 4 MHz |
| 0 | 1 | 1 | 8 | 2 MHz |
| 1 | 0 | 0 | 16 | 1 MHz |
| 1 | 0 | 1 | 32 | 500 kHz |
| 1 | 1 | 0 | 64 | 250 kHz |
| 1 | 1 | 1 | 128 | **125 kHz** |

For 16 MHz the factor must be between $16\,000\,000/200\,000=80$ and $16\,000\,000/50\,000=320$. Only **128** fits. So on the Uno:

```c
ADCSRA |= (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0);   /* 16 MHz / 128 = 125 kHz */
```

Note that `000` and `001` both give 2. A division factor of 1 does not exist.

**Conversion time** (beyond the slides: the numbers 13 and 25 are from the datasheet). A normal conversion takes **13 ADC clock cycles**. The first conversion after the ADC is enabled takes **25** cycles, because the analog circuits must start up first. At 125 kHz one cycle is 8 µs:
- normal: $13\cdot8=104$ µs, so at most $125\,000/13\approx9615$ conversions per second,
- first: $25\cdot8=200$ µs.

**The other bits** (slide 38, with the two that the slide does not explain):
- **`ADEN`** (ADC enable): 1 = ADC on, 0 = ADC off. Nothing works before this bit is 1.
- **`ADSC`** (ADC start conversion). In single conversion mode: writing 1 starts a new conversion, writing 0 has no effect. Reading 1 = conversion in progress, reading 0 = conversion completed. The hardware clears the bit when the conversion ends. So `ADSC` is both the start signal (SC) and the end signal (EOC) of slide 7. In free running mode you write 1 once, to start the first conversion only.
- **`ADATE`** (ADC auto trigger enable): 1 = a trigger source starts the conversions. The source is selected by `ADTS2:0` in `ADCSRB`. `000` = free running mode (section 13).
- **`ADIF`** (ADC interrupt flag): the hardware sets it to 1 when a conversion is finished and the data registers are updated. It is cleared when the interrupt routine runs, or by writing a 1 to it.
- **`ADIE`** (ADC interrupt enable): 1 = the ADC conversion complete interrupt runs when `ADIF` is set (the global interrupts must be enabled too).

One single conversion in C:

```c
ADCSRA |= (1 << ADSC);                    /* start */
loop_until_bit_is_clear(ADCSRA, ADSC);    /* wait: ADSC stays 1 until done */
uint16_t value = ADC;                     /* read ADCL, then ADCH */
```

> [!warning] Slide error
> In the left table of slide 37 (CPU clock, prescale, ADC frequency) the block for **8 MHz** is wrong by a factor of 2. Correct values: 8 MHz / 16 = 500 kHz, / 32 = 250 kHz, / 64 = 125 kHz, / 128 = 62.5 kHz. The slide lists 250, 125, 62.5 and 31.25 kHz. So at 8 MHz only 64 and 128 are inside 50 to 200 kHz. The rows for 1 MHz, 12 MHz and 16 MHz are correct. (The table is copied from Williams' book, which has the same mistake.)
> Not an error, but watch out: the table also lists some values outside 50 to 200 kHz, for example 1 MHz / 4 = 250 kHz, 1 MHz / 32 = 31.25 kHz and 16 MHz / 64 = 250 kHz. The book says that they are just outside the official range and not guaranteed. For 16 MHz use 128.

> [!info] What a faster ADC clock costs (beyond the slides)
> The limit of 200 kHz is for the full 10-bit resolution. The datasheet allows a faster ADC clock if a lower resolution is enough: the conversion is faster, but the lowest bits become unreliable. This is why the slide programs, which use too small prescalers for a 16 MHz board, still seem to work when only the top bits are used.

> [!info] Sample and hold timing (beyond the slides)
> In a normal conversion the input is sampled 1.5 ADC clock cycles after the start (13.5 cycles in the first conversion). The remaining cycles are the bit-by-bit comparisons. At about 9615 samples per second the ADC can follow signals up to about 4.8 kHz (half the sampling rate).

> [!question]- Check (slide 37): A board runs at 20 MHz. Which division factors keep the ADC clock between 50 and 200 kHz? Give $f_{ADC}$, the `ADPS` bits, and the time of a normal and of the first conversion.
> Allowed factors: from $20\,000\,000/200\,000=100$ to $20\,000\,000/50\,000=400$. Only **128** is in this range.
> $f_{ADC}=20\,000\,000/128=$ **156.25 kHz**. Bits: `ADPS2 ADPS1 ADPS0` = `111`.
> One cycle $=1/156\,250=6.4$ µs. Normal conversion: $13\cdot6.4=$ **83.2 µs**. First conversion: $25\cdot6.4=$ **160 µs**.
> (Factor 64 would give 312.5 kHz: too fast.)

> [!question]- Check (slide 38): `ADCSRA` contains `0xC7`. Decode every bit. What is the ADC doing right now, and what will the register contain when it is done?
> `0xC7` = `1100 0111`.
> `ADEN` = 1: ADC enabled. `ADSC` = 1: a conversion is in progress. `ADATE` = 0: single conversion mode. `ADIF` = 0, `ADIE` = 0: no interrupt. `ADPS2:0` = `111`: prescaler 128.
> When the conversion is done the hardware clears `ADSC` and sets `ADIF`: `1001 0111` = **`0x97`**. `ADIF` stays 1 because no interrupt routine clears it. This does not disturb the polling method, which only looks at `ADSC`.

## 10. The steps to use the ADC (slide 39)
![[cmpe453_lecture_7_lecture7_adc_p039.jpg]]

**Notes.** The checklist of the slide, with the register for every step:

| Step | What | Bits |
|---|---|---|
| I | set the ADC clock prescaler | `ADPS2:0` in `ADCSRA` |
| II | define $V_{ref}$ (default: external voltage on AREF) | `REFS1:0` in `ADMUX` |
| III | select the analog channel (default: ADC0 = PC0) | `MUX3:0` in `ADMUX` |
| IV | choose the mode: single conversion or auto-triggered | `ADATE` in `ADCSRA`, `ADTS2:0` in `ADCSRB` |
| V | optional: interrupt when a conversion completes | `ADIE` in `ADCSRA` |
| VI | other options: 8-bit mode, power saving | `ADLAR` in `ADMUX`, `DIDR0` |

Then enable the ADC (`ADEN`) and start a conversion (`ADSC`). The slide list does not name these two, but nothing happens without them.

The two modes:
- **Single conversion mode**: the program must start every conversion itself by setting `ADSC = 1`.
- **Auto trigger mode**: a hardware event starts each conversion automatically. The event can be internal (a timer, or the end of the last conversion = free running) or external (the INT0 pin).

A complete setup for the Uno (AVCC reference, channel ADC0, single conversion):

```c
ADMUX  = (1 << REFS0);                                   /* AVCC, right-adjusted, ADC0 */
ADCSRA = (1 << ADEN) | (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0);  /* on, clock / 128 */
```

> [!warning] Slide error
> Step I says "default: disabled i.e. no conversion". The prescaler cannot be disabled. After reset `ADPS2:0` = `000`, which is a division factor of 2 (8 MHz at 16 MHz, far too fast). No conversion happens after reset because the **ADC** is disabled (`ADEN = 0`), not the prescaler.

> [!info] DIDR0: switching off the digital input buffers (beyond the slides)
> Each pin PC0 ... PC5 also has a digital input buffer (it feeds `PINC`). With an analog voltage near the middle between 0 and 5 V, this buffer wastes power.
> The register `DIDR0` (digital input disable register 0, address `0x7E`) switches the buffers off: bits 5 ... 0 are `ADC5D`, `ADC4D`, `ADC3D`, `ADC2D`, `ADC1D`, `ADC0D`. Bits 7 and 6 are not used.
> Writing 1 to `ADCnD` disables the digital input of pin ADCn. The `PINC` bit of that pin then always reads 0. The analog measurement is not changed.
> Example for an analog sensor on A0: `DIDR0 |= (1 << ADC0D);`. This is the "power saving" option of step VI.

> [!question]- Check (slide 39): Write the two register values for: internal 1.1 V reference, channel ADC1, right-adjusted, single conversion, no interrupt, 16 MHz board, ADC enabled but no conversion started yet. Which line starts a conversion later?
> `ADMUX`: `REFS1:0` = `11`, `ADLAR` = 0, `MUX3:0` = `0001`: `1100 0001` = **`0xC1`**.
> `ADCSRA`: `ADEN` = 1, `ADSC` = 0, `ADATE` = 0, `ADIF` = 0, `ADIE` = 0, `ADPS2:0` = `111` (16 MHz / 128 = 125 kHz): `1000 0111` = **`0x87`**.
> Start: `ADCSRA |= (1 << ADSC);`. The `|=` keeps the other bits. `ADCSRA = (1 << ADSC);` would switch the ADC off again.

## 11. Example 1: a potentiometer and eight LEDs (slides 40-41)
![[cmpe453_lecture_7_lecture7_adc_p040.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p041.jpg]]

**Notes.** A potentiometer is a voltage divider that you turn by hand. Its two outer pins go to 5 V and GND, the middle pin goes to the analog input. The middle pin gives 0 to 5 V. Here it is on ADC0 (PC0, A0), the default channel: in the figure of slide 40 the wire from the potentiometer goes to the pin A0. Eight LEDs are on port D (digital pins 0 to 7).

The plan (slide 40): initialise the ADC and the LED port, then forever: start a conversion, wait until it completes, take the three MSBs of the result, light that many LEDs.

```c
#include <avr/io.h>
#include <util/delay.h>
#include "pinDefines.h"
#define LED_DDR DDRD
#define LED_PORT PORTD
// -------- Functions --------- //
static inline void initADC0(void)
      {
      ADMUX |= (1 << REFS0);  /* reference voltage on AVCC */
      ADCSRA |= (1 << ADPS2); /* ADC clock prescaler /16 */
      ADCSRA |= (1 << ADEN);  /* enable ADC */
      }
int main(void)
      {
      // -------- Inits --------- //
      uint8_t ledValue;
      uint16_t adcValue;
      uint8_t i;
      initADC0();
      LED_DDR = 0xff;
      // ------ Event loop ------ //
      while (1)
            {
            ADCSRA |= (1 << ADSC);                  /* start ADC conversion */
            loop_until_bit_is_clear(ADCSRA, ADSC);  /* wait until done */
            adcValue = ADC;                         /* read ADC in */
            /* Have 10 bits, want 3 (eight LEDs after all) */
            ledValue = (adcValue >> 7);
            /* Light up all LEDs up to ledValue */
            LED_PORT = 0;
            for (i = 0; i <= ledValue; i++)
                  {
                  LED_PORT |= (1 << i);
                  }
            _delay_ms(50);
            } /* End event loop */
      return 0; /* This line is never reached */
      }
```

Line by line:
- `initADC0`: AVCC as reference, prescaler bits, ADC on. The channel is not written, so it stays at the default ADC0.
- `LED_DDR = 0xff;`: all eight pins of port D are outputs.
- `ADCSRA |= (1 << ADSC);` starts one conversion. `loop_until_bit_is_clear(ADCSRA, ADSC);` waits in an empty loop until the hardware clears `ADSC`.
- `adcValue = ADC;` reads the 10-bit result (0 to 1023).
- `ledValue = (adcValue >> 7);` shifts right by 7. Of 10 bits, 3 are left: the three MSBs, a number 0 to 7. The slide example: `0110100110` (422) `>> 7` = `011` (3). It is the same as dividing by 128 and cutting off the fraction.
- The `for` loop sets the bits 0 ... `ledValue` of port D. So `ledValue + 1` LEDs are on: for 3, the LEDs on PD0 to PD3.

Each LED stands for $5/8=0.625$ V of input voltage.

> [!warning] Problems in the slide program
> - **The prescaler fits a 1 MHz chip**, not the Uno. The program is adapted from Williams' book, where the chip runs at 1 MHz: $1\text{ MHz}/16=62.5$ kHz. On the 16 MHz Uno, `ADPS2` alone gives $16\text{ MHz}/16=1$ MHz, five times above the limit. It still works here because only the top 3 bits are used. For correct 10-bit results set all three `ADPS` bits (125 kHz).
> - **At least one LED is always on.** The loop condition is `i <= ledValue`, so even `ledValue = 0` lights the LED on PD0. The bar shows 1 to 8 LEDs, never 0.
> - PD0 and PD1 are also the USART pins RXD and TXD of the Uno. This program does not use the USART, so it is fine here.

> [!question]- Check (slide 40): The potentiometer gives 2.0 V ($V_{ref}=5$ V). Follow the pseudocode: which ADC value is read, what are its three MSBs, and how many LEDs are on in the slide program?
> $\text{ADC}=2.0\cdot1024/5=409.6$, cut off: **409** = `0110011001`.
> Three MSBs: `011` = 3. ($409\gg7=3$, because $3\cdot128=384\le409<512$.)
> The loop runs for `i` = 0, 1, 2, 3: **4 LEDs** are on (PD0 to PD3).

> [!question]- Check (slide 41): (a) What does the program show for ADC values 100 and 1023? (b) A student changes the shift to `adcValue >> 6`. What goes wrong? (c) Which single line must change so that the ADC clock is correct on the 16 MHz Uno?
> (a) $100\gg7=0$: the loop runs once, **1 LED**. $1023\gg7=7$: the loop runs 8 times, **8 LEDs**.
> (b) `>> 6` leaves 4 bits: 0 to 15. For values above 7 the loop computes `1 << i` with `i` up to 15. Only bits 0 to 7 exist in the 8-bit port, so all 8 LEDs are already on when `ledValue` reaches 7. That is at the ADC value $7\cdot64=448$ (about 2.19 V), below half of the range. From there up to 5 V the bar shows no change.
> (c) `ADCSRA |= (1 << ADPS2);` becomes `ADCSRA |= (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0);`. Then $16\text{ MHz}/128=125$ kHz.

## 12. Example 2: two channels with the multiplexer (slides 42-44)
![[cmpe453_lecture_7_lecture7_adc_p042.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p043.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p044.jpg]]

**Notes.** Purpose: use the MUX of the on-chip ADC to read two analog inputs.
- A potentiometer on **PC3** (ADC3) sets a threshold voltage.
- A light dependent resistor (**LDR**) on **PC0** (ADC0) measures the light.
- When the LDR signal crosses the threshold, the 8 LEDs on port D are turned off. Otherwise they stay on.

An LDR is a resistor whose resistance falls when more light hits it. A resistor alone gives no voltage, so it is used in a voltage divider with a fixed resistor (the small resistor next to it in the figure). The middle point of the divider goes to the ADC pin.

The plan (slide 43): initialise the ADC and port D, then forever: read the threshold from the pot channel, read the sensor value from the LDR channel, if sensor < threshold turn the LEDs on, else turn them off.

```c
#include <avr/io.h>
#include <util/delay.h>
#include "pinDefines.h"

#define LED_DDR DDRD
#define LED_PORT PORTD
#define POT PC3
#define LIGHT_SENSOR PC0
uint16_t readADC(uint8_t channel)
{
ADMUX = (0xf0 & ADMUX) | channel;
ADCSRA |= (1 << ADSC);
loop_until_bit_is_clear(ADCSRA, ADSC);
return (ADC);
}

int main(void)
{
// -------- Inits --------- //
uint16_t lightThreshold;
uint16_t sensorValue;
// Set up ADC
ADMUX |= (1 << REFS0); /* reference voltage on AVCC */
ADCSRA |= (1 << ADPS1) | (1 << ADPS0); /* ADC clock prescaler /8 */
ADCSRA |= (1 << ADEN); /* enable ADC */

LED_DDR = 0xff;
// ------ Event loop ------ //
while (1)
         {
         lightThreshold = readADC(POT);
         sensorValue = readADC(LIGHT_SENSOR);
         if (sensorValue < lightThreshold)
                 LED_PORT = 0xff;
         else
                 LED_PORT = 0x00;
         } /* End event loop */
return 0; /* This line is never reached */
}
```

The new part is `readADC(channel)`:
- `ADMUX = (0xf0 & ADMUX) | channel;` changes only the channel. `0xf0 & ADMUX` keeps the upper four bits (`REFS1`, `REFS0`, `ADLAR`, bit 4) and clears `MUX3:0`. Then `| channel` writes the new channel number into the cleared bits.
- Then the same three steps as before: start, wait, return `ADC`.
- `POT` is `PC3`, which is the number 3. `LIGHT_SENSOR` is `PC0`, the number 0. The pin numbers are used as channel numbers. This works because ADC$n$ is on PC$n$.

The two voltages are measured one after the other, with the same ADC, and compared as numbers. Both use the same $V_{ref}$, so comparing the numbers is the same as comparing the voltages.

> [!warning] Problems in the slide program
> - **Prescaler /8** gives $16\text{ MHz}/8=2$ MHz on the Uno, ten times above the 200 kHz limit (it is right for a 1 MHz chip: 125 kHz). The two 10-bit values are compared in full, so here the low bits matter. Use `ADPS2`, `ADPS1` and `ADPS0` together.
> - The code turns the LEDs **on** when the sensor value is **below** the threshold. Whether "below" means dark or bright depends on how the divider is wired. In the enlarged figure of slide 42 the LDR goes from the 5 V rail to the A0 wire, and the fixed resistor goes from the A0 wire to the GND rail (as far as the wires can be followed). Then more light gives a lower LDR resistance and a **higher** voltage on A0. So the LEDs are on in the dark and go off when the daylight crosses the limit. This agrees with the text of slide 42. With the LDR and the resistor swapped it would be the other way round.
> - Near the threshold the LEDs can flicker, because noise moves the reading above and below it. A small gap between the on and the off threshold (hysteresis) fixes this.

> [!question]- Check (slide 42): The potentiometer is set to 2.0 V and the LDR divider gives 1.5 V ($V_{ref}=5$ V). Which two numbers does the program compare, and are the LEDs on or off? What happens when the LDR voltage rises to 2.4 V?
> Threshold: $2.0\cdot1024/5=409.6$, so **409**. Sensor: $1.5\cdot1024/5=307.2$, so **307**.
> $307<409$ is true: `LED_PORT = 0xff`, the LEDs are **on**.
> At 2.4 V: $2.4\cdot1024/5=491.52$, so 491. $491<409$ is false: `LED_PORT = 0x00`, the LEDs are **off**. The signal has crossed the threshold.

> [!question]- Check (slide 43): The pseudocode reads the threshold inside the `while(1)` loop, not once before it. Why? How many conversions does one pass of the loop need, and how long do they take at a correct ADC clock of 125 kHz?
> The user can turn the potentiometer at any time. Reading it in every pass makes a new threshold work immediately. Read once before the loop, it would be fixed until the next reset.
> One pass needs **2** conversions (pot, then LDR). Each takes 13 cycles of 8 µs = 104 µs, together about **208 µs** (the very first conversion after power-on takes 200 µs instead of 104 µs).

> [!question]- Check (slide 44): `ADMUX` is `0x43` (AVCC, channel 3). The program calls `readADC(LIGHT_SENSOR)`. Compute the new `ADMUX` step by step. What would happen with `ADMUX |= channel;` instead?
> `LIGHT_SENSOR` = `PC0` = 0.
> `0xf0 & 0x43` = `1111 0000 & 0100 0011` = `0100 0000` = `0x40`. The channel bits are cleared, `REFS0` is kept.
> `0x40 | 0` = **`0x40`**: AVCC, channel ADC0.
> With `ADMUX |= channel;` nothing is cleared: `0x43 | 0` = `0x43`. The channel would stay 3, and the program would measure the potentiometer twice. OR can only set bits. To select a new channel the old bits must be cleared first.

## 13. Free running mode and Example 3 (slides 45-49)
![[cmpe453_lecture_7_lecture7_adc_p045.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p046.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p047.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p048.jpg]]
![[cmpe453_lecture_7_lecture7_adc_p049.jpg]]

**Notes.** With `ADATE = 1` in `ADCSRA` the conversions are started by a trigger source. The source is selected in `ADCSRB`, the ADC control and status register B (address `0x7B`):

| Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|
| Name | - | `ACME` | - | - | - | `ADTS2` | `ADTS1` | `ADTS0` |

`ACME` belongs to the analog comparator, not to the ADC. Leave it 0. `ADTS2:0` are the ADC auto trigger source bits:

| `ADTS2` | `ADTS1` | `ADTS0` | Trigger source |
|---|---|---|---|
| 0 | 0 | 0 | **free running mode** |
| 0 | 0 | 1 | analog comparator |
| 0 | 1 | 0 | external interrupt request 0 |
| 0 | 1 | 1 | Timer/Counter0 compare match A |
| 1 | 0 | 0 | Timer/Counter0 overflow |
| 1 | 0 | 1 | Timer/Counter1 compare match B |
| 1 | 1 | 0 | Timer/Counter1 overflow |
| 1 | 1 | 1 | Timer/Counter1 capture event |

**Free running mode** (`ADTS2:0` = `000`): the end of one conversion starts the next one. The ADC converts again and again without the program. The program writes `ADSC = 1` once to start the first conversion. After that the data register always holds the newest result, and the program reads it whenever it wants.

**Example 3** (slides 46-49): the microcontroller reads an LDR and sends the value to the laptop through the USART. A Python script on the laptop draws the signal in real time. Plan: initialise the USART, initialise the ADC in free running mode, then forever: read the ADC value and transmit it.

```c
#include <avr/io.h>
#include <util/delay.h>
#include "pinDefines.h"
#include "USART.c"

#define SAMPLE_DELAY 200 /* ms, controls how closely the plot drawn by python script
                            follows the changes in sensor signal */
// -------- Functions --------- //
static inline void initFreerunningADC(void)
{
ADMUX |= (1 << REFS0); /* reference voltage on AVCC */
ADCSRA |= (1 << ADPS1) | (1 << ADPS0); /* ADC clock prescaler /8 */
ADMUX |= (1 << ADLAR); /* left-adjust result*/
ADCSRA |= (1 << ADEN); /* enable ADC */
ADCSRA |= (1 << ADATE); /* auto-trigger enable */
ADCSRA |= (1 << ADSC); /* start first conversion */
}

int main(void)
{
// -------- Inits --------- //
initUSART();
initFreerunningADC();
// ------ Event loop ------ //
while (1)
     {
     transmitByte(ADCH); /* transmit the high byte, left-adjusted */
     _delay_ms(SAMPLE_DELAY);
     } /* End event loop */
return 0; /* This line is never reached */
}
```

What is different from the examples before:
- `ADLAR = 1`: the result is left-adjusted, so `ADCH` holds the top 8 bits. One USART frame carries 8 bits, so one byte per sample is convenient.
- `ADATE = 1` and one `ADSC = 1`: free running. There is no "start" and no "wait" in the loop.
- The loop only reads `ADCH` and sends it. Reading only `ADCH` is allowed (section 8).
- `SAMPLE_DELAY` = 200 ms: 5 values per second go to the laptop.

Slide 48 asks "Where is free running mode?": the program never writes `ADTS2:0`. It does not need to. `ADCSRB` is `0x00` after reset, so `ADTS2:0` = `000` = free running. The line on the slide makes it explicit:

```c
ADCSRB &= ~((1 << ADTS2) | (1 << ADTS1) | (1 << ADTS0));
```

With 8 bits the value is 0 to 255 and one step is $5/256=19.53$ mV: $V_{in}\approx\text{ADCH}\cdot5/256$.

> [!warning] Problems in the slide program
> - Prescaler /8 again: 2 MHz on the 16 MHz Uno. Only 8 bits are used, so it works, but it is outside the specification. Use all three `ADPS` bits.
> - `transmitByte(ADCH)` sends one **raw byte** (0 to 255), not text. A serial monitor shows strange characters. The Python script must read bytes, not lines.
> - The ADC makes thousands of conversions per second and only 5 are used. This wastes nothing important, but a timer trigger (`ADTS2:0` = `011`) would give exact sample times.

> [!question]- Check (slide 45): You want Timer/Counter0 compare match A to start every conversion. Which `ADTS` bits are 1? Write the lines for `ADCSRB` and `ADCSRA`. Why is `ADSC` not written in a loop any more?
> Timer/Counter0 compare match A = `011`: `ADTS1` = 1 and `ADTS0` = 1, `ADTS2` = 0.
> `ADCSRB |= (1 << ADTS1) | (1 << ADTS0);`
> `ADCSRA |= (1 << ADATE);` (auto trigger on; `ADEN` and the prescaler as before).
> With `ADATE = 1` the trigger event starts each conversion in hardware. The program no longer starts them, it only reads the results.
> Beyond the slides: the trigger is the moment when the timer flag `OCF0A` goes from 0 to 1. The flag must be cleared again after each conversion (by the timer interrupt routine, or by writing a 1 to it), or no new trigger comes.

> [!question]- Check (slides 46-47): In Example 3 the ADC runs free and the loop sends one value every 200 ms. With a correct ADC clock of 125 kHz, about how many conversions finish between two transmissions? Which of them is sent?
> One conversion: 13 cycles at 125 kHz = 104 µs.
> In 200 ms: $0.2\cdot125\,000/13\approx$ **1923** conversions.
> Only the **newest** one is sent. Each new result overwrites the data register, so the older ones are lost. For a slow signal like room light this is fine.

> [!question]- Check (slide 48): Give the contents of `ADMUX` and `ADCSRA` right after `initFreerunningADC()`. Which channel is measured? Is the line `ADCSRB &= ~(...)` from the slide needed?
> `ADMUX`: `REFS0` (bit 6) and `ADLAR` (bit 5): `0110 0000` = **`0x60`**. `MUX3:0` = `0000`: channel **ADC0** (PC0, A0).
> `ADCSRA`: `ADEN` (bit 7), `ADSC` (bit 6), `ADATE` (bit 5), `ADPS1`, `ADPS0`: `1110 0011` = **`0xE3`**. In free running mode `ADSC` reads 1 all the time, because a conversion is always in progress.
> The `ADCSRB` line is not needed after a reset, because `ADTS2:0` are already `000`. It is good practice if other code could have changed `ADCSRB`.

> [!question]- Check (slide 49): The laptop receives the byte `0xB4`. Which voltage is on the LDR pin ($V_{ref}=5$ V)? Which 10-bit results can be behind this byte? What changes if `ADLAR` is 0 and the program still sends `ADCH`?
> `0xB4` = 180. $V_{in}\approx180\cdot5/256=$ **3.52 V**.
> `ADCH` holds D9 ... D2. The two lost bits D1 D0 can be `00` to `11`: the 10-bit result is $180\cdot4=720$ to $723$.
> With `ADLAR = 0`, `ADCH` holds only D9 D8, a number 0 to 3. The laptop would see only four different values, a plot with four levels.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Resolution | number of bits $n$, $2^n$ steps, codes 0 to $2^n-1$ |
| Step size | $V_{ref}/2^n$. 10 bits, 5 V: 4.88 mV. 10 bits, 1.1 V: 1.07 mV |
| Output | $D_{out}=V_{in}\cdot2^n/V_{ref}$, fraction cut off. ATmega328P: $\text{ADC}=V_{in}\cdot1024/V_{ref}$ |
| Inverse | $V_{in}\approx\text{ADC}\cdot V_{ref}/1024$ (8 bits from `ADCH`: $\text{ADCH}\cdot V_{ref}/256$) |
| Quantisation error | real voltage minus the voltage of the code, less than one step |
| Successive approximation | SAR + DAC + comparator, one comparison per bit from the MSB down, $V_{DAC}=\text{SAR}\cdot\text{step}$ |
| Channels | ADC0 ... ADC5 = PC0 ... PC5 = A0 ... A5. One ADC, one channel at a time |
| `ADMUX` (`0x7C`) | `REFS1 REFS0 ADLAR - MUX3 MUX2 MUX1 MUX0`. `REFS` 00 AREF, 01 AVCC, 11 internal 1.1 V |
| `ADLAR` | 0: right-adjusted, read `ADC` (10 bits). 1: left-adjusted, `ADCH` = top 8 bits |
| `ADCSRA` (`0x7A`) | `ADEN ADSC ADATE ADIF ADIE ADPS2 ADPS1 ADPS0` |
| ADC clock | $f_{CPU}$ / division factor, must be 50 to 200 kHz. 16 MHz: `ADPS2:0` = `111`, 128, 125 kHz |
| Conversion time | 13 ADC clock cycles (first: 25). At 125 kHz: 104 µs (200 µs), about 9615 per second |
| `ADCSRB` (`0x7B`) | `ADTS2:0` trigger source when `ADATE = 1`. `000` = free running |
| `ADCL` / `ADCH` (`0x78` / `0x79`) | read `ADCL` first, then `ADCH`. In C: `ADC` |
| `DIDR0` (`0x7E`) | `ADC5D ... ADC0D`: 1 switches off the digital input buffer of that pin |
| One conversion | `ADCSRA \|= (1 << ADSC);` then wait until `ADSC` is 0, then read `ADC` |

## Check yourself
> [!question]- 1. The ADC uses the internal 1.1 V reference. What is the step size? Which number does 0.4 V give, and which voltage does the program compute back?
> Step $=1.1/1024=$ **1.074 mV**.
> $\text{ADC}=0.4\cdot1024/1.1=372.36$, cut off: **372**.
> Back: $372\cdot1.1/1024=$ **0.3996 V**.

> [!question]- 2. A 4-bit successive approximation ADC has a step of 1 V. Convert $V_{in}=13.5$ V. Show every comparison.
> `1000` = 8 V: $13.5>8$, bit 3 stays 1.
> `1100` = 12 V: $13.5>12$, bit 2 stays 1.
> `1110` = 14 V: $13.5<14$, bit 1 is cleared.
> `1101` = 13 V: $13.5>13$, bit 0 stays 1.
> Result: **`1101`** = 13. Quantisation error: $13.5-13=0.5$ V.

> [!question]- 3. On the 16 MHz Uno, which prescaler must be used and why? How long does a normal conversion take, and the first one?
> The ADC clock must be 50 to 200 kHz. $16\text{ MHz}/128=$ **125 kHz** is the only choice (64 gives 250 kHz). Bits: `ADPS2`, `ADPS1`, `ADPS0` all 1.
> One cycle $=8$ µs. Normal: $13\cdot8=$ **104 µs**. First: $25\cdot8=$ **200 µs**.

> [!question]- 4. Give `ADMUX` for the reference AVCC, a left-adjusted result and channel ADC4. Which Uno pin is this?
> `REFS1:0` = `01`, `ADLAR` = 1, `MUX3:0` = `0100`: `0110 0100` = **`0x64`**.
> ADC4 = PC4 = **A4** (also SDA of the TWI).

> [!question]- 5. In which order must `ADCL` and `ADCH` be read, and why? When may you read only one of them?
> `ADCL` first, then `ADCH`. Reading `ADCL` locks the data register until `ADCH` is read, so both bytes come from the same conversion.
> With `ADLAR = 1` you may read only `ADCH` (the top 8 bits). Reading only `ADCL` is never right: the register stays locked.

> [!question]- 6. A temperature sensor gives 10 mV per °C. At 27.5 °C it gives 0.275 V. Compare $V_{ref}=1.1$ V and $V_{ref}=5$ V: the ADC number, the temperature computed back, and the temperature of one step.
> $V_{ref}=1.1$ V: $0.275\cdot1024/1.1=256$ exactly. Back: $256\cdot1.1/1024=0.275$ V = **27.5 °C**. One step $=1.074$ mV = **0.11 °C**.
> $V_{ref}=5$ V: $0.275\cdot1024/5=56.32$, cut off: 56. Back: $56\cdot5/1024=0.2734$ V = **27.3 °C**. One step $=4.88$ mV = **0.49 °C**.
> The small reference fits the small signal and measures about 4.5 times finer.

## Links
- Course index: [[CMPE453 Home]]
- Previous: [[CMPE453 L06.2 - I2C and LM75]]
- Sending bytes over the USART (`initUSART`, `transmitByte`): [[CMPE453 L05.2 - Programming the USART]]
- Original slides (PDF): [Lecture7-ADC.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_7/Lecture7-ADC.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_7/Lecture7-ADC.pdf`
- Quiz: [[CMPE453 L07 - ADC - quiz]]

Slides not embedded: 1 (title).
