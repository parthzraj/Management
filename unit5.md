
Let's understand these three components in very simple language, with examples you can remember for your quiz.

# 1. ADC — Analog-to-Digital Converter

## First, understand the problem

Imagine you connect a temperature sensor to Arduino. The sensor produces a voltage depending on the temperature.

For example:

- Low temperature → 1 V
- Medium temperature → 2.5 V
- High temperature → 4 V

But the Arduino's microcontroller works with digital numbers (0s and 1s). It cannot directly process a continuously varying voltage as a number.

So, it needs an ADC.

ADC converts an input voltage into a digital number that Arduino can understand.

Sensor

Produces an analog voltage, e.g. 2.5 V

ADC

Measures the voltage relative to a reference

Digital number: approximately 512

Arduino can now process this value

## What does 10-bit resolution mean?

The Arduino Uno R3 has a 10-bit ADC.

A bit can have two values: 0 or 1.

With 10 bits, the number of possible combinations is:

\\[ 2^{10}=1024 \\]

Therefore, the ADC can produce 1024 different output codes, numbered from 0 to 1023.

| Input voltage (approx.) | ADC reading (approx.) |
| ----------------------- | --------------------- |
| 0 V                     | 0                     |
| 1.25 V                  | 256                   |
| 2.5 V                   | 512                   |
| 3.75 V                  | 767                   |
| 5 V                     | 1023                  |

These examples assume a 5 V reference and an input voltage within the ADC's allowed range. Actual readings can vary slightly.

### Why does the maximum reading equal 1023, not 1024?

There are 1024 possible values, but counting starts from zero.

So the values are:

`0, 1, 2, 3, ... 1022, 1023`

That gives 1024 values in total.

## How is the ADC reading calculated?

For a reference voltage of 5 V, the approximate formula is:

\\[ \text{ADC reading} \approx \frac{V\_{\text{in}}}{5}\times1023 \\]

Here:

- \\(V\_{\text{in}}\\) = input voltage from the sensor.
- \\(5\\) = reference voltage in volts.
- \\(1023\\) = maximum ADC output code.

Let's solve the example from your notes.

Suppose the sensor produces 2.5 V.

Step 1: Divide the input voltage by the reference voltage.

\\[ \frac{2.5}{5}=0.5 \\]

Step 2: Multiply by 1023.

\\[ 0.5\times1023=511.5 \\]

Step 3: The ADC returns an integer reading near 512.

Therefore, `analogRead()` returns approximately 512.

## Where is ADC used in Arduino?

Whenever Arduino needs to measure a varying analog voltage, ADC is useful.

Examples:

- A potentiometer used to control LED brightness.
- A light sensor measuring light intensity.
- An analog temperature sensor.
- A joystick producing varying voltages.

Example code:

```
int value = analogRead(A0);
```

This reads the analog voltage connected to pin A0 and stores the digital result in `value`.

Memory trick: ADC = Analog voltage → Digital number.

# 2. ICSP header — In-Circuit Serial Programming

## What is it?

The ICSP header is a group of pins on the Arduino board that allows a compatible programmer to communicate directly with the microcontroller for programming.

Think of it as an alternative programming connection.

Normally, you write code on your computer and upload it through USB. However, a compatible external programmer can use the ICSP connections to program the microcontroller directly.

## Understand it with an example

Imagine your Arduino has a program that controls a traffic light.

The usual method is:

1. Write the code on your computer.
2. Connect Arduino to the computer using USB.
3. Click Upload in the Arduino IDE.
4. The program is transferred to the microcontroller.

Alternatively, a compatible programmer can connect through the ICSP header and program the microcontroller without using the normal USB upload route.

### Two ways to program Arduino

Normal method

Computer → USB → Arduino

Alternative method

External programmer → ICSP header → Microcontroller

## What does the ICSP header contain?

The standard 6-pin ICSP header exposes these connections:

| Pin signal | Function                                                      |
| ---------- | ------------------------------------------------------------- |
| MISO       | Data sent from the target microcontroller to the programmer   |
| MOSI       | Data sent from the programmer to the target microcontroller   |
| SCK        | Clock signal that coordinates communication                   |
| RESET      | Allows the target microcontroller to be reset for programming |
| VCC        | Supply-voltage connection                                     |
| GND        | Ground connection                                             |

MISO, MOSI and SCK are the main SPI signals used for this programming method.

### Does ICSP only program Arduino?

No. Its connections can also provide access to SPI-related signals for compatible devices. The exact use depends on the board and connected hardware.

### Is ICSP the same as USB?

No.

- USB: The normal, convenient way to upload sketches and communicate with a computer.
- ICSP: A direct programming interface used with a compatible external programmer.

Memory trick: ICSP = a direct route to program the microcontroller.

# 3. AREF pin — Analog Reference

## First, understand the reference voltage

Remember that ADC converts a voltage into a number.

But how does it know whether a voltage is small or large?

It compares the input voltage against a reference voltage.

Think of the reference voltage as the maximum voltage represented by the ADC's full-scale reading.

For example, imagine the reference is 5 V.

- 0 V produces a reading near 0.
- 2.5 V produces a reading near 512.
- 5 V produces a reading near 1023.

Now imagine your sensor only produces voltages between 0 V and 1 V.

If the ADC still uses a 5 V reference, it uses only about one-fifth of its available measurement range.

This is where a suitable lower reference can help.

## What does AREF do?

AREF stands for Analog Reference.

It is a pin that can provide an external reference voltage to the ADC when the Arduino's ADC reference is configured appropriately.

For example, suppose you have a sensor whose output ranges from 0 V to 1 V.

Using a suitable 1.1 V reference, where supported and configured correctly, lets the ADC use more of its output range for that sensor.

Compare the approximate readings:

| Sensor voltage | With 5 V reference | With 1.1 V reference |
| -------------- | ------------------ | -------------------- |
| 0 V            | 0                  | 0                    |
| 0.5 V          | 102                | 465                  |
| 1.0 V          | 205                | 930                  |

The exact values depend on the reference voltage and ADC behaviour.

Notice that 0.5 V gives a much larger digital reading with the lower reference. This means the ADC can distinguish smaller voltage changes more effectively over that particular input range.

Important: A lower reference does not change the sensor's voltage. It changes how the ADC maps that voltage to a digital number.

## How is AREF used in code?

On a standard Uno R3, an external reference can be selected using:

```
analogReference(EXTERNAL);
```

This tells Arduino to use the voltage applied to AREF as the ADC reference.

The external reference must be connected and configured correctly before taking readings.

Safety rule: Do not apply an arbitrary voltage to AREF, and do not select the external reference without a suitable reference source. Incorrect connections or reference configuration can produce invalid readings or damage the board.

## When is AREF useful?

- Measuring low-voltage sensor outputs.
- Improving the use of the ADC's available resolution over a limited input range.
- Projects requiring a particular ADC reference voltage.

Memory trick: AREF = the voltage scale against which the ADC measures the sensor.

## Final comparison

| Component | Simple meaning                | Main purpose                                               |
| --------- | ----------------------------- | ---------------------------------------------------------- |
| ADC       | Voltage-to-number converter   | Reads analog sensors                                       |
| ICSP      | Direct programming connection | Programs the microcontroller using a compatible programmer |
| AREF      | ADC reference-voltage pin     | Sets the voltage scale used for analog measurements        |

The key relationship is: AREF provides the reference for ADC measurements, ADC converts the input voltage into a number, and ICSP provides an alternative way to program the microcontroller.







# Design and Development: Arduino & Raspberry Pi — Complete Quiz Notes

These notes are based on your three teacher-provided PDFs and the complete unit syllabus. They follow the terminology used in the slides, with additional explanations, examples, architecture details, and important quiz points.

The most important topics to understand are Arduino Uno architecture, the function of every major component, Arduino board types, memory, pins, communication modules, Raspberry Pi architecture and types, and Python programming with interfaces.

## Unit syllabus coverage

1\. Introduction to Arduino and its architecture

Microcontroller, ATmega328P, digital and analog pins, ADC, PWM, memory, clock, reset, power supply, USB, ICSP and AREF.

2\. Arduino board types

Uno, Nano, Micro; differences, specifications, selection and applications.

3\. Modules and wireless communication

ESP8266, NodeMCU, ESP32, HC-05, ESP-01, Wi-Fi, Bluetooth and IoT.

4\. Raspberry Pi

Single-board computer, Pi models, hardware components, interfaces, operating system and comparison with Arduino.

5\. IDE and programming

Arduino IDE, sketch structure, compiling, uploading, Raspberry Pi with Python, GPIO and basic programs.

# Part 1 — Introduction to Arduino

## 1.1 What is Arduino?

Arduino is an open-source electronics platform consisting of programmable hardware boards and software development tools. It is used to build embedded systems that interact with the physical world.

An Arduino board typically contains a microcontroller, which executes a program to read inputs, process information and control outputs.

Examples:

- A temperature sensor detects a high temperature, and Arduino turns on a fan.
- A light sensor detects darkness, and Arduino switches on a lamp.
- A button is pressed, and Arduino activates a motor.
- A soil-moisture sensor detects dry soil, and Arduino starts a water pump.

### The basic working principle

INPUT

Sensors, switches, buttons

PROCESSING — ARDUINO MICROCONTROLLER

Reads input → executes program → makes a decision

OUTPUT

LEDs, displays, buzzers, motor drivers, relays

For example, in an automatic streetlight:

1. A light sensor detects the surrounding light.
2. Arduino reads the sensor value.
3. The program compares the reading with a threshold.
4. If it is dark, Arduino activates the light.
5. If it is bright, Arduino switches the light off.

This is the fundamental input–process–output model of an embedded system.

## 1.2 What is an embedded system?

An embedded system is a computer-based system designed to perform a specific function within a larger device or machine.

Examples include washing machines, digital thermometers, traffic-light controllers, drones and automatic irrigation systems.

An embedded system commonly contains:

