---
course: CMPE 453 Embedded Systems
lecture: 0
title: Course introduction and the lab kit
tags: [cmpe453, lecture]
slides: lectures/lecture_00/Lecture0.pdf
slides_2: lectures/lecture_00/Lecture1.pdf
full_notes: lecture_notes/lecture_00/CMPE453_Lecture_00_Course_Intro.pdf
---
# CMPE 453 · Lecture 0 · Course introduction and the lab kit

> [!summary] In short
> - An embedded system is a small computer inside a device that does one job: sensors → MCU → actuators.
> - A microcontroller (MCU) has CPU, memory and I/O on one chip. A microprocessor (MPU) is the CPU only.
> - Grade: $0.32\cdot\text{midterm}+0.50\cdot\text{final}+\text{lab points}$ (6 labs × 3, no make-up). Under 50 % attendance gives FX.
> - AVR half: Arduino Uno (ATmega328P, 8-bit, 16 MHz). ARM half: STM32F407 Discovery (32-bit).
> - The deck "Lecture 1" (part B below) gives the overview of embedded systems and of the AVR chip: memories, I/O, serial units, timers, ADC, interrupts.

This note covers two decks: **A. Lecture 0** (course organisation, sections 1 to 4) and **B. Lecture 1** (overview, microcontrollers and AVR, sections 5 to 9).

> [!warning] Lecture 1 is not in the easybook notes
> Our full notes (PDF) cover only Lecture 0. They say `Lecture1.pdf` is a copy of `Lecture0.pdf`. That was true earlier. The file in the folder now (dated 8 Nov) is a real 23-page deck. Part B below is written from those slides only.

## A. Lecture 0

## 1. People, outcomes and topics (L0 slides 2-6)
![[cmpe453_lecture_00_lecture0_p002.jpg]]
![[cmpe453_lecture_00_lecture0_p003.jpg]]
![[cmpe453_lecture_00_lecture0_p004.jpg]]
![[cmpe453_lecture_00_lecture0_p005.jpg]]
![[cmpe453_lecture_00_lecture0_p006.jpg]]

**Notes.** Prof. H. Gökhan İlk teaches Sections 1 to 3, Asst. Prof. Mert Burkay Çöteli teaches Section 4. Announcements come on LMS.

The eight learning outcomes (LO1 to LO8) are the exam checklist: components of an embedded system, types of microprocessors, C on embedded systems, I/O ports and protocols, serial communication, interrupts, PWM, ADC.

Topics by week (weeks come from the syllabus, not from the slide):

| Topic | Week | Lab / exam |
|---|---|---|
| AVR architecture | W1 | |
| C programming | W2 | |
| I/O port programming | W3-W4 | Lab 1 (LEDs), Lab 2 (traffic light + 7-segment) |
| Serial: USART, SPI, I2C | W5-W6 | Lab 3 (UART data on a 7-segment) |
| ADC | W7 | Midterm Thu 12 Nov (weeks 1 to 6 plus lab questions) |
| Interrupts | W8 | |
| Timers/counters | W9 | Lab 4 |
| PWM | W10 | |
| ARM architecture | W11 | |
| ARM (Cortex) in embedded C | W12-W13 | Lab 5 (GPIO), Lab 6 (PWM) |

Expected background: computer organization (CMPE 361) and any programming language. The course uses C.

## 2. Books, grading and attendance (L0 slides 7-9)
![[cmpe453_lecture_00_lecture0_p007.jpg]]
![[cmpe453_lecture_00_lecture0_p008.jpg]]
![[cmpe453_lecture_00_lecture0_p009.jpg]]

**Notes.** Textbook: Elliot Williams, *Make: AVR Programming* (2015). ARM board manual: ST user manual UM1472 (Discovery kit with STM32F407VG).

Grade weights: midterm 32, final 50, labs 18 (6 labs × 3 points, **no make-up**).

$$\text{total}=0.32\cdot\text{midterm}+0.50\cdot\text{final}+\text{lab points}$$

Exam scores are out of 100, so they must be weighted first. Example: midterm 70, final 80, all labs: $0.32\times70+0.50\times80+18=22.4+40+18=80.4$.

