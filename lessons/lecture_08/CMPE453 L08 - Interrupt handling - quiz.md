---
course: CMPE 453 Embedded Systems
lesson: CMPE453 L08 - Interrupt handling
type: quiz
questions: 20
---

# CMPE453 L08 - Interrupt handling - quiz

Lesson note: [[CMPE453 L08 - Interrupt handling]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. What an interrupt is (slide 2)

### Q1 (slide 2)

A main loop needs 350 ms for one turn. It reads a button once per turn. (a) What is the worst-case delay between a press and the reaction? (b) A press lasts 120 ms. Can the program miss it?

- A) (a) About 175 ms. (b) No, it cannot miss it.
- B) (a) About 350 ms. (b) No, it cannot miss it.
- C) (a) About 350 ms. (b) Yes, it can miss it.
- D) (a) About 175 ms. (b) Yes, it can miss it.

> [!success]- Answer
> **C.** (a) The press can come just after a check. The next check is one full turn later: about 350 ms.
> (b) The loop reads the pin only at one moment per turn. A 120 ms press can start after one check and end before the next one (120 ms < 350 ms). Both reads say "not pressed".
> With an interrupt the edge sets a flag in hardware, and the ISR starts a few clock cycles later. The press cannot be missed.
> - A: 175 ms is the average delay (half a turn), not the worst case. And the press can be missed, see (b).
> - B: (a) is right. But a press is not seen just because it is longer than one read of the pin. It must cover a moment of a check.
> - D: (b) is right, but 175 ms is the average delay, not the worst case.

## 2. The ISR and the interrupt vector table (slide 3)

### Q2 (slide 3)

On the ATmega328P, vector 1 (RESET) is at word address `0x0000`, and each vector takes 2 words. Vector numbers: INT1 = 3, PCINT0 = 4, TIMER0 OVF = 17, ADC = 22. (a) At which word address is the ADC vector? (b) The flags of TIMER0 OVF, INT1 and PCINT0 are set at the same moment. All three are enabled. In which order do the ISRs run?

- A) (a) `0x002A`. (b) INT1, then PCINT0, then TIMER0 OVF.
- B) (a) `0x002C`. (b) INT1, then PCINT0, then TIMER0 OVF.
- C) (a) `0x002A`. (b) TIMER0 OVF, then PCINT0, then INT1.
- D) (a) `0x002C`. (b) TIMER0 OVF, then PCINT0, then INT1.

> [!success]- Answer
> **A.** (a) Vector $n$ is at $2\cdot(n-1)$, because vector 1 starts at 0. ADC: $2\cdot(22-1)=42$ = `0x002A`.
> (b) Priority is the position in the table: the lowest vector number runs first. INT1 (3), then PCINT0 (4), then TIMER0 OVF (17).
> - B: $2\cdot22=44$ = `0x002C`. This forgets that vector 1 is at address 0. `0x002C` is the vector of EE READY.
> - C: (a) is right, but the order is reversed. A higher vector number has a lower priority.
> - D: Both slips: $2\cdot22$ and the reversed order.

## 3. What the CPU does when an interrupt arrives (slides 4-5)

### Q3 (slide 4)

Before an interrupt, `SP` = `0x08F0` and the next instruction of main is at `0x0200`. The interrupt is PCINT1, vector number 5. Vector 1 is at word address `0x0000`, and each vector takes 2 words. The CPU pushes the 2 bytes of the PC. The PC holds word addresses in flash; `SP` holds byte addresses in the data SRAM. Give the PC and `SP` right after the CPU has entered the vector table.

- A) PC = `0x000A`, `SP` = `0x08EE`.
- B) PC = `0x0008`, `SP` = `0x08F2`.
- C) PC = `0x000A`, `SP` = `0x08F2`.
- D) PC = `0x0008`, `SP` = `0x08EE`.

