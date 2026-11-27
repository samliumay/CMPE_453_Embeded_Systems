---
course: CMPE 453 Embedded Systems
lesson: CMPE453 L07 - ADC
type: quiz
questions: 36
---

# CMPE453 L07 - ADC - quiz

Lesson note: [[CMPE453 L07 - ADC]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Why an ADC, and where it sits (slides 2-3)

### Q1 (slide 2)

A pressure sensor gives 0 to 50 mV. The ADC accepts 0 to 5 V. The three steps are: sensor, signal conditioning, ADC. Here step 2 (signal conditioning) is an amplifier. Which gain must it have? And which part of the ADC range would the sensor use without step 2?

- A) Gain 1000. Without step 2 the sensor uses 1 % of the range.
- B) Gain 100. Without step 2 the sensor uses 0.1 % of the range.
- C) Gain 100. Without step 2 the sensor uses 1 % of the range.
- D) Gain 1000. Without step 2 the sensor uses 0.1 % of the range.

> [!success]- Answer
> **C.** The range must grow from 50 mV = 0.05 V to 5 V. Gain $=5/0.05=100$. Check: half pressure gives $0.025\cdot100=2.5$ V.
> Without the amplifier the sensor uses $0.05/5=0.01=1$ % of the ADC range. Almost all steps of the ADC are wasted.
> - A: The gain slip: 50 mV was taken as 0.005 V, so $5/0.005=1000$.
> - B: The range slip: 50 mV was taken as 0.005 V, so $0.005/5=0.1$ %.
> - D: Both parts use 50 mV = 0.005 V. The right value is 0.05 V.

### Q2 (slide 3)

![[cmpe453_lecture_7_lecture7_adc_p003.jpg]]

The figure leaves out one of the three steps of slide 2: signal conditioning. Between which two blocks does it belong? On an Arduino Uno, which of the blocks ADC and CPU are inside the ATmega328P chip?

- A) Between the ADC and the CPU. Only the CPU is inside the chip.
- B) Between the sensor and the ADC. The ADC and the CPU are inside the chip.
- C) Between the ADC and the CPU. The ADC and the CPU are inside the chip.
- D) Between the sensor and the ADC. Only the CPU is inside the chip.

> [!success]- Answer
> **B.** Signal conditioning scales the analog voltage so that it fits the ADC range. So it sits on the wire "analog voltage", between the sensor and the ADC.
> The ADC is an on-chip peripheral of the ATmega328P, like the USART and the TWI. Only the sensor and the display are outside.
> - A: After the ADC the signal is already a number. Scaling the voltage there is too late. And the ADC is not a separate chip.
> - C: The chip part is right, but the position is wrong: conditioning works on the analog voltage, before the ADC.
> - D: The position is right, but the ATmega328P has its own ADC. No external ADC chip is needed.

## 2. Resolution, step size, conversion time and V_ref (slides 4-5)

### Q3 (slide 4, block diagram)

An $n$-bit ADC has $n$ data output lines, named from the LSB to the MSB. A 12-bit ADC has 12 lines. What are their names, and what are the smallest and the largest number it can give?

- A) D1 (LSB) to D12 (MSB). Numbers 0 to 4095.
- B) D0 (LSB) to D11 (MSB). Numbers 0 to 4095.
- C) D1 (LSB) to D12 (MSB). Numbers 0 to 4096.
- D) D0 (LSB) to D11 (MSB). Numbers 0 to 4096.

> [!success]- Answer
> **B.** The lines are counted from 0, like bit numbers: D0 (LSB) to D11 (MSB).
> 12 bits give $2^{12}=4096$ different numbers. They start at 0, so the largest is $4096-1=4095$.
> - A: The range is right, but the names start at D0, not D1.
> - C: Both slips: the names start at D0, and $2^{12}=4096$ is the number of values, not the largest value.
> - D: The names are right, but 4096 would need a 13th bit. The largest 12-bit number is 4095.

### Q4 (slide 4, table)

Step size $=V_{ref}/2^n$. The slide table is for $V_{ref}=5$ V and gives 4.88 mV for 10 bits. Now $V_{ref}=3.3$ V. What is the step size for 10 bits? How does the step change if you go from 10 to 12 bits?

- A) 3.22 mV. With 12 bits the step is 4 times smaller.
- B) 4.88 mV. With 12 bits the step is 2 times smaller.
- C) 4.88 mV. With 12 bits the step is 4 times smaller.
- D) 3.22 mV. With 12 bits the step is 2 times smaller.

> [!success]- Answer
> **A.** 10 bits: $3.3/1024=0.003223$ V $=3.22$ mV.
> 12 bits: $3.3/4096=0.81$ mV. Two more bits multiply the number of steps by $2^2=4$, so the step is 4 times smaller.
> - B: Both slips: 4.88 mV is the 5 V value from the table, and two bits change $2^n$ by 4, not by 2.
> - C: The ratio is right, but 4.88 mV belongs to $V_{ref}=5$ V. The step depends on $V_{ref}$.
> - D: The step is right, but each extra bit halves the step. Two bits halve it twice: 4 times.

### Q5 (slide 5, conversion time)

The conversion time depends on three factors: the clock source connected to the ADC, the conversion method, and the technology (MOS or TTL). An ADC needs 13 clock cycles for one conversion. How long is one conversion with a 200 kHz and with a 50 kHz ADC clock? Which factor did you change?

- A) 200 kHz: 260 µs, 50 kHz: 65 µs. The factor: the conversion method.
- B) 200 kHz: 65 µs, 50 kHz: 260 µs. The factor: the conversion method.
- C) 200 kHz: 260 µs, 50 kHz: 65 µs. The factor: the clock source.
- D) 200 kHz: 65 µs, 50 kHz: 260 µs. The factor: the clock source.

> [!success]- Answer
> **D.** 200 kHz: one cycle is $1/200\,000=5$ µs, so $13\cdot5=65$ µs.
> 50 kHz: one cycle is $1/50\,000=20$ µs, so $13\cdot20=260$ µs.
> Only the clock changed. The method (13 cycles) and the technology stayed the same.
> - A: Both slips: a faster clock gives a shorter conversion, and the method (13 cycles) did not change.
> - B: The times are right, but the 13 cycles of the method are the same in both cases.
> - C: The factor is right, but the times are swapped. A faster clock gives a shorter time.