One missed lab costs 3 course points, which equals $3/0.5=6$ points on the final.

> [!warning] Watch out
> - The slide says "LPC2148 Datasheet". That chip is not used. The ARM board is the STM32F407 Discovery (UM1472).
> - Under 50 % attendance means FX for sure. 50 % or more does not mean you pass, it only removes this reason to fail. Exactly 50 % is not FX.

## 3. Labs and software (L0 slides 10-12)
![[cmpe453_lecture_00_lecture0_p010.jpg]]
![[cmpe453_lecture_00_lecture0_p011.jpg]]
![[cmpe453_lecture_00_lecture0_p012.jpg]]

**Notes.** Slide 12 is the STM32CubeIDE welcome screen (start a new STM32 project, start from a CubeMX `.ioc` file, import a project, import an example).

| Program | What it does | Used for |
|---|---|---|
| Arduino IDE | editor + compiler + uploader | Labs 1-4 (Uno) |
| Tinkercad | simulates Arduino circuits in the browser | testing a circuit before building it |
| STM32CubeIDE | ST's IDE for STM32 projects | Labs 5-6 (Discovery) |

Tinkercad *simulates*, no board needed. The Arduino IDE *uploads* the program into the real chip's flash.

> [!info] Own setup
> Umay's setup is plain C with `avr-gcc`, `make` and `avrdude` for the Uno, and STM32CubeMX + CMake + OpenOCD for the Discovery. The machine code is the same. Lab sheets ask for the Arduino IDE, so know both.

## 4. The parts box, AVR versus ARM (L0 slide 13)
![[cmpe453_lecture_00_lecture0_p013.jpg]]

**Notes.** The kit: Arduino Uno, STM32 Discovery, USB cable type A-B, breadboard, LEDs (need a series resistor), push buttons (digital input), LDRs (resistance falls in bright light, read with the ADC), 7-segment display, touch sensor (outputs HIGH while touched).

Board versus chip: the **Arduino Uno** is the board, the **ATmega328P** is the MCU chip on it. Registers, ports and memory sizes belong to the chip.

| | AVR: ATmega328P (Uno) | ARM: STM32F407VG (Discovery) |
|---|---|---|
| Data width | 8-bit | 32-bit (Cortex-M4F) |
| Clock on the board | 16 MHz | up to 168 MHz |
| Flash (program) | 32 KB | 1 MB |
| SRAM (data) | 2 KB | 192 KB |
| EEPROM | 1 KB | none |

"8-bit" means one register holds 8 bits. The largest value is $0\text{b}11111111=2^8-1=255$.

| Memory | Holds | Kept at power-off? |
|---|---|---|
| Flash | the program | yes |
| SRAM | variables while running | no |
| EEPROM | small settings | yes |

## B. Lecture 1: overview, microcontrollers and AVR

## 5. What an embedded system is (L1 slides 2-6)
![[cmpe453_lecture_00_lecture1_p002.jpg]]
![[cmpe453_lecture_00_lecture1_p003.jpg]]
![[cmpe453_lecture_00_lecture1_p004.jpg]]
![[cmpe453_lecture_00_lecture1_p005.jpg]]
![[cmpe453_lecture_00_lecture1_p006.jpg]]

**Notes.** An embedded system is a computing system built into an electronic device. The slides call it "nearly any computing system other than a desktop computer". Billions of units are made each year, against millions of desktop units, and each costs much less.

Our notes' definition: a small computer built into a bigger device to do **one** job. It reads the world through **sensors** (inputs), decides in software, and acts through **actuators** (outputs).

Application domains on slide 6: consumer products, transportation, plant control, networks. A system can be local (an appliance), locally distributed (aircraft control over a LAN) or geographically distributed (the telephone network).

## 6. Parts and characteristics (L1 slides 7-11)
![[cmpe453_lecture_00_lecture1_p007.jpg]]
![[cmpe453_lecture_00_lecture1_p008.jpg]]
![[cmpe453_lecture_00_lecture1_p009.jpg]]
![[cmpe453_lecture_00_lecture1_p010.jpg]]
![[cmpe453_lecture_00_lecture1_p011.jpg]]

