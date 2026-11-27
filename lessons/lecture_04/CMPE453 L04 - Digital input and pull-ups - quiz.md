---
course: CMPE 453 Embedded Systems
lesson: CMPE453 L04 - Digital input and pull-ups
type: quiz
questions: 14
---

# CMPE453 L04 - Digital input and pull-ups - quiz

Lesson note: [[CMPE453 L04 - Digital input and pull-ups]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. A button needs a pull-up (slides 2-3)

### Q1 (slide 2)

![[cmpe453_lecture_04_lecture4_p002.jpg]]

A push-button connects an AVR input pin to GND, as in the figure. There is no pull-up: the pin's `DDRx` bit and `PORTx` bit are both 0. A student says: "Released, the pin is not connected to GND, so it must read 1." What is wrong with this?

- A) Nothing is wrong. An input pin with no connection is always read as 1 by the AVR.
- B) Released, the pin is connected to nothing, so noise decides its voltage and it may read 0 or 1.
- C) Released, the input impedance pulls the pin to 0 V, so it always reads 0, not 1.
- D) Released, the button contacts still touch a little, so the pin stays near 0 V and reads 0.

> [!success]- Answer
> **B.** Released, the pin is not connected to Vcc either. It is connected to nothing: it is floating.
> An input pin draws almost no current, so stray charges and noise decide its voltage. `PINx` may read 0 or 1 at random.
> A pin reads 1 for sure only if something (a pull-up) connects it to a high voltage.
> - A: An unconnected input has no defined level. Nothing inside makes it 1 while the pull-up is off.
> - C: The pin impedance is very large (100 kΩ to 1 MΩ). It does not hold the pin at a defined 0 V.
> - D: A released button is open. Its contacts do not touch.

### Q2 (slide 3)

A button connects the pin to GND and a pull-up resistor $R_1$ connects the pin to Vcc = 5 V. The slide advises a pull-up of 10 kΩ to 100 kΩ. Compute the current through the pull-up while the button is pressed for (a) $R_1=47$ kΩ and (b) $R_1=220$ Ω. Which resistor fits the slide's advice?

- A) (a) 0.11 mA, (b) 2.27 mA; 220 Ω fits the advice.
- B) (a) 1.06 mA, (b) 22.7 mA; 220 Ω fits the advice.
- C) (a) 1.06 mA, (b) 2.27 mA; 47 kΩ fits the advice.
- D) (a) 0.11 mA, (b) 22.7 mA; 47 kΩ fits the advice.

> [!success]- Answer
> **D.** Pressed, the full 5 V lies across the resistor: $I=V/R_1$.
> (a) $I=5/47\,000=0.000106$ A $\approx 0.11$ mA. (b) $I=5/220=0.0227$ A $\approx 22.7$ mA.
> 47 kΩ is inside 10 kΩ to 100 kΩ. 220 Ω works, but wastes about 214 times more current while the button is held.
> - A: (b) uses 2.2 kΩ instead of 220 Ω: $5/2200=2.27$ mA. And 220 Ω is far below 10 kΩ.
> - B: (a) uses 4.7 kΩ instead of 47 kΩ: $5/4700=1.06$ mA. And 220 Ω is far below 10 kΩ.
> - C: Both values lose a factor of 10: $5/4700=1.06$ mA and $5/2200=2.27$ mA.

## 2. How large may the pull-up be (slide 4)

### Q3 (slide 4)

![[cmpe453_lecture_04_lecture4_p004.jpg]]

Vcc = 5 V. The button is released. The pull-up $R_1$ and the pin impedance $R_2=200$ kΩ form the divider in the figure. The pin reads low up to $0.3\,V_{cc}=1.5$ V and high from $0.6\,V_{cc}=3.0$ V; in between nothing is promised. Compute $V_{in}$ and say what the pin reads for (a) $R_1=47$ kΩ and (b) $R_1=330$ kΩ.

- A) (a) 4.05 V, reads 1. (b) 1.89 V, undefined.
- B) (a) 0.95 V, reads 0. (b) 3.11 V, reads 1.
- C) (a) 4.05 V, reads 1. (b) 1.89 V, reads 1.
- D) (a) 4.05 V, reads 1. (b) 1.89 V, reads 0.

