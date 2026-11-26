---
course: CMPE 453 Embedded Systems
lecture: 8
title: "Interrupt handling: INT0, INT1 and pin-change interrupts"
tags: [cmpe453, lecture]
slides: lectures/lecture_8/Lecture8.pdf
---
# CMPE 453 · Lecture 8 · Interrupt handling

> [!summary] In short
> - An interrupt is a signal from a device that needs service. The CPU stops the main program, runs a short function (the **ISR**), then continues where it stopped.
> - When an interrupt arrives the CPU finishes the current instruction, pushes the PC on the stack, clears the **I** bit of `SREG`, and jumps to a fixed entry of the **interrupt vector table**. That entry jumps to the ISR. `RETI` pops the PC and sets I again.
> - An interrupt runs only if three things are 1: its **flag**, its own **enable bit**, and the global **I** bit (`sei()` sets it, `cli()` clears it).
> - External interrupts: INT0 = PD2 = D2, INT1 = PD3 = D3. `EICRA` chooses the trigger (low level, any change, falling edge, rising edge), `EIMSK` enables, `EIFR` holds the flags.
> - Pin-change interrupts work on 23 pins in three groups: `PCMSK0/1/2` choose the pins, `PCICR` (`PCIE0/1/2`) enables the group, `PCIFR` holds the flags. They react to **any** change only.
> - In C: `ISR(INT0_vect) { ... }`. Keep ISRs short, make shared variables `volatile`, read 16-bit shared variables with interrupts off.

## 1. What an interrupt is (slide 2)
![[cmpe453_lecture_8_lecture8_p002.jpg]]

**Notes.** A device that needs service sends an **interrupt signal** to the microcontroller. The microcontroller stops what it is doing and serves the device. The program that serves the device is the **interrupt service routine (ISR)**, also called the interrupt handler.

Two more points from the slide:
- **Priority.** If several devices ask at the same time, they are served in a fixed order of priority.
- **Masking.** The program can ignore a device's request. Each interrupt has its own enable bit, and there is one global enable bit for all of them.

The other way to notice an event is **polling**: the main loop reads the device again and again and asks "do you need service?".

| | Polling | Interrupt |
|---|---|---|
| Who starts the service | the main loop asks the device | the device tells the CPU |
| CPU time | wasted on asking, also when nothing happens | used only when an event happens |
| Reaction time | up to one full turn of the main loop | a few clock cycles |
| Short events | are missed if they end before the next check | the hardware stores them in a flag |
| Code | simple, everything in one loop | needs an ISR and shared variables |

> [!example] Why polling fails with a slow loop
> A main loop blinks an LED with `_delay_ms(200)` and then checks a button once. The button is checked every 200 ms. A press of 50 ms that starts and ends inside the delay is never seen. With an interrupt the same press is seen at once, also in the middle of the delay.

> [!question]- Check (slide 2): A main loop needs 350 ms for one turn and polls a button once per turn. (a) What is the worst-case delay between a press and the reaction? (b) A press lasts 120 ms. Can the program miss it? (c) What changes for (a) and (b) if the button uses an interrupt?
> (a) The press can come just after the check. The next check is one full turn later, so the worst case is about **350 ms**.
> (b) **Yes.** If the press starts after one check and ends before the next one (120 ms is shorter than 350 ms), the loop reads "not pressed" both times.
> (c) The pin edge sets a flag in hardware. The CPU finishes the current instruction and starts the ISR after a few clock cycles (well below 1 ms). The 120 ms press cannot be missed, because the edge itself starts the ISR.

## 2. The ISR and the interrupt vector table (slide 3)
![[cmpe453_lecture_8_lecture8_p003.jpg]]

**Notes.** The left side of the slide is a chain: an interrupt is invoked, so an ISR must run. For each interrupt there is a fixed **location in program memory**. This location holds the address of the ISR (in fact a jump instruction to the ISR).

All these locations together are the **interrupt vector table**. It is at the start of the flash memory. The ATmega328P has 26 vectors:

| Vector No. | Program address | Source | Meaning |
|---|---|---|---|
| 1 | `0x0000` | RESET | reset (power-on, external pin, brown-out, watchdog) |
| 2 | `0x0002` | INT0 | external interrupt request 0 |
| 3 | `0x0004` | INT1 | external interrupt request 1 |
| 4 | `0x0006` | PCINT0 | pin change interrupt request 0 |
| 5 | `0x0008` | PCINT1 | pin change interrupt request 1 |
| 6 | `0x000A` | PCINT2 | pin change interrupt request 2 |
| 7 | `0x000C` | WDT | watchdog time-out |
| 8-10 | `0x000E` to `0x0012` | TIMER2 COMPA, COMPB, OVF | Timer/Counter2 |
| 11-14 | `0x0014` to `0x001A` | TIMER1 CAPT, COMPA, COMPB, OVF | Timer/Counter1 |
| 15-17 | `0x001C` to `0x0020` | TIMER0 COMPA, COMPB, OVF | Timer/Counter0 |
| 18 | `0x0022` | SPI, STC | SPI serial transfer complete |
| 19-21 | `0x0024` to `0x0028` | USART, RX / UDRE / TX | USART |
| 22 | `0x002A` | ADC | ADC conversion complete |
| 23 | `0x002C` | EE READY | EEPROM ready |
| 24 | `0x002E` | ANALOG COMP | analog comparator |
| 25 | `0x0030` | TWI | 2-wire serial interface |
| 26 | `0x0032` | SPM READY | store program memory ready |

How to read it:
- Each entry is 2 words long (one `jmp` instruction), so the address of vector number $n$ is $2\cdot(n-1)$. Vector 6: $2\cdot5=10$ = `0x000A`.
- The addresses are **word** addresses of the flash (one word = 2 bytes).
- **Priority = position in the table.** A lower vector number has a higher priority. RESET is the highest, then INT0, then INT1, and so on.

> [!warning] Slide typo
> The table on the slide shows `0x002` for INT0. The address is `0x0002`.

> [!info] Who fills the vector table (beyond the slides)
> You do not write the table yourself. When you write `ISR(INT0_vect) { ... }`, avr-gcc puts a `jmp` to this function at address `0x0002`. Every vector without an ISR gets a jump to a default handler (`__bad_interrupt`), which jumps to address 0. So an enabled interrupt **without** an ISR restarts the program.

> [!question]- Check (slide 3): (a) At which program address is the vector of the ADC interrupt (vector number 22), computed with the formula? (b) The flags of TIMER0 OVF, INT1 and PCINT0 are all set at the same moment and all three are enabled. In which order do the ISRs run?
> (a) Address $=2\cdot(22-1)=42$ = **`0x002A`**. The table gives the same value.
> (b) The lowest vector number wins. INT1 is vector 3, PCINT0 is vector 4, TIMER0 OVF is vector 17.
> Order: **INT1, then PCINT0, then TIMER0 OVF**.

## 3. What the CPU does when an interrupt arrives (slides 4-5)
![[cmpe453_lecture_8_lecture8_p004.jpg]]
![[cmpe453_lecture_8_lecture8_p005.jpg]]

**Notes.** An event (for example an edge on a pin) sets a **flag** bit in a register. The flag means "this interrupt is waiting". The CPU serves it only if the interrupt's own enable bit is 1 and the global I bit in `SREG` is 1.

The two slides together give this full sequence:

