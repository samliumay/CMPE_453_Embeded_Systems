# Learning brief: CMPE453 L04 - Digital input and pull-ups

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CMPE 453 Embedded Systems so that I understand every slide and can answer its questions without help.
ATmega328P (Arduino Uno) in AVR C. Exams and lab quizzes ask for register setup, bit operations, timing and baud-rate numbers, and reading or completing short C code.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 14 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_04/CMPE453 L04 - Digital input and pull-ups.md`
2. **Quiz note** (14 multiple-choice questions, one per check question, each with a folded answer that explains every option): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_04/CMPE453 L04 - Digital input and pull-ups - quiz.md`
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/others/slides/cmpe453_lecture_04_lecture4`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_04/Lecture4.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_04/CMPE453_Lecture_04_Digital_Input.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_04/lecture_04.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. A button needs a pull-up (slides 2-3)
- 2. How large may the pull-up be (slide 4)
- 3. The built-in pull-ups (slides 5-6)
- 4. Reading a port and testing one bit (slides 7-8)
- 5. Helpful macros (slide 9)
- 6. Example 2: 0xC3 while pressed (slides 10-12)
- 7. Example 3: toggle once per press (slides 13-15)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** "10 k to 100 kΩ should avoid most problems": with the slide's lowest pin impedance (100 kΩ), a 100 kΩ pull-up gives exactly half of Vcc. Stay near 10 kΩ. Smaller is not always better: the pressed current grows.
- **Watch out:** - The slide's figure labels the internal pull-up "10 kΩ". The ATmega328P datasheet gives 20 to 50 kΩ. - `PORTx = 0xFF` means "pull-ups on" only for pins whose `DDRx` bit is 0. On output pins it drives them high. - Read the pin from `PINx`. `PORTx` gives back t...
- **Watch out:** - With a pull-up button "set" means **released**. To act on a press test for a clear bit: `if (!(PIND & (1 << 3)))`. - The result is `1<<n`, not 1. `if ((PIND & (1<<5)) == 1)` is never true. - `PIND && (1<<5)` is a logical AND. It is 1 for any non-zero `PIND` ...
- **Slide errors:** - The comment of `loop_until_bit_is_clear` says "Wait until bit ... is set". Correct is "is **clear**". - The slide writes the defines with a colon, `#define _BV(bit): (1 << (bit))`. A real `#define` has no colon. You never type these lines: `<avr/io.h>` defin...
- **Watch out:** - Testing `PORTB` instead of `PINB` gives `0x3C` always: `PORTB` bit 0 is the pull-up setting, always 1. - Without the pull-up line, PB0 floats and the released pattern is random. - `DDRD = 0xff` also drives PD0 and PD1, the Uno's RX/TX. Unplug LEDs 0 and 1 if...
- **Watch out:** - The slide uses `bool` without `#include <stdbool.h>`. With the lab's `-std=gnu11`, `avr-gcc` says `unknown type name 'bool'`. Add the include (as above), or use `uint8_t`. - Real buttons **bounce** for a few milliseconds. The loop sees every bounce as a new ...

## How to run a session

1. Ask which part I want, or start from section 1. Ask two or three short questions to see what I already know.
2. Go section by section. Explain one slide or one idea, then ask the **check question** of that slide from the lesson note (the callouts `> [!question]- Check (slide N): ...`). Do not show the answer. Wait for mine, then compare it with the answer in the note and tell me what is missing.
3. At the end of a section, or in Practice mode, ask the **multiple-choice questions** of that section from the quiz note. Show the four options, wait for my letter, then explain why the right option is right and why my choice was wrong, using the answer callout.
4. Make new questions of the same kind (new numbers, a changed condition) for the points I miss.
5. Finish with a short list: what I know, what I should repeat, and which slides to read again.

## About me

MS student at TED University (CS/CMPE). I study alone, often on a phone. English is not my first language.

- Probe my starting point first.
- One idea at a time, short explanations, a check question after each step. Never skip steps.
- Give the Turkish equivalent of a key term at its first use.
- Ask me to think out loud. When I am wrong, show me which step went wrong.
- For graded homework or labs: hints and explanations only, never the submission.