> [!success]- Answer
> **D.** Vector address: $2\cdot(5-1)=8$ = `0x0008`.
> The stack starts at the top of the RAM and grows down. Two bytes are pushed: `0x08F0` − 2 = `0x08EE`.
> After `RETI`: PC = `0x0200` and `SP` = `0x08F0` again. The CPU saves the **next** address, because the current instruction is already finished. Saving it would run it twice.
> - A: $2\cdot5=10$ = `0x000A` forgets that vector 1 is at 0. `0x000A` is the PCINT2 vector.
> - B: The address is right, but the stack does not grow up. A push makes `SP` smaller.
> - C: Both slips: $2\cdot5$ and a stack that grows up.

### Q4 (slide 5)

The ISR of INT0 (vector 2) is running. During it, first a TIMER1 overflow (vector 14) happens, then a pin-change event PCINT0 (vector 4). Both are enabled. When do their ISRs run, and in which order?

- A) At once, nested inside the INT0 ISR: TIMER1 OVF first, then PCINT0.
- B) After the `RETI` of the INT0 ISR: PCINT0 first, then TIMER1 OVF.
- C) After the `RETI` of the INT0 ISR: TIMER1 OVF first, then PCINT0.
- D) At once, nested inside the INT0 ISR: PCINT0 first, then TIMER1 OVF.

> [!success]- Answer
> **B.** The CPU cleared the I bit when it entered the INT0 ISR. The two events only set their flags and wait.
> `RETI` sets I = 1. One instruction of main runs. Then the waiting interrupt with the lowest vector number runs: PCINT0 (4). After its `RETI` and one more instruction of main, TIMER1 OVF (14) runs.
> Without "I is set back to 1" in `RETI`, I would stay 0 for ever and only one ISR would ever run.
> - A: An ISR is not nested, because I = 0 inside it. And the order is by arrival, not by priority.
> - C: The time is right, but waiting interrupts are served by vector number, not in order of arrival.
> - D: The order is right, but nothing starts while I = 0.

## 4. Kinds of interrupts and the vector for one pin (slides 6-7)

### Q5 (slide 6)

![[cmpe453_lecture_8_lecture8_p006.jpg]]

You want an interrupt when the USART has finished sending a frame (Tx complete). On the ATmega328P n = 0. The avr-libc USART vectors are `USART_RX_vect`, `USART_UDRE_vect` and `USART_TX_vect`. Which line enables this interrupt (before `sei();`), and which ISR header do you write?

- A) `UCSR0A |= (1 << TXC0);` and `ISR(USART_TX_vect)`.
- B) `UCSR0B |= (1 << TXCIE0);` and `ISR(USART_TX_vect)`.
- C) `UCSR0A |= (1 << TXC0);` and `ISR(USART_UDRE_vect)`.
- D) `UCSR0B |= (1 << TXCIE0);` and `ISR(USART_UDRE_vect)`.

> [!success]- Answer
> **B.** The enable bit is `TXCIEn` (bit 6 of `UCSRnB`). The flag is `TXCn` (bit 6 of `UCSRnA`). The hardware sets the flag when the frame is sent.
> `UCSR0B |= (1 << TXCIE0);` sets bit 6 ($2^6=64$ = `0x40`). The vector of "Tx complete" is `USART_TX_vect`.
> - A: The vector is right, but `TXC0` is the flag. The hardware sets it. Writing a 1 to it by hand clears the flag; it enables nothing.
> - C: The flag is set in place of the enable bit, and UDRE means "data register empty", not "frame sent".
> - D: The enable line is right, but `USART_UDRE_vect` belongs to the data-register-empty interrupt. The TX vector would then have no ISR.

### Q6 (slide 7)

![[cmpe453_lecture_8_lecture8_p007.jpg]]

The button moves from PD7 to PC3 (A3 on the Uno). PC3 is the pin PCINT11. The pin-change groups are PCINT0 = port B, PCINT1 = port C, PCINT2 = port D, in the order of the three "Pin Change Interrupt Request" rows. The addresses in the table are word addresses. Which ISR header is right now, and why?

