# Learning brief: CMPE453 L07 - ADC

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CMPE 453 Embedded Systems so that I understand every slide and can answer its questions without help.
ATmega328P (Arduino Uno) in AVR C. Exams and lab quizzes ask for register setup, bit operations, timing and baud-rate numbers, and reading or completing short C code.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 36 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_07/CMPE453 L07 - ADC.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/others/slides/cmpe453_lecture_7_lecture7_adc`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_7/Lecture7-ADC.pdf`

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Why an ADC, and where it sits (slides 2-3)
- 2. Resolution, step size, conversion time and V_ref (slides 4-5)
- 3. The digital output formula (slide 6)
- 4. The analog input pins (slide 7)
- 5. Successive approximation (slides 8-31)
- 6. The ADC of the ATmega328P (slides 32-33)
- 7. ADMUX: reference, channel and alignment (slides 34-35)
- 8. The ADC data register and the read order (slide 36)
- 9. ADCSRA: the clock prescaler and the control bits (slides 37-38)
- 10. The steps to use the ADC (slide 39)
- 11. Example 1: a potentiometer and eight LEDs (slides 40-41)
- 12. Example 2: two channels with the multiplexer (slides 42-44)
- 13. Free running mode and Example 3 (slides 45-49)

## Known errors and traps in the sources (do not teach the errors)

- **Slide error:** - Table 13-3, row 1.28 V: the slide writes "1/1024 = 1.25". It must be $1.28/1024=1.25$ mV. The result is right, the fraction is wrong. - Table 13-2 cuts digits off and does not round. It writes 11.71 and 3.90. Exact: $3/256=11.719$ mV (rounded 11.72) and $1/2...
- **Watch out:** PC4 and PC5 are also SDA and SCL of the TWI (lecture 6.2). If I2C is used, only ADC0 to ADC3 are free for analog inputs.
- **Slide error:** The seven steps on the slides are not exact. Use the table. - Step 4 sets the next bit before step 5 compares. Read literally, the first comparison would use `1100` (12 V). The table compares with `1000` (8 V) first. The comparison in step 5 uses the $V_{DAC}$...
- **Watch out:** $V_{in}$ must stay between GND and $V_{ref}$. A higher voltage only gives 1023. A voltage above the supply (or below GND) can damage the pin.
- **Watch out:** - With an external voltage connected to AREF, never select AVCC or the internal 1.1 V. The two voltages would be shorted together inside the chip (beyond the slides). - The slide table of channels ends with `0110` = ADC6. ADC6 and ADC7 exist only in the 32-pin...
- **Slide error:** In the left table of slide 37 (CPU clock, prescale, ADC frequency) the block for **8 MHz** is wrong by a factor of 2. Correct values: 8 MHz / 16 = 500 kHz, / 32 = 250 kHz, / 64 = 125 kHz, / 128 = 62.5 kHz. The slide lists 250, 125, 62.5 and 31.25 kHz. So at 8 ...
- **Slide error:** Step I says "default: disabled i.e. no conversion". The prescaler cannot be disabled. After reset `ADPS2:0` = `000`, which is a division factor of 2 (8 MHz at 16 MHz, far too fast). No conversion happens after reset because the **ADC** is disabled (`ADEN = 0`)...
- **Problems in the slide program:** - **The prescaler fits a 1 MHz chip**, not the Uno. The program is adapted from Williams' book, where the chip runs at 1 MHz: $1\text{ MHz}/16=62.5$ kHz. On the 16 MHz Uno, `ADPS2` alone gives $16\text{ MHz}/16=1$ MHz, five times above the limit. It still work...
- **Problems in the slide program:** - **Prescaler /8** gives $16\text{ MHz}/8=2$ MHz on the Uno, ten times above the 200 kHz limit (it is right for a 1 MHz chip: 125 kHz). The two 10-bit values are compared in full, so here the low bits matter. Use `ADPS2`, `ADPS1` and `ADPS0` together. - The co...
- **Problems in the slide program:** - Prescaler /8 again: 2 MHz on the 16 MHz Uno. Only 8 bits are used, so it works, but it is outside the specification. Use all three `ADPS` bits. - `transmitByte(ADCH)` sends one **raw byte** (0 to 255), not text. A serial monitor shows strange characters. The...

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
