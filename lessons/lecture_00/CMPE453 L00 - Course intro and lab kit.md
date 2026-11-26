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

> [!question]- Check (deck A, slides 3-5): The topic list on slide 5 covers two processor families. Which topics belong to which family, and which language is used for both? Which topic matches learning outcome 8?
> AVR: the first eight topics (AVR architecture, C programming, I/O ports, serial communication, ADC, interrupts, timers/counters, PWM). ARM: the last two (ARM architecture, coding the ARM Cortex).
> Both are programmed in embedded C (slide 4).
> Learning outcome 8 on slide 3 is "Convert analogue signal to its digital counterparts / use of quantization, ADC". That is the topic Analog-to-Digital Conversion (ADC).

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

> [!question]- Check (deck A, slides 8-9): A student has midterm 60, final 70 and did five of the six labs with full marks. Assume the course has 56 class hours in total (an example number, the slides give none) and the student attended 27. What is the weighted total, and what grade follows?
> Labs: $5\times3=15$ points (the missed lab gives 0, no make-up).
> Total: $0.32\times60+0.50\times70+15=19.2+35+15=69.2$.
> Attendance: $27/56=0.482=48.2\,\%$. This is less than 50 %, so the grade is **FX** whatever the total is.
> The student needed at least $0.5\times56=28$ hours.

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

> [!question]- Check (deck A, slides 10-11): You are at home without the kit and want to test a traffic-light circuit for the Uno. Which program from the slides do you use? Which program do you need later in the lab, and what does it do that the first one cannot? Which program belongs to the Discovery board?
> At home: Tinkercad. It simulates the Arduino and the circuit in the browser, so no hardware is needed.
> In the lab: the Arduino IDE. It compiles the program and uploads it over USB into the flash of the real ATmega328P. Tinkercad never touches a real chip.
> The Discovery board (ARM) is programmed from STM32CubeIDE.

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