### Q6 (slide 5, V_ref)

A sensor gives 0 to 1 V. A 10-bit ADC gives $\text{ADC}=V_{in}\cdot1024/V_{ref}$, fraction cut off. With $V_{ref}=1.024$ V the step is 1 mV and the sensor uses about 1000 codes. About how many codes does the sensor use with $V_{ref}=5$ V? What is the price of the 1.024 V reference?

- A) About 1024 codes. Price: each conversion takes longer.
- B) About 205 codes. Price: each conversion takes longer.
- C) About 205 codes. Price: inputs above 1.024 V cannot be measured.
- D) About 1024 codes. Price: inputs above 1.024 V cannot be measured.

> [!success]- Answer
> **C.** With 5 V the step is $5/1024=4.88$ mV. 1 V gives $1\cdot1024/5=204.8$, so 204: the codes 0 to 204, about 205.
> The 1.024 V reference measures almost 5 times finer. But the range is only 0 to $V_{ref}$: nothing above 1.024 V can be measured.
> - A: Both slips: the sensor uses only the codes up to 1 V, and $V_{ref}$ does not change the number of clock cycles.
> - B: The count is right, but the conversion time depends on the ADC clock, not on $V_{ref}$.
> - D: The price is right, but 1 V is only a fifth of 5 V. The sensor reaches only code 204.

## 3. The digital output formula (slide 6)

### Q7 (slide 6)

An 8-bit ADC with $V_{ref}=2.56$ V gives $D_{out}=V_{in}\cdot256/V_{ref}$, fraction cut off, written as D7 ... D0 (MSB first). (a) Give D7 ... D0 for $V_{in}=0.85$ V. (b) For $V_{in}=1.234$ V, which voltage does the program compute back from the number?

- A) (a) `10101010`; (b) 1.234 V
- B) (a) `01010101`; (b) 1.234 V
- C) (a) `10101010`; (b) 1.23 V
- D) (a) `01010101`; (b) 1.23 V

> [!success]- Answer
> **D.** Step $=2.56/256=10$ mV.
> (a) $0.85/0.01=85=64+16+4+1$, so D7 ... D0 = `01010101`.
> (b) $1.234/0.01=123.4$, cut off: 123. Back: $123\cdot0.01=1.23$ V. The 4 mV lost are the quantisation error.
> - A: Both slips: `10101010` (170) is 85 with the bits in reverse order (D0 written first), and the number cannot hold the 4 mV below one step.
> - B: (a) is right, but the code is 123, a whole number. It stands for 1.23 V, not 1.234 V.
> - C: (b) is right, but `10101010` is the bit order D0 ... D7. It is 170, not 85.

## 4. The analog input pins (slide 7)

### Q8 (slide 7)

On the Uno the analog pins A0 to A5 are channels ADC0 to ADC5. A sensor is on A4. The same board has an LM75 on I2C. Give the port pin of A4, the I2C line on that pin, and the reason why A0 and A1 cannot be measured at exactly the same time.

- A) PD4. It is SDA. There are six ADCs, but only one result register.
- B) PC4. It is SDA. There is one ADC, and the MUX connects one channel.
- C) PD4. It is SCL. There is one ADC, and the MUX connects one channel.
- D) PC4. It is SCL. There are six ADCs, but only one result register.

> [!success]- Answer
> **B.** A4 = PC4 = ADC4 = chip pin 27. PC4 is also SDA of the TWI, so with the LM75 the sensor must move to A0 to A3.
> The chip has one ADC and one result register. The multiplexer connects one channel at a time: first A0, then A1.
> - A: PD4 is the digital pin 4, not A4. And there are not six ADCs: the six channels share one ADC.
> - C: The reason is right, but A4 is on port C (PC4), and PC4 is SDA. SCL is PC5 (A5).
> - D: PC4 is SDA, not SCL. And the chip has only one ADC, not six.

## 5. Successive approximation (slides 8-31)

### Q9 (slide 8)

The ATmega328P ADC has 10 bits and uses successive approximation (one guess per step, from the MSB down). A converter that tests the codes one by one from 0 needs up to $2^{10}=1024$ comparisons. How many comparisons does successive approximation need, and does this number depend on the input voltage?

- A) 10. The number is the same for every input voltage.
- B) 512. The number is the same for every input voltage.
- C) 10 at most. It can stop early when $V_{in}$ is near a guess.
- D) 512 at most. It can stop early when $V_{in}$ is near a guess.

> [!success]- Answer
> **A.** Successive approximation decides one bit per comparison, from the MSB down to the LSB. 10 bits need exactly 10 comparisons.
> It never stops early: every bit must be decided, whatever $V_{in}$ is.
> - B: Each comparison halves the range of possible codes. It does not test half of the 1024 codes: $\log_2 1024=10$, not $1024/2$.
> - C: The count is right, but there is no early stop. All 10 bits are always decided.
> - D: Both slips: 512 is $1024/2$, not the number of halvings, and the count does not depend on $V_{in}$.

### Q10 (slide 9)

A 4-bit successive approximation converter has a step size of 0.5 V. Use step size $=V_{ref}/2^n$ and $V_{DAC}=\text{SAR}\cdot\text{step size}$. What is $V_{ref}$? What is $V_{DAC}$ for SAR = `0110` and for the first guess `1000`?

- A) $V_{ref}=7.5$ V. `0110`: 6 V, `1000`: 8 V.
- B) $V_{ref}=8$ V. `0110`: 6 V, `1000`: 8 V.
- C) $V_{ref}=8$ V. `0110`: 3 V, `1000`: 4 V.
- D) $V_{ref}=7.5$ V. `0110`: 3 V, `1000`: 4 V.

