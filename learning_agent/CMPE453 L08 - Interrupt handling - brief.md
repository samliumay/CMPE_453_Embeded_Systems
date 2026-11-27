# Learning brief: CMPE453 L08 - Interrupt handling

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CMPE 453 Embedded Systems so that I understand every slide and can answer its questions without help.
ATmega328P (Arduino Uno) in AVR C. Exams and lab quizzes ask for register setup, bit operations, timing and baud-rate numbers, and reading or completing short C code.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 20 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_08/CMPE453 L08 - Interrupt handling.md`
2. **Quiz note** (20 multiple-choice questions, one per check question, each with a folded answer that explains every option): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_08/CMPE453 L08 - Interrupt handling - quiz.md`
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/others/slides/cmpe453_lecture_8_lecture8`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_8/Lecture8.pdf`

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. What an interrupt is (slide 2)
- 2. The ISR and the interrupt vector table (slide 3)
- 3. What the CPU does when an interrupt arrives (slides 4-5)
- 4. Kinds of interrupts and the vector for one pin (slides 6-7)
- 5. Example with INT0: the task, the pins, the CPU (slides 8-10)
- 6. The registers: SREG, EIMSK, EICRA (slides 11-13)
- 7. The INT0 program (slide 14)
- 8. Pin-change interrupts (slides 15-17)
- 9. Example 1: enable PCINT10 on PC2 (slides 18-20)
- 10. Example 2: enable PCINT21 on PD5 (slides 21-23)

## Known errors and traps in the sources (do not teach the errors)

- **Slide typo:** The table on the slide shows `0x002` for INT0. The address is `0x0002`.
- **Watch out:** Because I is 0 inside an ISR, a long ISR blocks all other interrupts. A UART byte or a timer tick that arrives twice during this time is lost once. This is the main reason to keep ISRs short.
- **Slide error:** - Slide 6 calls the USART transmit interrupt a "software interrupt". It is triggered by the USART **hardware** inside the chip (the flag `TXCn` is set by the transmitter). A better name is **internal** (peripheral) interrupt, against **external** interrupts fr...
- **Watch out:** - A wrong vector name, for example `ISR(PCINT23_vect)` for the pin PCINT23, does not exist. avr-gcc only gives a warning ("appears to be a misspelled signal handler"). The real vector then has no ISR, and the first interrupt restarts the program. - `PCINT2` is...
- **Slide error:** Slide 11 says "1: Interrupts with their respective **flag** bits set to 1 are enabled". It should be **enable** (mask) bits. An interrupt runs when three bits are 1 at the same time: flag AND enable bit AND I. For INT0: `INTF0` AND `INT0` in `EIMSK` AND I.
- **Watch out:** - **Low level (00) is not an edge.** As long as the pin is low, the ISR starts again and again. Between two runs the main program gets exactly one instruction. With a button held down, main almost stops. - `EICRA |= ...` only sets bits. If the register had ano...
- **Watch out:** - Forgetting `sei()` is the most common mistake: everything is set up and the ISR never runs. - The slide enables INT0 in `EIMSK` first and chooses the mode in `EICRA` after that. It works here because I is still 0. The safe order is: choose the mode in `EICRA...
- **Slide error:** - Slide 15 says the PCINTs support "all 8x3=24 I/O pins". The ATmega328P has **23** PCINT pins. Port C has only PC0 to PC6, so PCINT15 does not exist. - Slide 16 says in the text "PC0-PC7 (Bank 1)". The table on the same slide is right: PC0..PC6. - On the Uno,...
- **Watch out:** - One ISR serves the whole group. `ISR(PCINT2_vect)` runs for a change on **any** enabled pin of port D. The ISR must find out which pin it was. - The ISR runs on both edges. To react to one edge only, read the pin in the ISR and check the level. - If two enab...
- **Slide error:** Slide 20 gives `SREG |= (1 << I);` as a second way. This line does **not** compile with avr-gcc: avr-libc has no name `I` (error: `'I' undeclared`). The name of the bit is `SREG_I` (value 7), so the working form is `SREG |= (1 << SREG_I);`. Use `sei();`. It is...
- **Slide error:** - Slides 22 and 23 still say "Write C-code to enable PCINT10". They belong to example 2 and mean **PCINT21**. The code on slide 22 (`PCIE2`, port D: bank 2) is right for PCINT21. - Slide 23 repeats `SREG |= (1 << I);`, which does not compile (see section 9).

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
