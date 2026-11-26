---
course: CMPE 453 Embedded Systems
lesson: CMPE453 L00 - Course intro and lab kit
type: quiz
questions: 31
---

# CMPE453 L00 - Course intro and lab kit - quiz

Lesson note: [[CMPE453 L00 - Course intro and lab kit]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. People, outcomes and topics (L0 slides 2-6)

### Q1 (deck A, slides 3-5)

The topic list of the course has ten topics in this order: AVR architecture, C programming, I/O port programming, serial communication, ADC, interrupts, timers/counters, PWM, ARM architecture, coding the ARM Cortex. Learning outcome 8 is "Convert analogue signal to its digital counterparts / use of quantization". Which statement is correct?

- A) The first eight topics are AVR and the last two are ARM. AVR is programmed in C and ARM in assembly. Outcome 8 matches the topic ADC.
- B) The first eight topics are AVR and the last two are ARM. Both are programmed in embedded C. Outcome 8 matches the topic PWM.
- C) The first eight topics are AVR and the last two are ARM. Both are programmed in embedded C. Outcome 8 matches the topic ADC.
- D) The first five topics are AVR and the last five are ARM. Both are programmed in embedded C. Outcome 8 matches the topic ADC.

> [!success]- Answer
> **C.** Everything before "ARM architecture" is done on the AVR, so eight topics are AVR and two are ARM. Both families are programmed in embedded C. "Analogue to digital" with quantization is the ADC.
> - A: The split and the outcome are right. The language is wrong: the ARM part also uses embedded C.
> - B: PWM makes an output signal with a variable duty cycle. It does not convert an analogue signal into a number.
> - D: Interrupts, timers/counters and PWM come before "ARM architecture" in the list. They are taught on the AVR.

## 2. Books, grading and attendance (L0 slides 7-9)

### Q2 (deck A, slides 8-9)

The grade is $0.32\cdot\text{midterm}+0.50\cdot\text{final}+\text{lab points}$. There are 6 labs with 3 points each and no make-up. Attendance under 50 % gives FX. A student has midterm 60, final 70 and did five of the six labs with full marks. Assume the course has 56 class hours and the student attended 27. What is the weighted total, and what follows?

- A) Total 72.2, and the grade is FX because of the attendance.
- B) Total 69.2, and the attendance rule is met, so the grade follows from the total.
- C) Total 56.9, and the grade is FX because of the attendance.
- D) Total 69.2, and the grade is FX because of the attendance.

> [!success]- Answer
> **D.** Labs: $5\times3=15$ points. Total: $0.32\times60+0.50\times70+15=19.2+35+15=69.2$.
> Attendance: $27/56=0.482=48.2\,\%$. This is under 50 %, so the grade is FX whatever the total is. The student needed $0.5\times56=28$ hours.
> - A: This counts all 18 lab points: $19.2+35+18=72.2$. The missed lab gives 0, there is no make-up.
> - B: The total is right. But 27 of 56 hours is 48.2 %, not 50 %. One hour is missing.
> - C: This weights the lab points a second time: $19.2+35+0.18\times15=56.9$. Lab points are added as they are.

## 3. Labs and software (L0 slides 10-12)

### Q3 (deck A, slides 10-11)

The slides name three programs: Arduino IDE, Tinkercad and STM32CubeIDE. You are at home without the kit and want to test a traffic-light circuit for the Uno. Later you build it in the lab. Which statement is correct?

- A) At home: Tinkercad, it simulates the circuit. In the lab: the Arduino IDE, it uploads the program into the real chip. The Discovery board uses STM32CubeIDE.
- B) At home: the Arduino IDE, it simulates the circuit. In the lab: Tinkercad, it uploads the program into the real chip. The Discovery board uses STM32CubeIDE.
- C) At home: Tinkercad, it simulates the circuit. In the lab: STM32CubeIDE, it uploads the program into the Uno. The Discovery board uses the Arduino IDE.
- D) At home: Tinkercad, it uploads the program over the internet. In the lab: the Arduino IDE, it simulates the circuit. The Discovery board uses STM32CubeIDE.

> [!success]- Answer
> **A.** Tinkercad simulates the Arduino and the circuit in the browser, so no hardware is needed. The Arduino IDE compiles the program and uploads it over USB into the flash of the real ATmega328P. STM32CubeIDE is the IDE for the ARM board.
> - B: The two jobs are swapped. The Arduino IDE needs a real board, and Tinkercad never touches a real chip.
> - C: STM32CubeIDE is for STM32 chips (the Discovery board). The Uno is programmed from the Arduino IDE.
> - D: Tinkercad only simulates, it uploads nothing. The Arduino IDE has no circuit simulator.

## 4. The parts box, AVR versus ARM (L0 slide 13)

### Q4 (deck A, slide 13a)

The kit has LEDs, push buttons, LDRs, a 7-segment display, a touch sensor and a breadboard. Four jobs: (1) measure how bright the room is, (2) show the digit 5, (3) detect a finger without a moving part, (4) build the circuit without soldering. Which assignment is correct? "Input" and "output" are seen from the MCU.