> [!success]- Answer
> **C.** $V_{ref}=2^4\cdot0.5=16\cdot0.5=8$ V.
> `0110` = 6, so $V_{DAC}=6\cdot0.5=3$ V. `1000` = 8, so $V_{DAC}=8\cdot0.5=4$ V, exactly half of $V_{ref}$.
> - A: Both slips: $V_{ref}$ uses $2^n=16$ steps, not the largest code 15, and the SAR value must be multiplied by the step 0.5 V.
> - B: $V_{ref}$ is right, but 6 V and 8 V forget the step: they take the step as 1 V.
> - D: The voltages are right, but $15\cdot0.5=7.5$ V uses the largest code instead of $2^4=16$.

### Q11 (slides 10-12)

The slide table (4 bits, step 1 V, so $V_{ref}=16$ V) starts with Index = 3, SAR = `1000`, $V_{in}=11.2$ V. Why does the SAR start at `1000`? If the input were 6.7 V instead, which of these three start values would change?

- A) `1000` is the reset value of the SAR. $V_{in}$ and the start SAR change.
- B) `1000` is the middle of the range. $V_{in}$ and the start SAR change.
- C) `1000` is the middle of the range. Only $V_{in}$ changes; Index and SAR stay.
- D) `1000` is the reset value of the SAR. Only $V_{in}$ changes; Index and SAR stay.

> [!success]- Answer
> **C.** `1000` = 8 V is the middle of 0 to 16 V. One comparison then removes half of all possible codes. Index 3 is the MSB, the bit with the largest weight.
> The first guess is always `1000` and the first Index is always 3. Only $V_{in}$ changes.
> - A: Both slips: the SAR is set to `1000` on purpose (the middle), and the first guess never depends on the input.
> - B: The reason is right, but the method does not know $V_{in}$ before the first comparison. It always starts in the middle.
> - D: The start values are right, but `1000` is chosen because it is the middle, not because of a reset.

### Q12 (slides 13-15)

Same table, but $V_{in}=6.7$ V (step 1 V). The table compares $V_{in}$ with $V_{DAC}$ of the SAR in the same row. Rule of the slides: if $V_{in}>V_{DAC}$ the tested bit stays 1, otherwise it is cleared to 0. In the first row, which $V_{DAC}$ is compared, and what happens to bit 3?

- A) 12 V (`1100`). Bit 3 stays 1.
- B) 8 V (`1000`). Bit 3 stays 1.
- C) 12 V (`1100`). Bit 3 is cleared to 0.
- D) 8 V (`1000`). Bit 3 is cleared to 0.

> [!success]- Answer
> **D.** The first row has SAR = `1000`, so $V_{DAC}=8\cdot1=8$ V.
> $6.7<8$, so the comment is $V_{in}<V_{DAC}$ and bit 3 is cleared to 0. (With 11.2 V on the slides, bit 3 stayed 1.)
> - A: Both slips: `1100` is the next guess (step 4 read literally), and $V_{in}<V_{DAC}$ clears the bit.
> - B: The voltage is right, but $6.7<8$ means the input is in the lower half. The bit is cleared.
> - C: The decision fits 12 V, but the first comparison uses `1000` (8 V). Bit 2 is set only for the next test.

### Q13 (slides 16-17)

Same table, $V_{in}=6.7$ V, step 1 V. In the first row, $6.7<8$ cleared bit 3. On the slides (11.2 V) the second row is SAR = `1100`, Index = 2. What does the second row show for 6.7 V?

- A) SAR `0100`, $V_{DAC}=4$ V, Index = 2.
- B) SAR `1100`, $V_{DAC}=12$ V, Index = 3.
- C) SAR `0100`, $V_{DAC}=4$ V, Index = 3.
- D) SAR `1100`, $V_{DAC}=12$ V, Index = 2.

> [!success]- Answer
> **A.** Bit 3 was cleared, and bit 2 is set for the next test: SAR = `0100`, $V_{DAC}=4\cdot1=4$ V.
> Index goes down by one after each comparison: Index = 2 (bit 2 is tested now).
> - B: Both slips: `1100` keeps bit 3, which was cleared, and Index must go down from 3 to 2.
> - C: The SAR is right, but Index was not decreased. Bit 3 is already decided.
> - D: `1100` is the slide row for 11.2 V, where bit 3 stayed 1. For 6.7 V bit 3 is 0.

### Q14 (slides 18-20)

Continue with $V_{in}=6.7$ V: SAR = `0100`, $V_{DAC}=4$ V, Index = 2. Rule: if $V_{in}>V_{DAC}$ the tested bit stays 1, otherwise it is cleared. What is the decision for bit 2, and which SAR value is tried next?

- A) Bit 2 is cleared. Next SAR `0010` (2 V).
- B) Bit 2 stays 1. Next SAR `0110` (6 V).
- C) Bit 2 is cleared. Next SAR `0000` (0 V).
- D) Bit 2 stays 1. Next SAR `0100` (4 V).

> [!success]- Answer
> **B.** $6.7>4$, so bit 2 stays 1.
> Then bit 1 is set for the next test: `0100` + `0010` = `0110`, $V_{DAC}=6$ V.
> - A: The next-bit step is right, but $6.7>4$ keeps bit 2. (On the slides $11.2<12$ cleared it.)
> - C: Both slips: bit 2 must stay 1, and bit 1 must be set before the next comparison.
> - D: The decision is right, but the next bit (bit 1) was not set. Comparing `0100` again decides nothing.

### Q15 (slides 21-23)

![[cmpe453_lecture_7_lecture7_adc_p021.jpg]]

Slide table, $V_{in}=11.2$ V. In the third row the SAR is `1010`, not `1110`. Why? Which numbers belong in the columns Index and Index-1 of this row?

- A) Bit 2 was cleared ($11.2<12$), then bit 1 was set. Index 1, Index-1 0.
- B) The 1 of bit 2 moves one place to the right. Index 1, Index-1 0.
- C) Bit 2 was cleared ($11.2<12$), then bit 1 was set. Index 2, Index-1 1.
- D) The 1 of bit 2 moves one place to the right. Index 2, Index-1 1.

