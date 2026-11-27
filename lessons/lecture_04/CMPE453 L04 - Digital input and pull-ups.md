---
course: CMPE 453 Embedded Systems
lecture: 4
title: "Digital input: push-buttons and pull-ups"
tags: [cmpe453, lecture]
slides: lectures/lecture_04/Lecture4.pdf
full_notes: lecture_notes/lecture_04/CMPE453_Lecture_04_Digital_Input.pdf
---
# CMPE 453 · Lecture 4 · Digital input: push-buttons and pull-ups

> [!summary] In short
> - A button to GND only defines "pressed" (0 V). Released, the pin floats. A pull-up to Vcc makes released = 1, so the button is active low.
> - Released pin voltage: $V_{in}=V_{cc}R_2/(R_1+R_2)$. Keep the pull-up $R_1$ small against the pin impedance $R_2$ (about 10 kΩ).
> - Input with internal pull-up: `DDRx &= ~(1<<n); PORTx |= (1<<n);`. Read `PINx`.
> - Pressed test: `if (!(PINx & (1<<n)))` or `bit_is_clear(PINx, n)`.
> - "Toggle once per press" needs edge detection: now pressed and before not pressed.

## 1. A button needs a pull-up (slides 2-3)
![[cmpe453_lecture_04_lecture4_p002.jpg]]
![[cmpe453_lecture_04_lecture4_p003.jpg]]

**Notes.** A button between the pin and ground:
- **pressed**: the pin is connected to GND, 0 V, logic 0.
- **not pressed**: the pin is connected to nothing. It is **floating**: noise decides the voltage and `PINx` may read 0 or 1 at random.

A **pull-up resistor** connects the pin to Vcc. Button open: the pin sits high (logic 1). Button pressed: the button connects the pin to GND and wins (logic 0). So a button with a pull-up is **active low**: pressed = 0, released = 1. Use a large resistor, 10 kΩ to 100 kΩ, so the current while pressed ($5\,\text{V}/R_1$) stays small.

> [!info] Why a "relatively large" resistor (beyond the slides)
> While the button is pressed, the pull-up sits between Vcc (5 V) and GND (0 V). The full 5 V lies across it, so Ohm's law gives the current $I=V/R_1$.
> - $R_1=10$ kΩ: $I=5/10\,000=0.0005$ A $=0.5$ mA.
> - $R_1=100$ Ω: $I=5/100=0.05$ A $=50$ mA, a hundred times more.
>
> This current does nothing useful. It only makes heat for as long as the button is held. A large resistor keeps it small.

> [!question]- Check (slide 2): A student says: "Released, the pin is not connected to GND, so it must read 1." What is wrong with this?
> Released, the pin is not connected to Vcc either. It is connected to nothing.
> An input pin draws almost no current, so small stray charges and noise decide its voltage. `PINx` may read 0 or 1 at random.
> A pin reads 1 for sure only if something connects it to a high voltage. That is the job of the pull-up on the next slide.

> [!question]- Check (slide 3): Vcc = 5 V. Compute the current through the pull-up while the button is pressed for (a) 47 kΩ and (b) 220 Ω. Which one fits the slide's advice?
> Pressed, the full 5 V lies across the resistor: $I=V/R_1$.
> (a) $I=5/47\,000=0.000106$ A $\approx 0.11$ mA.
> (b) $I=5/220=0.0227$ A $\approx 22.7$ mA.
> 47 kΩ fits: it is inside the slide's range of 10 kΩ to 100 kΩ. 220 Ω still gives a clean 0 and 1, but it wastes about 200 times more current while the button is held.

## 2. How large may the pull-up be (slide 4)
![[cmpe453_lecture_04_lecture4_p004.jpg]]

**Notes.** With the button open, the pull-up $R_1$ and the input impedance of the pin $R_2$ form a voltage divider:

$$V_{in}=\frac{V_{cc}}{R_1+R_2}\,R_2$$

The slide says $R_2$ can vary from 100 kΩ to 1 MΩ. With $R_1=R_2=1$ MΩ, $V_{in}=V_{cc}/2=2.5$ V: the chip may read a high or a low.