- A) (1) LDR, input. (2) 7-segment display, output. (3) Push button, input. (4) Breadboard, neither input nor output.
- B) (1) LDR, input. (2) 7-segment display, output. (3) Touch sensor, input. (4) Breadboard, neither input nor output.
- C) (1) LDR, output. (2) 7-segment display, output. (3) Touch sensor, input. (4) Breadboard, neither input nor output.
- D) (1) LDR, input. (2) 7-segment display, input. (3) Touch sensor, output. (4) Breadboard, neither input nor output.

> [!success]- Answer
> **B.** The LDR is read as a voltage with the ADC (input). The display is driven from output pins. The touch sensor gives a digital signal that is HIGH while touched (input). The breadboard only holds and connects the parts.
> - A: A push button has a moving part. The job says "without a moving part", so it is the touch sensor.
> - C: The MCU reads the LDR, it does not drive it. A sensor is an input.
> - D: The directions of (2) and (3) are swapped. The MCU drives the display and reads the touch sensor.

### Q5 (deck A, slide 13b)

A yellow LED has $V_f=2.1$ V. It is driven from a 5 V pin through a series resistor $R=(V_{cc}-V_f)/I$. You want the current as close to 15 mA as possible, but not more than 15 mA. You have 150, 220 and 330 Ω. Which resistor do you take, and what current flows then?

- A) 150 Ω, the current is 19.3 mA.
- B) 220 Ω, the current is 13.2 mA.
- C) 330 Ω, the current is 8.8 mA.
- D) 220 Ω, the current is 22.7 mA.

> [!success]- Answer
> **B.** Voltage across the resistor: $5-2.1=2.9$ V. $R=2.9/0.015=193.3\ \Omega$. Take the next larger value, 220 Ω: $I=2.9/220=0.0132$ A $=13.2$ mA. This is a little under 15 mA and safe.
> - A: This takes the next smaller value. $2.9/150=19.3$ mA is more than wanted and close to the 20 mA limit of a pin.
> - C: This forgets $V_f$ in the formula: $5/0.015=333\ \Omega$, so 330 Ω. The real current is then $2.9/330=8.8$ mA. That is allowed, but 220 Ω comes closer to 15 mA (13.2 mA).
> - D: The resistor is right. The current is computed without $V_f$: $5/220=22.7$ mA. The resistor has only 2.9 V across it.

### Q6 (deck A, slide 13c)

In the middle area of a breadboard each strip of 5 holes is one piece of metal. A student puts both legs of an LED into the same 5-hole strip. A 330 Ω resistor goes from the 5 V pin to that strip, and a wire goes from that strip to GND. The LED stays dark. Why, and what current flows from the pin?

- A) The holes of one strip are not connected, so the circuit is open. The current is 0 mA.
- B) The strip is a short circuit across the LED. The current through the resistor is 9.1 mA.
- C) The strip is a short circuit across the LED. No current flows anywhere, so it is 0 mA.
- D) The strip is a short circuit across the LED. The current through the resistor is 15.2 mA.

> [!success]- Answer
> **D.** Both legs are in the same piece of metal, so the voltage across the LED is 0 V and no current goes through it. The current goes through the resistor and the wire to GND: $I=5/330=0.0152$ A $=15.2$ mA.
> Fix: put the two legs in two different strips, with the longer leg (anode) on the 5 V side.
> - A: It is the other way round. The 5 holes of one strip are connected. That is the cause of the problem.
> - B: This subtracts $V_f=2$ V: $(5-2)/330=9.1$ mA. The LED is shorted and takes no voltage, so the resistor has the full 5 V.
> - C: No current goes through the LED, but the path resistor, strip, wire, GND is closed. Current flows there.

### Q7 (deck A, slide 13d)

An LDR is on the 5 V side of a voltage divider, and $R_1=10\ \text{k}\Omega$ goes to GND. The output is the point between them: $V_{out}=V_{cc}\cdot R_1/(R_{LDR}+R_1)$. In the dark the LDR has 40 kΩ, in bright light 2.5 kΩ. What is $V_{out}$?

- A) Dark: 1.0 V. Bright: 4.0 V.
- B) Dark: 4.0 V. Bright: 1.0 V.
- C) Dark: 0.2 V. Bright: 0.8 V.
- D) Dark: 1.25 V. Bright: 20.0 V.

> [!success]- Answer
> **A.** Dark: $5\times10/(40+10)=5\times0.2=1.0$ V. Bright: $5\times10/(2.5+10)=5\times0.8=4.0$ V. More light gives a higher voltage, because the LDR takes a smaller share of the 5 V.
> - B: This puts $R_{LDR}$ in the numerator: $5\times40/50=4.0$ V and $5\times2.5/12.5=1.0$ V. That is the voltage across the LDR, not across $R_1$.
> - C: These are only the ratios 0.2 and 0.8. The factor $V_{cc}=5$ V is missing.
> - D: This forgets $R_1$ in the denominator: $5\times10/40=1.25$ V and $5\times10/2.5=20$ V. A divider can never give more than its 5 V supply.

### Q8 (deck A, slide 13e)