> [!success]- Answer
> **A.** $V_{in}=V_{cc}R_2/(R_1+R_2)$.
> (a) $5\cdot200/(47+200)=1000/247=4.05$ V. This is above 3.0 V: reads 1.
> (b) $5\cdot200/(330+200)=1000/530=1.89$ V. This is between 1.5 V and 3.0 V: undefined, may read 0 or 1.
> The safe limit is $R_1\le\tfrac23 R_2=133$ kΩ. 47 kΩ is inside, 330 kΩ is not.
> - B: This puts $R_1$ on top: $5\cdot47/247=0.95$ V and $5\cdot330/530=3.11$ V. That is the voltage across the pull-up, not at the pin.
> - C: 1.89 V is above 1.5 V, so it is not a sure low. But it is not a high either: a high needs at least 3.0 V.
> - D: This uses half of Vcc (2.5 V) as the only threshold. The datasheet has no promise between 1.5 V and 3.0 V.

## 3. The built-in pull-ups (slides 5-6)

### Q4 (slide 5)

Vcc = 5 V. The internal pull-up is $R_1=50$ kΩ (the datasheet's largest value) and the pin impedance is $R_2=200$ kΩ. Released, $V_{in}=V_{cc}R_2/(R_1+R_2)$; the pin reads high from 3.0 V and low up to 1.5 V. Pressed, the button connects the pin to GND. What is the released level, and how much current flows through the pull-up while the button is pressed?

- A) 1.0 V, a clean low (reads 0); 0.1 mA while pressed.
- B) 4.0 V, a clean high (reads 1); 0.025 mA while pressed.
- C) 4.0 V, a clean high (reads 1); 0.1 mA while pressed.
- D) 1.0 V, a clean low (reads 0); 0.025 mA while pressed.

> [!success]- Answer
> **C.** Released: $V_{in}=5\cdot200/(50+200)=1000/250=4.0$ V. This is above 3.0 V, so the pin reads 1.
> Pressed: the full 5 V lies across the pull-up, $I=5/50\,000=0.0001$ A $=0.1$ mA.
> So the built-in pull-up gives a safe high and a very small pressed current.
> - A: This puts $R_1$ on top: $5\cdot50/250=1.0$ V. That is the voltage across the pull-up, not at the pin.
> - B: This uses $R_2$ for the pressed current: $5/200\,000=0.025$ mA. Pressed, the pin is at 0 V and the current flows through $R_1$.
> - D: Both mistakes together: $R_1$ on top (1.0 V) and $R_2$ for the current (0.025 mA).

### Q5 (slide 6)

Bit n of `DDRB` and of `PORTB` belongs to pin PBn (bit 0 is the rightmost bit). `DDRB` bit 1 = output, 0 = input. For an input pin, `PORTB` bit 1 = pull-up on; for an output pin it drives the pin high. Before: `DDRB` = `0x2F`, `PORTB` = `0x21`. Then these two lines run:

```c
DDRB &= ~(1 << PB3);
PORTB |= (1 << PB3);
```

What is in both registers afterwards, and what is PB3 now?

- A) `DDRB` = `0x08`, `PORTB` = `0x29`; PB3 is an output that drives high.
- B) `DDRB` = `0x27`, `PORTB` = `0x29`; PB3 is an input with pull-up.
- C) `DDRB` = `0x27`, `PORTB` = `0x08`; PB3 is an input with pull-up.
- D) `DDRB` = `0x08`, `PORTB` = `0x08`; PB3 is an output that drives high.

> [!success]- Answer
> **B.** `1 << PB3` = `0000 1000`. Inverted: `1111 0111`.
> `DDRB`: `0010 1111 & 1111 0111` = `0010 0111` = `0x27`. Only bit 3 is cleared.
> `PORTB`: `0010 0001 | 0000 1000` = `0010 1001` = `0x29`. Only bit 3 is set.
> PB3 has `DDB3` = 0 and `PORTB3` = 1: input with pull-up. The other pins are unchanged.
> - A: This forgets the `~`: `0x2F & 0x08` = `0x08`. Then PB3 stays an output, and `PORTB` bit 3 = 1 drives it high.
> - C: This reads `|=` as `=`: `PORTB` = `0x08`. That clears bit 0 (PB0), which `|=` keeps.
> - D: Both mistakes together: no `~` (`DDRB` = `0x08`) and `=` instead of `|=` (`PORTB` = `0x08`).

## 4. Reading a port and testing one bit (slides 7-8)

### Q6 (slide 7)