> [!success]- Answer
> **A.** The test of bit 2 failed ($11.2<12$), so bit 2 was cleared: `1000`. Then bit 1 was set: `1000` + `0010` = `1010`.
> Bit 1 is tested now, so Index = 1. Bit 0 is set next, so Index-1 = 0.
> - B: The indices are right, but there is no shift. Bit 2 is cleared because of a comparison. If $V_{in}$ were above 12 V, it would stay 1 (`1110`).
> - C: The reason is right, but Index = 2 and Index-1 = 1 belong to the row before.
> - D: Both slips: the SAR does not shift, and Index has gone down to 1 in this row.

### Q16 (slides 24-26)

$V_{in}=6.7$ V, step 1 V, third row: SAR = `0110`. Rule: if $V_{in}>V_{DAC}$ the tested bit stays 1, otherwise it is cleared. What happens to bit 1, and which SAR value does the last row test?

- A) Bit 1 is cleared. Last SAR `0101` (5 V).
- B) Bit 1 stays 1. Last SAR `0110` (6 V).
- C) Bit 1 is cleared. Last SAR `0100` (4 V).
- D) Bit 1 stays 1. Last SAR `0111` (7 V).

> [!success]- Answer
> **D.** $V_{DAC}=6\cdot1=6$ V. $6.7>6$, so bit 1 stays 1.
> Bit 0 is set for the last test: SAR = `0111`, $V_{DAC}=7$ V.
> - A: Bit 0 is set correctly, but $6.7>6$ keeps bit 1.
> - B: The decision is right, but bit 0 must be set before the last comparison. It is still untested.
> - C: Both slips: bit 1 stays 1, and bit 0 must be set for the last test.

### Q17 (slides 27-29)

![[cmpe453_lecture_7_lecture7_adc_p029.jpg]]

In the last row of the table, Index = 0 and Index-1 = −1. The row below shows `101x`. What does −1 mean, and what does `101x` show?

- A) The loop starts again at bit 3. In `101x`, x is the bit set to 1 for the next test.
- B) No next bit is set: the last comparison. In `101x`, x is the bit set to 1 for the next test.
- C) The loop starts again at bit 3. In `101x`, bits 3 to 1 are fixed, bit 0 is open.
- D) No next bit is set: the last comparison. In `101x`, bits 3 to 1 are fixed, bit 0 is open.

> [!success]- Answer
> **D.** There is no bit −1. So no next bit is set, and the comparison of `1011` (11 V) with 11.2 V is the last one. The loop ends when Index becomes smaller than 0.
> In the slide notation, x marks the bit that is being decided. `101x`: bits 3, 2, 1 are fixed (`101`), and only bit 0 is still open.
> - A: Both slips: the loop does not start again (the result would never be final), and x is the open bit, not a bit set for the next test.
> - B: The −1 is read right, but x marks the open bit. There is no next test after bit 0.
> - C: The `101x` part is right, but "go to step 4" does not loop forever. The conversion ends after 4 comparisons.

### Q18 (slides 30-31)

Finish $V_{in}=6.7$ V (step 1 V): the last row tests SAR = `0111`, $V_{DAC}=7$ V. Rule: if $V_{in}>V_{DAC}$ the tested bit stays 1, otherwise it is cleared. Give the result and the quantisation error (the distance from $V_{in}$ to the voltage of the result).

- A) Result `0111` (7 V). Error 0.3 V.
- B) Result `0111` (7 V). Error 0.7 V.
- C) Result `0110` (6 V). Error 0.7 V.
- D) Result `0110` (6 V). Error 0.3 V.

> [!success]- Answer
> **C.** $6.7<7$, so bit 0 is cleared. Result `0110` = 6, which stands for 6 V. Check: $6.7/1=6.7$, cut off: 6.
> Error $=6.7-6=0.7$ V, less than one step. (Slide example: $11.2-11=0.2$ V.)
> - A: This rounds to the nearest code. Successive approximation gives the largest code whose voltage is below $V_{in}$, so it cuts off.
> - B: Bit 0 was kept by mistake ($6.7<7$ clears it), and then the error was taken from 6 V, not from the result 7 V.
> - D: The result is right, but 0.3 V is the distance to the next code (7 V), not to the result.

## 6. The ADC of the ATmega328P (slides 32-33)

### Q19 (slide 32)

![[cmpe453_lecture_7_lecture7_adc_p032.jpg]]

Follow one conversion in the block diagram. Which block does the job of "SAR + Control" of the successive approximation figure (slide 9)? Through which register does the program start a conversion?

- A) Conversion Logic. The conversion is started through `ADCSRA`.
- B) 10-bit DAC. The conversion is started through `ADCSRA`.
- C) Conversion Logic. The conversion is started through `ADMUX`.
- D) 10-bit DAC. The conversion is started through `ADMUX`.

> [!success]- Answer
> **A.** The conversion logic holds the guess (SAR), sends it to the 10-bit DAC and gets the answer from the comparator (Control). It gets its clock from the clock prescaler.
> The input multiplexer and the ref mux are set by `ADMUX`. The conversion is started through `ADCSRA` (control and status). The result is in `ADCH` / `ADCL`.
> - B: The register is right, but the DAC only turns the guess into a voltage. It does not hold or change the guess.
> - C: The block is right, but `ADMUX` only selects the channel and the reference. It does not start anything.
> - D: The DAC makes $V_{DAC}$ from the guess; it does not hold or change the guess. And `ADMUX` does not start a conversion.

### Q20 (slide 33)

A 10-bit ADC gives $\text{ADC}=V_{in}\cdot1024/V_{ref}$, fraction cut off, written as D9 ... D0. (a) $V_{ref}=2.56$ V, $V_{in}=0.6$ V: give D9 ... D0. (b) ATmega328P with $V_{ref}=5$ V, $V_{in}=3.2$ V: give the result in decimal and hex.

- A) (a) `1111000000`; (b) 655 = `0x28F`
- B) (a) `0011110000`; (b) 655 = `0x28F`
- C) (a) `1111000000`; (b) 656 = `0x290`
- D) (a) `0011110000`; (b) 656 = `0x290`