Each segment of the 7-segment display is an LED with its own 330 Ω resistor, $V_f=2$ V, and a 5 V supply. The digit 7 lights the three segments a, b, c. The digit 8 lights all seven segments (no dot). How much current does the display take for each digit?

- A) Digit 7: 27.3 mA. Digit 8: 72.7 mA.
- B) Digit 7: 45.5 mA. Digit 8: 106.1 mA.
- C) Digit 7: 27.3 mA. Digit 8: 63.6 mA.
- D) Digit 7: 18.2 mA. Digit 8: 42.4 mA.

> [!success]- Answer
> **C.** One segment: $I=(5-2)/330=9.09$ mA. Digit 7: $3\times9.09=27.3$ mA. Digit 8: $7\times9.09=63.6$ mA.
> Each segment needs its own resistor. With one shared resistor the total current is fixed and is split between the lit segments, so the digit 8 would be much dimmer than the digit 1.
> - A: The digit 8 is counted with eight LEDs: $8\times9.09=72.7$ mA. The dot is not lit, so it is seven.
> - B: This forgets $V_f$: $5/330=15.15$ mA for one segment, then $\times3$ and $\times7$.
> - D: This puts $V_f$ across the resistor: $2/330=6.06$ mA for one segment. The resistor has $5-2=3$ V.

## 5. What an embedded system is (L1 slides 2-6)

### Q9 (deck B, slides 2-3)

The slide defines an embedded system as a computing system built into an electronic device, "nearly any computing system other than a desktop computer". Which of these are embedded systems by this definition: a laptop, the controller of a washing machine, a web server, the anti-lock brake unit of a car?

- A) The washing machine controller and the anti-lock brake unit.
- B) All four, because every one of them contains a processor.
- C) The laptop and the web server, because they are the most complex.
- D) The washing machine controller, the brake unit and the laptop.

> [!success]- Answer
> **A.** Each of the two is a computer built into a device, and the user does not see it as a computer. The laptop and the server are general-purpose computers of the "desktop" type.
> This also explains the numbers: one household or one car holds many embedded computers but only a few desktops, so embedded units are made in billions.
> - B: A processor alone is not enough. The definition excludes general-purpose computers.
> - C: Complexity is not the criterion. These two are the general-purpose computers that the definition excludes.
> - D: A laptop is portable, but it is still a general-purpose computer of the desktop type.

### Q10 (deck B, slide 4)

The slide lists "anti-lock brakes" and "temperature controllers" as embedded systems. A sensor is what the computer reads, an actuator is what it drives. Which assignment is correct?

- A) Anti-lock brakes: reads the brake pressure valves, drives the wheel speed sensors. Temperature controller: reads a temperature sensor, drives a heater.
- B) Anti-lock brakes: reads the wheel speed sensors, drives the brake pressure valves. Temperature controller: reads the heater, drives a temperature sensor.
- C) Anti-lock brakes: reads the wheel speed sensors, drives the brake pressure valves. Temperature controller: reads a temperature sensor, drives a heater.
- D) Anti-lock brakes: reads the wheel speed sensors, drives the brake pressure valves. Temperature controller: reads a temperature sensor and drives nothing.

> [!success]- Answer
> **C.** Both follow the same pattern: sensor, decision in software, actuator. The brake unit reads the wheel speed and drives the valves that lower the brake pressure. The temperature controller reads the temperature and switches a heater (or a cooler) through a relay.
> - A: Sensor and actuator are swapped for the brakes. A valve is driven, a speed sensor is read.
> - B: Sensor and actuator are swapped for the temperature controller. The heater is the part that is driven.
> - D: A device that only measures is a thermometer. A controller must act on the temperature, so it needs an actuator.

### Q11 (deck B, slide 5)

A normal PC controls a manufacturing assembly line. Slide 5 calls this PC an embedded system, although slide 3 says "other than a desktop computer". What is the reason?

- A) An industrial PC is smaller and cheaper than a desktop PC.
- B) It runs a real-time operating system and not a general-purpose one.
- C) It is connected to a network of other machines in the plant.
- D) It runs dedicated software and does one fixed task inside a bigger system.

> [!success]- Answer
> **D.** What counts is the job, not the box. The PC has dedicated software and interfaces to the line, and nobody uses it as a general-purpose computer. In the same way the small processor inside a keyboard is embedded: it only does keyboard control.
> - A: The question says it is a normal PC. Size and price do not decide.
> - B: The operating system is not the criterion, and the slide does not name one. A PC with a normal operating system can do this job.
> - C: Office desktops are in a network too. A network connection does not make a computer embedded.

### Q12 (deck B, slide 6)

![[cmpe453_lecture_00_lecture1_p006.jpg]]

Sort three systems by the right column of the slide: (a) a microwave oven, (b) the control computers of an aircraft, linked by an on-board network, (c) the cash machines of a bank across a country.

- A) (a) local, (b) geographically distributed, (c) locally distributed.
- B) (a) local, (b) locally distributed, (c) geographically distributed.
- C) (a) locally distributed, (b) local, (c) geographically distributed.
- D) (a) local, (b) locally distributed, (c) locally distributed.