| Step | What happens | Who does it |
|---|---|---|
| 1 | The current instruction is finished. | CPU |
| 2 | The address of the **next** instruction (the program counter, PC) is pushed on the stack. | CPU |
| 3 | The global interrupt bit **I** in `SREG` is cleared to 0. No other interrupt can start now. | CPU |
| 4 | The PC is loaded with the address of the vector, for example `0x0002` for INT0. The flag of this interrupt is cleared (true for INT0, INT1 on an edge, the PCINTs and the timers, not for every source). | CPU |
| 5 | The `jmp` in the vector table leads to the ISR. The ISR runs. | your code |
| 6 | The last instruction of the ISR is `RETI` (return from interrupt). | compiler |
| 7 | `RETI` pops the saved address from the stack into the PC and sets **I** back to 1. | CPU |
| 8 | If other interrupts are waiting, they are served in order of priority. If not, the main program continues at the point where it left. | CPU |

Slide 4 does not mention the I bit, slide 5 does not mention the stack. You need both.

> [!info] Details the slides skip (beyond the slides)
> - **The stack.** The stack is a part of the SRAM. The stack pointer `SP` starts at the top of the RAM (`0x08FF` on the ATmega328P) and moves down. The PC of the ATmega328P is stored as 2 bytes, so a push of the PC makes `SP` smaller by 2.
> - **Time.** The response takes at least 4 clock cycles. At 16 MHz one cycle is 62.5 ns, so this is 0.25 µs. Then the `jmp` in the table is executed. `RETI` also takes 4 cycles.
> - **The registers are not saved by the CPU.** Only the PC is saved automatically. `SREG` (the flags Z, C, N, ...) and the working registers are saved and restored by code that avr-gcc adds at the start and end of an `ISR(...)` when the ISR changes them. You do not write it in C.
> - **"Served in order"** on slide 5 means in order of priority (vector number), not in order of arrival.
> - **One instruction of main in between.** After `RETI` the CPU always executes one more instruction of the main program before it serves the next waiting interrupt.
> - **Events during an ISR are not lost.** I is 0, so they cannot start, but their flags are set and wait. Each flag is only one bit: two events of the same source during one long ISR count as one.

> [!example] Trace of one INT0 interrupt
> The main program executes an instruction. The next instruction is at address `0x0124`. `SP` = `0x08FF`, I = 1. Now the button on INT0 is pressed.
> 1. The edge sets the flag `INTF0`.
> 2. The CPU finishes the current instruction.
> 3. It pushes `0x0124` on the stack (2 bytes). `SP` = `0x08FD`.
> 4. It clears I (I = 0) and clears `INTF0`.
> 5. PC = `0x0002`. There is `jmp` to the code of `ISR(INT0_vect)`.
> 6. The ISR runs and ends with `RETI`.
> 7. `RETI` pops `0x0124` into the PC. `SP` = `0x08FF`. I = 1.
> 8. The main program continues at `0x0124`. It does not notice anything, except that time has passed.

> [!warning] Watch out
> Because I is 0 inside an ISR, a long ISR blocks all other interrupts. A UART byte or a timer tick that arrives twice during this time is lost once. This is the main reason to keep ISRs short.

> [!question]- Check (slide 4): Before an interrupt, `SP` = `0x08F0` and the next instruction of main is at `0x0200`. The interrupt is PCINT1. Give the PC and `SP` right after the CPU has entered the vector table, and after `RETI`. Why does the CPU save the address of the **next** instruction and not of the current one?
> PCINT1 is vector 5, address $2\cdot(5-1)=8$ = `0x0008`.
> After the jump to the table: **PC = `0x0008`**, `SP` = `0x08F0` − 2 = **`0x08EE`** (the two bytes of `0x0200` are on the stack).
> After `RETI`: **PC = `0x0200`**, **`SP` = `0x08F0`**.
> The current instruction is finished before the interrupt starts (step 1). If the CPU saved its address, the instruction would run a second time after the return.

> [!question]- Check (slide 5): During the ISR of INT0, a pin-change event (PCINT0) and a TIMER1 overflow happen. Both are enabled. (a) Do their ISRs start at once? (b) What happens after the `RETI` of the INT0 ISR? (c) What would happen without step "I bit of SREG is set back to 1"?
> (a) **No.** The CPU cleared I when it entered the INT0 ISR. The two events only set their flags.
> (b) `RETI` sets I = 1 and returns to main. One instruction of main is executed. Then the waiting interrupt with the lowest vector number runs: PCINT0 (vector 4). After its `RETI` and one more instruction of main, TIMER1 OVF (vector 14) runs. Then main continues normally.
> (c) I would stay 0 after the first interrupt. All interrupts would be disabled for ever, and only one ISR would ever run.

## 4. Kinds of interrupts and the vector for one pin (slides 6-7)
![[cmpe453_lecture_8_lecture8_p006.jpg]]
![[cmpe453_lecture_8_lecture8_p007.jpg]]