ATmega328P thresholds (datasheet, not on the slides): at most $V_{IL}=0.3\,V_{cc}$ (1.5 V) is read as low, at least $V_{IH}=0.6\,V_{cc}$ (3.0 V) is read as high. In between nothing is promised.

> [!example] Pin impedance 500 kΩ, Vcc = 5 V
> - $R_1=500$ kΩ: $V_{in}=5\cdot500/1000=2.5$ V, undefined.
> - $R_1=22$ kΩ: $V_{in}=5\cdot500/522=4.79$ V, reads 1.

> [!warning] Watch out
> "10 k to 100 kΩ should avoid most problems": with the slide's lowest pin impedance (100 kΩ), a 100 kΩ pull-up gives exactly half of Vcc. Stay near 10 kΩ. Smaller is not always better: the pressed current grows.

> [!info] The largest safe pull-up (beyond the slides)
> The released pin must reach at least $V_{IH}=0.6\,V_{cc}$. Put this into the divider and solve for $R_1$:
>
> $$\frac{V_{cc}\,R_2}{R_1+R_2}\ge 0.6\,V_{cc}$$
>
> 1. Divide both sides by $V_{cc}$: $R_2/(R_1+R_2)\ge 0.6$.
> 2. Multiply both sides by $(R_1+R_2)$: $R_2\ge 0.6R_1+0.6R_2$.
> 3. Subtract $0.6R_2$: $0.4R_2\ge 0.6R_1$.
> 4. Divide by 0.6: $R_1\le \tfrac{0.4}{0.6}R_2=\tfrac{2}{3}R_2$.
>
> For the slide's lowest pin impedance, $R_2=100$ kΩ, this gives $R_1\le 66.7$ kΩ. Check: $5\cdot100/166.7=3.0$ V, exactly $V_{IH}$.

> [!question]- Check (slide 4): Vcc = 5 V and the pin impedance is $R_2=200$ kΩ. Compute the released pin voltage for (a) $R_1=47$ kΩ and (b) $R_1=330$ kΩ. What does the pin read in each case?
> Formula: $V_{in}=V_{cc}R_2/(R_1+R_2)$. Thresholds at 5 V: low up to $0.3\cdot5=1.5$ V, high from $0.6\cdot5=3.0$ V.
> (a) $V_{in}=5\cdot200/(47+200)=1000/247=4.05$ V. This is above 3.0 V: reads 1.
> (b) $V_{in}=5\cdot200/(330+200)=1000/530=1.89$ V. This is between 1.5 V and 3.0 V: undefined, may read 0 or 1.
> The safe limit is $R_1\le\tfrac23\cdot200=133$ kΩ. 47 kΩ is inside, 330 kΩ is not.

## 3. The built-in pull-ups (slides 5-6)
![[cmpe453_lecture_04_lecture4_p005.jpg]]
![[cmpe453_lecture_04_lecture4_p006.jpg]]

**Notes.** The AVR has built-in pull-up resistors on all pins. No external resistors are needed, just activate them for input pins.

- `DDRx`: 1 = output, **0 = input (default)**.
- `PORTx`, for a pin configured as input: 0 = pull-up disabled (default), **1 = pull-up enabled**.
- `PINx`: contains the input logic values.

```c
DDRD &= ~(1 << PD2);   //makes double-sure we are in input mode
PORTD |= (1 << PD2);   //enables pull-up resistor
```

| `DDxn` | `PORTxn` | The pin |
|---|---|---|
| 0 | 0 | input, floating |
| 0 | 1 | input with pull-up |
| 1 | 0 | output, drives low |
| 1 | 1 | output, drives high |

> [!warning] Watch out
> - The slide's figure labels the internal pull-up "10 kΩ". The ATmega328P datasheet gives 20 to 50 kΩ.
> - `PORTx = 0xFF` means "pull-ups on" only for pins whose `DDRx` bit is 0. On output pins it drives them high.
> - Read the pin from `PINx`. `PORTx` gives back the pull-up setting you wrote.