Port B is set up as `DDRB = 0x00; PORTB = 0xFF;`: all eight pins are inputs with pull-ups. Each pin PB0..PB7 has a button to GND. Bit n of `PINB` (bit 0 is the rightmost bit) is the level of pin PBn. Then `tmp = PINB;` gives `0xED`. Which buttons are pressed?

- A) The buttons on PB4 and PB1
- B) The buttons on PB6 and PB3
- C) The buttons on PB4 and PB2
- D) The buttons on PB5 and PB3

> [!success]- Answer
> **A.** `0xED` = `1110 1101`. A pressed button pulls its pin to 0 V, so it shows as a 0.
> Count from the right, starting at 0: the zeros are bit 4 and bit 1. So PB4 and PB1 are pressed.
> - B: This counts the bits from the left: the zeros are at positions 3 and 6 from the left.
> - C: This writes the hex digit D as `1011`. D = 13 = `1101`, so the byte is `1110 1101`, not `1110 1011`.
> - D: This writes D as `1011` and also counts from the left: `1110 1011` has zeros at positions 3 and 5 from the left.

### Q7 (slide 8)

A pull-up button is on PD3 (bit 3 of `PIND`). The code `if (PIND & (1<<3) == 0) { doStuff(); }` should run `doStuff()` when bit three is clear, but it never runs. Why?

- A) `1<<3` is the value 3, so the mask tests bits 0 and 1 of `PIND`, not bit 3.
- B) `&` acts as a logical AND here, so `PIND & (1<<3)` is 1 for any non-zero `PIND`.
- C) A pin with a pull-up must be read from `PORTD`, so `PIND` never shows the button.
- D) `==` binds tighter than `&`, so the test is `PIND & ((1<<3) == 0)`, which is always 0.

> [!success]- Answer
> **D.** In C, `==` binds tighter than `&`. The compiler reads `PIND & ((1<<3) == 0)`.
> `(1<<3) == 0` is `8 == 0`, which is 0. Then `PIND & 0` is 0 for every `PIND`: always false.
> Fix: `if ((PIND & (1<<3)) == 0)` or `if (!(PIND & (1<<3)))`.
> - A: `1<<3` is 1 shifted left by 3 places: `0000 1000` = 8. It is the mask for bit 3.
> - B: `&` is the bitwise AND. The logical AND is `&&`.
> - C: The pin level is in `PIND`. `PORTD` only gives back the pull-up setting you wrote.

## 5. Helpful macros (slide 9)

### Q8 (slide 9)

A pull-up button is on PD2 (bit 2 of `PIND`) and an LED on PB5 (bit 5 of `PORTB`, an output). `_BV(n)` is `(1 << (n))`. Inside `while(1)` there are three lines:

```c
loop_until_bit_is_clear(PIND, PD2);
PORTB ^= _BV(PB5);
loop_until_bit_is_set(PIND, PD2);
```

What do lines 1 and 3 wait for, and what does the user see?

- A) Line 1 waits for a release, line 3 for a press. The LED toggles once per release.
- B) Line 1 waits for a press, line 3 for a release. The LED toggles once per release.
- C) Line 1 waits for a press, line 3 for a release. The LED toggles once per press.
- D) Line 1 waits for a release, line 3 for a press. The LED turns on at the first release and stays on.

> [!success]- Answer
> **C.** With a pull-up, pressed = bit clear and released = bit set.
> Line 1 loops while bit 2 is set, so it waits until the button is pressed.
> Line 2: `_BV(PB5)` = `0x20`. The XOR flips bit 5 of `PORTB`: the LED toggles right after the press.
> Line 3 waits until the button is released. So the LED toggles once per press, however long it is held (ignoring bounce).
> - A: This swaps the two macros (the slide's comment for `loop_until_bit_is_clear` has this error). Line 1 ends when the bit is clear, that is at a press.
> - B: The waits are right, but the toggle comes after line 1, at the press, not after line 3.
> - D: Two mistakes: the macros swapped (line 1 ends at a release) and `^=` read as `|=` (set bit 5 instead of flipping it). `^=` flips the bit each time, so the LED toggles.

## 6. Example 2: 0xC3 while pressed (slides 10-12)

### Q9 (slide 10)

LEDs are on PD0..PD7. Bit n of `PORTD` (bit 0 is the rightmost bit) drives pin PDn, and each LED is lit when its pin is high. Which Port D pins have a lit LED for `0xC3`, which for `0x3C`, and how are the two patterns related?

- A) `0xC3`: PD7, PD6, PD1, PD0. `0x3C`: PD5 to PD2. Each is the inverse of the other.
- B) `0xC3`: PD5 to PD2. `0x3C`: PD7, PD6, PD1, PD0. Each is the inverse of the other.
- C) `0xC3`: PD7, PD6, PD1, PD0. `0x3C`: PD5 to PD2. `0x3C` is `0xC3 >> 2`.
- D) `0xC3`: PD5 to PD2. `0x3C`: PD7, PD6, PD1, PD0. `0x3C` is `0xC3 >> 2`.