> [!info] Kit electronics: LED, resistor, breadboard, button, LDR (beyond the slides)
> Slide 13 shows only photos. This is what you need to wire the parts.
> - **LED polarity.** An LED is a diode. Current flows only from the anode (+, the longer leg) to the cathode (−, the shorter leg, at the flat side of the case). Turned around, it stays dark.
> - **Series resistor.** A lit LED keeps an almost fixed voltage $V_f$ across it, about 2 V for the red, yellow and green LEDs of this kind. It does not limit the current by itself. A resistor in series takes the rest of the voltage and sets the current (Ohm's law, $V=I\cdot R$):
> $$R=\frac{V_{cc}-V_f}{I}$$
> With $V_{cc}=5$ V, $V_f=2$ V and $I=10$ mA: $R=(5-2)/0.010=300\ \Omega$. The next common value is $330\ \Omega$. It gives $I=3/330=0.0091$ A $=9.1$ mA. Keep one ATmega328P pin at 20 mA or less (the absolute limit is 40 mA).
> - **Breadboard.** In the middle area each short strip of 5 holes is one connected piece of metal. The gap in the centre separates the 5 holes on one side from the 5 holes on the other side. The long lines at the two edges are the power rails. They are connected along the board and are used for 5 V and GND. Two legs in the same 5-hole strip are joined together.
> - **Push button.** Wire it between a pin and GND, and switch on the pull-up resistor of the pin. Not pressed: the pin reads 1. Pressed: the pin is connected to GND and reads 0. Without a pull-up an open pin "floats" and reads random values.
> - **LDR.** The MCU measures a voltage, not a resistance. Put the LDR and a fixed resistor $R_1$ in series from 5 V to GND and read the point between them (a voltage divider). With the LDR on the 5 V side:
> $$V_{out}=V_{cc}\cdot\frac{R_1}{R_{LDR}+R_1}$$
> More light gives a smaller $R_{LDR}$ and so a higher $V_{out}$. With $R_1=R_{LDR}=10\ \text{k}\Omega$: $V_{out}=5\times10/20=2.5$ V.
> - **7-segment display.** Seven LED bars named a to g, plus a dot. Each bar is an LED and needs its own series resistor. Digit 1 lights b and c. Digit 7 lights a, b and c. Digit 8 lights all seven.
> - **Touch sensor module.** Three pins: SIG, VCC, GND (printed on the board in the photo). SIG is a digital output, HIGH while the pad is touched.
> - **USB cable type A-B.** It powers the Uno with 5 V and carries the program upload and the serial data to the PC.

> [!question]- Check (deck A, slide 13a): Which kit part do you take for each job, and is it an input or an output of the MCU? (1) measure how bright the room is, (2) show the digit 5, (3) detect a finger without a moving part, (4) build the circuit without soldering.
> (1) LDR. Input, read as a voltage with the ADC.
> (2) 7-segment display. Output, driven from output pins.
> (3) Touch sensor. Input, a digital signal that is HIGH while touched.
> (4) Breadboard. It is neither input nor output, it only holds and connects the parts.

> [!question]- Check (deck A, slide 13b): A yellow LED has $V_f=2.1$ V. You want about 15 mA from a 5 V pin. Compute the resistor. You have 150, 220 and 330 Ω. Which one do you take, and what current flows then?
> Formula: $R=(V_{cc}-V_f)/I$.
> Voltage across the resistor: $5-2.1=2.9$ V.
> $R=2.9/0.015=193.3\ \Omega$.
> Take the next larger value, $220\ \Omega$: $I=2.9/220=0.0132$ A $=13.2$ mA. That is a little under 15 mA and safe.
> $150\ \Omega$ would give $2.9/150=19.3$ mA, more than wanted and close to the 20 mA limit. $330\ \Omega$ gives $2.9/330=8.8$ mA, dimmer than needed.

> [!question]- Check (deck A, slide 13c): Find the mistake. A student puts both legs of an LED into the same 5-hole strip of the breadboard. A 330 Ω resistor goes from the 5 V pin to that strip, and a wire goes from that strip to GND. The LED stays dark. Why, and what current flows?
> The 5 holes of one strip are connected. So both LED legs are at the same voltage: the strip is a short circuit across the LED. The voltage across the LED is 0 V and no current goes through it.
> The current goes from the pin through the resistor and the wire to GND: $I=5/330=0.0152$ A $=15.2$ mA.
> Fix: put the two legs in two different strips (or across the centre gap), with the longer leg (anode) on the side of the resistor and 5 V.

> [!question]- Check (deck A, slide 13d): An LDR is on the 5 V side of a voltage divider, with $R_1=10\ \text{k}\Omega$ to GND. In the dark the LDR has 40 kΩ, in bright light 2.5 kΩ. Compute $V_{out}$ for both cases.
> Formula: $V_{out}=V_{cc}\cdot R_1/(R_{LDR}+R_1)$.
> Dark: $V_{out}=5\times10/(40+10)=5\times0.2=1.0$ V.
> Bright: $V_{out}=5\times10/(2.5+10)=5\times0.8=4.0$ V.
> More light gives a higher voltage, because the LDR takes a smaller share of the 5 V.

> [!question]- Check (deck A, slide 13e): Each segment of the 7-segment display has its own 330 Ω resistor and $V_f=2$ V at 5 V. How much current does the display take for the digit 7 and for the digit 8? Why is one shared resistor for all segments a bad idea?
> One segment: $I=(5-2)/330=9.09$ mA.
> Digit 7 lights a, b, c: $3\times9.09=27.3$ mA.
> Digit 8 lights all seven: $7\times9.09=63.6$ mA.
> With one shared resistor the total current is fixed and is split between the lit segments. Then the digit 8 (seven segments) is much dimmer than the digit 1 (two segments).

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

> [!question]- Check (deck B, slides 2-3): Which of these are embedded systems by the definition on slide 3: a laptop, the controller of a washing machine, a web server, the anti-lock brake unit of a car? Why are embedded units produced in billions and desktops only in millions?
> Embedded: the washing machine controller and the anti-lock brake unit. Each is a computer built into a device and the user does not see it as a computer.
> Not embedded: the laptop and the server. They are general-purpose "desktop" type computers.
> One household or one car holds perhaps hundreds of embedded computers but only a few desktops, and each embedded unit costs much less.

> [!question]- Check (deck B, slide 4): Take "anti-lock brakes" and "temperature controllers" from the list. For each, name one thing the computer reads (sensor) and one thing it drives (actuator).
> Anti-lock brakes: reads the wheel speed sensors, drives the valves that lower the brake pressure.
> Temperature controller: reads a temperature sensor, drives a heater (or a cooler) through a relay.
> Both follow the same pattern: sensor, decision in software, actuator.

> [!question]- Check (deck B, slide 5): A normal PC controls a manufacturing assembly line. Slide 5 calls it an embedded system. Why, although slide 3 says "other than a desktop computer"?
> What counts is the job, not the box. The PC runs dedicated software and has interfaces to the line, so it does one fixed task inside a bigger system.
> Nobody uses it as a general-purpose computer. In the same way the small processor inside a keyboard is embedded: it only does keyboard control.

> [!question]- Check (deck B, slide 6): Sort by the right column of slide 6: (a) a microwave oven, (b) the control computers of an aircraft linked by an on-board network, (c) the cash machines of a bank across a country.
> (a) Local: one device at one place, like the appliance on the slide.
> (b) Locally distributed: several computers in one vehicle, linked by a local network (LAN).
> (c) Geographically distributed: many units far apart, linked over a wide network, like the telephone network.

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

> [!question]- Check (deck B, slide 7): Read the figure. Through which block does the environment send information into the system, and through which block does the system act on the environment? Which three things are inside the "hardwired unit", and which block talks to the user?
> In: the sensors (arrow from the left). Out: the actuators (arrow to the right).
> Hardwired unit: application-specific logic, timers, A/D and D/A conversion. This is fixed hardware, not software.
> The user is reached through the I/O block (top left). Memory and processor exchange data with each other and with the hardwired unit.

> [!question]- Check (deck B, slide 8): Slide 8 names an accelerometer for airbag control. Follow the four steps of the slide for this system: sensor, data conversion, decision-making, actuator.
> Sensor: the accelerometer measures how fast the car slows down. It gives an analog voltage.
> Data conversion: an A/D converter turns this voltage into a number.
> Decision-making: the program compares the number with a crash threshold.
> Actuator: if it is above the threshold, the system fires the airbag igniter.
> "System on a chip" means all these parts (processor, memory, converter, logic) are on one chip.

> [!question]- Check (deck B, slide 9): Slide 9 has two kinds of functions. Which kind is (a) cruise control in a car, (b) the data compression in a modem? Which design criterion is most important for a heart pacemaker?
> (a) Monitoring and control: it measures the speed and controls the engine.
> (b) Information processing: it only transforms data.
> Pacemaker: safety and reliability. A failure can kill, so these come before performance or usability.

> [!question]- Check (deck B, slide 10): Find the mistake: "Real-time means that the processor is very fast."
> Real-time means that certain results must be ready in time, without delay, when the environment changes. It is about meeting a time limit, not about raw speed.
> A slow 8-bit MCU is real-time if it always answers within the limit (for example an airbag within a few milliseconds). A fast PC that sometimes pauses is not.

> [!question]- Check (deck B, slide 11): Read the block diagram of the camera chip. Which two blocks convert between analog and digital? Which block would send pictures to a PC over a serial line? Why does the slide say "reactive and real-time only to a small extent"?
> A2D (analog to digital) after the CCD sensor, and D2A (digital to analog) at the output.
> The UART is the serial unit.
> The camera reacts only when the user presses the button. Nothing breaks if saving a picture takes a little longer, so there are few hard time limits.

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

> [!question]- Check (deck B, slides 12-15): Classify by the slides: (a) a TV remote with an 8-bit MCU and no operating system, (b) a device with a 32-bit MCU and a DSP that runs an RTOS, (c) a system with multicore 64-bit processors, programmable logic and a high performance RTOS. Where does the STM32F407 of this course fit?
> (a) Small scale: 8 or 16 bit, low cost, an operating system is not needed.
> (b) Medium scale: 16/32 bit processors and DSPs, with a GPOS or an RTOS.
> (c) Large scale: 32 or 64 bit RISC or multicore processors with programmable logic, highly complex hardware and firmware.
> The STM32F407 is a 32-bit MCU. "32 bit" is on slide 14 (16/32) and also on slide 15 (32 or 64), so the data width alone does not decide. It is one low-cost single-core MCU that runs without an operating system or with a small RTOS, not a multicore or reconfigurable system. So it is medium scale.

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

> [!question]- Check (deck B, slide 16): Count in the two figures. How many separate chips does the microprocessor system need for CPU, RAM, ROM, I/O port, timer and serial port? How many does the microcontroller need? What connects the blocks in both figures?
> Microprocessor system: 6 chips (the CPU plus five separate parts), wired together on a circuit board.
> Microcontroller: 1 chip. The same six blocks are inside one package.
> In both figures the blocks are connected by buses. In the MPU system the buses run on the board between chips, in the MCU they are inside the chip.

> [!question]- Check (deck B, slide 17): A data logger is finished, and then the customer wants four times more RAM. With which kind of system can you do this without changing the processor chip, and what price do you pay for this property?
> With a microprocessor system. RAM, ROM and I/O are external, so you choose the amount by the requirement and can swap or add a RAM chip.
> The price: more chips and a bigger board. The slide calls this "bulkier". It also means more cost and more power.
> A microcontroller has a fixed amount of RAM on the chip. For more RAM you must change to another MCU.

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

> [!info] The fetch, decode, execute cycle of slide 18 (beyond the slides)
> The right figure on slide 18 has no explanation. Read it from the bottom.
> - The **PC** (program counter) holds the address of the next instruction in program memory.
> - **Fetch**: the CPU reads the instruction at that address. The PC then moves on to the following instruction.
> - **Decode**: the instruction decoder finds out which operation it is and which registers it uses.
> - **Execute**: the ALU takes the values from the registers (R1, R2, ...), computes, and the result goes back into a register.
> - The **flags** keep facts about the last result, for example "the result was zero" or "there was a carry". A later instruction can test them to decide a jump.
> - RISC means "reduced instruction set computer": few, simple instructions, and each one does one small step.

> [!info] The numbers behind slides 19 to 21 (beyond the slides)
> - **Default 1 MHz.** A new chip uses the internal 8 MHz oscillator and divides it by 8: $8\ \text{MHz}/8=1\ \text{MHz}$.
> - **Input levels.** "$V_{cc}/2$" is a rule of thumb. The datasheet promises a 1 only above $0.6\,V_{cc}$ and a 0 only below $0.3\,V_{cc}$. At $V_{cc}=5$ V these are 3.0 V and 1.5 V. Between them the result is not guaranteed.
> - **I2C, 128 devices.** An I2C address has 7 bits, so there are $2^7=128$ addresses.
> - **Timer width.** An $n$-bit timer counts from 0 to $2^n-1$ and then starts again at 0 (overflow). 8-bit: 0 to 255, that is 256 steps. 16-bit: 0 to 65535, that is 65536 steps.
> - **ADC, 10 bit.** The ADC turns a voltage between 0 V and the reference $V_{ref}$ (5 V on the Uno) into a number from 0 to $2^{10}-1=1023$:
> $$\text{code}=\left\lfloor\frac{V_{in}}{V_{ref}}\cdot1024\right\rfloor$$
> One step is $5/1024=0.00488$ V $=4.88$ mV. The 6 channels are the pins ADC0 to ADC5 (PC0 to PC5 in the pinout of slide 22).

> [!question]- Check (deck B, slide 18): The instruction "add R2 to R1" is next in the program. Put the blocks of the right figure in the order in which they work, and say what each one does for this instruction.
> 1. PC: gives the address of the instruction.
> 2. Memory and instruction fetch: the instruction is read from program memory at that address.
> 3. Instruction decoder: it recognises "add" and selects the registers R1 and R2.
> 4. ALU: it adds the two register values.
> 5. Registers and flags: the sum is written back to R1, and the flags are updated (for example the carry flag if the sum did not fit in 8 bits).

> [!question]- Check (deck B, slide 19a): In which of the three memories of slide 19 is each item kept, and what happens to it at power-off? (a) the compiled program, (b) a loop counter, (c) a calibration value that the device measured once and must still know next week.
> (a) Program ROM (flash). It stays.
> (b) RAM. It is temporary data and is lost.
> (c) EEPROM. It is "permanent but critical data", it stays, and the program itself can write it.

> [!question]- Check (deck B, slide 19b): By slide 19 the master clock is 8 MHz and the CPU clock is 1 MHz by default. What is the division factor? How long is one CPU clock period at 1 MHz and at the Uno's 16 MHz, and how many clock periods fit into 1 ms at 16 MHz?
> Factor: $8\ \text{MHz}/1\ \text{MHz}=8$.
> Period $T=1/f$. At 1 MHz: $T=1/10^6\ \text{s}=1\ \mu\text{s}$.
> At 16 MHz: $T=1/(16\times10^6)\ \text{s}=62.5$ ns.
> In 1 ms: $16\times10^6\times0.001=16\,000$ periods.

> [!question]- Check (deck B, slide 20a): $V_{cc}=5$ V. A pin is an input. What does it read at 3.4 V and at 1.2 V by the rule of slide 20? The same pin is then an output and the program writes logic 1. What voltage is on the pin? What is the threshold if the chip runs at 3.3 V?
> Threshold: $V_{cc}/2=5/2=2.5$ V.
> 3.4 V is above 2.5 V: logic 1. 1.2 V is below 2.5 V: logic 0.
> Output logic 1 = supply voltage = 5 V. Output logic 0 = ground = 0 V.
> At $V_{cc}=3.3$ V the threshold is $3.3/2=1.65$ V.
> "$V_{cc}/2$" is the simple rule of the slide, not an exact threshold (beyond the slides). The datasheet guarantees logic 1 only from $0.6\,V_{cc}=3.0$ V up and logic 0 only from $0.3\,V_{cc}=1.5$ V down (at 5 V). Here 3.4 V is above 3.0 V and 1.2 V is below 1.5 V, so both readings are also guaranteed. A level such as 2.7 V would not be guaranteed.

> [!question]- Check (deck B, slide 20b): Choose USART, SPI or I2C by the slide: (a) send measured values to a terminal program on the PC, (b) read a fast memory chip a few centimetres away, (c) connect five sensors with as few wires as possible. Can one program use all of them?
> (a) USART: "typically for communication with PC".
> (b) SPI: very fast, over a short distance, for example with memories.
> (c) I2C: many devices share the same bus of only two wires (see the Notes), up to 128 by the slide.
> Yes. They are three separate hardware units in the chip, and all three can be used at the same time.

> [!question]- Check (deck B, slide 20c): Timer 0 is 8-bit and Timer 1 is 16-bit. Each counts one step per clock period at 16 MHz. After how much time does each one overflow?
> Steps until overflow: $2^8=256$ and $2^{16}=65\,536$.
> Time = steps / frequency.
> Timer 0: $256/(16\times10^6)=16\ \mu\text{s}$.
> Timer 1: $65\,536/(16\times10^6)=4.096$ ms.
> The 16-bit timer can measure a time 256 times longer.

> [!question]- Check (deck B, slide 21a): The ADC has 10-bit resolution and the reference is 5 V. How many different values can it give? How large is one step? Which number does it give for an input of 3.3 V?
> Values: $2^{10}=1024$ (0 to 1023).
> Step: $5/1024=4.88$ mV.
> Code: $3.3/5\times1024=675.84$, rounded down: **675**.
> Check: $675\times5/1024=3.296$ V, just under 3.3 V.

> [!question]- Check (deck B, slide 21b): A program blinks an LED and must also react to data on the serial port. Without interrupts it must ask "is there data?" again and again. What changes with an interrupt, and what does the slide mean by "free microcontroller for other useful tasks"? Which unit raises the interrupts "a counter value being reached" and "an analog-to-digital conversion finishing"?
> With an interrupt the hardware watches for the event. When a byte arrives, the CPU stops the main program for a moment, runs a specific piece of code (the interrupt service routine), and then continues where it stopped.
> So the main program does not have to check all the time. It is free to do other work, here the blinking.
> "Counter value reached" comes from a timer/counter. "Conversion finishing" comes from the ADC.

> [!question]- Check (deck B, slide 22a): Read the block diagram and the pinout. How many I/O pins do the three ports have together? Confirm the number from the 28 pins of the package. Which package pin is PB5, and what is its second job?
> Block diagram: PORT D (8) + PORT B (8) + PORT C (7) $=23$ I/O pins.
> Pinout: 28 pins minus the 5 supply and reference pins (VCC, GND, GND, AREF, AVCC) $=23$. The same.
> PB5 is package pin 19. Its second job is SCK, the clock line of SPI.
> On the Uno three of the 23 are taken: PB6 and PB7 by the 16 MHz crystal (XTAL1, XTAL2) and PC6 by RESET. That leaves $23-3=20$ pins.

> [!question]- Check (deck B, slide 22b): The diagram shows 32K flash and 2K SRAM. A program wants the array `uint16_t buf[1500];`. Does it fit? What is the largest number of such elements that could ever fit in the SRAM?
> One `uint16_t` is 2 bytes. The array needs $1500\times2=3000$ bytes.
> SRAM: $2\times1024=2048$ bytes. $3000>2048$, so it does not fit.
> Upper limit: $2048/2=1024$ elements, and only if nothing else used the SRAM. In practice it is fewer, because other variables and the stack need room too.

> [!question]- Check (deck B, slide 23): The slide writes `PORTB = 42;`. All port B pins are outputs. Which pins are HIGH after this line? Which number makes only PB0 and PB5 HIGH? If only PB2 and PB4 are HIGH, what does `thePins = PINB;` give?
> $42=32+8+2=2^5+2^3+2^1=0\text{b}00101010$. Bits 5, 3 and 1 are set, so PB5, PB3 and PB1 are HIGH.
> PB0 and PB5: $2^0+2^5=1+32=33$ ($0\text{b}00100001$).
> PB2 and PB4: $2^2+2^4=4+16=20$, so `thePins` is 20 ($0\text{b}00010100$).
> One 8-bit register holds the state of all 8 pins of a bank, one bit per pin.

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