> [!success]- Answer
> **B.** (a) Step $=2.56/1024=2.5$ mV. $0.6/0.0025=240=128+64+32+16$ = `11110000`. With 10 bits: `0011110000`.
> (b) $3.2\cdot1024/5=655.36$, cut off: 655 = `10 1000 1111` = `0x28F`. Back: $655\cdot5/1024=3.198$ V.
> - A: (b) is right, but in (a) the two missing zeros belong in front (D9 D8), not at the end. `1111000000` is 960.
> - C: Both slips: zeros added at the wrong end, and 655.36 was rounded up instead of cut off.
> - D: (a) is right, but the course cuts the fraction off: 655, not 656.

## 7. ADMUX: reference, channel and alignment (slides 34-35)

### Q21 (slide 34)

`ADMUX` bits: 7 `REFS1`, 6 `REFS0`, 5 `ADLAR`, 4 unused, 3-0 `MUX3:0`. `REFS1:0`: `00` AREF, `01` AVCC, `11` internal 1.1 V. `ADLAR` = 1 gives a left-adjusted result. `MUX3:0` is the channel number in binary. Which value selects the internal 1.1 V reference, channel ADC2, right-adjusted?

- A) `ADMUX = 0xE4`
- B) `ADMUX = 0x44`
- C) `ADMUX = 0x62`
- D) `ADMUX = 0xC2`

> [!success]- Answer
> **D.** 1.1 V: `REFS1` = 1, `REFS0` = 1. Right-adjusted: `ADLAR` = 0. ADC2: `MUX3:0` = `0010`.
> `1100 0010` = `0xC2`. In C: `ADMUX = (1 << REFS1) | (1 << REFS0) | (1 << MUX1);`
> - A: `1110 0100`: `ADLAR` = 1 is left-adjusted, and `0100` is ADC4 (bit 2 set instead of the number 2).
> - B: `0100 0100`: only `REFS0` selects AVCC, and `0100` is ADC4, not ADC2.
> - C: `0110 0010`: the channel is right, but `REFS1:0` = `01` is AVCC and `ADLAR` = 1 is left-adjusted.

### Q22 (slide 35, channel)

`ADMUX` bits: 7 `REFS1`, 6 `REFS0`, 5 `ADLAR`, 4 unused, 3-0 `MUX3:0`. `REFS1:0`: `00` AREF, `01` AVCC, `10` reserved, `11` internal 1.1 V. Give `ADMUX` for AVCC, left-adjusted result, channel ADC5. Then decode `ADMUX = 0x43`.

- A) `0x65`. `0x43` = AVCC, right-adjusted, ADC3.
- B) `0xE5`. `0x43` = AVCC, right-adjusted, ADC3.
- C) `0x65`. `0x43` = internal 1.1 V, right-adjusted, ADC3.
- D) `0xE5`. `0x43` = internal 1.1 V, right-adjusted, ADC3.

> [!success]- Answer
> **A.** AVCC: `REFS1:0` = `01`, so only `REFS0` (bit 6). Left-adjusted: `ADLAR` (bit 5). ADC5: `0101`. `0100 0000` + `0010 0000` + `0000 0101` = `0110 0101` = `0x65`.
> `0x43` = `0100 0011`: bit 6 = `REFS0`, so `REFS1:0` = `01` (AVCC). `ADLAR` = 0 (right-adjusted). `MUX3:0` = `0011` = ADC3.
> - B: `1110 0101` uses `REFS1:0` = `11` for AVCC. `11` is the internal 1.1 V; AVCC is `01`.
> - C: The value is right, but in `0x43` `REFS1:0` = `01`. That is AVCC; the internal 1.1 V is `11`.
> - D: Both parts swap the table rows `01` (AVCC) and `11` (internal 1.1 V).

### Q23 (slide 35, ADLAR)

![[cmpe453_lecture_7_lecture7_adc_p035.jpg]]

A conversion gives the 10-bit result `0x2D7` (D9 ... D0). `ADLAR` = 1. What do `ADCH` and `ADCL` contain?

- A) `ADCH` = `0x2D`, `ADCL` = `0xC0`
- B) `ADCH` = `0xB5`, `ADCL` = `0x03`
- C) `ADCH` = `0x2D`, `ADCL` = `0x03`
- D) `ADCH` = `0xB5`, `ADCL` = `0xC0`

> [!success]- Answer
> **D.** `0x2D7` = 727 = `10 1101 0111`.
> `ADCH` = D9 ... D2 = `1011 0101` = `0xB5` = 181 ($727\gg2$). `ADCL` = D1 D0 in bits 7 and 6, then six zeros: `1100 0000` = `0xC0`.
> (With `ADLAR` = 0 it would be `ADCH` = `0x02`, `ADCL` = `0xD7`.)
> - A: `ADCL` is right, but `0x2D` drops one hex digit (4 bits). Left adjust drops only 2 bits: `ADCH` = `0x2D7` $\gg2$.
> - B: `ADCH` is right, but with `ADLAR` = 1 the bits D1 D0 go to bits 7 and 6 of `ADCL`, not to bits 1 and 0.
> - C: Both slips: a shift by 4 bits instead of 2, and D1 D0 in the wrong end of `ADCL`.

## 8. The ADC data register and the read order (slide 36)

### Q24 (slide 36)

(a) `ADLAR` = 0. A program reads `ADCL` = `0x9C` and then `ADCH` = `0x01`. What is the result, and which voltage is it with $V_{ref}=5$ V ($V_{in}\approx\text{ADC}\cdot V_{ref}/1024$)? (b) Another program reads only `ADCL` in every loop and never `ADCH`. What goes wrong?

- A) (a) 156, about 0.76 V. (b) The register stays locked; the value never changes.
- B) (a) 412, about 2.01 V. (b) Only the two top bits are lost; new values still arrive.
- C) (a) 412, about 2.01 V. (b) The register stays locked; the value never changes.
- D) (a) 156, about 0.76 V. (b) Only the two top bits are lost; new values still arrive.