> [!success]- Answer
> **B.** (a) One device at one place, like the appliance on the slide. (b) Several computers in one vehicle, linked by a local network (LAN). (c) Many units far apart, linked over a wide network, like the telephone network.
> - A: (b) and (c) are swapped. An aircraft moves, but its computers sit together on one LAN. The cash machines are far apart.
> - C: A microwave oven is one unit, there is nothing to distribute. The aircraft has several linked computers.
> - D: The cash machines belong to one bank, but they are spread over a country. That is geographically distributed.

## 6. Parts and characteristics (L1 slides 7-11)

### Q13 (deck B, slide 7)

![[cmpe453_lecture_00_lecture1_p007.jpg]]

Read the figure. Which statement is correct?

- A) The environment sends information in through the I/O block. The hardwired unit holds logic, timers and A/D and D/A conversion. The user is reached through the sensors.
- B) The environment sends information in through the sensors. The hardwired unit holds memory, processor and timers. The user is reached through the I/O block.
- C) The environment sends information in through the sensors. The hardwired unit holds logic, timers and A/D and D/A conversion. The user is reached through the I/O block.
- D) The environment sends information in through the actuators. The hardwired unit holds logic, timers and A/D and D/A conversion. The user is reached through the I/O block.

> [!success]- Answer
> **C.** The arrow from the left enters at the sensors, and the arrow to the right leaves at the actuators. The hardwired unit lists application-specific logic, timers, A/D and D/A conversion. The user is at the I/O block (top left).
> - A: I/O and sensors are swapped. In the figure the I/O block has the two arrows to the user, and the sensors face the environment.
> - B: Memory and processor are their own blocks above the hardwired unit. The hardwired unit is fixed hardware next to them.
> - D: The actuators are the way out: the system acts on the environment through them. Information comes in through the sensors.

### Q14 (deck B, slide 8)

Slide 8 describes four steps: sensor, data conversion, decision-making, actuator. Its example is an accelerometer for airbag control. Which row follows the four steps correctly?

- A) Sensor: the accelerometer gives an analog voltage. Conversion: an A/D converter makes a number of it. Decision: the program compares it with a threshold. Actuator: the airbag igniter.
- B) Sensor: the accelerometer gives an analog voltage. Conversion: the program compares it with a threshold. Decision: an A/D converter makes a number of it. Actuator: the airbag igniter.
- C) Sensor: the airbag igniter gives an analog voltage. Conversion: an A/D converter makes a number of it. Decision: the program compares it with a threshold. Actuator: the accelerometer.
- D) Sensor: the accelerometer gives a number. Conversion: a D/A converter makes an analog voltage of it. Decision: the program compares it with a threshold. Actuator: the airbag igniter.

> [!success]- Answer
> **A.** The accelerometer measures how fast the car slows down and gives an analog voltage. The A/D converter turns it into a number. The program compares the number with a crash threshold. Above the threshold the system fires the igniter.
> - B: Steps two and three are swapped. The program cannot compare before the voltage is a number.
> - C: Sensor and actuator are swapped. The igniter is driven, the accelerometer is read.
> - D: The direction of the conversion is wrong. The sensor gives a voltage and the program needs a number, so it is A/D, not D/A.

### Q15 (deck B, slide 9)

Slide 9 has two kinds of functions: "monitoring and control" and "information processing". Its criteria are performance, reliability, availability, safety, usability. Which row is correct for (a) cruise control in a car, (b) the data compression in a modem, (c) the most important criteria for a heart pacemaker?

- A) (a) information processing, (b) monitoring and control, (c) safety and reliability.
- B) (a) monitoring and control, (b) information processing, (c) performance and usability.
- C) (a) monitoring and control, (b) monitoring and control, (c) safety and reliability.
- D) (a) monitoring and control, (b) information processing, (c) safety and reliability.

> [!success]- Answer
> **D.** Cruise control measures the speed and controls the engine. Compression only transforms data. A failure of a pacemaker can kill, so safety and reliability come before the other criteria.
> - A: (a) and (b) are swapped. Cruise control acts on a physical system. Compression does not.
> - B: The functions are right. For a pacemaker speed and comfort come after safety.
> - C: A modem compresses data and controls no physical process. That is information processing.

### Q16 (deck B, slide 10)

Slide 10 names three characteristics of embedded systems: single-functioned, tightly constrained, reactive and real-time. A student says: "Real-time means that the processor is very fast." What does real-time mean?

- A) The processor has a high clock frequency and runs every instruction quickly.
- B) Certain results must be ready within a time limit when the environment changes.
- C) The system runs one program again and again and never another one.
- D) The system must be low cost, low power and small at the same time.

> [!success]- Answer
> **B.** Real-time is about meeting a time limit, not about raw speed. A slow 8-bit MCU is real-time if it always answers within the limit (for example an airbag within a few milliseconds). A fast PC that sometimes pauses is not.
> - A: This is the student's mistake. A fast processor can still miss a deadline.
> - C: This is "single-functioned", another characteristic of the slide.
> - D: This is "tightly constrained", another characteristic of the slide.

### Q17 (deck B, slide 11)