- A) `ISR(PCINT2_vect)`: every pin-change pin uses the row at `0x000A`.
- B) `ISR(PCINT11_vect)`: PC3 is the pin PCINT11, and each pin has a vector.
- C) `ISR(PCINT3_vect)`: PC3 is pin 3 of its port, so the vector number is 3.
- D) `ISR(PCINT1_vect)`: port C is group PCINT1, the row at `0x0008`.

> [!success]- Answer
> **D.** PC3 is on port C. Port C is the second pin-change group, PCINT1, at address `0x0008`. So the header is `ISR(PCINT1_vect)`.
> If you keep `ISR(PCINT2_vect)`, the vector at `0x0008` has no ISR. It jumps to the default handler and then to address 0: the program restarts at every change of PC3.
> - A: The rows are one per port, not one for all pins. `0x000A` serves only port D.
> - B: Vectors exist per group, not per pin. `PCINT11_vect` does not exist; avr-gcc only warns, and the program restarts.
> - C: The pin number inside the port is the bit in `PCMSK1`, not the vector. `PCINT3_vect` does not exist.

## 5. Example with INT0: the task, the pins, the CPU (slides 8-10)

### Q7 (slide 8)

Task: LED0 blinks with `_delay_ms(200)` in the main loop, LED1 goes on when a button is pressed. Suppose the button were polled once per turn of the main loop, not handled by INT0. What would the user see?

- A) LED1 reacts up to 200 ms late, and a short tap between two checks does nothing.
- B) LED1 reacts at once, but LED0 stops blinking while the button is held down.
- C) LED1 reacts at once, because one turn of 200 ms is short compared with a press.
- D) LED0 blinks at half speed, because each check of the button also takes 200 ms.

> [!success]- Answer
> **A.** The loop spends almost all its time in `_delay_ms(200)`. The button is read only once every 200 ms. A press can wait up to 200 ms, and a tap that starts and ends inside the delay is never seen.
> With INT0 the ISR runs within microseconds, in the middle of the delay, and LED0 keeps blinking.
> - B: LED0 stopping is the problem of the low-level mode of INT0, not of polling.
> - C: A check happens only at one moment per turn. "Short compared with a press" is not true for a quick tap.
> - D: Reading one pin takes less than a microsecond. The 200 ms are the delay, not the check.

### Q8 (slide 9)

![[cmpe453_lecture_8_lecture8_p009.jpg]]

Use the pinout. The pin-change vectors are `PCINT0_vect` (port B), `PCINT1_vect` (port C) and `PCINT2_vect` (port D). INT0 is the function of PD2. (a) Which vector serves a button on A5? (b) Can you use INT0 for a button on D7?

- A) (a) `PCINT13_vect`. (b) Yes, `EICRA` can move INT0 to D7.
- B) (a) `PCINT1_vect`. (b) Yes, `EICRA` can move INT0 to D7.
- C) (a) `PCINT1_vect`. (b) No, INT0 is fixed to PD2 (D2).
- D) (a) `PCINT13_vect`. (b) No, INT0 is fixed to PD2 (D2).

> [!success]- Answer
> **C.** (a) A5 = PC5 = PCINT13. It is on port C, so its vector is the group vector `PCINT1_vect`.
> (b) INT0 exists only on PD2 (D2). D7 = PD7 = PCINT23 can only use the pin-change group of port D (`PCINT2_vect`).
> - A: There is one vector per group, so `PCINT13_vect` does not exist. And `EICRA` only selects the trigger, not the pin.
> - B: (a) is right, but `EICRA` only chooses low level, change or edge. It cannot move INT0 to another pin.
> - D: (b) is right, but PCINT13 is the pin name, not a vector name.

### Q9 (slide 10)

![[cmpe453_lecture_8_lecture8_p010.jpg]]

In this block diagram, where is (a) the return address stored while an ISR runs, and (b) the global interrupt bit I?

- A) (a) In the 32 x 8 General Purpose Registers. (b) In the Interrupt Unit.
- B) (a) In the Data SRAM. (b) In the Interrupt Unit.
- C) (a) In the 32 x 8 General Purpose Registers. (b) In Status and Control.
- D) (a) In the Data SRAM. (b) In Status and Control.