> [!success]- Answer
> **C.** (a) Result = `(0x01 << 8) | 0x9C` = `0x19C` = $256+156=412$. $412\cdot5/1024=2.01$ V.
> (b) Reading `ADCL` locks the data register until `ADCH` is read. That never happens, so new results are not stored. The program sees the same old value forever (and loses the two top bits too).
> - A: (b) is right, but 156 is only `ADCL`. `ADCH` = 1 adds 256.
> - B: (a) is right, but reading `ADCL` locks the register. Without a read of `ADCH` no new value arrives.
> - D: Both slips: `ADCH` was ignored in (a), and the lock in (b) was missed.

## 9. ADCSRA: the clock prescaler and the control bits (slides 37-38)

### Q25 (slide 37)

A board runs at 20 MHz. The ADC clock is $f_{CPU}$ / division factor, with factors 2, 4, 8, 16, 32, 64, 128, and it must be 50 to 200 kHz. A normal conversion takes 13 ADC clock cycles, the first one after enabling takes 25. Give $f_{ADC}$ and the time of a normal and of the first conversion.

- A) 125 kHz. Normal 104 µs, first 200 µs.
- B) 156.25 kHz. Normal 83.2 µs, first 160 µs.
- C) 125 kHz. Normal 104 µs, first 104 µs.
- D) 156.25 kHz. Normal 83.2 µs, first 83.2 µs.

> [!success]- Answer
> **B.** The factor must be from $20\,000\,000/200\,000=100$ to $20\,000\,000/50\,000=400$. Only 128 fits (`ADPS2:0` = `111`). 64 would give 312.5 kHz.
> $f_{ADC}=20\,000\,000/128=156.25$ kHz. One cycle $=6.4$ µs. Normal: $13\cdot6.4=83.2$ µs. First: $25\cdot6.4=160$ µs.
> - A: These are the 16 MHz values ($16\text{ MHz}/128$). At 20 MHz the same factor gives a faster clock.
> - C: Both slips: the 16 MHz clock, and the first conversion needs 25 cycles, not 13.
> - D: The clock is right, but the first conversion takes 25 cycles: 160 µs.

### Q26 (slide 38)

`ADCSRA` bits: 7 `ADEN`, 6 `ADSC`, 5 `ADATE`, 4 `ADIF`, 3 `ADIE`, 2-0 `ADPS2:0`. The register contains `0xC7`. What does `ADSC` = 1 mean here? What will `ADCSRA` contain when the hardware has finished (no interrupt routine runs)?

- A) A conversion is running now. After the end: `0x97`.
- B) The last conversion is finished. After the end: `0x97`.
- C) A conversion is running now. After the end: `0x87`.
- D) The last conversion is finished. After the end: `0x87`.

> [!success]- Answer
> **A.** `0xC7` = `1100 0111`: `ADEN` = 1 (on), `ADSC` = 1, `ADATE` = 0 (single conversion), `ADIF` = 0, `ADIE` = 0, `ADPS2:0` = `111` (128). Reading `ADSC` = 1 means a conversion is in progress.
> At the end the hardware clears `ADSC` and sets `ADIF`: `1001 0111` = `0x97`. `ADIF` stays 1 because no interrupt routine clears it.
> - B: The end value is right, but `ADSC` reads 1 while the conversion runs. The hardware clears it at the end.
> - C: The meaning is right, but the end of a conversion also sets `ADIF` (bit 4). `0x87` forgets it.
> - D: `ADSC` = 1 means "in progress"; it becomes 0 at the end. And `ADIF` is set at the end.

## 10. The steps to use the ADC (slide 39)

### Q27 (slide 39)

`ADMUX` bits: 7 `REFS1`, 6 `REFS0`, 5 `ADLAR`, 3-0 `MUX3:0`; `REFS1:0` = `11` is the internal 1.1 V. `ADCSRA` bits: 7 `ADEN`, 6 `ADSC`, 5 `ADATE`, 4 `ADIF`, 3 `ADIE`, 2-0 `ADPS2:0`. The board runs at 16 MHz; the ADC clock must be 50 to 200 kHz. Set: internal 1.1 V, channel ADC1, right-adjusted, single conversion, no interrupt, ADC enabled but no conversion started yet.

- A) `ADMUX` = `0x41`, `ADCSRA` = `0xC7`
- B) `ADMUX` = `0xC1`, `ADCSRA` = `0x87`
- C) `ADMUX` = `0xC1`, `ADCSRA` = `0xC7`
- D) `ADMUX` = `0x41`, `ADCSRA` = `0x87`

> [!success]- Answer
> **B.** `ADMUX`: `REFS1:0` = `11`, `ADLAR` = 0, `MUX3:0` = `0001`: `1100 0001` = `0xC1`.
> `ADCSRA`: `ADEN` = 1, `ADSC` = 0, `ADPS2:0` = `111` (16 MHz / 128 = 125 kHz): `1000 0111` = `0x87`.
> A conversion is started later with `ADCSRA |= (1 << ADSC);`.
> - A: Both slips: `0x41` sets only `REFS0` (AVCC), and `0xC7` already sets `ADSC`, so a conversion would start.
> - C: `ADMUX` is right, but `ADSC` (bit 6) must stay 0: no conversion is started yet.
> - D: `ADCSRA` is right, but `0x41` selects AVCC. The 1.1 V reference needs both `REFS` bits.

## 11. Example 1: a potentiometer and eight LEDs (slides 40-41)

### Q28 (slide 40)

The potentiometer gives 2.0 V, $V_{ref}=5$ V, $\text{ADC}=V_{in}\cdot1024/V_{ref}$ with the fraction cut off. The slide program does:

```c
ledValue = (adcValue >> 7);
LED_PORT = 0;
for (i = 0; i <= ledValue; i++)
      {
      LED_PORT |= (1 << i);
      }
```

Which ADC value is read, and how many LEDs are on?

- A) ADC = 410. 3 LEDs are on.
- B) ADC = 409. 3 LEDs are on.
- C) ADC = 410. 4 LEDs are on.
- D) ADC = 409. 4 LEDs are on.