![[cmpe453_lecture_00_lecture1_p011.jpg]]

Read the block diagram of the digital camera chip. Which two blocks convert between analog and digital, and which block would send pictures to a PC over a serial line?

- A) The CCD preprocessor and the pixel coprocessor convert. The UART is the serial unit.
- B) A2D and D2A convert. The ISA bus interface is the serial unit.
- C) The JPEG codec and A2D convert. The UART is the serial unit.
- D) A2D and D2A convert. The UART is the serial unit.

> [!success]- Answer
> **D.** A2D (analog to digital) sits after the CCD sensor, D2A (digital to analog) is at the output. The UART is the serial unit.
> The slide says "reactive and real-time only to a small extent": the camera reacts only when the user presses the button, and nothing breaks if saving a picture takes a little longer.
> - A: These two blocks work on digital pixel data. They do not convert between analog and digital.
> - B: The converters are right. ISA is a parallel bus with many lines, not a serial line.
> - C: The JPEG codec compresses digital picture data. It stays in the digital world.

## 7. Small, medium and large scale (L1 slides 12-15)

### Q18 (deck B, slides 12-15)

The slides classify embedded systems. Small scale: 8 or 16 bit MCU, low performance, low cost, may have no operating system. Medium scale: slightly complex hardware, medium performance, low cost, 16/32 bit MCUs and DSPs, with a GPOS or an RTOS. Large scale: highly complex hardware and firmware, high performance, 32 or 64 bit RISC, multicore or reconfigurable SoC, high performance RTOS. Three systems: (a) a TV remote with an 8-bit MCU and no operating system, (b) a device with a 32-bit MCU and a DSP that runs an RTOS, (c) a system with multicore 64-bit processors, programmable logic and a high performance RTOS. The STM32F407 of this course is one low-cost single-core 32-bit MCU of medium performance, without programmable logic. Which row is correct?

- A) (a) small, (b) medium, (c) large. The STM32F407 is medium scale.
- B) (a) small, (b) medium, (c) large. The STM32F407 is large scale.
- C) (a) small, (b) large, (c) large. The STM32F407 is medium scale.
- D) (a) small, (b) medium, (c) large. The STM32F407 is small scale.

> [!success]- Answer
> **A.** (a), (b) and (c) match the three descriptions directly. "32 bit" appears in medium (16/32) and in large (32 or 64), so the data width alone does not decide. The STM32F407 is one low-cost single-core MCU of medium performance, not a highly complex multicore or reconfigurable system. So it is medium scale.
> - B: This decides by "32 bit" alone. Large scale also means highly complex, high performance hardware. A low-cost MCU of medium performance is not that.
> - C: An RTOS does not make (b) large. Medium scale already names 16/32 bit processors and DSPs with a GPOS or an RTOS.
> - D: Small scale is 8 or 16 bit. A 32-bit MCU is above that, even if it runs without an operating system.

## 8. Microprocessor versus microcontroller (L1 slides 16-17)

### Q19 (deck B, slide 16)

![[cmpe453_lecture_00_lecture1_p016.jpg]]

Count in the two figures. How many separate chips does the microprocessor system need for CPU, RAM, ROM, I/O port, timer and serial port? How many does the microcontroller need? What connects the blocks?

- A) Microprocessor system: 5 chips. Microcontroller: 1 chip. Buses connect the blocks.
- B) Microprocessor system: 6 chips. Microcontroller: 6 chips. Buses connect the blocks.
- C) Microprocessor system: 6 chips. Microcontroller: 1 chip. Buses connect the blocks.
- D) Microprocessor system: 6 chips. Microcontroller: 1 chip. The serial port connects the blocks.

> [!success]- Answer
> **C.** Microprocessor system: the CPU plus five separate parts, $1+5=6$ chips on a circuit board. Microcontroller: the same six blocks are inside one package. In both figures the blocks are connected by buses, on the board or inside the chip.
> - A: This counts only the five parts and forgets the CPU chip itself.
> - B: In the lower figure the six blocks are drawn inside one package with pins. They are parts of one chip.
> - D: The serial port is one of the blocks. It talks to the outside world. Inside, the wide arrow "Buses" connects everything.

### Q20 (deck B, slide 17)

A data logger is finished, and then the customer wants four times more RAM. With which kind of system can you do this without changing the processor chip, and what is the price of this property?

- A) With a microcontroller, because its RAM is on the chip. The price is a more expensive chip.
- B) With a microprocessor system, because its RAM is external. The price is more chips and a bigger board.
- C) With a microprocessor system, because its RAM is external. The price is that it can do only a single task.
- D) With both kinds, because RAM is always a separate chip. There is no price for it.

> [!success]- Answer
> **B.** In a microprocessor system RAM, ROM and I/O are external, so you choose the amount by the requirement and can swap or add a RAM chip. The price: more chips and a bigger board ("bulkier" on the slide), with more cost and more power.
> - A: RAM on the chip means a fixed amount. For more RAM you must change to another MCU.
> - C: The first half is right. "Single task" describes the microcontroller. A microprocessor system is general-purpose.
> - D: A microcontroller has its RAM inside the chip. That is the main difference between the two kinds.