> [!success]- Answer
> **D.** (a) The PC is pushed on the stack, and the stack is part of the Data SRAM.
> (b) I is bit 7 of `SREG`, which sits in the Status and Control block.
> The Interrupt Unit only decides which waiting interrupt runs first. The vector table is in the Flash Program Memory.
> - A: The CPU does not save the PC in a working register. And the Interrupt Unit picks the interrupt, but it does not hold `SREG`.
> - B: (a) is right, but I is in `SREG`, not in the Interrupt Unit.
> - C: (b) is right, but the return address goes to the stack in the SRAM.

## 6. The registers: SREG, EIMSK, EICRA (slides 11-13)

### Q10 (slide 11)

INT0 is enabled for the falling edge (`ISC01:ISC00` = 10; codes: 00 low level, 01 any change, 10 falling edge, 11 rising edge). A program runs `cli();`, then a block of code that takes 3 ms, then `sei();`. During the 3 ms the button is pressed and released twice. How many times does `ISR(INT0_vect)` run after `sei();`?

- A) 0 times.
- B) 1 time.
- C) 2 times.
- D) 4 times.

> [!success]- Answer
> **B.** The first falling edge sets `INTF0`. I is 0, so the ISR cannot start; the request waits. The second falling edge finds the flag already 1. A flag is one bit, so it cannot count to 2.
> After `sei();` the CPU runs one more instruction and then the ISR, once. One press is served late (up to 3 ms), the other is lost.
> - A: The flag is set also while I = 0. `cli()` delays the request; it does not delete it.
> - C: This assumes that each edge is stored. The flag stores only "at least one edge".
> - D: This counts both edges of each press, as in the "any change" mode. Here only falling edges set the flag.

### Q11 (slide 12)

`EIMSK` = `0x02` (`INT0` = bit 0, `INT1` = bit 1), I = 1, both INT0 (PD2) and INT1 (PD3) are set to the falling edge. `EIFR` holds the flags `INTF0` (bit 0) and `INTF1` (bit 1). A falling edge comes on PD2, then one on PD3. Later the program runs `EIMSK |= (1 << INT0);`. What happens to `INTF0` at the PD2 edge, and what is `EIMSK` after the line?

- A) `INTF0` stays 0, because INT0 is masked: the edge is lost. `EIMSK` = `0x03`.
- B) `INTF0` is set and waits: `ISR(INT0_vect)` runs when INT0 is enabled. `EIMSK` = `0x01`.
- C) `INTF0` is set and waits: `ISR(INT0_vect)` runs when INT0 is enabled. `EIMSK` = `0x03`.
- D) `INTF0` stays 0, because INT0 is masked: the edge is lost. `EIMSK` = `0x01`.

> [!success]- Answer
> **C.** `0x02` = `0b00000010`: only INT1 is enabled. So only `ISR(INT1_vect)` runs for the PD3 edge.
> The PD2 edge still sets `INTF0`. The mask only stops the ISR, not the flag.
> `EIMSK |= (1 << INT0);` gives `0b00000011` = `0x03`. The old flag is still 1, so `ISR(INT0_vect)` runs at once for the old edge. To avoid that, write `EIFR = (1 << INTF0);` first.
> - A: The hardware sets the flag also when the interrupt is masked.
> - B: The flag part is right, but `|=` keeps bit 1: `0x02 | 0x01 = 0x03`. `0x01` is the result of `=`.
> - D: Both slips: the flag is set, and `|=` keeps INT1.

### Q12 (slide 13)

`EICRA`: `ISC11:ISC10` are bits 3:2 (INT1), `ISC01:ISC00` are bits 1:0 (INT0). Codes: 00 low level, 01 any change, 10 falling edge, 11 rising edge. (a) Which value makes INT0 react to the rising edge and INT1 to any logical change? (b) Decode `EICRA` = `0x09`.