**Notes.** Parts: processor, memory, I/O, and a hardwired unit (application-specific logic, timers, A/D and D/A conversion). The user is on one side, the environment on the other.

Three common characteristics (slide 10):
- **Single-functioned**: runs one program, repeatedly.
- **Tightly constrained**: low cost, low power, small, fast.
- **Reactive and real-time**: reacts all the time to changes in its environment and must compute some results without delay.

The digital camera (slide 11) is the example: always a camera, tightly constrained, reactive only to a small extent.

> [!example] Sort the parts of a microwave oven
> Ask of each part: does the program read it, or drive it? Inputs (sensors): keypad, door switch. Outputs (actuators): display, turntable motor, magnetron. The MCU reads the inputs on input pins, keeps the time with a timer, and switches the outputs.

## 7. Small, medium and large scale (L1 slides 12-15)
![[cmpe453_lecture_00_lecture1_p012.jpg]]
![[cmpe453_lecture_00_lecture1_p013.jpg]]
![[cmpe453_lecture_00_lecture1_p014.jpg]]
![[cmpe453_lecture_00_lecture1_p015.jpg]]

**Notes.** The classification is by complexity and performance.

| Scale | Processor | Operating system |
|---|---|---|
| Small | 8 or 16 bit MPU/MCU, low performance, low cost | may or may not have one |
| Medium | 16/32 bit MPU/MCU, DSPs, medium performance | GPOS or RTOS |
| Large | 32 or 64 bit RISC, reconfigurable SoC, multicore | high performance RTOS |

GPOS = general-purpose operating system, RTOS = real-time operating system. The Arduino Uno with its 8-bit ATmega328P is a small-scale system.

## 8. Microprocessor versus microcontroller (L1 slides 16-17)
![[cmpe453_lecture_00_lecture1_p016.jpg]]
![[cmpe453_lecture_00_lecture1_p017.jpg]]

**Notes.**

| | Microprocessor (MPU) | Microcontroller (MCU) |
|---|---|---|
| On the chip | CPU only | CPU, ROM (flash), RAM, I/O ports, timer, serial port |
| RAM, ROM, I/O | external, amount chosen as needed | fixed amount on the chip |
| Use | general-purpose computing | embedded systems that do a single task |
| Examples | Pentium, PowerPC | TV remote control, printer |
| Trade-off | flexible but bulkier | small, cheap, low power |

> [!warning] Watch out
> "An MCU has memory on the chip" does not mean an MPU system has no memory. A laptop has lots of RAM, on other chips next to the MPU. The difference is where the memory is.

## 9. About the AVR and the ATmega328P (L1 slides 18-23)
![[cmpe453_lecture_00_lecture1_p018.jpg]]
![[cmpe453_lecture_00_lecture1_p019.jpg]]
![[cmpe453_lecture_00_lecture1_p020.jpg]]
![[cmpe453_lecture_00_lecture1_p021.jpg]]
![[cmpe453_lecture_00_lecture1_p022.jpg]]
![[cmpe453_lecture_00_lecture1_p023.jpg]]

**Notes.** What the slides say about the AVR family:
- **CPU**: 8-bit RISC (except AVR32). Groups: Mega, Tiny, Special purpose, Classic. The program counter (PC) says where the next instruction is. The CPU fetches it, decodes it, and the ALU works on the registers.
- **Program memory**: flash, holds the compiled program. **RAM**: temporary data. **EEPROM**: small permanent data.
- **Clocks**: a master clock, the CPU clock derived from it, peripheral clocks derived from the CPU clock.
- **I/O**: almost every pin can be an input or an output. Output: logic 1 = supply voltage, logic 0 = ground. Input: logic 1 above about $V_{cc}/2$, logic 0 below.
- **Serial**: USART (typically to the PC), SPI (very fast, short distance), I2C (many devices on two wires). All three can be used at the same time.
- **Timers/counters**: T/C 0 is 8-bit, T/C 1 is 16-bit, T/C 2 is 8-bit. Used to count events, measure time, or make PWM.
- **ADC**: 10-bit resolution, 6 channels. **Interrupts**: events that trigger a piece of code, the interrupt service routine.