> [!success]- Answer
> **D.** $2.0\cdot1024/5=409.6$, cut off: 409 = `0110011001`.
> $409\gg7=3$ (the three MSBs `011`). The loop runs for `i` = 0, 1, 2, 3: 4 LEDs (PD0 to PD3).
> - A: Both slips: 409.6 rounded up instead of cut off, and the loop condition `i <= ledValue` runs 4 times, not 3.
> - B: The value is right, but `<=` includes `i` = 3. The loop lights `ledValue + 1` LEDs.
> - C: The LED count is right ($410\gg7$ is also 3), but the course cuts the fraction off: 409.

### Q29 (slide 41)

Same program (`ledValue = (adcValue >> 7);` and the loop `for (i = 0; i <= ledValue; i++)` that sets bit `i` of the 8-bit port D). (a) How many LEDs are on for ADC = 100 and for ADC = 1023? (b) A student changes the shift to `adcValue >> 6`. From which ADC value are all 8 LEDs on ($V_{ref}=5$ V)?

- A) (a) 0 LEDs and 7 LEDs. (b) From 448 (2.19 V).
- B) (a) 0 LEDs and 7 LEDs. (b) From 512 (2.50 V).
- C) (a) 1 LED and 8 LEDs. (b) From 448 (2.19 V).
- D) (a) 1 LED and 8 LEDs. (b) From 512 (2.50 V).

> [!success]- Answer
> **C.** (a) $100\gg7=0$: the loop runs once, 1 LED. $1023\gg7=7$: the loop runs 8 times, 8 LEDs.
> (b) `>> 6` gives 0 to 15. All 8 LEDs are on when `ledValue` = 7, so from $7\cdot64=448$, $448\cdot5/1024=2.19$ V. Above that the bar does not change.
> - A: (b) is right, but `<=` lights `ledValue + 1` LEDs. The bar is never empty.
> - B: Both parts count `ledValue` LEDs instead of `ledValue + 1`. Then 8 LEDs would need `ledValue` = 8, at $8\cdot64=512$.
> - D: (a) is right, but 8 LEDs already come at `ledValue` = 7, not 8: $7\cdot64=448$.

## 12. Example 2: two channels with the multiplexer (slides 42-44)

### Q30 (slide 42)

The program compares two readings ($V_{ref}=5$ V, $\text{ADC}=V_{in}\cdot1024/V_{ref}$, fraction cut off):

```c
if (sensorValue < lightThreshold)
        LED_PORT = 0xff;
else
        LED_PORT = 0x00;
```

The potentiometer (threshold) gives 2.0 V, so `lightThreshold` = 409. Are the LEDs on or off when the LDR gives 1.5 V and when it gives 2.4 V? What is the code for 2.4 V?

- A) Off at 1.5 V, on at 2.4 V. The 2.4 V code is 492.
- B) On at 1.5 V, off at 2.4 V. The 2.4 V code is 491.
- C) Off at 1.5 V, on at 2.4 V. The 2.4 V code is 491.
- D) On at 1.5 V, off at 2.4 V. The 2.4 V code is 492.

> [!success]- Answer
> **B.** 1.5 V: $1.5\cdot1024/5=307.2$, so 307. $307<409$ is true: `LED_PORT = 0xff`, on.
> 2.4 V: $2.4\cdot1024/5=491.52$, so 491. $491<409$ is false: `LED_PORT = 0x00`, off.
> - A: Both slips: the `if` turns the LEDs on below the threshold, and 491.52 is cut off to 491.
> - C: The code is right, but the condition is reversed. Below the threshold the LEDs are on.
> - D: The states are right, but 491.52 is cut off, not rounded: 491.

### Q31 (slide 43)

The loop reads the threshold (potentiometer) and then the LDR, every pass. Why is the threshold read inside `while(1)` and not once before it? How many conversions does one pass need, and how long do they take (125 kHz ADC clock, 13 cycles per conversion)?

- A) Otherwise the LDR result would overwrite it. 1 conversion, 104 µs.
- B) Otherwise the LDR result would overwrite it. 2 conversions, 208 µs.
- C) The user can turn the pot at any time. 2 conversions, 208 µs.
- D) The user can turn the pot at any time. 1 conversion, 104 µs.

> [!success]- Answer
> **C.** The user can turn the potentiometer at any time. Read in every pass, a new threshold works at once.
> There is one ADC, so the two channels need 2 conversions. Each takes $13\cdot8=104$ µs, together 208 µs.
> - A: Both slips: the threshold is kept in its own variable `lightThreshold`, and one ADC cannot measure two channels in one conversion.
> - B: The time is right, but the reason is wrong. `lightThreshold` is a variable; the next conversion does not change it.
> - D: The reason is right, but the two channels are measured one after the other: 2 conversions.

### Q32 (slide 44)

`readADC(channel)` selects the channel with `ADMUX = (0xf0 & ADMUX) | channel;`. `ADMUX` is `0x43` (AVCC, channel 3). The program calls `readADC(LIGHT_SENSOR)` with `LIGHT_SENSOR` = `PC0` = 0. What is the new `ADMUX`? What would `ADMUX |= channel;` give instead?

- A) `0x03`. With `ADMUX |= channel;` it stays `0x43`.
- B) `0x40`. With `ADMUX |= channel;` it stays `0x43`.
- C) `0x03`. With `ADMUX |= channel;` it becomes `0x40`.
- D) `0x40`. With `ADMUX |= channel;` it becomes `0x40`.

> [!success]- Answer
> **B.** `0xf0 & 0x43` = `1111 0000 & 0100 0011` = `0100 0000` = `0x40`. The channel bits are cleared, `REFS0` is kept. `0x40 | 0` = `0x40`: AVCC, ADC0.
> `ADMUX |= 0` clears nothing: `0x43 | 0` = `0x43`. The channel stays 3, and the program measures the potentiometer twice.
> - A: `0x03` keeps the low half. The mask `0xf0` keeps the upper four bits (the reference and `ADLAR`).
> - C: Both slips: the mask keeps the upper half, and OR can only set bits, never clear them.
> - D: The first part is right, but OR with 0 changes nothing. Old channel bits must be cleared first.

## 13. Free running mode and Example 3 (slides 45-49)