- A) (a) `0x07`. (b) INT1 on the falling edge, INT0 on any change.
- B) (a) `0x0D`. (b) INT1 on the falling edge, INT0 on any change.
- C) (a) `0x07`. (b) INT1 on any change, INT0 on the falling edge.
- D) (a) `0x0D`. (b) INT1 on any change, INT0 on the falling edge.

> [!success]- Answer
> **A.** (a) INT0 rising: bits 1:0 = `11`. INT1 any change: bits 3:2 = `01`. `0b00000111` = `0x07`.
> (b) `0x09` = `0b00001001`. Bits 3:2 = `10`: INT1 on the falling edge. Bits 1:0 = `01`: INT0 on any change.
> For a light barrier with one short pulse per object, choose a falling or rising edge. "Any change" gives two interrupts per object, and "low level" repeats the ISR while the pulse is low.
> - B: In (a) the two pairs are swapped: INT0's `11` in bits 3:2 and INT1's `01` in bits 1:0 give `0b1101` = `0x0D`.
> - C: (a) is right, but (b) swaps the pairs: bits 3:2 belong to INT1.
> - D: The pairs are swapped in both parts.

## 7. The INT0 program (slide 14)

### Q13 (slide 14, trace)

The slide program sets `EICRA |= (1 << ISC00);` (`ISC01:ISC00` = 01: any change). The ISR turns LED1 on when PD2 is low (pressed) and off when it is high. Main toggles LED0 every 200 ms. The button is pressed, held for 1 s, then released. What does the user see?

- A) LED1 is on exactly while the button is held. LED0 keeps blinking.
- B) LED1 is on exactly while the button is held. LED0 stops while it is held.
- C) LED1 goes on at the press and stays on. LED0 keeps blinking.
- D) LED1 goes on at the press and stays on. LED0 stops while it is held.

> [!success]- Answer
> **A.** The press (1 to 0) is a change: the ISR runs, PD2 is low, LED1 goes on. `RETI` sets I = 1 again.
> While the button is held, the level does not change, so no ISR runs. Main toggles LED0 every 200 ms.
> The release (0 to 1) is a change too: the ISR runs again, PD2 is high, LED1 goes off. Each ISR takes a few microseconds, so LED0 shows no pause.
> - B: LED0 would stop only in the low-level mode (00), where the ISR repeats while the pin is low.
> - C: This forgets the release. "Any change" also reacts to the rising edge.
> - D: This is the behaviour of the low-level mode, not of "any change".

### Q14 (slide 14, change)

Same program. (1) A student sets `ISC01` in place of `ISC00` (code 10: falling edge). (2) Another student leaves `EICRA` at 0 (code 00: low level). The button is pressed, held, then released. What happens in each case?

- A) (1) LED1 is on only while held. (2) LED0 blinks normally while held.
- B) (1) LED1 is on only while held. (2) LED0 almost stops while held.
- C) (1) LED1 goes on and stays on. (2) LED0 almost stops while held.
- D) (1) LED1 goes on and stays on. (2) LED0 blinks normally while held.

> [!success]- Answer
> **C.** (1) `EICRA` = `0x02`. The ISR runs only at the press, sees a low pin and turns LED1 on. The release is a rising edge and makes no interrupt, so the `else` branch never runs. LED1 stays on for ever.
> (2) Low level: while the pin is low the ISR starts again and again, with one instruction of main between two runs. LED0 almost stops. After the release nothing calls the ISR, so LED1 also stays on.
> - A: The ISR does read the level, but it must run to do so. In (1) it does not run at the release. In (2) the ISR repeats while the pin is low.
> - B: (2) is right, but in (1) no interrupt comes at the release.
> - D: (1) is right, but low level is not an edge: it repeats the ISR as long as the pin is low.

## 8. Pin-change interrupts (slides 15-17)

### Q15 (slide 15)

A sensor on A0 (PC0 = PCINT8, group PCINT1, vector `PCINT1_vect`) gives a high pulse. You need an interrupt only at the start of the pulse (rising edge). INT0 and INT1 are already used. What works?