> [!question]- Check (slide 5): Take the datasheet's largest internal pull-up, $R_1=50$ kΩ, and a pin impedance of $R_2=200$ kΩ at Vcc = 5 V. Is the released level a clean high? How much current flows while the button is pressed?
> Released: $V_{in}=V_{cc}R_2/(R_1+R_2)=5\cdot200/(50+200)=1000/250=4.0$ V. This is above $V_{IH}=3.0$ V, so the pin reads 1.
> Pressed: the full 5 V lies across the pull-up, $I=5/50\,000=0.0001$ A $=0.1$ mA.
> So the built-in pull-up gives a safe high and a very small pressed current. No external resistor is needed.

> [!question]- Check (slide 6): Before: `DDRB` = `0x2F`, `PORTB` = `0x21`. Then the slide's two lines run, written for PB3 instead of PD2. What is in both registers afterwards, and what is PB3 (Uno pin D11) now?
> The two lines are `DDRB &= ~(1 << PB3);` and `PORTB |= (1 << PB3);`.
> Mask: `1 << PB3` = `0000 1000`. Inverted: `1111 0111`.
> `DDRB`: `0010 1111 & 1111 0111` = `0010 0111` = **`0x27`**. Only bit 3 is cleared.
> `PORTB`: `0010 0001 | 0000 1000` = `0010 1001` = **`0x29`**. Only bit 3 is set.
> PB3 now has `DDB3` = 0 and `PORTB3` = 1: input with pull-up. Before, it was an output that drove low (1, 0). All other pins are unchanged.

## 4. Reading a port and testing one bit (slides 7-8)
![[cmpe453_lecture_04_lecture4_p007.jpg]]
![[cmpe453_lecture_04_lecture4_p008.jpg]]

**Notes.** Example 1: configure port B as input, enable its pull-ups, read the value.

```c
DDRB = 0x00;   //just to be sure
PORTB = 0xFF;  //enable pull-up(s)
tmp = PINB;    // 11111111 when nothing is pressed
```

Grounding PB2 with a push button gives `PINB` = `0b11111011` = `0xFB`. A pressed button shows as a single 0.

**Testing bit three** (slide 8): make a mask with a 1 in bit three, `(1 << 3)` = `00001000`, and AND it with `PIND`:

```
PIND :      xxxxxxxx
(1 << 3) :  00001000
& :         0000x000
```

```c
if (PIND & (1<<3)){
  doStuff();
}
```

If the bit is 0 the result is eight zeros (false). If the result is not zero the bit was set (true).

> [!warning] Watch out
> - With a pull-up button "set" means **released**. To act on a press test for a clear bit: `if (!(PIND & (1 << 3)))`.
> - The result is `1<<n`, not 1. `if ((PIND & (1<<5)) == 1)` is never true.
> - `PIND && (1<<5)` is a logical AND. It is 1 for any non-zero `PIND` and does not look at bit 5.

> [!question]- Check (slide 7): Port B is set up as on the slide (`DDRB = 0x00; PORTB = 0xFF;`). Then `tmp = PINB;` gives `0xED`. Which buttons are pressed?
> `0xED` = `1110 1101`. A pressed button shows as a 0.
> The zeros are in bit 4 and bit 1 (count from the right, starting at 0).
> So the buttons on **PB4** and **PB1** are pressed. All other pins read 1 through their pull-ups.

> [!question]- Check (slide 8): Find the bug: `if (PIND & (1<<3) == 0) { doStuff(); }` should run `doStuff()` when bit three is clear, but it never runs. Why? Fix it.
> In C, `==` binds tighter than `&`. So the compiler reads it as `PIND & ((1<<3) == 0)`.
> `(1<<3) == 0` is `8 == 0`, which is 0. Then `PIND & 0` is 0 for every value of `PIND`: always false.
> Fix: put the mask test in its own brackets, `if ((PIND & (1<<3)) == 0)`, or write `if (!(PIND & (1<<3)))`.

## 5. Helpful macros (slide 9)
![[cmpe453_lecture_04_lecture4_p009.jpg]]

**Notes.** Included with `<avr/io.h>`:

| Macro | Meaning |
|---|---|
| `_BV(bit)` | `(1 << (bit))`, a bit number as a byte value |
| `bit_is_set(sfr, bit)` | `sfr & _BV(bit)`: non-zero (`1<<bit`) if the bit is set, 0 if clear |
| `bit_is_clear(sfr, bit)` | `!(sfr & _BV(bit))`: 1 if the bit is clear, 0 if set |
| `loop_until_bit_is_set(sfr, bit)` | `do { } while (bit_is_clear(sfr, bit))` |
| `loop_until_bit_is_clear(sfr, bit)` | `do { } while (bit_is_set(sfr, bit))` |