- Processor or microcontroller: Executes instructions.
- Memory: Stores the program and data.
- Input interfaces: Receive information from sensors and switches.
- Output interfaces: Control LEDs, motors, displays or other devices.
- Power supply: Provides the electrical energy needed for operation.
- Communication interfaces: Exchange information with other devices when required.

Arduino is useful for embedded systems because it simplifies hardware connections, programming and testing.

## 1.3 What does open-source mean?

Open-source means that the platform's hardware designs and software tools are made available for people to study, modify and reuse under their respective licences.

Its advantages include:

- Lower development cost.
- A large community and many tutorials.
- Reusable libraries and example programs.
- Easy prototyping.
- Support for a wide variety of sensors and modules.

Quiz distinction: **Arduino is a development platform; the ATmega328P is the microcontroller fitted to the standard Arduino Uno R3.**

# Part 2 — Arduino Uno R3 architecture in detail

This is one of the most important topics for your quiz.

## 2.1 What is Arduino Uno R3?

The Arduino Uno R3 is a popular microcontroller development board used for learning electronics, prototyping, robotics and IoT projects.

The standard Uno R3 uses the ATmega328P 8-bit AVR microcontroller as its main controller.

Its typical specifications are:

RISC architecture 

Harvard architecture

| Feature                  | Arduino Uno R3                 |
| ------------------------ | ------------------------------ |
| Main microcontroller     | ATmega328P                     |
| Processor type           | 8-bit AVR                      |
| Operating logic voltage  | 5 V                            |
| Clock frequency          | 16 MHz                         |
| Digital I/O pins         | 14                             |
| Analog input pins        | 6                              |
| PWM-capable digital pins | 3, 5, 6, 9, 10, 11             |
| Flash memory             | 32 KB                          |
| SRAM                     | 2 KB                           |
| EEPROM                   | 1 KB                           |
| UART serial signals      | Digital pins 0 (RX), 1 (TX)    |
| ADC resolution           | 10-bit                         |
| Operating system         | No general-purpose OS required |

These specifications refer to the standard Uno R3, not every board sold under the Arduino name.

## 2.2 Arduino Uno architecture diagram

The following is a functional architecture diagram. It represents how the main blocks interact, rather than the exact physical arrangement of components on the board.

USB PORT / DC POWER JACK / VIN

Power input and programming connection

POWER REGULATION AND CLOCK

Regulates supply voltage; clock provides timing

ATmega328P MICROCONTROLLER

CPU • Control unit • Registers • ALU

Flash

Program

SRAM

Working data

EEPROM

Saved data

INPUT INTERFACES

Digital pins, analog pins, ADC, UART, I²C, SPI

OUTPUT INTERFACES

Digital pins, PWM, UART, I²C, SPI

Sensors and switches provide inputs; LEDs, displays, drivers and other devices receive outputs.

The ATmega328P is the central processing component. The USB interface, power circuit, clock source and programming headers support the microcontroller, while its pins connect it to external devices.

## 2.3 The microcontroller: the brain of Arduino

A microcontroller (MCU) is a compact integrated circuit that contains a processor, memory and peripheral hardware on a single chip.

The ATmega328P contains the following major functional blocks.

### A. CPU — Central Processing Unit

The CPU executes the instructions in the uploaded program.

Its main responsibilities are:

- Fetching instructions from program memory.
- Decoding instructions to determine what they mean.
- Executing instructions.
- Coordinating data movement and peripheral operations.

For example, if the program says to turn on an LED when a button is pressed, the CPU executes the instructions that read the button and control the output pin.

### B. ALU — Arithmetic Logic Unit

The ALU performs arithmetic and logical operations.

Arithmetic operations include addition, subtraction and incrementing a value. Logical operations include comparisons, bitwise AND, OR and XOR.

Example:

```
int temperature = 35;

if (temperature > 30) {
    // The condition is true.
}
```

The processor evaluates the comparison `temperature > 30` using its instruction-execution hardware, including arithmetic and logic circuitry.

Remember: CPU is responsible for executing instructions; the ALU performs arithmetic and logical operations within that processing system.

### C. Control unit

The control unit coordinates the execution of instructions and the operation of the processor's internal components.

It helps determine which operations should occur and when, including the movement of data between registers, memory and peripherals.

### D. Registers

Registers are very small, very fast storage locations used during processing.

They hold temporary values, addresses, instruction-related information and processor status.

The ATmega328P has general-purpose registers and special-purpose registers. Peripheral registers configure hardware functions such as digital I/O, timers, serial communication and ADC operation.

For example, a program can configure a data-direction register to specify whether a digital pin should act as an input or output.

### E. Clock source

The standard Uno R3 uses a 16 MHz clock source to provide timing for the microcontroller.

A 16 MHz clock means approximately 16 million clock cycles per second. **An instruction does not necessarily take exactly one cycle;** the number of cycles depends on the instruction.

The clock helps the CPU and timing-related peripherals coordinate their operations.

Important distinction: Clock frequency is not the same as the number of instructions executed per second.

### F. Timers and counters

Timers and counters are hardware peripherals used to measure time, count events and generate timed signals.

They are useful for:

- Producing PWM signals.
- Measuring the duration of a pulse.
- Generating periodic events.
- Timing operations without relying entirely on software delays.

For example, PWM can be used to vary the average power delivered to an LED or motor driver.

### G. Interrupt system

An interrupt is a mechanism that allows the processor to respond to a particular event by temporarily executing an interrupt service routine.

Interrupts can be generated by certain external events or internal peripherals.

Example: A pulse arriving at a supported input can trigger a response without requiring the program to continuously check the pin in a tight loop.

Interrupts improve responsiveness, but the implementation must be designed carefully; they do not make every task instantaneous.

### H. ADC — Analog-to-Digital Converter

The ADC converts an analog input voltage into a digital number that the program can process.

The Uno R3's ADC has 10-bit resolution, which produces \\(2^{10}=1024\\) possible output codes, from 0 to 1023.

For a typical 5 V analog reference:

\\[ \text{ADC reading}\approx\frac{V\_{\text{in}}}{5}\times1023 \\]

For example, if the input is approximately 2.5 V:

\\[ \frac{2.5}{5}\times1023\approx512 \\]

So `analogRead()` would return a value near 512, assuming the standard reference and a stable input.

This is why a sensor's voltage can be represented as a digital value.

### I. Digital I/O hardware

Digital input/output circuitry allows the microcontroller to read HIGH/LOW signals and drive pins HIGH/LOW.

Examples:

- Reading a push button.
- Turning an LED on or off.
- Sending a digital control signal to a relay module.

A HIGH or LOW reading depends on the configured voltage thresholds and logic level; it does not mean that every input is exactly 5 V or 0 V.

### J. Serial communication peripherals

The ATmega328P provides hardware support for several communication methods:

- UART/USART: Serial communication, used by the Uno's main UART on pins 0 and 1.
- SPI: Synchronous communication often used by displays, memory chips and peripheral modules.
- I²C/TWI: A two-wire bus commonly used for sensors and displays.

These interfaces allow the microcontroller to exchange information with other devices.

## 2.4 Arduino Uno R3 board: physical components and every pin's function