- A) Nothing: a pin-change interrupt reacts to both edges, so one edge cannot be selected.
- B) In `ISR(PCINT1_vect)` do the work only if `PINC & (1 << PC0)` is not 0.
- C) Set the rising-edge code `11` in `EICRA`, then enable PCINT8 in `PCMSK1`.
- D) In `ISR(PCINT1_vect)` do the work only if `PINC & (1 << PC0)` is 0.

> [!success]- Answer
> **B.** The ISR runs at both edges. Read the pin inside it. If PC0 is high now, the change was a rising edge: do the work. At the falling edge the test is false and the ISR returns at once.
> - A: True that the hardware has no edge selection, but software can test the level after the change.
> - C: `EICRA` controls only INT0 and INT1. Pin-change interrupts always react to any change.
> - D: The pin is 0 after a falling edge. This keeps the end of the pulse, not the start.

### Q16 (slide 16)

Buttons are on A3 and D6. Uno pins: A0 to A5 = PC0 to PC5, D0 to D7 = PD0 to PD7. In `PCMSK1` (port C) and `PCMSK2` (port D), bit $k$ belongs to pin $k$ of the port. Which values must `PCMSK1` and `PCMSK2` have?

- A) `PCMSK1` = `0x03`, `PCMSK2` = `0x06`.
- B) `PCMSK1` = `0x08`, `PCMSK2` = `0x06`.
- C) `PCMSK1` = `0x03`, `PCMSK2` = `0x40`.
- D) `PCMSK1` = `0x08`, `PCMSK2` = `0x40`.

> [!success]- Answer
> **D.** A3 = PC3 = PCINT11 ($8+3$): bit 3 of `PCMSK1`, value `1 << 3` = `0x08`.
> D6 = PD6 = PCINT22 ($16+6$): bit 6 of `PCMSK2`, value `1 << 6` = `0x40`.
> With a third button on D9 (PB1): `PCMSK0` = `0x02`. All three groups are then used: `PCICR` = `0x07`, and three ISRs are needed.
> - A: The bit numbers 3 and 6 are written as values. A register needs the mask `1 << k`, not $k$.
> - B: `PCMSK1` is right, but 6 is the bit number of PD6, not its mask `0x40`.
> - C: `PCMSK2` is right, but 3 is the bit number of PC3, not its mask `0x08`.

### Q17 (slide 17)

A program sets the bit `PCINT4` in `PCMSK0` (PB4, port B) and then calls `sei();`. PB4 changes, but the ISR never runs. `PCICR` holds `PCIE0` (bit 0, port B), `PCIE1` (bit 1, port C), `PCIE2` (bit 2, port D). Which line is missing? And would the ISR run if, after adding it, `sei();` were removed?

- A) `EIMSK |= (1 << INT0);`. Without `sei();` it would not run.
- B) `EIMSK |= (1 << INT0);`. Without `sei();` it would still run.
- C) `PCICR |= (1 << PCIE0);`. Without `sei();` it would not run.
- D) `PCICR |= (1 << PCIE0);`. Without `sei();` it would still run.

> [!success]- Answer
> **C.** Layer 1 (pin mask in `PCMSK0`) and layer 3 (I bit) are there. Layer 2, the group enable of port B, is missing: `PCICR |= (1 << PCIE0);` (value `0x01`).
> The three layers work like an AND: mask bit AND `PCIE0` AND I. Without `sei();` I = 0 and nothing runs.
> - A: `EIMSK` enables only INT0 and INT1 on PD2 and PD3. It has nothing to do with pin-change groups. The I part is right.
> - B: Wrong register, and I is needed by every interrupt.
> - D: The line is right, but I = 0 blocks all interrupts, also pin-change ones.

## 9. Example 1: enable PCINT10 on PC2 (slides 18-20)

### Q18 (slide 18)

![[cmpe453_lecture_8_lecture8_p018.jpg]]

A0 = PC0 and A4 = PC4 on the Uno. Which single value of `PCMSK1` enables exactly the pins A0 and A4?

- A) `0x11`
- B) `0x22`
- C) `0x10`
- D) `0x1100`