## 9. About the AVR and the ATmega328P (L1 slides 18-23)

### Q21 (deck B, slide 18)

![[cmpe453_lecture_00_lecture1_p018.jpg]]

The instruction "add R2 to R1" is next in the program. In which order do the blocks of the right figure work for this instruction?

- A) PC, then memory and instruction fetch, then instruction decoder, then ALU, then registers and flags.
- B) Memory and instruction fetch, then PC, then instruction decoder, then ALU, then registers and flags.
- C) PC, then memory and instruction fetch, then ALU, then instruction decoder, then registers and flags.
- D) PC, then instruction decoder, then memory and instruction fetch, then ALU, then registers and flags.

> [!success]- Answer
> **A.** The PC gives the address. The instruction is read from program memory at that address (fetch). The decoder recognises "add" and selects R1 and R2. The ALU adds the two values. The sum goes back to R1 and the flags are updated (for example the carry flag).
> - B: Without the address from the PC the CPU does not know which instruction to fetch.
> - C: The ALU cannot work before the decoder has said which operation and which registers.
> - D: The decoder needs the instruction itself. It must be fetched from memory first.

### Q22 (deck B, slide 19a)

Slide 19 names three memories: program ROM (flash), RAM and EEPROM. Where is each item kept, and what happens to it at power-off? (a) the compiled program, (b) a loop counter, (c) a calibration value that the device measured once and must still know next week.

- A) (a) flash, it stays. (b) EEPROM, it stays. (c) RAM, it is lost.
- B) (a) EEPROM, it stays. (b) RAM, it is lost. (c) flash, it stays.
- C) (a) flash, it stays. (b) RAM, it is lost. (c) RAM, it stays.
- D) (a) flash, it stays. (b) RAM, it is lost. (c) EEPROM, it stays.

> [!success]- Answer
> **D.** The program is in flash and stays. A loop counter is temporary data in RAM and is lost. The calibration value is "permanent but critical data": it goes into the EEPROM, it stays, and the program itself can write it.
> - A: (b) and (c) are swapped. A value in RAM is gone next week, and a loop counter does not need to be permanent.
> - B: The program is in the flash, the large program memory. The EEPROM is small and holds data that the program writes.
> - C: RAM loses its content at power-off, so the calibration value cannot stay there.

### Q23 (deck B, slide 19b)

By slide 19 the master clock is 8 MHz and the CPU clock is 1 MHz by default. The Uno runs at 16 MHz. The clock period is $T=1/f$. What is the division factor from master clock to CPU clock, the period at 1 MHz, the period at 16 MHz, and the number of clock periods in 1 ms at 16 MHz?

- A) Factor 8. Periods: 1 µs and 62.5 ns. In 1 ms: 16 periods.
- B) Factor 8. Periods: 1 µs and 6.25 ns. In 1 ms: 160 000 periods.
- C) Factor 8. Periods: 1 µs and 62.5 ns. In 1 ms: 16 000 periods.
- D) Factor 8. Periods: 1 ms and 62.5 µs. In 1 ms: 16 periods.

> [!success]- Answer
> **C.** Factor: $8\ \text{MHz}/1\ \text{MHz}=8$. At 1 MHz: $T=1/10^6\ \text{s}=1\ \mu\text{s}$. At 16 MHz: $T=1/(16\times10^6)\ \text{s}=62.5$ ns. In 1 ms: $16\times10^6\times0.001=16\,000$ periods.
> - A: The periods are right. The count uses 1 µs and not 1 ms: $16\times10^6\times10^{-6}=16$.
> - B: A power-of-ten slip: $1/(16\times10^6)$ s is 62.5 ns, not 6.25 ns. The count is then also ten times too large.
> - D: This reads MHz as kHz: $1/10^3\ \text{s}=1$ ms and $1/(16\times10^3)\ \text{s}=62.5\ \mu\text{s}$.

### Q24 (deck B, slide 20a)

By the rule of slide 20 an input pin reads logic 1 above about $V_{cc}/2$ and logic 0 below. An output pin gives the supply voltage for logic 1 and ground for logic 0. With $V_{cc}=5$ V: what does an input read at 3.4 V and at 1.2 V, what voltage is on an output pin that is written to logic 1, and what is the input threshold if the chip runs at 3.3 V?

- A) 3.4 V reads 1, 1.2 V reads 0. Output logic 1 is 5 V. Threshold at 3.3 V supply: 2.5 V.
- B) 3.4 V reads 1, 1.2 V reads 0. Output logic 1 is 5 V. Threshold at 3.3 V supply: 1.65 V.
- C) 3.4 V reads 1, 1.2 V reads 0. Output logic 1 is 2.5 V. Threshold at 3.3 V supply: 1.65 V.
- D) 3.4 V reads 0, 1.2 V reads 0. Output logic 1 is 5 V. Threshold at 3.3 V supply: 1.65 V.