[1.3 Getting to Know Your Arduino Board — SunFounder Beginners Lab Kit  documentation](https://images.openai.com/static-rsc-4/By4kAB7G0iw4Bso8W6cvR1AbJQ85hSwnjhk0ZvH7QDLoLOJ4dzufPfx2XYJu-ywOrAFYc4q2f--4ZPqJsdnE7vxKrYNfLVYTr7lGOQo1H0Ry0qbgcPTjhz_Ot0hGat909Cv2JFH83IQ5s-u9BGsIvfsIgiDIlRO5htzUV8l4RtI?purpose=inline)

[docs.sunfounder.com](https://docs.sunfounder.com/projects/beginners-lab-kit/en/latest/lessons/1.3_know_the_board.html?utm_source=chatgpt.com)

The architecture diagram explains the internal functions. The physical board exposes these functions through connectors, pins and electronic components.

### A. USB port

The USB port connects the Arduino to a computer for programming and serial communication.

Its main functions are:

1. Supplies power to the board when connected to a suitable USB power source.
2. Transfers a compiled program from the computer to the Arduino.
3. Supports serial communication for sending debugging information and receiving data.

Example: You connect the Uno to a laptop, write a program in Arduino IDE and upload it over USB.

The USB connector does not mean the ATmega328P directly understands the entire USB protocol. The Uno R3's separate USB-to-serial interface handles the conversion between USB communication and the microcontroller's serial interface.

### B. DC power jack

The DC power jack accepts an external power supply.

It is useful when the board must operate independently of a computer, such as in a robot or automatic irrigation system.

The board's power circuitry regulates the input voltage as needed.

Safety: Use the board's recommended input voltage. Never assume that any voltage supplied through the jack is safe.

### C. VIN pin

VIN is used to supply an appropriate external input voltage to the board's power circuitry, typically when powering the board without USB.

It is not the same as the regulated 5 V output pin.

For example, a suitable external supply may feed VIN, and the onboard regulator provides the regulated voltage needed by the board.

### D. 5 V pin

The 5 V pin provides the board's 5 V supply rail when the board is powered appropriately.

It can supply suitable low-power external circuits within the board's current limits.

Do not use it to power a high-current motor directly. A motor normally requires a separate suitable driver and power arrangement.

### E. 3.3 V pin

This pin provides a 3.3 V supply for compatible low-power modules.

For example, a sensor that requires 3.3 V may use this supply if its current requirements are within the board's limits.

Important: The Uno's digital logic is normally 5 V. A module powered at 3.3 V is not necessarily safe to connect directly to every 5 V signal.

### F. GND — Ground

GND provides the common electrical reference for the circuit.

Suppose Arduino controls an LED through a resistor. Current must have a complete path through the circuit, including a return path to ground.

Similarly, when Arduino communicates with an external module, both devices usually need a shared ground reference unless their interfaces are appropriately isolated.

Memory aid: Voltage is measured relative to a reference; GND provides that reference in a typical circuit.

### G. Reset button

The reset button restarts the microcontroller.

When reset occurs, the microcontroller begins its startup process and normally runs the uploaded program again.

Example: If the LED blinking program is running and you press reset, the program starts again from its beginning.

Reset does not normally erase the program stored in Flash memory.

### H. Digital pins D0–D13

The Uno has 14 digital input/output pins, numbered 0 through 13.

They can be configured to read digital signals or produce digital output signals.

For example:

```
pinMode(7, INPUT);
pinMode(8, OUTPUT);
```

Here, pin 7 reads an input, while pin 8 controls an output.

- `INPUT`: Reads a digital signal.
- `OUTPUT`: Drives a digital signal.
- `HIGH`: Represents the logical high state.
- `LOW`: Represents the logical low state.

On a standard 5 V Uno, an output configured HIGH is approximately at 5 V under suitable conditions, while LOW is approximately 0 V.

### I. Analog pins A0–A5

The Uno has six analog input pins: A0, A1, A2, A3, A4 and A5.

They are typically used to read varying voltages from sensors.

Examples include:

- Potentiometers.
- Analog temperature sensors.
- Light-dependent resistor circuits.
- Analog output sensors.

```
int sensorValue = analogRead(A0);
```

The function reads the voltage at A0 through the ADC and returns a number from 0 to 1023 under the standard 10-bit conversion configuration.

Unlike a digital input, an analog reading can represent a range of voltage levels.

### J. PWM pins

The PWM-capable pins on the standard Uno R3 are:

3, 5, 6, 9, 10 and 11.

PWM stands for Pulse Width Modulation.

PWM rapidly switches a digital signal between HIGH and LOW while changing the proportion of time it remains HIGH. This proportion is called the duty cycle.

### PWM duty cycle

Duty cycle

## 45%

0%

50%

100%

Illustration of the HIGH/LOW signal pattern. At 0%, the signal stays LOW; at 100%, it stays HIGH.

Average signal level for an ideal 0–5 V PWM waveform: approximately \\(5\times\frac{45}{100}=2.25\text{ V}\\).

The Uno's standard `analogWrite()` uses a value from 0 to 255 to set PWM duty cycle on supported pins.

| `analogWrite()` value | Approximate duty cycle | Typical LED result |
| ----------------------- | ---------------------- | ------------------ |
| 0                       | 0%                     | Off                |
| 64                      | 25%                    | Dim                |
| 128                     | 50%                    | Medium brightness  |
| 192                     | 75%                    | Brighter           |
| 255                     | 100%                   | Fully on           |

PWM does not create a true continuously varying analog voltage at the output pin. It creates a digital pulse waveform. The perceived brightness of an LED changes because of the changing average delivered power. Motor speed can also be controlled using PWM, provided an appropriate motor driver is used.

Quiz trap: On the standard Uno R3, the PWM pins are not all the digital pins. Remember `3, 5, 6, 9, 10, 11`.

### K. RX and TX pins

- RX = Receive: Receives serial data.
- TX = Transmit: Transmits serial data.

On the Uno R3, digital pin 0 is RX and digital pin 1 is TX for the hardware UART.

These pins are used for serial communication with compatible devices.

For example, an Arduino may send sensor measurements to a computer through its serial interface.

Avoid using these pins for unrelated purposes while relying on the same hardware serial interface, because doing so can interfere with communication or uploading.

### L. ICSP header

ICSP stands for In-Circuit Serial Programming.

The ICSP header exposes programming and SPI-related connections used by a compatible external programmer or device.

It can be used to program the microcontroller without relying on the normal USB upload route.

Its role is different from the normal USB connector: USB is the usual beginner-friendly programming and communication interface, whereas ICSP provides a lower-level programming interface.

### M. AREF pin

AREF stands for Analog Reference.

The ADC compares an analog input voltage against its reference voltage. AREF allows an external analog reference to be used when the reference configuration is set up correctly.

For example, a suitable lower reference voltage can improve the effective measurement range for a low-voltage sensor.

Never connect an arbitrary voltage to AREF without configuring the ADC reference appropriately.

### N. Onboard LED

The standard Uno R3 has an onboard LED associated with digital pin 13, also marked `L`.

It can be used to test whether a simple program runs without connecting an external LED.

For example, the classic Blink sketch repeatedly turns this LED on and off.

### O. Crystal or clock oscillator

The standard Uno R3 has a 16 MHz clock source associated with the main microcontroller.

Its purpose is to provide a stable timing reference for instruction execution and hardware peripherals.

It should not be confused with the reset button: the clock determines timing, while reset restarts execution.

### P. USB-to-serial interface

The Uno R3 includes a separate USB interface controller, commonly the ATmega16U2 on the official R3 design.

Its main purpose is to bridge USB communication on the computer side with serial communication to the ATmega328P.

Some compatible Uno boards use a different USB-to-serial chip, so this component can vary by board manufacturer.

### Q. Voltage regulator and power-selection circuitry

The power circuitry helps provide suitable voltage rails to the board from supported input sources.

It allows the board to operate from USB or a suitable external supply, depending on the power arrangement.

It does not make the board immune to incorrect wiring, excessive current, or excessive input voltage.

## 2.5 The three types of memory in Arduino

This is a frequent quiz topic.

The ATmega328P uses three main types of memory: Flash, SRAM and EEPROM.

1\. Flash memory — program storage

## 32 KB

Stores the uploaded program and remains non-volatile when power is removed.

Example: The Blink sketch remains stored after disconnecting the USB cable.

2\. SRAM — working memory

## 2 KB

Stores temporary variables, buffers and runtime data while the program executes.

Example: The current sensor reading stored in `int sensorValue` is held in working memory.

SRAM loses its contents when power is removed.

3\. EEPROM — persistent data storage

## 1 KB

Stores small pieces of data that must survive a power cycle.

Example: A device can save a counter value and retrieve it after being switched off and restarted.

EEPROM is non-volatile, but it has limited write endurance, so avoid unnecessary repeated writes.

### Flash vs SRAM vs EEPROM

| Property                    | Flash           | SRAM                      | EEPROM               |
| --------------------------- | --------------- | ------------------------- | -------------------- |
| Main purpose                | Program code    | Temporary runtime data    | Persistent user data |
| Retains data without power? | Yes             | No                        | Yes                  |
| Used for                    | Uploaded sketch | Variables, stack, buffers | Settings, counters   |
| Typical Uno capacity        | 32 KB           | 2 KB                      | 1 KB                 |
| Quiz keyword                | Code            | Temporary                 | Permanent            |

Memory aid: Flash = Firmware, SRAM = Short-term, EEPROM = Enduring.

Note that Flash also has a small amount reserved for the bootloader on a standard Uno R3, so not all nominal Flash capacity is available to the sketch.

## 2.6 Bootloader: how Arduino accepts a program

A bootloader is a small program stored in a reserved area of Flash memory.

Its purpose is to help the board receive and install a sketch through the usual upload process.

The typical sequence is:

1. You write the program in Arduino IDE.
2. The IDE compiles the source code into machine code.
3. The computer communicates with the board over USB.
4. The bootloader receives the program data.
5. The sketch is written into program Flash.
6. The microcontroller executes the uploaded sketch after startup or reset.

A compatible external programmer can also program the microcontroller using ICSP, potentially without relying on the normal bootloader upload method.

# Part 3 — Analog pins, digital pins, ADC and PWM

## 3.1 Digital signals vs analog signals

A digital signal represents discrete logic states, usually HIGH and LOW.

An analog signal can vary continuously over a voltage range.

[Electrical Waveforms | Tutorials on Electronics | Next Electronics](https://images.openai.com/static-rsc-4/gefdRYK5oCoU79mk3U4QhwYYETm_TViYGBx-iuh6RnXg75SGzXxuGUOtpeAHSA8DvPSS0yiVGo5IJjbyt5IeYyioZtcnNZ-8D5Hm5sUJIeaGSoOaVEP-7mTWIdq2OByPIqeJUP3EjO-J0DzAqdqnM1PJfhdRsXx0Jpcb1bAbs9A?purpose=inline)

Digital signal

Example: A button is pressed or not pressed.

[The Oscilloscope | PPTX](https://images.openai.com/static-rsc-4/DfdHmAmpbm6olR1smIxDB9AfI9RNnLDWJjNuNNaQExyhu9io-ZoOsshmsj2JWroz8I7pENJCiDUywzFaHbZ6JjyovaJgbq9uL8tYLTkKldS6TKTvb0LTTBUd2LyiTF-wOTnUfAmdyi6uruxCzv1mTkBN_RCwAHeWGeD-XiRRlu0?purpose=inline)

Analog signal

Example: A sensor produces a voltage that varies with light intensity.

A digital pin is useful for detecting a binary state. An analog input is useful for measuring a varying voltage.

However, the analog pins on the Uno are not inherently restricted to analog input. They can also function as digital I/O pins, and A4/A5 have I²C functions.

## 3.2 ADC in detail

ADC means Analog-to-Digital Converter.

It converts an analog voltage into a digital representation.

Consider a light sensor connected to A0. As the light changes, the voltage changes. The ADC converts that voltage into a number that the program can use.

For the Uno's 10-bit ADC:

\\[ 2^{10}=1024\text{ levels} \\]

Therefore, the digital result ranges from 0 to 1023.

With a 5 V reference, the approximate voltage represented by one ADC step is:

\\[ \frac{5}{1024}\approx 0.00488\text{ V} \\]

That is about 4.88 mV per step.

The approximate voltage corresponding to a reading of 700 is:

\\[ V\_{\text{in}}\approx\frac{700}{1023}\times5 \approx3.42\text{ V} \\]

This is an approximation; reference accuracy, input noise and ADC characteristics affect real measurements.

### Important ADC terms

- Resolution: Number of possible digital output levels.
- Reference voltage: Voltage used to establish the measurement range.
- Quantization: Mapping a continuous input voltage into one of a finite number of digital codes.
- Sampling: Taking a measurement of the analog input at a particular time.

A higher ADC resolution gives more possible codes, but it does not automatically guarantee greater measurement accuracy.

## 3.3 PWM in detail

PWM means Pulse Width Modulation.

A digital pin alternates between HIGH and LOW. By adjusting how long the signal remains HIGH in each cycle, the controller can change the duty cycle.

\\[ \text{Duty cycle}= \frac{\text{HIGH time}}{\text{Total period}}\times100\\% \\]

Suppose a signal has a period of 10 ms and stays HIGH for 5 ms.

\\[ \text{Duty cycle}=\frac{5}{10}\times100=50\\% \\]

For an LED, a higher duty cycle generally produces greater perceived brightness. For a motor, changing duty cycle changes the electrical power delivered through the driver and can alter motor speed.

Important difference:

- ADC converts an analog voltage to a digital number.
- PWM generates a digital waveform with a controlled duty cycle.

The Uno's `analogRead()` uses the ADC. Its `analogWrite()` on supported PWM pins generates PWM, not a true analog output voltage.

# Part 4 — Arduino board types: Uno, Nano and Micro

Arduino boards differ in physical size, microcontroller, available pins, memory, USB support and intended use.

## 4.1 Arduino Uno

[Arduino Uno Rev3 Microcontroller Programmable Development Board - Walmart.com](https://images.openai.com/static-rsc-4/daLIDqFs0g6eMmhv6IoBQkXAofgTqZN1WkPARBdhoL7HwK87yO24H--3ZPBKzkieQ2qWf7wFupJuScfNurpu3V5_pAr7cNkFCEJYunccQLcXD9xxd1W2fJDZGhTPbM4x4jARbGKSYBpUcnoyYNWGfeBkI0S4DDiC-e2wi-gytFk?purpose=inline)

[walmart.com](https://www.walmart.com/ip/Arduino-Uno-Rev3-Microcontroller-Programmable-Development-Board/133534784?utm_source=chatgpt.com)

The Uno is a general-purpose development board commonly used by beginners and for classroom experiments.

Main characteristics

- Uses the ATmega328P in the standard Uno R3.
- Operates at 5 V logic.
- Has 14 digital I/O pins and six analog inputs.
- Has 32 KB Flash, 2 KB SRAM and 1 KB EEPROM.
- Has a USB connection for programming and serial communication.
- Is convenient for breadboard experiments and prototyping.

Applications: LED control, sensor monitoring, traffic-light models, automatic irrigation and simple robots.

Example: A student connects a temperature sensor to A0 and controls a fan through a suitable driver when the measured temperature exceeds a threshold.

## 4.2 Arduino Nano

[Arduino Nano (Arduino-Italy)](https://images.openai.com/static-rsc-4/PlDgD0T4lE1IQVTjcpKXYL12dFYyBzFPyetW0xqya4C1VpQZ-wDDqWzgZDfYLLAFmZnbqd1v7cfMShBDLihSd_jtqb5vG2mVKPbB72qo9qC60moUr6htXEqYcuGG1egv0vHLoQvVaI1TQRSwBDreBb1Oy60r91Xa4hls8ks4iwE?purpose=inline)

[rhydolabz.com](https://www.rhydolabz.com/arduino-nano-orginal-arduino-italy?utm_source=chatgpt.com)

The Nano is a compact Arduino board designed for projects where space is limited.

The classic Nano uses an ATmega328P, with specifications broadly similar to the Uno's controller, although board revisions and compatible versions can differ.

Main characteristics

- Small physical size.
- Convenient for breadboard mounting.
- Suitable for compact embedded projects.
- Offers digital and analog connections.
- Usually connects through a smaller USB connector on classic versions.

Applications: Wearable prototypes, compact robots, small sensor nodes and embedded projects.

Example: A small robot may use a Nano to read obstacle sensors and send control signals to its motor driver.

## 4.3 Arduino Micro

[Arduino Micro with Headers | Compact USB Dev Board — Arduino Official Store](https://images.openai.com/static-rsc-4/zpklYpOAfD2mGvYmA2b875WlWLUI8i51pzE38CfXnGLwmBAwCKJIz_uO4-pShWSuW4T_MQQgYAJn1yM4brKcltRC-CZ3Ubdh3A8AYSgdRsGjg1RoAdsSwOXg3A2pcNmYpn58ygNXoDDmS57tWULdgwcTeijeLlsSsZ1iWctDjDY?purpose=inline)

[store.arduino.cc](https://store.arduino.cc/products/arduino-micro?utm_source=chatgpt.com)

The classic Arduino Micro uses the ATmega32U4 microcontroller.

Its distinguishing feature is native USB support, which allows the microcontroller to implement USB device functions directly.

For example, it can be programmed to behave like a USB keyboard or mouse.

Applications:

- Custom USB controllers.
- Keyboard and mouse projects.
- Small embedded USB devices.
- Compact interactive electronics.

A Micro may be useful when a project needs to send keyboard or mouse inputs directly to a computer.

## 4.4 Comparison of Uno, Nano and Micro

| Feature                   | Uno R3                                 | Classic Nano                                | Classic Micro     |
| ------------------------- | -------------------------------------- | ------------------------------------------- | ----------------- |
| Main MCU                  | ATmega328P                             | ATmega328P                                  | ATmega32U4        |
| Main advantage            | General-purpose learning               | Compact size                                | Native USB        |
| Physical form             | Larger board                           | Small, breadboard-friendly                  | Compact           |
| Logic voltage             | 5 V                                    | 5 V                                         | 5 V               |
| Typical use               | General prototyping                    | Space-constrained projects                  | USB input devices |
| Native USB device support | Handled through separate USB interface | Typically through a USB-to-serial interface | Supported by MCU  |

Do not assume every Arduino board has identical memory, voltage, USB functions or pin counts. Specifications depend on the exact model and revision.

### How to choose the correct board

- Choose Uno for learning, classroom experiments and general prototyping.
- Choose Nano when the circuit must fit into a smaller space.
- Choose Micro when native USB device functionality is an important requirement.

# Part 5 — Arduino IDE and program execution

## 5.1 What is an IDE?

IDE stands for Integrated Development Environment.

It is software that provides tools for writing, compiling, uploading and debugging programs.

The Arduino IDE provides a convenient interface for developing Arduino sketches.

Its main features include:

1. Code editor.
2. Verify/compile function.
3. Upload function.
4. Serial Monitor.
5. Board and port selection.
6. Library management.

### Important IDE terms

Sketch: An Arduino program or source-code file.

Compile: Convert the program into machine code that the microcontroller can execute, while checking for compilation errors.

Upload: Transfer the compiled program to the board.

Serial Monitor: A tool used to view data sent through a serial connection, such as sensor readings or debugging messages.

Library: Reusable code that provides functions for a device or feature, such as a sensor, display or communication module.

## 5.2 Arduino program structure

A basic Arduino sketch contains two main functions.

```
void setup() {
    // Runs once after startup or reset.
}

void loop() {
    // Runs repeatedly.
}
```

### `setup()`

The `setup()` function runs once after startup or reset.

It is used for initial configuration, such as:

- Setting pin modes.
- Starting serial communication.
- Initializing sensors.
- Configuring displays.

Example:

```
void setup() {
    pinMode(13, OUTPUT);
    Serial.begin(9600);
}
```

Here, pin 13 is configured as an output, and serial communication is initialized at 9600 baud.

### `loop()`

The `loop()` function runs repeatedly while the Arduino is operating normally.

It is used for the main control logic.

Example:

```
void loop() {
    digitalWrite(13, HIGH);
    delay(1000);

    digitalWrite(13, LOW);
    delay(1000);
}
```

The LED turns on for approximately one second and off for approximately one second, then repeats.

## 5.3 Complete example: LED blinking

Components: Arduino Uno, USB cable, and either the onboard LED or an external LED with a suitable series resistor.

```
const int ledPin = 13;

void setup() {
    pinMode(ledPin, OUTPUT);
}

void loop() {
    digitalWrite(ledPin, HIGH);
    delay(1000);

    digitalWrite(ledPin, LOW);
    delay(1000);
}
```

### Line-by-line explanation

| Code                            | Function                                       |
| ------------------------------- | ---------------------------------------------- |
| `const int ledPin = 13;`      | Defines a constant containing the pin number.  |
| `void setup()`                | Declares the one-time initialization function. |
| `pinMode(ledPin, OUTPUT);`    | Configures pin 13 as an output.                |
| `void loop()`                 | Declares the function that repeats.            |
| `digitalWrite(ledPin, HIGH);` | Sets the output HIGH.                          |
| `delay(1000);`                | Waits approximately 1000 milliseconds.         |
| `digitalWrite(ledPin, LOW);`  | Sets the output LOW.                           |
| `delay(1000);`                | Waits again before repeating the cycle.        |

One second equals 1000 milliseconds.

## 5.4 Reading a sensor

Suppose a sensor is connected to analog pin A0.

```
const int sensorPin = A0;

void setup() {
    Serial.begin(9600);
}

void loop() {
    int reading = analogRead(sensorPin);

    Serial.println(reading);

    delay(500);
}
```

The program reads A0, sends the result to the Serial Monitor and waits half a second before the next reading.

On the standard Uno R3, the analog reading is between 0 and 1023.

## 5.5 Controlling an LED using a sensor threshold

```
const int sensorPin = A0;
const int ledPin = 13;

void setup() {
    pinMode(ledPin, OUTPUT);
}

void loop() {
    int reading = analogRead(sensorPin);

    if (reading < 500) {
        digitalWrite(ledPin, HIGH);
    } else {
        digitalWrite(ledPin, LOW);
    }

    delay(50);
}
```

This program illustrates conditional control.

1. Read the sensor.
2. Compare its reading with 500.
3. If the reading is below 500, turn the LED on.
4. Otherwise, turn it off.
5. Repeat.

The value 500 is an illustrative threshold, not a universal light or temperature threshold.

## 5.6 Program upload workflow

### 1

Write

Create the sketch in Arduino IDE.

### 2

Select

Choose the correct board and port.

### 3

Verify

Compile the sketch and identify errors.

### 4

Upload

Transfer the compiled program to the board.

### 5

Execute

The microcontroller runs the uploaded program.

### 6

Observe

Check outputs or use the Serial Monitor.

Exam point: The Arduino IDE runs on the computer. The uploaded sketch is stored in the Arduino's program memory and normally continues executing even after the computer is disconnected, provided the board remains powered.

# Part 6 — Wireless modules: Wi-Fi, Bluetooth and ESP

## 6.1 Why are communication modules required?

A basic Arduino Uno does not have built-in Wi-Fi or Bluetooth connectivity.

A communication module allows it to exchange information with phones, computers, other microcontrollers or network services.

Examples:

- Bluetooth for controlling a small robot using a phone.
- Wi-Fi for monitoring a room's temperature remotely.
- ESP8266 for sending sensor measurements over a network.
- ESP32 for projects requiring wireless connectivity and additional processing capability.

Wireless connectivity is not the same as internet access. Bluetooth can work locally without the internet, while Wi-Fi can connect devices locally or to a wider network, depending on the network setup.

## 6.2 Wi-Fi

Wi-Fi is a wireless networking technology that allows devices to communicate over a local network and, when the network provides it, access the internet.

Example: An Arduino-based temperature monitoring system uses a Wi-Fi module to send measurements to a dashboard.

Typical applications:

- Smart-home devices.
- Remote sensor monitoring.
- Internet-connected appliances.
- IoT dashboards and notifications.

## 6.3 Bluetooth

Bluetooth is a short-range wireless communication technology designed to exchange data between nearby devices.

Example: A phone sends commands to a robot through a Bluetooth serial module.

Typical applications:

- Phone-controlled robots.
- Wireless sensor communication.
- Local control panels.
- Data exchange between nearby devices.

### Bluetooth Classic vs BLE

BLE means Bluetooth Low Energy. It is designed to support low-power wireless communication.

Bluetooth Classic is commonly used for continuous data links and serial-style modules such as the HC-05.

BLE is common in battery-powered sensors, fitness devices and peripherals that transmit small amounts of data efficiently.

Do not assume that a Bluetooth Classic module automatically supports BLE.

## 6.4 ESP8266 and NodeMCU

[Buy Original NodeMCU ESP8266 CP2102 NodeMCU WiFi Serial Wireless Module online at best price | Electronics Dukaan](https://images.openai.com/static-rsc-4/aP0aEP3jFjusD0LV62vHdGN1cQdl-_fweJ5y5fDBiNafHZFkoOEFoLXbpNrPxT8nXleeRnSJ1kN_e0V0Id8ei-YvWjp7aNbHqLky1gnvzH-MOQx_Ed8N62u365dPkwRMBD7C52IRWb-lDo86eNiJzV3FG6Tw1PC3sViqfQSZdi8?purpose=inline)

ESP8266

The ESP8266 is a Wi-Fi-enabled microcontroller platform used in IoT projects.

NodeMCU commonly refers to ESP8266-based development boards or the associated firmware ecosystem. A development board generally exposes power, GPIO and programming connections more conveniently than a bare chip.

Example: A temperature sensor is connected to a NodeMCU board, which sends measurements to a local web dashboard over Wi-Fi.

The ESP8266 can be used in two broad ways:

1. As an independent controller running its own program.
2. As a Wi-Fi communication module associated with another controller, depending on the hardware and firmware.

This distinction matters because an ESP8266 development board is not merely a passive wireless adapter; it has its own processor.

## 6.5 ESP32

[Duinotech ESP32 Main Board with Wi-Fi and Bluetooth® | Jaycar New Zealand](https://images.openai.com/static-rsc-4/jVd_D727dzGr6kYFVPOtV_fn1JWZWsFBwIpfGb5A4eM9Z54jnmaOtZzyOIbxoVNWfm55DLUk6-QWVLnHDIPSPF7qUuCaxIHC8KBZtzJcT2VRSP0obpggJd7n5XwvIlvpasA_lJhByLsBSefvjAxnRqknJc-k9xjAJlk79cpRiGQ?purpose=inline)

ESP32

ESP32 is a family of microcontrollers and modules. Many common ESP32 variants support both Wi-Fi and Bluetooth, although exact wireless capabilities depend on the variant.

Typical uses include wireless sensor networks, smart-home systems, connected robots and IoT devices.

An ESP32 can often replace the combination of an Uno and a separate wireless module when its voltage, GPIO and other requirements fit the project.

## 6.6 ESP-01

[Electrónica Mendoza. MODULO WIFI ESP01 ESP8266 Wifi Esp8266 Esp01](https://images.openai.com/static-rsc-4/ZHDqjZFEPsOJQMfZe6sexIblq_JrneehhPyCd60QKu_k_fdltL7uojzw7rDH_3jAGOiZCgBfgjwT7XnLSHtjACg2zMhAM1kUkVMtjgFsq8zHKb1vOnJOoOhogM3r9b4y0QYqWm7XK0mP4Tm_L_9dMGKHAWLX8pR232aMxVf1gWU?purpose=inline)

ESP-01

ESP-01 is a compact ESP8266-based Wi-Fi module.

It is useful when a project needs a small wireless interface. Its exposed pins are limited compared with larger ESP8266 development boards, and it requires suitable power and serial connections.

It can communicate with another microcontroller using a serial interface, depending on the firmware and configuration.

## 6.7 HC-05 Bluetooth module

[HC 05 Bluetooth Module Versatile Wireless Communication with UART Interface, Master/Slave Mode, Ideal for Arduino and Embedded Projects | Shopping India](https://images.openai.com/static-rsc-4/QeYHwJOsBbAmm3nHBBBBBZeJgIDOaYNCzC0qW91cWMFuiJKRbUSdnD1cpr6by6aVRqvEFbtejEGMvZRXFA6L8JOSQj-HAViu-R0xKwy2Z_IiTgqM7cvG2UK1lbuIzMFQNvIaNlHBjawzKvQtlaIqOluOvrmg43wJEEyJ4i5C7wg?purpose=inline)

HC-05

The HC-05 is commonly used for Bluetooth Classic serial communication.

It provides a wireless serial link between a controller and a compatible Bluetooth device.

Example: A phone sends a character such as `1` or `0`; the Arduino reads the character and switches an output on or off.

Check the exact breakout board's logic-level requirements before connecting its signal pins to a 5 V Uno.

## 6.8 Comparison of wireless modules

| Module            | Technology                           | Main use                                 |
| ----------------- | ------------------------------------ | ---------------------------------------- |
| ESP8266 / NodeMCU | Wi-Fi                                | Network-connected IoT                    |
| ESP-01            | Wi-Fi                                | Compact Wi-Fi interface                  |
| ESP32             | Wi-Fi and Bluetooth on many variants | Wireless embedded projects               |
| HC-05             | Bluetooth Classic                    | Serial communication with nearby devices |

### Wi-Fi vs Bluetooth: quiz distinction

| Feature            | Wi-Fi                                   | Bluetooth                                            |
| ------------------ | --------------------------------------- | ---------------------------------------------------- |
| Primary purpose    | Wireless networking                     | Short-range device communication                     |
| Internet required? | No; a local network can operate offline | No                                                   |
| Common example     | Smart-home dashboard                    | Phone-controlled robot                               |
| Typical range      | Depends on network and environment      | Depends on Bluetooth version, class and environment  |
| Power use          | Varies with hardware and activity       | BLE is specifically designed for low-power operation |

### Important electrical rule

If a sensor or module produces 5 V signals but the receiving controller accepts a maximum of 3.3 V, a suitable level-shifting or voltage-divider circuit may be required.

Never assume that a module's power-input voltage is the same as its GPIO signal voltage.

# Part 7 — Raspberry Pi: introduction and architecture

## 7.1 What is Raspberry Pi?

[Raspberry Pi 4 Model B - 8 GB RAM : Adafruit Industries, Unique &amp; fun DIY electronics and kits](https://images.openai.com/static-rsc-4/T0WGTCpIH-bViSTKRTN-GoyX7n36nKPd-VPu0bSXPLln3qQCfqL8o1InQDLL_ynGJnT7aQQn6TWwiqurJnt1Y7hIbk0iTzj4Uscgous_NMQ-BffAIua36Bm0g0Zq-qcKf_WjWgIYf7KAVuSXV64dsdb53b-5YVPkulB6-UyL-ng?purpose=inline)

The Raspberry Pi is a low-cost, compact single-board computer (SBC).

Unlike the standard Arduino Uno, Raspberry Pi is designed to run a general-purpose operating system, commonly a Linux distribution such as Raspberry Pi OS.

It can execute Python programs, run multiple applications, connect to networks, process images, display graphical interfaces and perform more computationally demanding tasks.

A Raspberry Pi is essentially a complete computer assembled on one circuit board.

It generally contains a processor, RAM, boot storage support, GPIO connections, USB ports, display interfaces and network connectivity, with exact features depending on the model.

### Examples of Raspberry Pi applications

- Image recognition using a camera.
- Home automation servers.
- Web servers and dashboards.
- Robotics with camera processing.
- Network monitoring.
- Python-based data analysis.
- Educational programming.
- Media and display systems.

## 7.2 Why is Raspberry Pi different from Arduino?

The fundamental distinction is that a standard Arduino Uno R3 is a microcontroller board, while a Raspberry Pi is a single-board computer.

| Feature           | Arduino Uno R3                       | Raspberry Pi                                  |
| ----------------- | ------------------------------------ | --------------------------------------------- |
| Main architecture | Microcontroller-based board          | Single-board computer                         |
| Main processor    | ATmega328P, 8-bit AVR                | Model-dependent application processor         |
| Operating system  | No general-purpose OS required       | Typically runs Linux                          |
| Main memory       | 2 KB SRAM                            | Model-dependent RAM, generally much larger    |
| Program execution | Embedded sketch runs directly on MCU | Programs run under an OS                      |
| Programming       | Arduino C/C++ framework              | Python and many other languages               |
| Hardware control  | Direct, predictable MCU pin control  | GPIO through software and OS interfaces       |
| Image processing  | Very limited on standard Uno         | Suitable for many camera and vision workloads |
| Typical strength  | Simple, responsive hardware control  | Networking, computing and multitasking        |

Raspberry Pi is not automatically better for every project. The right choice depends on the work required.

For instance, a microcontroller is often a good fit for immediate sensor reading and motor control, while Raspberry Pi is better suited to processing camera images and running complex software.

## 7.3 Raspberry Pi architecture

The following diagram illustrates the major functional blocks of a typical Raspberry Pi single-board computer. Exact components and interfaces vary across models.

POWER SUPPLY

Provides the required input power

SoC — System on Chip

CPU + graphics and other integrated controllers

RAM

Working data and active programs

Boot storage

microSD or supported storage

OPERATING SYSTEM

Linux kernel, device drivers, services and applications

Connectivity

USB, Ethernet, Wi-Fi, Bluetooth (model-dependent)

I/O interfaces

GPIO, I²C, SPI, UART, camera and display interfaces

### A. SoC — System on Chip

The SoC is the central integrated circuit on the Raspberry Pi.

It contains the main processor and typically integrates graphics and other controllers.

The CPU executes instructions, manages computations and runs the operating system and applications.

Unlike the Uno's ATmega328P, a typical Raspberry Pi application processor is designed to run a much more complex software environment.

### B. CPU — Central Processing Unit

The CPU runs programs and processes instructions.

It handles tasks such as:

- Executing Python code.
- Running the operating system.
- Processing files and data.
- Performing image-processing calculations.
- Managing applications and services.

For example, when Raspberry Pi processes a camera image, the CPU executes the relevant program instructions.

### C. RAM

RAM is the temporary working memory of the Raspberry Pi.

It stores active programs, their data, and information needed by the operating system.

For example, a Python program that loads an image into memory uses RAM to hold the image data while processing it.

RAM is volatile, meaning its contents are not retained after power is removed.

Difference from Arduino: A typical Raspberry Pi has far more working memory than the Uno's 2 KB SRAM, making it suitable for operating systems and more demanding applications.

### D. Storage: microSD and other supported devices

Many Raspberry Pi models use a microSD card to store the operating system, applications and files. Other models support different boot-storage options.

Storage differs from RAM:

- RAM holds active working data.
- Storage retains files and the operating system when power is removed.

For example, a Python script saved on the storage device remains available after the Pi is shut down.

### E. Graphics processor

The GPU handles graphics-related work and, depending on the model and software, can accelerate supported visual and computational tasks.

It is useful for displaying graphical interfaces, video and other supported graphics operations.

Do not assume every AI or image-processing workload automatically runs on the GPU. Software and hardware support determine how work is accelerated.

### F. GPIO header

GPIO means General-Purpose Input/Output.

It provides connections for interacting with external electronics, including sensors, LEDs, buttons and compatible modules.

A GPIO pin may be configured as an input or output, or assigned an alternate peripheral function.

For example, Python can read the state of a button connected to a GPIO input.

Critical safety distinction: Raspberry Pi GPIO is generally 3.3 V logic and is not 5 V tolerant. Do not connect a 5 V digital signal directly to a Pi GPIO pin.

### G. USB ports

USB ports allow connection of external devices such as keyboards, mice, storage devices and other USB peripherals.

For example, a keyboard and mouse can be used to operate the Raspberry Pi desktop.

### H. HDMI or display output

Supported models provide display outputs, commonly HDMI-based, for connecting a monitor.

This allows Raspberry Pi to operate as a desktop computer with a graphical user interface.

The exact number and form of display connectors depend on the model.

### I. Ethernet

Ethernet provides wired network connectivity on models that include an Ethernet port.

It can connect Raspberry Pi to a router, switch or other compatible network.

Applications include web servers, local dashboards and networked automation.

### J. Wi-Fi and Bluetooth

Many Raspberry Pi models provide wireless networking and Bluetooth connectivity, but availability depends on the model.

Wi-Fi can provide access to local networks and the internet. Bluetooth supports nearby wireless peripherals and compatible devices.

### K. Camera and display interfaces

Supported Raspberry Pi boards provide camera and display interfaces, such as CSI and DSI on applicable models.

These can be used to connect compatible camera modules or displays.

Example: A camera supplies images to a Raspberry Pi application that performs object detection.

### L. Power input

The power input supplies electrical energy to the board.

Different models have different power requirements and connector arrangements. An insufficient or unsuitable supply can cause instability, unexpected restarts or peripheral problems.

### M. Operating system

A typical Raspberry Pi boots an operating system from supported storage.

The OS manages memory, files, processes, device drivers and networking. It allows several applications to run and share hardware resources.

For example, Raspberry Pi can run a Python sensor-monitoring script while also hosting a local web dashboard.

## 7.4 Raspberry Pi board types

Your teacher's slides specifically identify Pi 3, Pi 4, Pi 5 and Pi Zero.

[Raspberry Pi 3 - Model B - ARMv8 with 1G RAM : Adafruit Industries, Unique &amp; fun DIY electronics and kits](https://images.openai.com/static-rsc-4/qBXX4UN6XfUWISh1me_PM4p-HgJUElGKwADWXEkUng8CdkdfJoV1xJU0DsFBz9W3JBcs4hLrMRpD99uOEjo764HTVRTzpE4Dt8NDsY9nxQjb8ZS0JgL6ecIxypz1xibLYm4XSY7agsD9zp0UZLGdF665v09P0PdI0roU1zV7Myk?purpose=inline)

Raspberry Pi 3

A compact general-purpose computer suitable for learning Linux, Python programming, network projects and lightweight automation. Capabilities vary across Pi 3 revisions.

[Raspberry Pi 4 Model B - 1GB, 2GB, 4GB, 8GB RAM - Thingbits India](https://images.openai.com/static-rsc-4/FoSntc9B3EO2fW0WxfMGITJT_NtxpQD1CZr9cJ_2hH0GjaTp_VfzskqdFtRys_IqHk2gC7Bvuqr1-6nnsqPBSB4-hDkKN0GRfqFmDIPWeZsLlGS0Ivg9-fOMIHvXzoTJ49Yjq1RX1r1eV3tdab_Z-4Eqe7lomHRAp7t8OoZjs0E?purpose=inline)

Raspberry Pi 4

Provides stronger computing capability than earlier Pi generations and is suitable for desktop use, networking, Python applications and many image-processing projects.

[Get your Raspberry Pi 5 first — Raspberry Pi Official Magazine](https://images.openai.com/static-rsc-4/llYiQK0LKpvYy-VaybgqhQfxjDTalcsmxf2okSg_szgTpvLAH127kbGrKxB1GG8ZrAkpdCRhIoqDSzhLRu_y0KYeFVYKGhbbrIpQ4KRa0U_cyayt5Es2llo_8tPCJqKLlCNu_f4EK6FXk0Nr_D3HeKs1vSIdIefrC3WwGgBwvHI?purpose=inline)

Raspberry Pi 5

A newer generation with improved processing and I/O capability compared with Raspberry Pi 4. It is useful for more demanding computing, desktop and hardware-interface projects.

[Raspberry Pi Zero W Basic Kit - PiShop.ca](https://images.openai.com/static-rsc-4/TeNBOOzEQvfrxVyY8X3aSy6PnLJBYezAVA3ZLSm8A5hOrStgPmCsqDqa7rGm8XInPkM5GJUxH41AcBTV8fKyF78ezY0rKMgyGZWVfb5CH2mlIkl8gScb8xKbWngJNdCW4mh1ISH8mjYvnNPNqd9O-56vZSRvFRMG6na0xuuzri4?purpose=inline)

Raspberry Pi Zero

A smaller, lower-power family designed for compact and embedded projects. It is suitable when space and power consumption matter more than maximum processing capability.

### How do you choose a Raspberry Pi?

- For basic learning and lightweight automation, an older model may be sufficient.
- For a general desktop or more demanding Python applications, a suitable Pi 4 or Pi 5 configuration may be preferable.
- For small embedded projects, consider a Pi Zero variant.
- For camera, AI or networking projects, check the exact model's processing, RAM, interface and software requirements.

Model names alone are insufficient to establish exact RAM, ports or wireless capabilities. Check the particular board revision when those details matter.

# Part 8 — Raspberry Pi interfaces

An interface is a means by which two components or systems exchange data or interact.

For Raspberry Pi, interfaces may be physical connectors, electrical signalling protocols or software interfaces.

## 8.1 GPIO

GPIO pins can be configured as inputs or outputs.

Example uses:

- Reading a push button.
- Controlling an LED.
- Reading a digital sensor.
- Sending a control signal to a compatible driver.

GPIO pins do not provide unlimited current and should not directly power high-current loads.

## 8.2 I²C

I²C stands for Inter-Integrated Circuit.

It is a synchronous serial communication bus that commonly uses two signal lines:

- SDA: Serial Data.
- SCL: Serial Clock.

I²C allows a controller to communicate with multiple compatible devices on a shared bus using addressing.

Example: Raspberry Pi communicates with a temperature sensor and an OLED display over the same I²C bus, provided their addresses and electrical requirements are compatible.

Memory aid: I²C = two main bus signal lines, data and clock.

## 8.3 SPI

SPI stands for Serial Peripheral Interface.

It is a synchronous serial interface commonly using:

- SCLK: Serial clock.
- MOSI: Master Out, Slave In.
- MISO: Master In, Slave Out.
- CS/SS: Chip Select or Slave Select.

SPI is commonly used with displays, ADCs, memory devices and other peripherals.

A separate chip-select signal is commonly used to select the target peripheral.

Memory aid: SPI = clock + data out + data in + device selection.

## 8.4 UART

UART stands for Universal Asynchronous Receiver/Transmitter.

It provides asynchronous serial communication, commonly using TX and RX.

- TX transmits data.
- RX receives data.

UART is often used to connect controllers, serial modules and debugging equipment.

The communicating devices must use compatible serial settings, including baud rate and data format.

## 8.5 USB

USB provides a standardized interface for connecting compatible peripherals.

On a Raspberry Pi, USB is commonly used for keyboards, mice, storage devices and other supported equipment.

It is different from GPIO: USB is a defined device interface, while GPIO provides individual general-purpose signals.

## 8.6 Ethernet and Wi-Fi

These interfaces allow Raspberry Pi to communicate with networks.

Examples include accessing a web server, transferring data, retrieving remote information and hosting a local application.

Wi-Fi is wireless; Ethernet is wired.

## 8.7 HDMI

HDMI is used to transmit supported digital audio/video signals to a display.

For example, Raspberry Pi can display a graphical desktop on a monitor.

## 8.8 Camera interface

A compatible camera interface allows Raspberry Pi to capture images or video.

This is useful for computer vision, object recognition, monitoring and robotics.

## 8.9 Interface comparison table

| Interface        | Main signal or purpose         | Example                 |
| ---------------- | ------------------------------ | ----------------------- |
| GPIO             | Digital input/output           | Button and LED          |
| I²C             | SDA and SCL                    | Sensor and OLED display |
| SPI              | Clock, data and chip select    | High-speed peripheral   |
| UART             | TX and RX                      | Serial module           |
| USB              | Standard peripheral connection | Keyboard or storage     |
| Ethernet         | Wired networking               | Local server            |
| Wi-Fi            | Wireless networking            | Remote monitoring       |
| HDMI             | Digital display output         | Monitor                 |
| Camera interface | Camera data                    | Vision application      |

Important: I²C, SPI and UART are communication protocols/interfaces. USB, HDMI and Ethernet are also interfaces, but they serve different purposes and use different signalling standards.

# Part 9 — Raspberry Pi with Python programming

## 9.1 Why Python is used

Python is a high-level programming language that is widely used on Raspberry Pi because it is readable, has a large ecosystem of libraries and supports hardware interaction, networking, data processing and automation.

Python can be used to:

- Read sensor values through supported interfaces.
- Control compatible GPIO pins.
- Communicate over I²C, SPI and UART.
- Process images and video.
- Store data in files or databases.
- Build dashboards and network applications.

Unlike a typical Uno sketch, a Python program on Raspberry Pi normally runs under an operating system.

## 9.2 Python development environment

The main tools include:

Raspberry Pi OS: The operating system that manages the computer's resources.

Python interpreter: Executes Python instructions.

IDE or code editor: A program used to write and edit Python code.

Terminal: A command-line interface used to run commands and programs.

Libraries: Reusable code that provides features such as GPIO access, camera support and data analysis.

A typical workflow is:

1. Install or boot a suitable Raspberry Pi OS.
2. Open a Python editor or terminal.
3. Write a `.py` program.
4. Save the program.
5. Run it with Python.
6. Observe the result.
7. Connect compatible hardware and test the interface code.

For example, a Python file called `hello.py` can be run from a terminal with:

```
python3 hello.py
```

## 9.3 First Python program

```
print("Hello, Raspberry Pi!")
```

The `print()` function displays text in the program's output.

This example verifies that Python code can run, but it does not interact with physical hardware.

## 9.4 Python variables and data types

A variable stores a value that a program can use.

```
temperature = 32.5threshold = 30if temperature > threshold:    print("Temperature is high")else:    print("Temperature is normal")
```

Explanation:

- `temperature` holds the measured or example temperature.
- `threshold` stores the comparison value.
- `if` checks a condition.
- `else` defines the alternative action.
- `print()` displays the result.

Because 32.5 is greater than 30, the program prints `Temperature is high`.

Common Python data types include:

| Type      | Example                     | Purpose               |
| --------- | --------------------------- | --------------------- |
| `int`   | `count = 10`              | Whole numbers         |
| `float` | `temperature = 25.5`      | Decimal values        |
| `str`   | `name = "Sensor"`         | Text                  |
| `bool`  | `is_on = True`            | True/false conditions |
| `list`  | `readings = [20, 22, 24]` | Collection of values  |

## 9.5 Reading a sensor: general approach

The code depends on the sensor and its electrical interface.

A digital sensor may provide a HIGH/LOW output, whereas an analog sensor produces a varying voltage.

Important difference from Arduino: Raspberry Pi models generally do not provide a built-in general-purpose analog input equivalent to the Uno's six ADC channels. To read an analog voltage, a compatible external ADC or sensor interface is usually required.

For example, a compatible external ADC can convert an analog sensor voltage into a digital value that Raspberry Pi reads using I²C or SPI.

The general data flow is:

Analog sensor

External ADC

Converts analog voltage into a digital value

Raspberry Pi

Reads the value over a supported interface

Python program

Processes the reading and decides what to do

## 9.6 Example: control an LED from Raspberry Pi

The following example uses the commonly available `gpiozero` Python library and a GPIO pin on a compatible Raspberry Pi.

Use an LED with a suitable series resistor, connected to the selected GPIO and GND. Check the board's GPIO numbering and electrical limits before wiring.

```
from gpiozero import LEDfrom time import sleepled = LED(17)while True:    led.on()    sleep(1)    led.off()    sleep(1)
```

### Line-by-line explanation

| Code                         | Function                                                           |
| ---------------------------- | ------------------------------------------------------------------ |
| `from gpiozero import LED` | Imports the LED control class.                                     |
| `from time import sleep`   | Imports a function for pausing execution.                          |
| `led = LED(17)`            | Creates an LED object associated with GPIO 17 using BCM numbering. |
| `while True:`              | Starts a loop that continues until the program is stopped.         |
| `led.on()`                 | Turns the LED output on.                                           |
| `sleep(1)`                 | Waits one second.                                                  |
| `led.off()`                | Turns the LED output off.                                          |
| `sleep(1)`                 | Waits another second.                                              |

This program is conceptually similar to the Arduino Blink program, but the language, runtime and hardware access method differ.

## 9.7 Python versus Arduino programming

| Feature             | Arduino sketch                                       | Raspberry Pi Python                        |
| ------------------- | ---------------------------------------------------- | ------------------------------------------ |
| Typical source file | `.ino`                                             | `.py`                                    |
| Language            | Arduino C/C++ framework                              | Python                                     |
| Main runtime        | Microcontroller executes sketch                      | Python runs under the OS                   |
| Startup structure   | `setup()` and `loop()`                           | Execution begins at the first statement    |
| Repetition          | `loop()` repeats automatically                     | Use loops such as `while`                |
| Pin access          | `pinMode()`, `digitalRead()`, `digitalWrite()` | GPIO libraries and supported interfaces    |
| Analog reading      | Built-in ADC on Uno                                  | Usually external ADC required              |
| Multitasking        | Usually application-controlled MCU execution         | OS schedules processes and threads         |
| Common strength     | Direct hardware control                              | General computing and software integration |

## 9.8 Important programming concepts

Condition: A logical test that determines which code executes.

Loop: Repeats a set of instructions.

Function: A reusable block of code.

Library: Reusable functionality supplied by external or standard code.

Variable: A named location or reference used to hold a value.

Interface library: Software that helps the program communicate with hardware through a supported interface.

For quiz purposes, understand not just the definitions but also why each is used in an embedded project.

# Part 10 — Combining Arduino and Raspberry Pi

The two platforms can work together in a single embedded system.

## 10.1 Why use both devices?

Arduino is suitable for reading sensors and controlling hardware with a compact microcontroller program. Raspberry Pi is suitable for higher-level computing, networking and image processing.

A combined design assigns each task to the platform that handles it most appropriately.

ARDUINO

Reads sensors • Applies control logic • Operates compatible motor drivers

Communication link

USB serial, UART or another supported interface

RASPBERRY PI

Processes images • Runs Python applications • Stores data • Provides networking and dashboards

### Example: smart surveillance robot

1. A camera captures an image.
2. Raspberry Pi processes the image to identify an object.
3. Raspberry Pi sends a command to Arduino.
4. Arduino controls a motor driver to move the robot.
5. Arduino reads local sensors and can report their values to Raspberry Pi.

The division of work can reduce the burden on each device.

However, a Raspberry Pi application running under Linux does not automatically provide hard real-time guarantees. Critical safety actions should be handled locally and independently where required.

## 10.2 Arduino vs Raspberry Pi vs ESP32

| Requirement                                            | Suitable starting point   |
| ------------------------------------------------------ | ------------------------- |
| Simple sensor control                                  | Arduino Uno               |
| Compact microcontroller project                        | Arduino Nano              |
| Native USB keyboard/mouse project                      | Arduino Micro             |
| Wi-Fi-connected sensor node                            | ESP8266 or ESP32          |
| Wi-Fi and Bluetooth on one controller                  | A suitable ESP32 variant  |
| Camera image processing                                | Raspberry Pi              |
| Python-based web dashboard                             | Raspberry Pi              |
| Low-level motor control combined with image processing | Arduino plus Raspberry Pi |

The final choice depends on the specific model, required interfaces, electrical compatibility, timing needs, memory and processing workload.

# Part 11 — Practical design concepts and common quiz scenarios

Your teacher's question sheet includes application-based questions, not only definitions. These concepts are worth revising.

## 11.1 Voltage-level compatibility

If a sensor generates 0–5 V but the receiving controller accepts only up to 3.3 V, connecting the signal directly can damage the receiving device.

A suitable voltage-divider or level-shifting circuit may be required.

For example, a 5 V Uno digital output should not be connected directly to a Raspberry Pi GPIO pin.

Remember: Power-supply voltage and signal logic voltage are related but distinct considerations.

## 11.2 Motor drivers and power supply

A microcontroller pin is designed for logic signals, not for directly powering a high-current motor.

Motors can draw substantial current, especially during startup or when stalled. The motor current can also cause the supply voltage to dip.

A suitable design should consider:

- Motor-driver current and voltage ratings.
- Motor startup and stall current.
- Battery or power-supply capability.
- Common ground where required.
- Suitable decoupling and wiring.
- Correct PWM configuration.

If an Arduino repeatedly restarts when a motor starts, investigate the supply voltage, current capacity, wiring and driver arrangement before rewriting the program.

## 11.3 Hysteresis

Hysteresis is a control technique that uses different activation and deactivation thresholds to prevent rapid switching near one threshold.

Example: A fan turns on at 30°C and turns off at 27°C.

- At 31°C, the fan turns on.
- At 29°C, it remains on.
- At 28°C, it remains on.
- At 26°C, it turns off.

Without hysteresis, a noisy sensor fluctuating around 30°C might cause the fan to turn on and off repeatedly.

## 11.4 Offline operation and synchronization

An IoT device does not always need a working internet connection to perform local control.

A suitable design can:

1. Read sensors locally.
2. Make essential control decisions locally.
3. Store measurements temporarily.
4. Continue operating if the internet fails.
5. Synchronize stored records after connectivity returns.

This is an example of offline-capable or store-and-forward operation.

The principle is especially important for smart locks, industrial control and monitoring systems.

## 11.5 Safety and processing responsibilities

Suppose Raspberry Pi processes camera images while Arduino monitors a safety switch.

If the camera program becomes slow, the safety response should not depend entirely on that program. Critical actions should be handled locally and independently as required.

For example, an Arduino can stop a motor when a safety input is triggered, while Raspberry Pi continues image processing.

For real machinery, the actual safety function must be designed and validated to the appropriate safety requirements; a classroom microcontroller prototype alone is not a safety-certified system.

# Part 12 — Teacher's quiz: all 20 questions with answers

Your first PDF contains 20 questions. Use this section to test yourself after studying the notes.

## Practice quiz

0/20 answered

1\. Which Arduino feature is primarily used to control LED brightness and motor speed?

A. ADC

B. PWM

C. EEPROM

D. AREF

2\. Which microcontroller is used in the standard Arduino Uno R3?

A. ATmega328P

B. ATmega32U4

C. ESP8266

D. BCM2711

3\. Which memory retains stored data after power is removed and is suitable for permanent data storage?

A. SRAM

B. Cache

C. EEPROM

D. Register

4\. What component converts analog voltage into a digital value?

A. PWM generator

B. ADC

C. DAC

D. UART

5\. What serial communication signals are associated with Uno digital pins 0 and 1?

A. SDA and SCL

B. RX and TX

C. MOSI and MISO

D. AREF and GND

6\. What is the standard operating logic voltage of Arduino Uno R3?

A. 1.8 V

B. 3.3 V

C. 5 V

D. 12 V

7\. What is the main architectural difference between Arduino Uno and Raspberry Pi?

A. Both are single-board computers running Linux

B. Uno is a microcontroller board; Raspberry Pi is a single-board computer

C. Raspberry Pi cannot run an OS

D. Uno always runs Linux

8\. What is the full form of ICSP?

A. Integrated Circuit Serial Programming

B. Internal Computer Signal Processing

C. Integrated Control System Protocol

D. Input Circuit Serial Port

9\. Which memory stores the program code on a standard Arduino Uno?

A. SRAM

B. Flash

C. Cache

D. Register

10\. Name either of the two popular ESP modules used to add Wi-Fi connectivity.

A. ESP8266

B. ESP-01

C. Either ESP8266 or ESP-01

D. HC-05 only

11\. A sensor reading changes correctly, but LED brightness does not. What should be checked first?

A. Whether the LED control pin supports PWM

B. Replace the EEPROM

C. Increase analog pins

D. Install Linux on Arduino

12\. A 0–5 V sensor feeds a controller accepting at most 3.3 V. What circuit may be needed?

A. Voltage multiplier

B. Voltage level-shifting circuit

C. PWM generator only

D. Clock divider only

13\. A smart lock must continue making local decisions if Wi-Fi fails. Which design is best?

A. All decisions in cloud

B. Local microcontroller for essential locking logic

C. Disable sensors

D. Bluetooth as the only power source

14\. An Arduino restarts whenever a motor starts. Which power problem should be investigated first?

A. Insufficient supply current or voltage drop

B. Too much EEPROM

C. Too few analog pins

D. Incorrect display resolution

15\. Raspberry Pi performs image recognition while Arduino reads sensors and controls motors. What is the main advantage?

A. Arduino gains unlimited RAM

B. Pi no longer needs an OS

C. Computational processing and hardware control are separated

D. Both become the same processor

16\. Which non-volatile Uno memory is suitable for a small counter value updated occasionally?

A. SRAM

B. EEPROM

C. CPU register

D. Cache

17\. A drone's motor speed changes slowly and inconsistently. What should be investigated before changing the algorithm?

A. Motor-driver capability, battery voltage and PWM configuration

B. Raspberry Pi screen resolution

C. Erase EEPROM

D. Change analog pin into a power pin

18\. What control technique uses different activation and deactivation thresholds to reduce rapid switching?

A. Hysteresis

B. Quantization

C. Bootloading

D. Multiplexing

19\. Raspberry Pi processes camera frames while Arduino must react immediately to a safety switch. What is the safest design principle?

A. Depend entirely on camera processing

B. Handle critical safety locally and independently

C. Send every decision to cloud

D. Store switch state only in EEPROM

20\. A wireless sensor network works offline and synchronizes records after connectivity returns. What capability describes this design?

A. Offline-first operation with store-and-forward synchronization

B. Permanent cloud dependency

C. Real-time image rendering

D. Volatile-only storage

Check answers

# Part 13 — Last-minute revision sheet

## Essential facts to memorize

| Question                                         | Answer                           |
| ------------------------------------------------ | -------------------------------- |
| Uno R3 microcontroller                           | ATmega328P                       |
| Uno logic voltage                                | 5 V                              |
| Uno clock frequency                              | 16 MHz                           |
| Number of digital I/O pins                       | 14                               |
| Number of analog input pins                      | 6                                |
| PWM-capable pins                                 | 3, 5, 6, 9, 10, 11               |
| ADC resolution                                   | 10-bit                           |
| ADC output range                                 | 0–1023                          |
| `analogWrite()` range on Uno PWM pins          | 0–255                           |
| Flash memory                                     | 32 KB                            |
| SRAM                                             | 2 KB                             |
| EEPROM                                           | 1 KB                             |
| Pin 0                                            | RX                               |
| Pin 1                                            | TX                               |
| A4 and A5                                        | SDA and SCL for I²C             |
| ICSP                                             | In-Circuit Serial Programming    |
| AREF                                             | Analog Reference                 |
| `setup()`                                      | Runs once after startup/reset    |
| `loop()`                                       | Repeats the main program         |
| ESP8266                                          | Wi-Fi-enabled controller         |
| HC-05                                            | Bluetooth Classic serial module  |
| Raspberry Pi                                     | Single-board computer            |
| Raspberry Pi programming language in your slides | Python                           |
| Raspberry Pi operating system in your slides     | Linux                            |
| Raspberry Pi analog input                        | Usually requires an external ADC |
| Raspberry Pi GPIO logic                          | 3.3 V; not 5 V tolerant          |

## The five distinctions most likely to cause mistakes

1. ADC vs PWM: ADC reads analog voltage; PWM controls a digital waveform's duty cycle.
2. Flash vs SRAM vs EEPROM: Program code, temporary working data, persistent user data.
3. Arduino vs Raspberry Pi: Microcontroller board vs single-board computer.
4. Wi-Fi vs Bluetooth: Wireless networking vs short-range device communication; neither inherently requires internet access.
5. GPIO vs power pins: GPIO carries logic signals; power pins supply electrical power within their limits.

## How to answer an architecture question for full marks

If asked, “Explain the architecture of Arduino Uno R3,” organize the answer in this order:

1. Introduction: Explain that Uno is a microcontroller-based development board.
2. Main controller: Identify ATmega328P and describe CPU, ALU, registers, clock and control functions.
3. Memory: Explain Flash, SRAM and EEPROM with their capacities and purposes.
4. Input interfaces: Explain digital pins, analog pins and ADC.
5. Output interfaces: Explain digital outputs and PWM.
6. Communication: Describe UART, SPI, I²C and the USB-to-serial interface.
7. Power and support components: Explain USB, power jack, VIN, 5 V, 3.3 V, GND, regulator, reset, ICSP and AREF.
8. Working: Explain write → compile → upload → execute → read inputs → process → produce outputs → repeat.
9. Applications: Give examples such as robotics, automatic irrigation and smart-home control.

For each component, state what it is, what it does, and one example of why it is needed. That structure demonstrates understanding rather than merely listing component names.

## Source coverage

The notes follow your teacher's three PDFs: Arduino & Raspberry Pi, Arduino and its application, and Design and Development: Arduino & Raspberry Pi. The first two provide the board components, specifications, applications and quiz questions; the third provides the unit introduction, Arduino architecture, board types, wireless connectivity, Raspberry Pi overview and applications.&#x20;

Arduino & Raspberry Pi.pdf

Arduino and its application.pdf

pdf.pdf

Some topics in your syllabus—particularly detailed Raspberry Pi interfaces and Python programming—receive only brief treatment in the slides, so those sections have been expanded using standard technical explanations. Where hardware details vary by board revision, the notes identify the standard Uno R3 or qualify the feature by model.

Recommended revision order for tonight: Arduino architecture and memory → pins, ADC and PWM → board types and wireless modules → Raspberry Pi architecture and interfaces → Python examples → the 20-question practice quiz.