ATmega328P (slides 22-23): 32 K flash, 2 K SRAM, 1 K EEPROM. The pins are in banks of 8 (PORT B, PORT D, and PORT C with 7 pins). Most pins have two jobs, for example RXD/PD0 and TXD/PD1. Each bank is used through 8-bit registers:

```c
thePins = PINB;   // reading
PORTB = 42;       // writing
```

> [!info] On the Uno
> The slide says the CPU clock is 1 MHz by default (internal 8 MHz oscillator). The Uno board uses an external 16 MHz clock instead, and all later timing in this course assumes 16 MHz.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Embedded system | a dedicated computer inside a device: sensors → MCU → actuators |
| Sensor / actuator | the program reads a sensor (input), drives an actuator (output) |
| MCU vs MPU | MCU = CPU + memory + I/O on one chip, MPU = CPU only |
| Single-board computer | a complete computer on one board, e.g. Arduino Uno |
| Boards | Arduino Uno (ATmega328P, 8-bit, 16 MHz), STM32F407 Discovery (32-bit ARM) |
| ATmega328P memory | 32 KB flash, 2 KB SRAM, 1 KB EEPROM |
| Largest 8-bit value | $2^8-1=255$ |
| Grade | $0.32\cdot\text{midterm}+0.50\cdot\text{final}+\text{lab points}$ |
| Rules | no lab make-up, attendance below 50 % gives FX |

## Check yourself
> [!question]- 1. True or false: a microprocessor contains its own program memory.
> False. An MPU is the CPU only, program memory sits on another chip. Having it on the chip is what makes a chip an MCU.

> [!question]- 2. A smart thermostat reads a temperature sensor, shows the temperature and switches a heater relay. List sensors and actuators.
> Read: the temperature sensor (sensor). Driven: the display and the heater relay (actuators). On-chip parts needed: CPU and flash, RAM, an ADC or a digital bus such as I2C for the sensor, output pins for the relay, a timer to measure at fixed intervals.

> [!question]- 3. Midterm 45, final 55, labs 12. Compute the total.
> $0.32\times45=14.4$, $0.50\times55=27.5$, so $14.4+27.5+12=$ **53.9**.

> [!question]- 4. Two labs missed, full marks in the other four, midterm 50. What final score gives a total of 60?
> Labs $4\times3=12$, midterm $0.32\times50=16$, so far 28. Needed from the final: $60-28=32$. $0.5\,x=32$, so $x=$ **64**.

> [!question]- 5. Compare the AVR and the ARM chip of this course in three points.
> Data width: 8-bit against 32-bit. Speed: 16 MHz against up to 168 MHz. Memory: 32 KB against 1 MB flash, 2 KB against 192 KB SRAM.

> [!question]- 6. In Lab 1 the yellow LED is on Uno pin 12. Which port bit is it, and which two lines switch it on?
> Pins 8 to 13 are port B bits 0 to 5, so pin 12 is bit $12-8=4$: **PB4**.
> ```c
> DDRB  |= (1 << PB4);   // DDRB:  0b00000000 -> 0b00010000 (output)
> PORTB |= (1 << PB4);   // PORTB: 0b00000000 -> 0b00010000 (HIGH, LED on)
> ```

## Links
- Course index: [[CMPE453 Home]]
- Next: [[CMPE453 L02.1 - AVR toolchain]]
- Full step-by-step notes (PDF, Lecture 0 only): [CMPE453_Lecture_00_Course_Intro.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_00/CMPE453_Lecture_00_Course_Intro.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lecture_notes/lecture_00/CMPE453_Lecture_00_Course_Intro.pdf`
- Original slides, Lecture 0 (PDF): [Lecture0.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_00/Lecture0.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_00/Lecture0.pdf`
- Original slides, Lecture 1 (PDF): [Lecture1.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_00/Lecture1.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cmpe_453/lectures/lecture_00/Lecture1.pdf`

Slides not embedded: Lecture 0 page 1, Lecture 1 page 1 (title slides).