For a pull-up button, `loop_until_bit_is_clear` waits for a press and `loop_until_bit_is_set` waits for a release.

> [!warning] Slide errors
> - The comment of `loop_until_bit_is_clear` says "Wait until bit ... is set". Correct is "is **clear**".
> - The slide writes the defines with a colon, `#define _BV(bit): (1 << (bit))`. A real `#define` has no colon. You never type these lines: `<avr/io.h>` defines them.

> [!question]- Check (slide 9): A pull-up button is on PD2 and an LED on PB5. Inside `while(1)` there are three lines: `loop_until_bit_is_clear(PIND, PD2); PORTB ^= _BV(PB5); loop_until_bit_is_set(PIND, PD2);`. What does each line do, and what does the user see?
> Line 1 waits while bit 2 of `PIND` is set, so it waits until the button is **pressed** (bit clear).
> Line 2: `_BV(PB5)` = `1 << 5` = `0010 0000` = `0x20`. The XOR flips bit 5 of `PORTB`: the LED toggles.
> Line 3 waits while the bit is clear, so it waits until the button is **released** (bit set).
> Result: the LED toggles once per press, however long the button is held (ignoring contact bounce).

## 6. Example 2: 0xC3 while pressed (slides 10-12)
![[cmpe453_lecture_04_lecture4_p010.jpg]]
![[cmpe453_lecture_04_lecture4_p011.jpg]]
![[cmpe453_lecture_04_lecture4_p012.jpg]]

**Notes.** Button on PB0, LEDs on PD0 to PD7. Show `0xC3` when the button is pressed, `0x3C` when released.

```c
#include <avr/io.h>
int main(void)
{
  PORTB |= (1 << PB0);      /* pull-up on our input pin PB0 */
  DDRD = 0xff;              /* all LEDs output */
  while (1)
  {
    if (!(PINB & (1<<PB0))) /* pressed */
      PORTD = 0b11000011;
    else                    /* not pressed */
      PORTD = 0b00111100;
  }
  return 0;
}
```

Slide 12 is the same program with the test written as `if (bit_is_clear(PINB, PB0))`.

> [!warning] Watch out
> - Testing `PORTB` instead of `PINB` gives `0x3C` always: `PORTB` bit 0 is the pull-up setting, always 1.
> - Without the pull-up line, PB0 floats and the released pattern is random.
> - `DDRD = 0xff` also drives PD0 and PD1, the Uno's RX/TX. Unplug LEDs 0 and 1 if an upload fails.

> [!question]- Check (slide 10): Each LED is lit when its pin is high. Which Port D pins have a lit LED for `0xC3`, and which for `0x3C`? How are the two patterns related?
> `0xC3` = `1100 0011`: bits 7, 6, 1, 0 are 1, so the LEDs on **PD7, PD6, PD1, PD0** are lit (the two outer pairs).
> `0x3C` = `0011 1100`: bits 5, 4, 3, 2 are 1, so the LEDs on **PD5, PD4, PD3, PD2** are lit (the four in the middle).
> Each pattern is the inverse of the other: `0xC3 ^ 0xFF` = `0x3C`. Every LED changes when the button changes.

> [!question]- Check (slide 11): At one moment `PINB` reads `0xA6`. Which branch of the program runs, and what is written to `PORTD`? Why do the other seven bits of `PINB` not matter?
> `0xA6` = `1010 0110`. Mask: `1 << PB0` = `0000 0001`.
> `PINB & (1<<PB0)` = `1010 0110 & 0000 0001` = `0000 0000` = 0. Then `!0` = 1: true.
> The "pressed" branch runs: `PORTD = 0b11000011` = `0xC3`.
> PB1 to PB7 have no pull-up, so they float and read at random. The mask has a 0 in their columns, so the AND forces them to 0. Only bit 0 reaches the test.