> [!success]- Answer
> **A.** A0 = PC0 = PCINT8 is bit 0. A4 = PC4 = PCINT12 is bit 4. Value: `(1 << 4) | (1 << 0)` = `0b00010001` = `0x11`.
> In C: `PCMSK1 |= (1 << PCINT12) | (1 << PCINT8);`. In avr-libc `PCINT12` = 4 and `PCINT8` = 0.
> - B: The bits are counted from 1: bits 1 and 5 give `0x22`.
> - C: `1 << 0` was taken as 0. It is 1, so A0 is lost.
> - D: The PCINT numbers 8 and 12 were used as bit positions: `(1 << 12) | (1 << 8)` = `0x1100`. This does not fit in an 8-bit register.

### Q19 (slides 19-20)

A student wants an interrupt on PC2 (PCINT10, port C) and writes:

```c
PCMSK1 |= (1 << PCINT10);
PCICR  |= (1 << PCIE2);
SREG   |= (1 << I);
```

Line 3 is copied from slide 20 and is a slide error: avr-libc has no name `I`, so it does not compile. Replace it with `sei();`. There is one more mistake. `PCMSK1`: bit $k$ = PC$k$. `PCICR`: `PCIE0` = bit 0 (port B), `PCIE1` = bit 1 (port C), `PCIE2` = bit 2 (port D). Both registers were 0. Which values do they have after the fully corrected code?

- A) `PCMSK1` = `0x02`, `PCICR` = `0x04`.
- B) `PCMSK1` = `0x04`, `PCICR` = `0x04`.
- C) `PCMSK1` = `0x02`, `PCICR` = `0x02`.
- D) `PCMSK1` = `0x04`, `PCICR` = `0x02`.

> [!success]- Answer
> **D.** The other mistake is line 2: `PCIE2` enables port D. PC2 is on port C, so write `PCICR |= (1 << PCIE1);`: `0b00000010` = `0x02`.
> Line 1 is right: `PCINT10` = 2 (bit 2), so `PCMSK1` = `1 << 2` = `0x04`.
> - A: `PCMSK1` = 2 writes the bit number as the value, and `PCICR` = `0x04` keeps the wrong `PCIE2`.
> - B: `PCMSK1` is right, but `0x04` is the value of the uncorrected line 2.
> - C: `PCICR` is right, but 2 is the bit number of PC2, not its mask `0x04`.

## 10. Example 2: enable PCINT21 on PD5 (slides 21-23)

### Q20 (slides 21-23)

Example 2 has a button on PD5 (PCINT21): `PCMSK2` = `0x20`, `PCICR` = `0x04` (`PCIE2`). Slides 22 and 23 say "PCINT10" in the title; they mean PCINT21. You add a second button on PD7 (PCINT23). In `PCMSK2`, bit $k$ = PD$k$. Both buttons must work. What is `PCMSK2` now, and do you need a second ISR?

- A) `PCMSK2` = `0x80`. No, `ISR(PCINT2_vect)` serves both.
- B) `PCMSK2` = `0xA0`. No, `ISR(PCINT2_vect)` serves both.
- C) `PCMSK2` = `0xA0`. Yes, `ISR(PCINT23_vect)` for PD7.
- D) `PCMSK2` = `0x80`. Yes, `ISR(PCINT23_vect)` for PD7.

> [!success]- Answer
> **B.** PD7 is bit 7. Both bits: `(1 << 7) | (1 << 5)` = `0b10100000` = `0xA0`. `PCICR` stays `0x04`, because both pins are on port D.
> Port D has one vector, so the same `ISR(PCINT2_vect)` runs for both pins. To find the pin, it compares `PIND` with the old value: `PIND ^ oldD` has bit 7 set after a press on PD7.
> - A: `0x80` is only bit 7, as after `PCMSK2 = (1 << PCINT23);`. The `=` clears bit 5, so PD5 stops working.
> - C: The value is right, but there is one vector per group. `PCINT23_vect` does not exist.
> - D: Both slips: bit 5 is lost, and a vector per pin does not exist.