> [!success]- Answer
> **B.** Threshold at 5 V: $5/2=2.5$ V. 3.4 V is above it (logic 1), 1.2 V is below it (logic 0). Output logic 1 is the supply voltage, 5 V. At $V_{cc}=3.3$ V the threshold is $3.3/2=1.65$ V.
> "$V_{cc}/2$" is the simple rule of the slide. The datasheet guarantees logic 1 from $0.6\,V_{cc}=3.0$ V up and logic 0 from $0.3\,V_{cc}=1.5$ V down, so both readings are also guaranteed.
> - A: The threshold is not fixed at 2.5 V. It is half of the supply, so it moves with $V_{cc}$.
> - C: $V_{cc}/2$ is the input threshold. An output that is written to 1 gives the full supply voltage.
> - D: An input does not need the full 5 V for a 1. Everything above about 2.5 V reads 1.

### Q25 (deck B, slide 20b)

Slide 20 describes three serial units. USART: typically for communication with a PC. SPI: very fast, over a short distance. I2C: many devices on one bus of two wires. Choose a unit for (a) sending measured values to a terminal program on the PC, (b) reading a fast memory chip a few centimetres away, (c) connecting five sensors with as few wires as possible. Can one program use all three units?

- A) (a) USART, (b) SPI, (c) I2C. Yes, all three can be used at the same time.
- B) (a) USART, (b) I2C, (c) SPI. Yes, all three can be used at the same time.
- C) (a) SPI, (b) USART, (c) I2C. Yes, all three can be used at the same time.
- D) (a) USART, (b) SPI, (c) I2C. No, only one unit can be active at a time.

> [!success]- Answer
> **A.** USART goes to the PC. SPI is very fast over a short distance, for example with memories. With I2C many devices share the same two wires. They are three separate hardware units in the chip, so all three can work at the same time.
> - B: (b) and (c) are swapped. SPI needs one extra select wire for each device, so it is not the choice for few wires. I2C is slower than SPI.
> - C: (a) and (b) are swapped. The PC link is the job of the USART, and the fast memory is the job of SPI.
> - D: The choice is right. The last part is wrong: the three units are separate hardware with their own pins.

### Q26 (deck B, slide 20c)

Timer 0 is 8-bit and Timer 1 is 16-bit. An $n$-bit timer overflows after $2^n$ steps. Each timer counts one step per clock period at 16 MHz. After how much time does each one overflow?

- A) Timer 0: 15.94 µs. Timer 1: 4.0959 ms.
- B) Timer 0: 0.5 µs. Timer 1: 1.0 µs.
- C) Timer 0: 16 µs. Timer 1: 4.096 ms.
- D) Timer 0: 16 ms. Timer 1: 4.096 s.

> [!success]- Answer
> **C.** Steps: $2^8=256$ and $2^{16}=65\,536$. Time = steps / frequency. Timer 0: $256/(16\times10^6)=16\ \mu\text{s}$. Timer 1: $65\,536/(16\times10^6)=4.096$ ms. The 16-bit timer can measure a time 256 times longer.
> - A: This uses the largest values 255 and 65 535 as the number of steps. The timer counts from 0, so there are 256 and 65 536 steps.
> - B: This uses the number of bits, 8 and 16, as the number of steps: $8/(16\times10^6)$ and $16/(16\times10^6)$.
> - D: This reads 16 MHz as 16 kHz: $256/16\,000=16$ ms and $65\,536/16\,000=4.096$ s.

### Q27 (deck B, slide 21a)

The ADC has 10-bit resolution and the reference is 5 V. The result is $\text{code}=\lfloor V_{in}/V_{ref}\cdot2^{10}\rfloor$ (rounded down). How many different values can the ADC give, how large is one step, and which number does it give for an input of 3.3 V?

- A) 1023 values, one step is 4.89 mV, the code is 675.
- B) 1024 values, one step is 4.88 mV, the code is 676.
- C) 20 values, one step is 250 mV, the code is 13.
- D) 1024 values, one step is 4.88 mV, the code is 675.

> [!success]- Answer
> **D.** Values: $2^{10}=1024$ (0 to 1023). Step: $5/1024=4.88$ mV. Code: $3.3/5\times1024=675.84$, rounded down: 675. Check: $675\times5/1024=3.296$ V, just under 3.3 V.
> - A: 1023 is the largest code, not the number of values. With 0 there are 1024 values, and the step is $5/1024$, not $5/1023$.
> - B: 675.84 is rounded to the nearest number here. The ADC rounds down.
> - C: This takes $2\times10=20$ and not $2^{10}=1024$. Then the step $5/20$ and the code $3.3/5\times20=13.2$ are wrong too.

### Q28 (deck B, slide 21b)

A program blinks an LED and must also react to data on the serial port. Without interrupts it must ask "is there data?" again and again. What changes with an interrupt, and which units raise the interrupts "a counter value being reached" and "an analog-to-digital conversion finishing"?

- A) The main program still checks for data in every loop, but the check is faster. The first interrupt comes from a timer/counter, the second from the ADC.
- B) The hardware watches for the event. The CPU pauses the main program, runs the service routine and then continues. The first interrupt comes from a timer/counter, the second from the ADC.
- C) The hardware watches for the event. The CPU pauses the main program, runs the service routine and then continues. The first interrupt comes from the ADC, the second from a timer/counter.
- D) The hardware watches for the event. The CPU runs the service routine and then starts the main program again from its first line. The first interrupt comes from a timer/counter, the second from the ADC.