> [!question]- Check (slide 12): Write the same test with `bit_is_set(PINB, PB0)` instead of `bit_is_clear`, so that the LEDs still show `0xC3` when pressed and `0x3C` when released.
> `bit_is_set(PINB, PB0)` is non-zero when the bit is 1, and that means **released** for a pull-up button. So the two branches change places:
> ```c
> if (bit_is_set(PINB, PB0))  /* not pressed */
>   PORTD = 0b00111100;
> else                        /* pressed */
>   PORTD = 0b11000011;
> ```
> Another way: keep the branches and negate the test, `if (!bit_is_set(PINB, PB0))`. This is exactly what `bit_is_clear` expands to.

## 7. Example 3: toggle once per press (slides 13-15)
![[cmpe453_lecture_04_lecture4_p013.jpg]]
![[cmpe453_lecture_04_lecture4_p014.jpg]]
![[cmpe453_lecture_04_lecture4_p015.jpg]]

**Notes.** The naive loop `if (button is pressed) Toggle LEDs` does not work: while the button is held, every pass of the loop toggles again (the slide says about 100,000 times per second). The LEDs look half-bright and stop in a random pattern.

Fix: toggle only on a **press edge**: now pressed **and** before not pressed. Then copy the current state into the old state.

```c
#include <avr/io.h>
#include <stdbool.h>
int main(void)
{
  PORTB |= (1 << PB0);
  DDRD = 0xff;
  bool old_state = false;
  bool current_state;
  PORTD = 0b00111100;
  while(1)
  {
    current_state = bit_is_clear(PINB, PB0);
    if(current_state && !old_state)
      PORTD ^= 0xFF;
    old_state = current_state;
  }
  return 0;
}
```

> [!warning] Watch out
> - The slide uses `bool` without `#include <stdbool.h>`. With the lab's `-std=gnu11`, `avr-gcc` says `unknown type name 'bool'`. Add the include (as above), or use `uint8_t`.
> - Real buttons **bounce** for a few milliseconds. The loop sees every bounce as a new edge, so one press may toggle several times. The fix is debouncing: accept a change only after it has been stable for about 20 ms. The slides do not cover it.

> [!info] The four cases of the edge test (beyond the slides)
> A **level** is what the button is now. It stays "pressed" for many thousands of loop passes. An **edge** is a change, and it is seen in one pass only. To see a change, the program must remember the state of the pass before: that is `old_state`.
>
> | `old_state` | `current_state` | Meaning | `current_state && !old_state` |
> |---|---|---|---|
> | 0 | 0 | still released | 0, no toggle |
> | 0 | 1 | **press edge** | 1, toggle |
> | 1 | 1 | still held | 0, no toggle |
> | 1 | 0 | release edge | 0, no toggle |
>
> Only one of the four cases toggles. The line `old_state = current_state;` is what turns the second row into the third row on the next pass.

> [!question]- Check (slide 13): Use the slide's figure of 100,000 toggles per second. The button is held for 0.25 s. How many times do the LEDs toggle? Why can nobody predict the pattern after the release?
> Toggles = rate times time = $100\,000\cdot0.25=25\,000$.
> The final pattern depends on whether the number of toggles is even (same pattern as before) or odd (the other pattern).
> One toggle takes $1/100\,000$ s $=10$ µs. A finger cannot control the hold time to 10 µs, so even or odd is a matter of chance.

> [!question]- Check (slide 14): Trace the pseudocode. `old_state` starts as not pressed (0). In nine passes the button reads `current_state` = 0, 1, 1, 1, 0, 0, 1, 1, 0. In which passes do the LEDs toggle? If they start at `0x3C`, what do they show at the end?
> A toggle needs `current_state` = 1 and `old_state` = 0. `old_state` is always the value of the pass before.
> Pass 1: (old 0, cur 0) no. Pass 2: (0, 1) **toggle**. Pass 3: (1, 1) no. Pass 4: (1, 1) no. Pass 5: (1, 0) no.
> Pass 6: (0, 0) no. Pass 7: (0, 1) **toggle**. Pass 8: (1, 1) no. Pass 9: (1, 0) no.
> Two toggles, in passes 2 and 7: one per press. `0x3C` → `0xC3` → `0x3C`, so the LEDs end at `0x3C`.