### Q33 (slide 45)

`ADCSRB` bits 2-0 are `ADTS2:0`, the auto trigger source. In the trigger table, Timer/Counter0 compare match A is `ADTS2:0` = `011`. `ADCSRA` bits: 7 `ADEN`, 6 `ADSC`, 5 `ADATE`, 4 `ADIF`, 3 `ADIE`, 2-0 `ADPS2:0`. Which `ADTS` bits are set, and which `ADCSRA` bit makes the trigger start the conversions?

- A) `ADTS1` and `ADTS0`. In `ADCSRA`: `ADATE`.
- B) `ADTS2` and `ADTS1`. In `ADCSRA`: `ADATE`.
- C) `ADTS1` and `ADTS0`. In `ADCSRA`: `ADIE`.
- D) `ADTS2` and `ADTS1`. In `ADCSRA`: `ADIE`.

> [!success]- Answer
> **A.** `011` read as `ADTS2 ADTS1 ADTS0`: `ADTS1` = 1, `ADTS0` = 1. `ADCSRB |= (1 << ADTS1) | (1 << ADTS0);`
> `ADATE` (auto trigger enable): `ADCSRA |= (1 << ADATE);`. Now each trigger event starts a conversion in hardware, so the program no longer writes `ADSC` in a loop.
> - B: `ADATE` is right, but `ADTS2` and `ADTS1` is `110`, the bits read in reverse order (Timer/Counter1 overflow).
> - C: The bits are right, but `ADIE` only enables the conversion complete interrupt. It does not start conversions.
> - D: Both slips: the bit order is reversed, and `ADIE` is the interrupt enable, not the auto trigger enable.

### Q34 (slides 46-47)

In Example 3 the ADC runs in free running mode (each conversion starts the next) and the loop sends one value every 200 ms. The ADC clock is 125 kHz and one conversion takes 13 cycles. About how many conversions finish between two transmissions, and which one is sent?

- A) About 25 000. The oldest unsent one is sent.
- B) About 1923. The oldest unsent one is sent.
- C) About 25 000. Only the newest one is sent.
- D) About 1923. Only the newest one is sent.

> [!success]- Answer
> **D.** One conversion: $13/125\,000=104$ µs. In 200 ms: $0.2\cdot125\,000/13\approx1923$.
> Each new result overwrites the data register. So only the newest one is sent; the older ones are lost.
> - A: Both slips: $0.2\cdot125\,000=25\,000$ counts clock cycles, not conversions (divide by 13), and there is no queue of results.
> - B: The count is right, but the data register holds only one result. It is always the newest.
> - C: The register part is right, but 25 000 is the number of ADC clock cycles. One conversion needs 13 of them.

### Q35 (slide 48)

`initFreerunningADC()` does, in this order:

```c
ADMUX |= (1 << REFS0); /* reference voltage on AVCC */
ADCSRA |= (1 << ADPS1) | (1 << ADPS0); /* ADC clock prescaler /8 */
ADMUX |= (1 << ADLAR); /* left-adjust result*/
ADCSRA |= (1 << ADEN); /* enable ADC */
ADCSRA |= (1 << ADATE); /* auto-trigger enable */
ADCSRA |= (1 << ADSC); /* start first conversion */
```

`ADCSRA` bits: 7 `ADEN`, 6 `ADSC`, 5 `ADATE`, 4 `ADIF`, 3 `ADIE`, 2-0 `ADPS2:0`. `ADCSRB` holds `ADTS2:0`; `000` = free running. What does `ADCSRA` read while the ADC runs? Is the slide line `ADCSRB &= ~((1 << ADTS2) | (1 << ADTS1) | (1 << ADTS0));` needed after a reset?

- A) `0xE3`. The `ADCSRB` line is not needed: it is 0 after reset.
- B) `0xA3`. The `ADCSRB` line is needed, or no trigger is chosen.
- C) `0xE3`. The `ADCSRB` line is needed, or no trigger is chosen.
- D) `0xA3`. The `ADCSRB` line is not needed: it is 0 after reset.

> [!success]- Answer
> **A.** `ADEN`, `ADSC`, `ADATE`, `ADPS1`, `ADPS0`: `1110 0011` = `0xE3`. In free running mode a conversion is always in progress, so `ADSC` reads 1 all the time.
> `ADCSRB` is `0x00` after reset, so `ADTS2:0` is already `000` = free running. The line only helps if other code changed `ADCSRB`. (`ADMUX` = `0x60`: AVCC, left-adjusted, ADC0.)
> - B: Both slips: `ADSC` does not go back to 0 in free running mode, and `000` is the reset value, which already means free running.
> - C: The value is right, but `ADTS2:0` = `000` after reset already selects free running.
> - D: The `ADCSRB` part is right, but `ADSC` stays 1: each conversion starts the next one.

### Q36 (slide 49)

`ADLAR` = 1 and the program sends `ADCH`, which holds D9 ... D2 of the 10-bit result ($\text{ADC}=V_{in}\cdot1024/V_{ref}$, $V_{ref}=5$ V). The laptop receives the byte `0xB4`. Which voltage is on the LDR pin, and which 10-bit results can be behind this byte?

- A) About 0.88 V. The 10-bit result is 180 to 183.
- B) About 3.52 V. The 10-bit result is 180 to 183.
- C) About 3.52 V. The 10-bit result is 720 to 723.
- D) About 0.88 V. The 10-bit result is 720 to 723.

> [!success]- Answer
> **C.** `0xB4` = 180. `ADCH` has 8 bits, so $V_{in}\approx180\cdot5/256=3.52$ V.
> The lost bits D1 D0 can be `00` to `11`: the 10-bit result is $180\cdot4=720$ to $723$.
> (With `ADLAR` = 0, `ADCH` would hold only D9 D8: four levels, 0 to 3.)
> - A: Both parts treat the byte as the full 10-bit result. It is the result shifted right by 2.
> - B: The voltage is right, but D1 D0 are the lowest bits: the byte must be multiplied by 4 first.
> - D: The range is right, but the byte counts steps of $5/256$ V, not $5/1024$ V.