> [!success]- Answer
> **A.** `0xC3` = `1100 0011`: bits 7, 6, 1, 0 are 1, so PD7, PD6, PD1, PD0 are lit.
> `0x3C` = `0011 1100`: bits 5, 4, 3, 2 are 1, so PD5 to PD2 are lit.
> `0xC3 ^ 0xFF` = `0x3C`: each pattern is the inverse of the other. Every LED changes.
> - B: This takes a 0 bit as a lit LED. Here a lit LED needs a high pin, that is a 1 bit.
> - C: `0xC3 >> 2` = `0011 0000` = `0x30`, not `0x3C`. The relation is the inverse.
> - D: Both mistakes: 0 bits as lit LEDs, and `0xC3 >> 2` is `0x30`, not `0x3C`.

### Q10 (slide 11)

Button on PB0 to GND, LEDs on PD0..PD7. Bit n of `PINB` (bit 0 is the rightmost bit) is the level of pin PBn. `DDRB` keeps its reset value `0x00`. The program is:

```c
PORTB |= (1 << PB0);      /* pull-up on our input pin PB0 */
DDRD = 0xff;              /* all LEDs output */
while (1)
{
  if (!(PINB & (1<<PB0))) /* pressed */
    PORTD = 0b11000011;
  else                    /* not pressed */
    PORTD = 0b00111100;
}
```

At one moment `PINB` reads `0xA6`. Which branch runs, and why do the other seven bits of `PINB` not matter?

- A) "Not pressed", `PORTD` = `0x3C`. The mask has 0 in bits 1 to 7, so the AND clears them.
- B) "Pressed", `PORTD` = `0xC3`. The mask has 0 in bits 1 to 7, so the AND clears them.
- C) "Pressed", `PORTD` = `0xC3`. Bits 1 to 7 read 1 through their pull-ups, so they never change.
- D) "Not pressed", `PORTD` = `0x3C`. Bits 1 to 7 read 1 through their pull-ups, so they never change.

> [!success]- Answer
> **B.** `0xA6` = `1010 0110`. Mask: `1 << PB0` = `0000 0001`.
> `1010 0110 & 0000 0001` = `0000 0000` = 0. Then `!0` = 1: true. The "pressed" branch writes `0b11000011` = `0xC3`.
> PB1 to PB7 have no pull-up, so they float and read at random. The mask has 0 in their columns, so the AND forces them to 0.
> - A: This takes bit 0 from the left end. The leftmost bit (bit 7) is 1, but bit 0 is the rightmost bit, and it is 0.
> - C: Only PB0 has its pull-up on (`PORTB |= (1 << PB0)`). PB1 to PB7 float.
> - D: Both mistakes: bit 0 read from the left, and pull-ups that are not on.

### Q11 (slide 12)

Same circuit as in Q10: a button from PB0 to GND, the pull-up of PB0 on (`PORTB |= (1 << PB0);`), bit 0 of `PINB` is the level of PB0. `bit_is_set(sfr, bit)` is non-zero when the bit is 1, and 0 when it is 0. Which test still shows `0xC3` while the button is pressed and `0x3C` while it is released?

- A) `if (bit_is_set(PINB, PB0)) PORTD = 0b11000011; else PORTD = 0b00111100;`
- B) `if (!bit_is_set(PINB, PB0)) PORTD = 0b00111100; else PORTD = 0b11000011;`
- C) `if (bit_is_set(PORTB, PB0)) PORTD = 0b11000011; else PORTD = 0b00111100;`
- D) `if (bit_is_set(PINB, PB0)) PORTD = 0b00111100; else PORTD = 0b11000011;`