> [!success]- Answer
> **B.** When a byte arrives, the CPU stops the main program for a moment, runs a specific piece of code (the interrupt service routine), and continues where it stopped. So the main program does not have to check all the time: the microcontroller is "free for other useful tasks", here the blinking.
> - A: This is still polling. With an interrupt the main program does not check at all.
> - C: The two sources are swapped. A timer/counter reaches a counter value. The ADC finishes a conversion.
> - D: After the service routine the main program continues at the place where it was stopped. It is not restarted.

### Q29 (deck B, slide 22a)

![[cmpe453_lecture_00_lecture1_p022.jpg]]

Read the block diagram and the pinout. How many I/O pins do the three ports have together? Which package pin is PB5, and what is its second job?

- A) 24 I/O pins. PB5 is package pin 19, its second job is SCK.
- B) 23 I/O pins. PB5 is package pin 17, its second job is MOSI.
- C) 23 I/O pins. PB5 is package pin 19, its second job is SCK.
- D) 28 I/O pins. PB5 is package pin 19, its second job is SCK.

> [!success]- Answer
> **C.** Block diagram: PORT D (8) + PORT B (8) + PORT C (7) $=23$. Check with the pinout: 28 pins minus the 5 supply and reference pins (VCC, GND, GND, AREF, AVCC) $=23$. PB5 is pin 19 and is also SCK, the clock line of SPI.
> On the Uno the crystal takes PB6 and PB7 and RESET takes PC6, so $23-3=20$ pins are left.
> - A: This takes three full ports of 8: $3\times8=24$. PORT C has only 7 pins.
> - B: Pin 17 is PB3 (MOSI / OC2A). PB5 is two rows higher in the pinout.
> - D: 28 is the number of package pins. Five of them are supply and reference pins, not I/O.

### Q30 (deck B, slide 22b)

The ATmega328P has 32K flash and 2K SRAM (1K = 1024 bytes). A program wants the array `uint16_t buf[1500];`. Variables are kept in the SRAM. Does the array fit, and what is the largest number of such elements that could ever fit in the SRAM?

- A) It does not fit: it needs 3000 bytes and the SRAM has 2048. The upper limit is 1024 elements.
- B) It fits: it needs 1500 bytes and the SRAM has 2048. The upper limit is 2048 elements.
- C) It does not fit: it needs 3000 bytes and the SRAM has 2000. The upper limit is 1000 elements.
- D) It fits: it needs 3000 bytes and the memory has 32 768. The upper limit is 16 384 elements.

> [!success]- Answer
> **A.** One `uint16_t` is 2 bytes. The array needs $1500\times2=3000$ bytes. SRAM: $2\times1024=2048$ bytes. $3000>2048$, so it does not fit. Upper limit: $2048/2=1024$ elements, and only if nothing else used the SRAM. In practice it is fewer, because other variables and the stack need room.
> - B: This counts 1 byte for each element. A `uint16_t` has 16 bits, that is 2 bytes.
> - C: The result "does not fit" is right, but 2K is $2\times1024=2048$ bytes, not 2000. So the limit is 1024, not 1000.
> - D: This uses the 32K flash: $32\times1024=32\,768$ bytes. Flash holds the program. A variable array is in the SRAM.

### Q31 (deck B, slide 23)

All port B pins are outputs. One 8-bit register holds the state of the 8 pins, bit $n$ for pin PB$n$. The slide writes:

```c
PORTB = 42;       // writing
thePins = PINB;   // reading
```

Which pins are HIGH after `PORTB = 42;`? Which number makes only PB0 and PB5 HIGH? If only PB2 and PB4 are HIGH, what does `thePins = PINB;` give?

- A) PB5, PB3 and PB1 are HIGH. PB0 and PB5 need 5. `thePins` is 6.
- B) PB5, PB3 and PB1 are HIGH. PB0 and PB5 need 132. `thePins` is 40.
- C) PB5, PB3 and PB1 are HIGH. PB0 and PB5 need 66. `thePins` is 40.
- D) PB5, PB3 and PB1 are HIGH. PB0 and PB5 need 33. `thePins` is 20.

> [!success]- Answer
> **D.** $42=32+8+2=2^5+2^3+2^1=0\text{b}00101010$, so PB5, PB3 and PB1 are HIGH. PB0 and PB5: $2^0+2^5=1+32=33$ ($0\text{b}00100001$). PB2 and PB4: $2^2+2^4=4+16=20$ ($0\text{b}00010100$).
> - A: This adds the pin numbers: $0+5=5$ and $2+4=6$. Each pin has the weight $2^n$, not $n$.
> - B: This reads the bits in the wrong direction, with PB0 as the left bit: $0\text{b}10000100=132$ and $0\text{b}00101000=40$. PB0 is the right bit, with weight 1.
> - C: This counts the bits from 1 and not from 0: $2^1+2^6=66$ and $2^3+2^5=40$. PB0 has the weight $2^0=1$.