**Notes.** Slide 6 gives two kinds:
- **Hardware interrupts**: triggered by events from outside the microcontroller, on the pins INT0, INT1 and the PCINT pins. Example: a push button on PD7 (a PCINT pin).
- **Software interrupts** (the slide's word): triggered by something inside the microcontroller. Example: the transmit interrupt of the USART.

The two register pictures on slide 6 show how every interrupt source is built: one **enable bit** and one **flag bit**.

| USART interrupt | Enable bit in `UCSRnB` | Flag bit in `UCSRnA` | Flag means | avr-libc vector |
|---|---|---|---|---|
| Rx complete | `RXCIEn` (bit 7) | `RXCn` (bit 7) | a received byte waits in the data register | `USART_RX_vect` |
| Tx complete | `TXCIEn` (bit 6) | `TXCn` (bit 6) | the whole frame has been sent | `USART_TX_vect` |
| Data register empty | `UDRIEn` (bit 5) | `UDREn` (bit 5) | the data register can take a new byte | `USART_UDRE_vect` |

On the ATmega328P n = 0: `UCSR0B`, `RXCIE0`, `TXC0`, and so on. The other bits of `UCSRnB` (`RXENn`, `TXENn`, `UCSZn2`, `RXB8n`, `TXB8n`) and of `UCSRnA` (`FEn`, `DORn`, `UPEn`, `U2Xn`, `MPCMn`) do not belong to the interrupts. They are explained in [[CMPE453 L05.2 - Programming the USART]].

Slide 7 connects a pin to its ISR. The button is on **PD7** (physical pin 13 of the chip, D7 on the Uno). PD7 belongs to port D, and all pins of port D share the third pin-change vector: "Pin Change Interrupt Request" at address `0x000A`, name `PCINT2`. So the ISR is:

```c
ISR(PCINT2_vect)
{
  //body of ISR.
}
```

`ISR( )` is a macro from `<avr/interrupt.h>`. The name in the brackets chooses the entry of the vector table. The name is always the source name of the datasheet with `_vect` at the end and `_` for spaces:

| Source | avr-libc name | Source | avr-libc name |
|---|---|---|---|
| INT0 | `INT0_vect` | TIMER0 COMPA | `TIMER0_COMPA_vect` |
| INT1 | `INT1_vect` | TIMER0 OVF | `TIMER0_OVF_vect` |
| PCINT0 | `PCINT0_vect` | SPI, STC | `SPI_STC_vect` |
| PCINT1 | `PCINT1_vect` | USART, RX | `USART_RX_vect` |
| PCINT2 | `PCINT2_vect` | USART, UDRE | `USART_UDRE_vect` |
| WDT | `WDT_vect` | USART, TX | `USART_TX_vect` |
| TIMER2 COMPA | `TIMER2_COMPA_vect` | ADC | `ADC_vect` |
| TIMER2 OVF | `TIMER2_OVF_vect` | EE READY | `EE_READY_vect` |
| TIMER1 CAPT | `TIMER1_CAPT_vect` | ANALOG COMP | `ANALOG_COMP_vect` |
| TIMER1 COMPA | `TIMER1_COMPA_vect` | TWI | `TWI_vect` |
| TIMER1 OVF | `TIMER1_OVF_vect` | SPM READY | `SPM_READY_vect` |

The B versions follow the same rule: `TIMER0_COMPB_vect`, `TIMER1_COMPB_vect`, `TIMER2_COMPB_vect`.

> [!warning] Slide error
> - Slide 6 calls the USART transmit interrupt a "software interrupt". It is triggered by the USART **hardware** inside the chip (the flag `TXCn` is set by the transmitter). A better name is **internal** (peripheral) interrupt, against **external** interrupts from pins. The AVR has no software-interrupt instruction.
> - The table on slide 7 stops at `0x002E`. Two vectors are missing: TWI at `0x0030` and SPM READY at `0x0032`.

> [!warning] Watch out
> - A wrong vector name, for example `ISR(PCINT23_vect)` for the pin PCINT23, does not exist. avr-gcc only gives a warning ("appears to be a misspelled signal handler"). The real vector then has no ISR, and the first interrupt restarts the program.
> - `PCINT2` is the name of a **group** (port D) and also of one **pin** (PCINT2 = PB2). In `PCINT2_vect` it is the group.

> [!question]- Check (slide 6): You want an interrupt when the USART has finished sending a frame. Use the two register pictures: which bit do you set, in which register, and which bit does the hardware set when the event happens? Write the line of C for the ATmega328P and the ISR header.
> Enable bit: `TXCIEn`, bit 6 of `UCSRnB`. Flag: `TXCn`, bit 6 of `UCSRnA`. The hardware sets `TXCn` when the frame is sent.
> On the ATmega328P n = 0:
> `UCSR0B |= (1 << TXCIE0);` sets bit 6, value $2^6=64$ = `0x40`. Then `sei();`.
> ISR header: `ISR(USART_TX_vect)`.
> The ISR runs when `TXC0` = 1 and `TXCIE0` = 1 and I = 1.

> [!question]- Check (slide 7): The button moves from PD7 to PC3 (A3 on the Uno). Which row of the table on the slide is used now, at which address, and how does the ISR header change? What happens if you keep `ISR(PCINT2_vect)` but enable the interrupt for PC3?
> PC3 is on port C. Port C uses the second "Pin Change Interrupt Request" row: PCINT1, address **`0x0008`**.
> Header: **`ISR(PCINT1_vect)`**.
> With the old header, the vector at `0x0008` has no ISR. It holds the jump to the default handler. On the first change of PC3 the CPU jumps there and then to address 0: the program restarts every time the button changes.

## 5. Example with INT0: the task, the pins, the CPU (slides 8-10)
![[cmpe453_lecture_8_lecture8_p008.jpg]]
![[cmpe453_lecture_8_lecture8_p009.jpg]]
![[cmpe453_lecture_8_lecture8_p010.jpg]]

**Notes.** The task (slide 8):
- LED0 blinks with a delay of 200 ms.
- LED1 is turned on when the push button is pressed.
- The push button must be handled with the ISR for INT0.

The main loop is busy with the 200 ms delay, so it cannot watch the button. The button gets its own interrupt.

Slide 9 is the Uno pinout. For interrupts you need these columns of it:

| Function | AVR pin | Uno pin |
|---|---|---|
| INT0 | PD2 | D2 |
| INT1 | PD3 | D3 |
| PCINT0 to PCINT5 | PB0 to PB5 | D8 to D13 |
| PCINT6, PCINT7 | PB6, PB7 | not on the headers (crystal) |
| PCINT8 to PCINT13 | PC0 to PC5 | A0 to A5 |
| PCINT14 | PC6 | RESET |
| PCINT16 to PCINT23 | PD0 to PD7 | D0 to D7 |

So the button of the example must be on **D2**, because INT0 exists only on PD2. The breadboard picture on slide 8 is too small to read the wires. The program turns on the internal pull-up, so the button must connect the pin to GND. Then the pin reads 1 when the button is free and 0 when it is pressed (see [[CMPE453 L04 - Digital input and pull-ups]]).

Slide 10 is the block diagram of the AVR CPU (datasheet figure 6-1). The slide has no text. The blocks that take part in an interrupt:

| Block | Job during an interrupt |
|---|---|
| Interrupt Unit | collects the flags and enable bits, picks the waiting interrupt with the highest priority |
| Status and Control | holds `SREG` with the global I bit |
| Program Counter | is saved, then loaded with the vector address |
| Data SRAM | holds the stack, where the saved PC goes |
| Flash Program Memory | holds the vector table (at the start) and the code of the ISRs |
| I/O Lines, I/O Modules | the sources: pins, timers, USART, ADC, ... |

Most blocks hang on the 8-bit data bus. The I/O modules do not call the CPU directly. They only set flags, and the Interrupt Unit decides.

> [!question]- Check (slide 8): Why is an interrupt a good choice for the button in this task? What would the user see if the button were polled once per turn of the main loop?
> The main loop spends almost all its time in `_delay_ms(200)`. With polling, the button is read only once every 200 ms.
> The user would see LED1 react late (up to 200 ms after the press), and a short tap between two checks would do nothing.
> With INT0 the ISR runs within microseconds, in the middle of the delay. LED0 keeps blinking, because the ISR is very short.

> [!question]- Check (slide 9): Use the pinout. (a) On which Uno pin is INT1? (b) Which PCINT number and which pin-change group does D8 have? (c) And A5? (d) Can you use INT0 for a button on D7?
> (a) INT1 = PD3 = **D3**.
> (b) D8 = PB0 = **PCINT0**, port B, group PCINT0.
> (c) A5 = PC5 = **PCINT13**, port C, group PCINT1.
> (d) **No.** INT0 is fixed to PD2 (D2). D7 = PD7 = PCINT23 can only use the pin-change interrupt of group PCINT2.

> [!question]- Check (slide 10): In the block diagram, where is (a) the vector table stored, (b) the return address stored during an ISR, (c) the global I bit? (d) Which block decides which interrupt runs first?
> (a) In the **Flash Program Memory**, at its lowest addresses (`0x0000` to `0x0033`: 26 vectors of 2 words).
> (b) On the stack, which is in the **Data SRAM**.
> (c) In `SREG`, inside the **Status and Control** block.
> (d) The **Interrupt Unit**.

## 6. The registers: SREG, EIMSK, EICRA (slides 11-13)
![[cmpe453_lecture_8_lecture8_p011.jpg]]
![[cmpe453_lecture_8_lecture8_p012.jpg]]
![[cmpe453_lecture_8_lecture8_p013.jpg]]

**Notes.** Three registers control INT0 and INT1. A fourth one, `EIFR`, holds the flags. It is not on the slides.

**`SREG`, the AVR status register (slide 11).** Bit 7 is **I**, the global interrupt enable.
- I = 0: all interrupts are disabled.
- I = 1: every interrupt whose own enable bit is 1 can run.

The other bits are the arithmetic flags of the CPU: T (bit copy storage), H (half carry), S (sign), V (overflow), N (negative), Z (zero), C (carry). They have nothing to do with interrupts. After reset all bits are 0, so **interrupts are off after reset**.

In C you do not write `SREG` directly. `<avr/interrupt.h>` gives two macros:
- `sei();` sets I to 1 (one machine instruction, `SEI`).
- `cli();` clears I to 0 (instruction `CLI`).

**`EIMSK`, the External Interrupt Mask Register (slide 12).**

| Bit | Name | 0 | 1 |
|---|---|---|---|
| 1 | `INT1` | INT1 disabled | INT1 enabled (if I = 1) |
| 0 | `INT0` | INT0 disabled | INT0 enabled (if I = 1) |

Bits 7 to 2 are not used and read as 0. `EIMSK |= (1 << INT0);` gives `0x01`, both interrupts on is `0x03`.

**`EICRA`, the External Interrupt Control Register A (slide 13).** Two bits per interrupt choose **which signal** on the pin makes the request. `ISC` means "interrupt sense control". `ISC11` and `ISC10` (bits 3, 2) belong to INT1, `ISC01` and `ISC00` (bits 1, 0) belong to INT0.

| `ISCn1` | `ISCn0` | Request is made by | Bits for INT0 | Bits for INT1 |
|---|---|---|---|---|
| 0 | 0 | the low level of the pin | `0x00` | `0x00` |
| 0 | 1 | any logical change (rising and falling edges) | `0x01` | `0x04` |
| 1 | 0 | the falling edge (1 to 0) | `0x02` | `0x08` |
| 1 | 1 | the rising edge (0 to 1) | `0x03` | `0x0C` |

After reset `EICRA` = 0, so the default is "low level".

> [!info] EIFR, the flag register (beyond the slides)
> `EIFR` (External Interrupt Flag Register) has two bits: `INTF1` (bit 1) and `INTF0` (bit 0).
> - The hardware sets `INTF0` when the chosen edge or change happens on the INT0 pin. This also happens when INT0 is disabled in `EIMSK` or when I = 0. The request then waits.
> - The flag is cleared automatically when the ISR starts.
> - You can clear it by hand by writing a **1** to it: `EIFR = (1 << INTF0);`. Do this before you enable an interrupt, if an old event should not count.
> - In low-level mode the flag is always 0. The request exists only as long as the pin is low.

> [!warning] Slide error
> Slide 11 says "1: Interrupts with their respective **flag** bits set to 1 are enabled". It should be **enable** (mask) bits. An interrupt runs when three bits are 1 at the same time: flag AND enable bit AND I. For INT0: `INTF0` AND `INT0` in `EIMSK` AND I.

> [!warning] Watch out
> - **Low level (00) is not an edge.** As long as the pin is low, the ISR starts again and again. Between two runs the main program gets exactly one instruction. With a button held down, main almost stops.
> - `EICRA |= ...` only sets bits. If the register had another mode before, clear the two bits first or write the whole register with `=`.
> - INT0 and INT1 also trigger when PD2 or PD3 is an **output** and the program changes it. This is the way to make a real software interrupt on the AVR.

> [!example] INT0 on the falling edge, INT1 on the rising edge
> INT0 falling: `ISC01` = 1, `ISC00` = 0. INT1 rising: `ISC11` = 1, `ISC10` = 1.
> ```c
> EICRA = (1 << ISC11) | (1 << ISC10) | (1 << ISC01);   /* 0b00001110 = 0x0E */
> EIMSK = (1 << INT1) | (1 << INT0);                    /* 0b00000011 = 0x03 */
> sei();
> ```

> [!question]- Check (slide 11): A program runs `cli();`, then a block of code that takes 3 ms, then `sei();`. INT0 is enabled for the falling edge. The button is pressed once during the 3 ms. What happens? What happens if it is pressed twice during the 3 ms?
> The falling edge sets `INTF0`. I is 0, so the ISR cannot start. The request waits.
> After `sei();` I is 1. The CPU executes one more instruction and then runs `ISR(INT0_vect)`. The press is served late (up to 3 ms), but it is not lost.
> Two presses: the flag is one bit and it is already 1 at the second press. The ISR runs only **once**. One event is lost.

> [!question]- Check (slide 12): `EIMSK` = `0x02`, I = 1, both pins are set to the falling edge. A falling edge comes on PD2 and, a little later, one on PD3. Which ISR runs? What must you write so that both run, and what is the new value of `EIMSK`?
> `0x02` = `0b00000010`: bit 1 (`INT1`) is 1, bit 0 (`INT0`) is 0.
> Only **`ISR(INT1_vect)`** runs, for the edge on PD3. The edge on PD2 sets `INTF0`, but INT0 is masked, so nothing runs.
> `EIMSK |= (1 << INT0);` gives `0b00000011` = **`0x03`**. Because `INTF0` is still set, `ISR(INT0_vect)` then runs at once for the old edge. To avoid that, clear the flag first: `EIFR = (1 << INTF0);`.

> [!question]- Check (slide 13): (a) Which value of `EICRA` makes INT0 react to the rising edge and INT1 to any logical change? (b) Decode `EICRA` = `0x09`. (c) A light barrier on INT0 gives a short low pulse for each object. You want exactly one interrupt per object. Which mode do you choose and which modes are wrong?
> (a) INT0 rising: `ISC01 ISC00` = `11`. INT1 any change: `ISC11 ISC10` = `01`. `EICRA` = `0b00000111` = **`0x07`**.
> (b) `0x09` = `0b00001001`. `ISC11 ISC10` = `10`: INT1 on the **falling edge**. `ISC01 ISC00` = `01`: INT0 on **any logical change**.
> (c) Falling edge (`10`, value `0x02`) or rising edge (`11`, value `0x03`): one edge per pulse, so one interrupt per object.
> "Any change" (`01`) gives two interrupts per object (start and end of the pulse). "Low level" (`00`) repeats the ISR as long as the pulse is low.

## 7. The INT0 program (slide 14)
![[cmpe453_lecture_8_lecture8_p014.jpg]]

**Notes.** The whole program from the slide:

```c
// ------- Preamble -------- //
#include <avr/io.h>
#include <util/delay.h>
#include <avr/interrupt.h>
#include "pinDefines.h"

ISR(INT0_vect)
{
  /* Run every time there is a change on button */
  if (bit_is_clear(BUTTON_PIN, BUTTON))
    LED_PORT |= (1 << LED1);
  else
    LED_PORT &= ~(1 << LED1);
}

void initInterrupt0(void)
{
  EIMSK |= (1 << INT0);   /* enable INT0 */
  EICRA |= (1 << ISC00);  /* trigger when button changes */
  sei();                  /* set (global) interrupt enable bit */
}

int main(void)
{
  // -------- Inits --------- //
  LED_DDR = 0xff;                 /* all LEDs active */
  BUTTON_PORT |= (1 << BUTTON);   /* pullup */
  initInterrupt0();
  // ------ Event loop ------ //
  while (1)
  {
    _delay_ms(200);
    LED_PORT ^= (1 << LED0);
  }                               /* End event loop */
  return 0;                       /* This line is never reached */
}
```

`pinDefines.h` is not shown on the slide. It gives the names. For INT0 the button must be on PD2, so the file must say: `BUTTON` = `PD2`, `BUTTON_PORT` = `PORTD`, `BUTTON_PIN` = `PIND`. The LED names (`LED_DDR`, `LED_PORT`, `LED0`, `LED1`) point to the port of the LEDs. The program is Example 8-1 (`helloInterrupt.c`) of the textbook (Williams, Make: AVR Programming). The book does not print `pinDefines.h`, but its text puts the button on PD2 and the eight LEDs on PB0 to PB7, with LED0 on PB0. So `LED_PORT` = `PORTB`, `LED_DDR` = `DDRB`, `LED0` = `PB0` and `LED1` = `PB1`.

Line by line:
- `#include <avr/interrupt.h>` gives `ISR( )`, `sei()` and `cli()`.
- `LED_DDR = 0xff;` makes all pins of the LED port outputs.
- `BUTTON_PORT |= (1 << BUTTON);` turns on the pull-up of PD2. Free button = 1, pressed = 0.
- `EIMSK |= (1 << INT0);` enables INT0 (`EIMSK` = `0x01`).
- `EICRA |= (1 << ISC00);` sets `ISC01 ISC00` = `01`: any logical change (`EICRA` = `0x01`). So the ISR runs at the press **and** at the release.
- `sei();` sets I. From here on the interrupt is active.
- The ISR reads the pin. `bit_is_clear(BUTTON_PIN, BUTTON)` is true when PD2 is 0 (pressed): LED1 on. If not (released): LED1 off.
- The main loop only blinks LED0. It never mentions the button. Nobody calls the ISR in the code. The hardware calls it.

Notice that the ISR does not use the edge direction. It asks "which level does the pin have now?" and copies it to LED1. This makes the program safe against switch bounce.

> [!info] Switch bounce (beyond the slides)
> The metal contacts of a button bounce. One press gives several fast edges during about 1 to 20 ms before the level is stable.
> - In the slide program, each bounce edge starts the ISR again. Each run sets LED1 from the present level, so after the last bounce LED1 is right. The bounce does no harm.
> - If an ISR **counts** presses or **toggles** an LED on the falling edge, each bounce counts: one press can give 3 or 5 counts.
> - Fixes: hardware (a capacitor of about 100 nF from the pin to GND), or software: in the ISR only set a flag, and in main accept a new press only if about 20 ms have passed since the last one. Do not put `_delay_ms(20)` inside the ISR, because it blocks all other interrupts for 20 ms.

> [!info] Rules for ISRs and shared variables (beyond the slides)
> **1. Keep the ISR short.** No delays, no printing over the UART, no long loops. Typical ISR: read or write one register, set one variable, return. The long work is done in main.
>
> **2. `volatile` for every variable used in both an ISR and main.**
> ```c
> volatile uint8_t buttonPressed = 0;
>
> ISR(INT0_vect) { buttonPressed = 1; }
>
> int main(void) {
>   /* ... init ... */
>   while (1) {
>     if (buttonPressed) {
>       buttonPressed = 0;
>       /* long work here */
>     }
>   }
> }
> ```
> The compiler sees that nothing in `main` changes `buttonPressed`, and that the ISR is never called by the code. Without `volatile` it may read the variable once, keep the value in a CPU register, and never look at the memory again. Then main never sees the 1. `volatile` forces a real read from memory at every use.
>
> **3. Atomic access to shared variables of more than 8 bits.** The AVR is an 8-bit CPU. It reads a `uint16_t` in two steps, one byte each. An interrupt can come between the two steps.
> Example: `volatile uint16_t count` is `0x01FF` (511). Main reads the low byte: `0xFF`. Now the ISR runs and does `count++`, so `count` = `0x0200` (512). Main reads the high byte: `0x02`. Main has `0x02FF` = 767, a value the counter never had.
> Fix: switch the interrupts off for the two reads.
> ```c
> #include <util/atomic.h>
>
> uint16_t copy;
> ATOMIC_BLOCK(ATOMIC_RESTORESTATE) {
>   copy = count;
> }
> ```
> `ATOMIC_BLOCK(ATOMIC_RESTORESTATE)` saves `SREG`, does `cli()`, runs the block and restores `SREG`. The short form is `cli(); copy = count; sei();`, but it always ends with I = 1, also if the interrupts were off before.
> An 8-bit variable is read in one step, so it does not need this for a simple read or write.

> [!warning] Watch out
> - Forgetting `sei()` is the most common mistake: everything is set up and the ISR never runs.
> - The slide enables INT0 in `EIMSK` first and chooses the mode in `EICRA` after that. It works here because I is still 0. The safe order is: choose the mode in `EICRA`, clear the old flag with `EIFR = (1 << INTF0);`, enable in `EIMSK`, then `sei()`.
> - (beyond the slides) An ISR that changes a port with `|=` or `&=` while main changes the **same** port with `^=` is safe on the AVR only when the operation in main is a single instruction. For a single constant bit, as on the slide, avr-gcc makes one single-bit instruction (`sbi`, `cbi`) for `|=` and `&=`. But `LED_PORT ^= (1 << LED0);` in main becomes three instructions: `in` (read the port), `eor` (change the bit), `out` (write the port). If the ISR changes `LED_PORT` between the `in` and the `out` of main, the `out` writes the old value of LED1 back and the change of the ISR is lost. The window is a few clock cycles every 200 ms, so it is rare, but it is a real fault of the slide program.

> [!question]- Check (slide 14, trace): The program runs. LED0 was just toggled, and main is inside `_delay_ms(200)`. Now the button is pressed and held for 1 s, then released. List what happens, with the value of the I bit, and say what the user sees on both LEDs.
> 1. Press: PD2 goes from 1 to 0. This is a logical change, so `INTF0` is set. `INT0` in `EIMSK` is 1 and I is 1.
> 2. The CPU finishes the current instruction of the delay loop, pushes the PC, clears I (I = 0), clears `INTF0` and jumps to `0x0002`, then to the ISR.
> 3. In the ISR `bit_is_clear(BUTTON_PIN, BUTTON)` is true. `LED_PORT |= (1 << LED1);` turns LED1 on.
> 4. `RETI`: PC restored, I = 1. The delay loop continues where it stopped.
> 5. During the next second main toggles LED0 every 200 ms. No ISR runs, because the level does not change.
> 6. Release: PD2 goes from 0 to 1, again a change. The ISR runs a second time. Now the bit is set, so the `else` branch turns LED1 off.
>
> The user sees: LED1 is on exactly while the button is held. LED0 blinks all the time without a visible pause, because each ISR takes only a few microseconds.

> [!question]- Check (slide 14, change): In `initInterrupt0` a student sets the bit `ISC01` in place of `ISC00` (nothing else changes). Which mode is this? What does LED1 do now? And what happens with `EICRA` left at 0?
> `ISC01 ISC00` = `10`: **falling edge** only (`EICRA` = `0x02`).
> The ISR runs only at the press (1 to 0). It reads the pin: low, so LED1 goes on. At the release there is a rising edge, which makes no interrupt, so the `else` branch never runs. **LED1 goes on at the first press and stays on for ever.**
> `EICRA` = 0 is the **low level** mode. While the button is held, the ISR runs again and again with one instruction of main between two runs. LED1 is on, but LED0 almost stops blinking while the button is held. After the release nothing calls the ISR, so LED1 also stays on.
> Only "any logical change" gives the right behaviour for this ISR.

## 8. Pin-change interrupts (slides 15-17)
![[cmpe453_lecture_8_lecture8_p015.jpg]]
![[cmpe453_lecture_8_lecture8_p016.jpg]]
![[cmpe453_lecture_8_lecture8_p017.jpg]]

**Notes.** INT0 and INT1 exist on two pins only. The **pin-change interrupt (PCINT)** works on almost every I/O pin.

| | INT0, INT1 | PCINT |
|---|---|---|
| Pins | PD2, PD3 only | PCINT0 to PCINT23 (not PCINT15) |
| Trigger | low level, any change, falling edge, rising edge (`EICRA`) | **any change** only |
| Enable | `EIMSK` | `PCMSKn` (pins) and `PCICR` (group) |
| Flags | `EIFR`: `INTF1`, `INTF0` | `PCIFR`: `PCIF2`, `PCIF1`, `PCIF0` |
| Vectors | one per pin: `INT0_vect`, `INT1_vect` | one per group: `PCINT0_vect`, `PCINT1_vect`, `PCINT2_vect` |
| Priority | vectors 2, 3 (highest after RESET) | vectors 4, 5, 6 |

The pins are in three groups ("banks" on the slide), one per port:

| Group | Vector for the ISR | Port | Pin numbers | Mask register | Enable bit in `PCICR` | Flag in `PCIFR` | Uno pins |
|---|---|---|---|---|---|---|---|
| PCINT0 (bank 0) | `PCINT0_vect` | B | PCINT0 to PCINT7 = PB0 to PB7 | `PCMSK0` | `PCIE0` | `PCIF0` | D8 to D13 (PB6, PB7: crystal) |
| PCINT1 (bank 1) | `PCINT1_vect` | C | PCINT8 to PCINT14 = PC0 to PC6 | `PCMSK1` | `PCIE1` | `PCIF1` | A0 to A5 (PC6: RESET) |
| PCINT2 (bank 2) | `PCINT2_vect` | D | PCINT16 to PCINT23 = PD0 to PD7 | `PCMSK2` | `PCIE2` | `PCIF2` | D0 to D7 |

Rule for the number: port B pin $k$ is PCINT$k$, port C pin $k$ is PCINT$(8+k)$, port D pin $k$ is PCINT$(16+k)$.

Enabling a pin-change interrupt is a **three-layer** process (slide 17). Think of three switches in series:
1. Set the bit of the pin PCINTx in the mask register `PCMSKn`. This chooses **which pins** of the group may trigger.
2. Set the bit `PCIEn` in `PCICR` for the group. This enables **the group**.
3. Set the I bit in `SREG` with `sei()`. This enables **all interrupts**.

The ISR runs only when all three are 1 and one of the chosen pins changes.

> [!warning] Slide error
> - Slide 15 says the PCINTs support "all 8x3=24 I/O pins". The ATmega328P has **23** PCINT pins. Port C has only PC0 to PC6, so PCINT15 does not exist.
> - Slide 16 says in the text "PC0-PC7 (Bank 1)". The table on the same slide is right: PC0..PC6.
> - On the Uno, PB6 and PB7 are used by the 16 MHz crystal and PC6 is the RESET pin. In practice 20 pins are free: D0 to D13 and A0 to A5.

> [!warning] Watch out
> - One ISR serves the whole group. `ISR(PCINT2_vect)` runs for a change on **any** enabled pin of port D. The ISR must find out which pin it was.
> - The ISR runs on both edges. To react to one edge only, read the pin in the ISR and check the level.
> - If two enabled pins of one group change during the ISR, the flag `PCIFn` is set only once. You see one more interrupt, not two.

> [!info] Finding the pin and the edge in a PCINT ISR (beyond the slides)
> Keep the old state of the port and compare it with the new one. XOR gives a 1 for every bit that changed.
> ```c
> volatile uint8_t oldD = 0xFF;          /* all pull-ups: pins start at 1 */
>
> ISR(PCINT2_vect)
> {
>   uint8_t nowD = PIND;
>   uint8_t changed = nowD ^ oldD;       /* 1 = this pin changed */
>   oldD = nowD;
>
>   if (changed & (1 << PD5)) {          /* was it PD5? */
>     if (!(nowD & (1 << PD5))) {
>       /* PD5 is 0 now: falling edge, button pressed */
>     } else {
>       /* PD5 is 1 now: rising edge, button released */
>     }
>   }
> }
> ```

> [!question]- Check (slide 15): A sensor on A0 gives a high pulse. You need an interrupt only at the **start** of the pulse (rising edge), and INT0 and INT1 are already used. Can a pin-change interrupt do this? How?
> A0 = PC0 = PCINT8. A pin-change interrupt has no edge selection: the ISR runs at the rising edge **and** at the falling edge.
> Solution in software: in `ISR(PCINT1_vect)` read the pin. `if (PINC & (1 << PC0)) { ... }` is true only when the pin is high now, which means the change was a rising edge. At the falling edge the ISR also runs, but the `if` is false and it returns at once.

> [!question]- Check (slide 16): Three buttons are on D9, A3 and D6. For each one give the AVR pin, the PCINT number, the group and the ISR vector name. Which values do `PCMSK0`, `PCMSK1`, `PCMSK2` and `PCICR` need?
> - D9 = PB1 = PCINT1, group PCINT0, `PCINT0_vect`. `PCMSK0` = `1 << 1` = **`0x02`**.
> - A3 = PC3 = PCINT11 ($8+3$), group PCINT1, `PCINT1_vect`. `PCMSK1` = `1 << 3` = **`0x08`**.
> - D6 = PD6 = PCINT22 ($16+6$), group PCINT2, `PCINT2_vect`. `PCMSK2` = `1 << 6` = **`0x40`**.
>
> All three groups are used: `PCICR` = `(1 << PCIE2) | (1 << PCIE1) | (1 << PCIE0)` = `0b00000111` = **`0x07`**.
> Three different ISRs are needed, one per group.

> [!question]- Check (slide 17): A program sets the bit `PCINT4` in `PCMSK0` and then calls `sei();`. The pin PB4 changes, but the ISR never runs. Which layer is missing? Write the missing line. Would the ISR run if instead `sei();` were missing?
> Layer 1 (pin mask) and layer 3 (global I) are there. Layer 2 is missing: the group enable for port B.
> Missing line: `PCICR |= (1 << PCIE0);` (value `0x01`).
> Without `sei();` the ISR would not run either. The three layers work like an AND: mask bit AND `PCIEn` AND I must all be 1.

## 9. Example 1: enable PCINT10 on PC2 (slides 18-20)
![[cmpe453_lecture_8_lecture8_p018.jpg]]
![[cmpe453_lecture_8_lecture8_p019.jpg]]
![[cmpe453_lecture_8_lecture8_p020.jpg]]

**Notes.** Task: write the C code that enables PCINT10. PCINT10 is PC2 (A2 on the Uno), port C, so bank 1.

**Step 1 (slide 18): the mask register.** The slide shows all three mask registers:

| Register | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
|---|---|---|---|---|---|---|---|---|
| `PCMSK0` (port B) | PCINT7 | PCINT6 | PCINT5 | PCINT4 | PCINT3 | PCINT2 | PCINT1 | PCINT0 |
| `PCMSK1` (port C) | not used | PCINT14 | PCINT13 | PCINT12 | PCINT11 | PCINT10 | PCINT9 | PCINT8 |
| `PCMSK2` (port D) | PCINT23 | PCINT22 | PCINT21 | PCINT20 | PCINT19 | PCINT18 | PCINT17 | PCINT16 |

A 1 in a bit means: a change on this pin may make the interrupt request of its group. PCINT10 is bit 2 of `PCMSK1`:

```c
PCMSK1 |= (1 << PCINT10);
```
or
```c
PCMSK1 |= (1 << PC2);
```

Both lines are the same. In avr-libc `PCINT10` is the bit position 2 and `PC2` is also 2. The bit position in the mask register is always the pin number inside the port. Result: `PCMSK1` = `0b00000100` = `0x04`.

**Step 2 (slide 19): the group enable.** `PCICR` is the Pin Change Interrupt Control Register.

| Bit | Name | 1 means |
|---|---|---|
| 2 | `PCIE2` | group PCINT2 (port D, pins chosen in `PCMSK2`) enabled |
| 1 | `PCIE1` | group PCINT1 (port C, pins chosen in `PCMSK1`) enabled |
| 0 | `PCIE0` | group PCINT0 (port B, pins chosen in `PCMSK0`) enabled |

Bits 7 to 3 are not used. Port C is bank 1:

```c
PCICR |= (1 << PCIE1);
```

Result: `PCICR` = `0b00000010` = `0x02`.

**Step 3 (slide 20): the global enable.**

```c
sei();
```

Now a change on PC2 starts `ISR(PCINT1_vect)`.

> [!warning] Slide error
> Slide 20 gives `SREG |= (1 << I);` as a second way. This line does **not** compile with avr-gcc: avr-libc has no name `I` (error: `'I' undeclared`). The name of the bit is `SREG_I` (value 7), so the working form is `SREG |= (1 << SREG_I);`. Use `sei();`. It is one instruction and it is the normal way.

> [!info] PCIFR, the flag register (beyond the slides)
> `PCIFR` (Pin Change Interrupt Flag Register) has three bits: `PCIF2` (bit 2), `PCIF1` (bit 1), `PCIF0` (bit 0).
> A change on an enabled pin of group n sets `PCIFn`. The flag is cleared automatically when the ISR starts, or by writing a 1 to it: `PCIFR = (1 << PCIF1);`.
> Full chain for PC2: pin change AND bit `PCINT10` in `PCMSK1` sets `PCIF1`. Then `PCIF1` AND `PCIE1` AND I starts `ISR(PCINT1_vect)`.

> [!question]- Check (slide 18): (a) Why do `(1 << PCINT10)` and `(1 << PC2)` give the same result? (b) Which single value of `PCMSK1` enables the pins A0 and A4, and which line of C sets it? (c) Why is bit 7 of `PCMSK1` marked with "-"?
> (a) Both names are defined as the number 2. PCINT10 is the pin PC2, and the mask bit has the same position as the pin in its port. So both give `1 << 2` = `0x04`.
> (b) A0 = PC0 = PCINT8 (bit 0). A4 = PC4 = PCINT12 (bit 4). Value: `0b00010001` = **`0x11`**.
> `PCMSK1 |= (1 << PCINT12) | (1 << PCINT8);`
> (c) Bit 7 would be PCINT15 = PC7. The ATmega328P has no pin PC7, so the bit does not exist and reads as 0.

> [!question]- Check (slides 19-20): A student wants an interrupt on PC2 and writes the three lines below. Find the two mistakes. Which values do `PCMSK1` and `PCICR` have after the corrected code, if both were 0 before?
> ```c
> PCMSK1 |= (1 << PCINT10);
> PCICR  |= (1 << PCIE2);
> SREG   |= (1 << I);
> ```
> Mistake 1: `PCIE2` enables the group of port **D**. PC2 is on port C, bank 1, so it must be `PCICR |= (1 << PCIE1);`. With `PCIE2` the ISR never runs, because `PCIE1` is 0.
> Mistake 2: `I` is not defined in avr-libc, so the line does not compile. Write `sei();` (or `SREG |= (1 << SREG_I);`).
> After the corrected code: `PCMSK1` = `0b00000100` = **`0x04`**, `PCICR` = `0b00000010` = **`0x02`**, and bit 7 of `SREG` is 1.

## 10. Example 2: enable PCINT21 on PD5 (slides 21-23)
![[cmpe453_lecture_8_lecture8_p021.jpg]]
![[cmpe453_lecture_8_lecture8_p022.jpg]]
![[cmpe453_lecture_8_lecture8_p023.jpg]]

**Notes.** The same three steps for another pin. PCINT21 is PD5 (D5 on the Uno), port D, so bank 2.

```c
PCMSK2 |= (1 << PCINT21);   /* or (1 << PD5): bit 5, PCMSK2 = 0x20 */
PCICR  |= (1 << PCIE2);     /* bank 2,           PCICR  = 0x04 */
sei();                      /* I = 1 */
```

Check of the number: port D pin 5 gives PCINT$(16+5)$ = PCINT21. The bit position in `PCMSK2` is 5, the same as in `PD5`.

Now a change on PD5 starts `ISR(PCINT2_vect)`.

> [!warning] Slide error
> - Slides 22 and 23 still say "Write C-code to enable PCINT10". They belong to example 2 and mean **PCINT21**. The code on slide 22 (`PCIE2`, port D: bank 2) is right for PCINT21.
> - Slide 23 repeats `SREG |= (1 << I);`, which does not compile (see section 9).

> [!example] A complete program for the button on PD5
> A button between D5 and GND. LED on PB0. Each press toggles the LED. The slides give only the three enable lines. The ISR and the rest are added here.
> ```c
> #include <avr/io.h>
> #include <avr/interrupt.h>
>
> volatile uint8_t pressed = 0;       /* shared with main: volatile */
>
> ISR(PCINT2_vect)
> {
>   if (bit_is_clear(PIND, PD5))      /* low now: it was a press */
>     pressed = 1;                    /* short ISR: only set a flag */
> }
>
> int main(void)
> {
>   DDRB  |= (1 << PB0);              /* LED pin is an output */
>   PORTD |= (1 << PD5);              /* pull-up on the button pin */
>
>   PCMSK2 |= (1 << PCINT21);         /* layer 1: pin PD5 */
>   PCIFR   = (1 << PCIF2);           /* clear an old flag */
>   PCICR  |= (1 << PCIE2);           /* layer 2: group of port D */
>   sei();                            /* layer 3: global enable */
>
>   while (1) {
>     if (pressed) {
>       pressed = 0;
>       PORTB ^= (1 << PB0);          /* toggle the LED */
>     }
>   }
> }
> ```
> The ISR runs at the press and at the release. The `if` keeps only the press. Bounce can still give more than one press (see section 7).

> [!question]- Check (slides 21-23): You add a second button on PD7 (D7) to example 2. (a) Which registers change and to which values? (b) Do you need a second ISR? (c) Both buttons are free. Then the button on PD7 is pressed. How does the ISR know that it was not PD5?
> (a) PD7 = PCINT23, bit 7 of `PCMSK2`. `PCMSK2` = `(1 << PCINT23) | (1 << PCINT21)` = `0b10100000` = **`0xA0`**. `PCICR` stays `0x04`, because both pins are in bank 2.
> (b) **No.** Both pins are on port D, and port D has only one vector. The same `ISR(PCINT2_vect)` runs for both.
> (c) The ISR reads `PIND` and compares it with the value from the last time: `changed = PIND ^ oldD`. Before the press bits 7 and 5 were 1. Now bit 7 is 0 and bit 5 is still 1. So `changed` has bit 7 set (`0x80`) and bit 5 clear: the change was on PD7.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Interrupt, ISR | a device asks for service, the CPU stops main and runs the interrupt service routine |
| Polling | the main loop asks the device again and again |
| Vector table | fixed entries at the start of the flash, each a jump to an ISR. Address of vector $n$: $2\cdot(n-1)$ |
| Priority | lower vector number wins: RESET, INT0, INT1, PCINT0, PCINT1, PCINT2, WDT, timers, SPI, USART, ADC, ... |
| CPU sequence | finish instruction, push PC, I = 0, jump to vector, ISR, `RETI` pops PC and sets I = 1 |
| Condition to run | flag AND own enable bit AND I bit |
| `SREG` bit 7 (I) | global enable. `sei()` sets it, `cli()` clears it. 0 after reset |
| INT0, INT1 | PD2 = D2, PD3 = D3. Vectors `INT0_vect`, `INT1_vect` |
| `EICRA` | `ISC11 ISC10` (INT1), `ISC01 ISC00` (INT0): 00 low level, 01 any change, 10 falling edge, 11 rising edge |
| `EIMSK` | `INT1` (bit 1), `INT0` (bit 0): enable |
| `EIFR` | `INTF1` (bit 1), `INTF0` (bit 0): flags, cleared by the ISR start or by writing 1 |
| PCINT groups | port B = PCINT0 to 7, port C = PCINT8 to 14, port D = PCINT16 to 23. Any change only |
| `PCMSK0`, `PCMSK1`, `PCMSK2` | choose the pins of port B, C, D. Bit position = pin number in the port |
| `PCICR` | `PCIE2`, `PCIE1`, `PCIE0` (bits 2, 1, 0): enable the group |
| `PCIFR` | `PCIF2`, `PCIF1`, `PCIF0` (bits 2, 1, 0): flags |
| PCINT vectors | `PCINT0_vect`, `PCINT1_vect`, `PCINT2_vect`: one ISR per port |
| `ISR(name_vect)` | macro from `<avr/interrupt.h>` that defines an ISR and puts it in the vector table |
| `volatile` | needed for every variable shared between an ISR and main |
| Atomic access | read or write a shared 16-bit variable with interrupts off (`ATOMIC_BLOCK` or `cli()` / `sei()`) |
| Bounce | one press gives several edges for some ms. Count presses only with debouncing |

## Check yourself
> [!question]- 1. Write the complete setup for this job: a sensor on D3 gives a rising edge, and `ISR(INT1_vect)` must run once per edge. Give the value of each register.
> D3 = PD3 = INT1.
> Mode: rising edge, `ISC11 ISC10` = `11`. `EICRA = (1 << ISC11) | (1 << ISC10);` = `0b00001100` = **`0x0C`**.
> Clear an old flag: `EIFR = (1 << INTF1);` (writes `0x02`).
> Enable: `EIMSK = (1 << INT1);` = **`0x02`**.
> Global: `sei();`.

> [!question]- 2. In main there is `while (!done) { }` and the ISR sets `done = 1;`. The variable is declared as `uint8_t done = 0;`. With optimisation the program hangs in the loop for ever, although the ISR runs. Why, and what is the fix?
> The compiler sees that the loop body does not change `done`, and it does not know that an ISR can change it. So it reads `done` once before the loop. The value is 0, and the loop becomes an endless loop that never reads the memory again.
> Fix: `volatile uint8_t done = 0;`. Then every test of `done` is a real read from memory, and main sees the 1 from the ISR.

> [!question]- 3. `volatile uint16_t ticks` is increased in a timer ISR. Main does `if (ticks == 0x0300) ...` without any protection. Show with numbers how main can read a value that never existed. Give the fix.
> The CPU reads the 16 bits in two steps. Let `ticks` = `0x02FF` (767).
> Main reads the low byte: `0xFF`. Now the ISR runs: `ticks` becomes `0x0300` (768). Main reads the high byte: `0x03`.
> Main has `0x03FF` = 1023. The counter went from 767 to 768 and was never 1023.
> Fix: copy with interrupts off, then use the copy.
> `ATOMIC_BLOCK(ATOMIC_RESTORESTATE) { copy = ticks; }` and then `if (copy == 0x0300) ...`.

> [!question]- 4. An ISR for INT0 (falling edge) does `count++` to count button presses. After 10 presses `count` is 34. What is the reason? Name two fixes. Why is `_delay_ms(20)` inside the ISR a bad fix?
> The contacts **bounce**: one press makes several falling edges within a few ms, and each one is counted.
> Fix 1 (hardware): a capacitor of about 100 nF from the pin to GND, so the pin cannot change fast.
> Fix 2 (software): accept a new press only if about 20 ms have passed since the last accepted one, for example with a timer tick counter that main checks.
> A delay inside the ISR keeps I = 0 for 20 ms. All other interrupts wait that long, and events that come twice in this time are lost. An ISR must stay short.

> [!question]- 5. `EIMSK` = `0x03`, `EICRA` = `0x0A`, `PCICR` = `0x01`, `PCMSK0` = `0x01`, I = 1. Decode all four values. Then: PD2 falls, PD3 falls and PB0 changes within the same instruction. In which order do the ISRs run?
> `EIMSK` = `0b00000011`: INT1 and INT0 are both enabled.
> `EICRA` = `0b00001010`: `ISC11 ISC10` = `10` and `ISC01 ISC00` = `10`. Both react to the **falling edge**.
> `PCICR` = `0b00000001`: `PCIE0` = 1, the group of port B is enabled.
> `PCMSK0` = `0b00000001`: only PCINT0 = PB0 (D8) may trigger.
> All three flags are set: `INTF0`, `INTF1`, `PCIF0`. Lowest vector number first: **`INT0_vect`** (vector 2), then **`INT1_vect`** (vector 3), then **`PCINT0_vect`** (vector 4). Between two ISRs one instruction of main runs.

> [!question]- 6. A program enables the ADC interrupt and calls `sei()`, but contains no `ISR(ADC_vect)`. What happens at the end of the first conversion? And what happens if the ISR is there but is written as `ISR(ADC_Vect)`?
> The ADC flag is set, its enable bit and I are 1, so the CPU jumps to the ADC vector at `0x002A`. Without an ISR this vector holds a jump to the default handler, which jumps to address 0. The program **starts again from the beginning**, at every finished conversion.
> `ADC_Vect` is not a name that avr-libc knows (C names are case sensitive). avr-gcc gives only a warning about a misspelled signal handler. The function is not put at `0x002A`, so the result is the same restart.

## Links
- Course index: [[CMPE453 Home]]
- Previous: [[CMPE453 L07 - ADC]]
- Pull-ups and buttons: [[CMPE453 L04 - Digital input and pull-ups]]
- The USART registers `UCSR0A`, `UCSR0B`: [[CMPE453 L05.2 - Programming the USART]]
- Original slides (PDF): [Lecture8.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_8/Lecture8.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_8/Lecture8.pdf`

Slides not embedded: 1 (title).
