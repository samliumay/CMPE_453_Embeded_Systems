# Learning brief: CMPE453 L00 - Course intro and lab kit

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CMPE 453 Embedded Systems so that I understand every slide and can answer its questions without help.
ATmega328P (Arduino Uno) in AVR C. Exams and lab quizzes ask for register setup, bit operations, timing and baud-rate numbers, and reading or completing short C code.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 31 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_00/CMPE453 L00 - Course intro and lab kit.md`
2. **Quiz note** (31 multiple-choice questions, one per check question, each with a folded answer that explains every option): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/lessons/lecture_00/CMPE453 L00 - Course intro and lab kit - quiz.md`
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/others/slides/cmpe453_lecture_00_lecture0`, `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/CMPE_453_Embedded_Systems/others/slides/cmpe453_lecture_00_lecture1`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_00/Lecture0.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_00/CMPE453_Lecture_00_Course_Intro.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_00/lecture_00.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. People, outcomes and topics (L0 slides 2-6)
- 2. Books, grading and attendance (L0 slides 7-9)
- 3. Labs and software (L0 slides 10-12)
- 4. The parts box, AVR versus ARM (L0 slide 13)
- 5. What an embedded system is (L1 slides 2-6)
- 6. Parts and characteristics (L1 slides 7-11)
- 7. Small, medium and large scale (L1 slides 12-15)
- 8. Microprocessor versus microcontroller (L1 slides 16-17)
- 9. About the AVR and the ATmega328P (L1 slides 18-23)

## Known errors and traps in the sources (do not teach the errors)

- **Lecture 1 is not in the easybook notes:** Our full notes (PDF) cover only Lecture 0. They say `Lecture1.pdf` is a copy of `Lecture0.pdf`. That was true earlier. The file in the folder now (dated 8 Nov) is a real 23-page deck. Part B below is written from those slides only.
- **Watch out:** - The slide says "LPC2148 Datasheet". That chip is not used. The ARM board is the STM32F407 Discovery (UM1472). - Under 50 % attendance means FX for sure. 50 % or more does not mean you pass, it only removes this reason to fail. Exactly 50 % is not FX.
- **Watch out:** "An MCU has memory on the chip" does not mean an MPU system has no memory. A laptop has lots of RAM, on other chips next to the MPU. The difference is where the memory is.

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