> [!success]- Answer
> **D.** With a pull-up, bit 0 of `PINB` is 1 when the button is released. So `bit_is_set` is true for "released", and that branch must write `0b00111100` = `0x3C`. The `else` branch (pressed) writes `0xC3`.
> Another correct way: keep the old branches and write `if (!bit_is_set(PINB, PB0))`, which is what `bit_is_clear` means.
> - A: Only the macro is changed. Now `0xC3` shows while released and `0x3C` while pressed.
> - B: The test is negated and the branches are swapped too. The two changes cancel the fix: `0x3C` while pressed.
> - C: `PORTB` bit 0 is the pull-up setting, always 1. The test is always true, so the LEDs always show `0xC3`.

## 7. Example 3: toggle once per press (slides 13-15)

### Q12 (slide 13)

The naive loop `if (button is pressed) Toggle LEDs` toggles about 100,000 times per second while the button is held (the slide's figure). The button is held for 0.25 s. How many toggles happen, and how long does one toggle take?

- A) 400,000 toggles; one toggle takes 10 µs, too short for a finger to control.
- B) 25,000 toggles; one toggle takes 1 µs, too short for a finger to control.
- C) 25,000 toggles; one toggle takes 10 µs, too short for a finger to control.
- D) 400,000 toggles; one toggle takes 1 µs, too short for a finger to control.

> [!success]- Answer
> **C.** Toggles = rate times time = $100\,000\cdot0.25=25\,000$.
> One toggle takes $1/100\,000$ s $=0.00001$ s $=10$ µs.
> The final pattern depends on whether the count is even or odd. A finger cannot set the hold time to 10 µs, so the pattern is a matter of chance.
> - A: This divides by the time: $100\,000/0.25=400\,000$. The count is rate times time.
> - B: $1/100\,000$ s is $10^{-5}$ s = 10 µs, not $10^{-6}$ s = 1 µs.
> - D: Both mistakes: divided by the time, and $10^{-5}$ s read as 1 µs.

### Q13 (slide 14)

The loop is `current_state = (button is pressed); if (current_state && !old_state) toggle; old_state = current_state;`. `old_state` starts as 0 (not pressed). In nine passes the button reads `current_state` = 0, 1, 1, 1, 0, 0, 1, 1, 0. A toggle is `PORTD ^= 0xFF` and the LEDs start at `0x3C`. In which passes do the LEDs toggle, and what do they show at the end?

- A) Passes 2 and 7; the LEDs end at `0x3C`.
- B) Passes 3, 4 and 8; the LEDs end at `0xC3`.
- C) Passes 2, 3, 4, 7 and 8; the LEDs end at `0xC3`.
- D) Passes 5 and 9; the LEDs end at `0x3C`.

> [!success]- Answer
> **A.** A toggle needs `current_state` = 1 and `old_state` = 0. `old_state` is the value of the pass before.
> Passes 1 to 9 as (old, cur): (0,0) (0,1) (1,1) (1,1) (1,0) (0,0) (0,1) (1,1) (1,0).
> Only passes 2 and 7 are (0,1). Two toggles: `0x3C` → `0xC3` → `0x3C`.
> - B: This drops the `!`: `current_state && old_state` is true in passes 3, 4, 8. Three toggles end at `0xC3`.
> - C: This ignores `old_state` and toggles whenever the button is pressed: five toggles end at `0xC3`.
> - D: These are the release edges (1,0). The test `current_state && !old_state` looks for (0,1).

### Q14 (slide 15)

The loop is the one from Q13: `current_state` is 1 while the button is pressed, and `old_state = current_state;` runs at the end of every pass. You change only the `if` line so that the LEDs toggle (`PORTD ^= 0xFF`) when the button is released. Starting from `0x3C`, what does `PORTD` hold after three full press-and-release actions (no bounce)?

- A) `if (current_state != old_state)`; `PORTD` ends at `0x3C`.
- B) `if (!current_state && old_state)`; `PORTD` ends at `0xC3`.
- C) `if (current_state != old_state)`; `PORTD` ends at `0xC3`.
- D) `if (!current_state && old_state)`; `PORTD` ends at `0x3C`.

> [!success]- Answer
> **B.** A release edge is: now not pressed and before pressed, `!current_state && old_state`.
> Each press-and-release has one release edge, so there are three toggles.
> `0x3C` → `0xC3` → `0x3C` → `0xC3`.
> - A: `!=` is true on every change, press and release. That gives six toggles and ends at `0x3C`, and it does not toggle only on release.
> - C: `!=` toggles on press and release, so it is the wrong line. It also gives six toggles, not three, so it would end at `0x3C`.
> - D: The line is right, but this counts six toggles (press and release). Only the three release edges toggle.