> [!question]- Check (slide 15): Change one line of the program so that the LEDs toggle when the button is **released**, not when it is pressed. Starting from `0x3C`, what does `PORTD` hold after three full press-and-release actions (no bounce)?
> A release edge is: now not pressed **and** before pressed. Change the test to `if (!current_state && old_state)`.
> Each press-and-release has exactly one release edge, so there are three toggles. Each toggle is `PORTD ^= 0xFF`, which inverts all eight bits.
> `0x3C` → `0xC3` → `0x3C` → `0xC3`. Result: **`0xC3`**.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Floating input | pin connected to nothing, reads 0 or 1 at random |
| Pull-up | resistor from the pin to Vcc, released = 1, pressed = 0 (active low) |
| Divider | $V_{in}=V_{cc}R_2/(R_1+R_2)$, $R_1$ pull-up, $R_2$ pin impedance |
| Thresholds | low up to $0.3V_{cc}$, high from $0.6V_{cc}$ |
| Input with pull-up | `DDRx &= ~(1<<n); PORTx \|= (1<<n);` |
| Internal pull-up | 20 to 50 kΩ (slide figure: 10 kΩ) |
| Pressed test | `!(PINx & (1<<n))` or `bit_is_clear(PINx, n)` |
| `_BV(n)` | `1<<n` |
| Press edge | `cur = bit_is_clear(PINB,PB0); if (cur && !old) ...; old = cur;` |

## Check yourself
> [!question]- 1. A button connects PD3 to GND, `DDRD` and `PORTD` bit 3 are 0. What does `PIND` bit 3 read pressed and released? Fix it in one line.
> Pressed: 0. Released: floating, 0 or 1 at random. Fix: `PORTD |= (1 << PD3);`.

> [!question]- 2. For `DDRB`/`PORTB` = (a) `0x00`/`0x00` (b) `0x00`/`0x08` (c) `0x08`/`0x00` (d) `0x08`/`0x08`, what does PB3 do?
> (a) input, floating. (b) input with pull-up. (c) output, drives low. (d) output, drives high.

> [!question]- 3. Make PD4 and PD5 inputs with pull-ups without changing other pins. What is `PIND` if all eight pull-ups are on and only PD5 is pressed?
> `DDRD &= ~((1<<PD4)|(1<<PD5));` then `PORTD |= (1<<PD4)|(1<<PD5);`. `PIND` = `1101 1111` = **`0xDF`**.

> [!question]- 4. `PIND` = `0xDF` (button on PD5 pressed). Evaluate `PIND & (1<<5)`, `!(PIND & (1<<5))`, `PIND && (1<<5)`. Which is the pressed test?
> `0x00`, 1, 1. The second is the pressed test. The third is 1 for any non-zero `PIND`.

> [!question]- 5. `PINC` = `0x2A`. Evaluate `PINC & (1<<3)`, `PINC & _BV(4)`, `bit_is_clear(PINC, 0)`, `bit_is_set(PINC, 5)`.
> `0x2A` = `0010 1010`. Results: **`0x08`, `0x00`, 1, `0x20`**.

> [!question]- 6. `while(1) { if (bit_is_clear(PINB, PB0)) PORTD ^= 0xFF; }` should toggle once per press. What happens? Fix it.
> While held, every pass toggles: the LEDs look half-bright and stop in a random pattern. Fix:
> ```c
> uint8_t old_state = 0, current_state;
> while (1) {
>   current_state = bit_is_clear(PINB, PB0);
>   if (current_state && !old_state) PORTD ^= 0xFF;
>   old_state = current_state;
> }
> ```

## Links
- Course index: [[CMPE453 Home]]
- Previous: [[CMPE453 L03.2 - Bitwise operations]] · Next: [[CMPE453 L05.1 - UART frame]]
- The three port registers: [[CMPE453 L03.1 - IO port programming]]
- Full step-by-step notes (PDF): [CMPE453_Lecture_04_Digital_Input.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_04/CMPE453_Lecture_04_Digital_Input.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_04/CMPE453_Lecture_04_Digital_Input.pdf`
- Original slides (PDF): [Lecture4.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_04/Lecture4.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_04/Lecture4.pdf`
- Quiz: [[CMPE453 L04 - Digital input and pull-ups - quiz]]

Slides not embedded: 1 (title).
