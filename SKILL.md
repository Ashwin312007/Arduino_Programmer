---
name: Arduino_Programmer
description: Arduino firmware engineering skill for board discovery, Arduino IDE/CLI/PlatformIO projects, library selection, custom library design, sensors, communications, timing, interrupts, memory, debugging, hardware safety, and empirical verification.
---

# Arduino Programmer Skill

Arduino_Programmer provides domain-specific engineering rules for Arduino-compatible embedded development.

If a general workflow skill such as `Essential_Skill` is active, that skill controls planning, approval, execution orchestration, verification policy, and Git operations. `Arduino_Programmer` controls Arduino-specific inspection, dependency decisions, implementation, debugging, and hardware verification.

---

## 1. Core Rules

### Rule 1 — Identify the Exact Board and Core

Never assume all Arduino-compatible boards behave like an Arduino Uno.

Determine, preferably from the existing project before asking the user:

- Exact board
- MCU / SoC
- Architecture
- Arduino core / board package
- Core version when available
- IDE or build system
- Upload method / programmer
- Operating voltage
- Relevant pin capabilities

Examples of architectures include:

```text
AVR
SAMD
ESP8266
ESP32
RP2040
STM32
Renesas RA
mbed-based boards
```

Do not use AVR-specific APIs, register names, memory assumptions, voltage levels, interrupt behavior, or pin mappings on another architecture unless verified.

---

### Rule 2 — Inspect Before Modification

Before changing code, inspect the relevant project files.

Look for:

```text
*.ino
*.h
*.hpp
*.c
*.cpp
platformio.ini
arduino-cli.yaml
library.properties
keywords.txt
examples/
src/
```

Also inspect existing:

- Libraries
- Pin definitions
- Constants
- Serial ports
- Bus instances
- Interrupt handlers
- Timers
- State machines
- Build flags
- Board configuration

Do not modify unrelated files or replace working code merely to fit a preferred style.

---

### Rule 3 — Mandatory Library Decision Gate

Before adding a new non-core dependency or implementing a component driver, explicitly ask the user:

```text
Do you want to use an existing library, or build a custom library?
```

Do not choose on the user's behalf.

If the user chooses an existing library:

1. Identify the exact hardware/component and board/core.
2. Research suitable libraries.
3. Prefer official vendor libraries or well-maintained widely used libraries.
4. Verify compatibility with the selected board/core and required features.
5. Confirm API usage from documentation/examples.
6. Avoid adding multiple libraries that solve the same problem unless comparison is requested.

If the user chooses a custom library, the next mandatory question is:

```text
What exactly should the custom library do?
```

Do not begin designing or coding the custom library until the user defines its intended behavior.

After the user explains the required behavior, determine or clarify only the missing details such as:

- Supported hardware
- Required public functions/classes
- Inputs and outputs
- Blocking vs non-blocking behavior
- Communication protocol
- Error handling
- Timing requirements
- Memory constraints
- Whether the library should support one board or multiple architectures

Keep the library API as small as practical.

This decision gate does not apply to Arduino core facilities already intrinsic to the selected board package, such as `pinMode()`, `digitalWrite()`, `millis()`, `Serial`, or `Wire`, unless the user specifically wants to replace or wrap them.

---

### Rule 4 — Use Authoritative Documentation

Prefer documentation in this order:

1. Component datasheet
2. Board documentation and pinout
3. MCU / SoC reference documentation when needed
4. Official Arduino core/API documentation
5. Official vendor library documentation
6. Maintainer repository documentation and examples

Do not infer electrical limits, pin functions, protocol timing, register behavior, or library APIs when they can be verified.

---

### Rule 5 — Hardware Safety First

Before wiring or enabling hardware, verify:

- Board logic voltage
- Sensor / actuator voltage
- GPIO current capability
- Required pull-up / pull-down resistors
- Open-drain requirements
- Level shifting
- Common ground requirements
- Power supply capacity
- Motor / relay / solenoid driver requirements
- Flyback protection where relevant
- Analog input voltage range

Never assume a GPIO can directly power or drive a motor, relay, high-current LED, heater, pump, or solenoid.

Do not enable hazardous actuators automatically during verification.

---

## 2. Task Classification

Classify the task into one or more categories:

```text
BOARD_CORE
PROJECT_SETUP
LIBRARY_EXISTING
LIBRARY_CUSTOM
GPIO
ADC
PWM
TIMER
INTERRUPT
UART_SERIAL
I2C
SPI
CAN
SENSOR
ACTUATOR
MOTOR
DISPLAY
STORAGE
NETWORK
BLE
WIFI
MEMORY
LOW_POWER
STATE_MACHINE
DEBUGGING
OPTIMIZATION
```

Focus inspection and documentation lookup on the active categories.

---

## 3. Project and Board Discovery

For an existing project, establish:

```text
Board:
MCU / SoC:
Architecture:
Core / package:
Build system:
Upload method:
Logic voltage:
Libraries:
Relevant pins:
Relevant peripherals:
```

Prefer reading project configuration rather than asking for information already available.

For PlatformIO, inspect `platformio.ini`.

For Arduino CLI projects, inspect the selected FQBN and configuration where available.

For Arduino IDE sketches, inspect included libraries and code structure.

Do not silently change the target board or core.

---

## 4. Existing Library Workflow

When the user chooses an existing library, evaluate candidates using:

- Board/core compatibility
- Hardware compatibility
- Maintenance activity
- API clarity
- License
- Dependency count
- Memory footprint when relevant
- Example quality
- Support for required operating mode

Prefer a simple library that satisfies the actual requirement over a larger framework with unused functionality.

Before implementation, identify:

```text
Library name
Source / maintainer
Required version if relevant
Include header
Initialization API
Main read/write API
Error/status API
Known architecture constraints
```

Do not fabricate methods based on how a different library works.

---

## 5. Custom Library Workflow

A custom library must start from user-defined behavior.

Mandatory sequence:

```text
1. Ask: existing library or custom library?
2. User chooses custom.
3. Ask: what exactly should the custom library do?
4. Inspect hardware/project constraints.
5. Define the smallest useful public API.
6. Implement.
7. Provide at least one minimal example.
8. Build and verify.
```

For a standard Arduino library, use a simple structure when appropriate:

```text
MyLibrary/
├── library.properties
├── src/
│   ├── MyLibrary.h
│   └── MyLibrary.cpp
└── examples/
    └── BasicUsage/
        └── BasicUsage.ino
```

Add other files only when needed.

### Custom Library API Rules

- Keep hardware ownership clear.
- Keep constructors simple.
- Use `begin()` where hardware initialization is required and consistent with Arduino conventions.
- Avoid hidden long blocking operations.
- Expose status/error information when failures are meaningful.
- Avoid unnecessary dynamic allocation on small MCUs.
- Do not create inheritance hierarchies unless they solve a real requirement.
- Do not expose internal implementation details as public API.
- Preserve compatibility only for architectures the user actually needs.

If portability is required, isolate architecture-specific code behind clear internal boundaries.

---

## 6. Sensor and Component Investigation

For each external device identify:

```text
Part number
Supply voltage
Logic voltage
Interface
Pinout
Operating range
Timing requirements
Address / chip-select behavior
Required passive components
```

For sensors also verify:

- Units
- Resolution
- Accuracy
- Calibration requirements
- Sampling rate
- Warm-up time if relevant
- Environmental constraints

Do not convert raw data into engineering units without verified scaling.

---

## 7. Pin Allocation

Before implementation, build a pin map for the relevant signals.

Check:

- Digital capability
- ADC capability
- PWM capability
- Interrupt capability
- UART/I2C/SPI alternate/default pins
- Input-only pins
- Boot/strapping pins on boards that have them
- Pins used by onboard flash, LEDs, USB, PSRAM, radio, or other board hardware

Do not assume every printed pin number maps directly to the MCU GPIO number.

Avoid using boot-sensitive or reserved pins unless verified safe.

---

## 8. GPIO

For GPIO verify:

- Input/output mode
- Pull-up/pull-down requirements
- Active-high vs active-low behavior
- Startup state
- Voltage compatibility

Use internal pull resistors only when electrically appropriate.

Remember that internal pull resistor values are typically weak and architecture-dependent.

---

## 9. Timing and Non-Blocking Code

Do not replace every `delay()` blindly.

Use `delay()` when blocking behavior is intentionally acceptable.

Use non-blocking timing when the application must perform concurrent activities.

Typical pattern:

```cpp
if (millis() - previousTime >= interval) {
    previousTime = millis();
    // task
}
```

Use subtraction-based timing so `millis()` rollover remains safe.

For microscale timing, inspect architecture-specific resolution and overflow behavior before relying on `micros()`.

Prefer explicit state machines for multi-step asynchronous behavior.

---

## 10. ADC and Analog Signals

Before using `analogRead()` verify:

- ADC resolution for the active board/core
- Reference voltage
- Input range
- Attenuation settings if architecture-specific
- Pin ADC capability
- Calibration behavior

Do not assume `analogRead()` always returns `0..1023`.

When converting to voltage:

```text
voltage = raw / max_count * reference_voltage
```

Use actual board/core resolution and reference assumptions.

For resistive dividers or sensor scaling, include the external circuit in the calculation.

---

## 11. PWM and Actuator Control

Do not assume `analogWrite()` behavior is identical across boards.

Verify:

- PWM-capable pin
- Frequency
- Resolution
- Timer/channel ownership
- Core-specific PWM API
- Interaction with servo/tone/timer libraries

For motors or power loads, use the appropriate external driver stage.

For servos verify pulse range and power supply requirements rather than powering multiple servos directly from the Arduino regulator.

---

## 12. Interrupts

Before attaching an interrupt verify:

- Pin supports the required interrupt mode
- Trigger mode
- Core-specific interrupt API
- Shared data behavior

Keep ISRs short.

Avoid inside ISR code:

- `delay()`
- long loops
- heavy serial printing
- blocking bus transactions
- memory allocation

Use flags, counters, ring buffers, or ISR-safe mechanisms to hand work back to normal code.

Mark ISR-shared variables appropriately and reason about atomicity; `volatile` alone does not make multi-byte operations atomic.

---

## 13. Serial / UART

Verify:

- Correct serial interface (`Serial`, `Serial1`, etc.)
- Baud rate
- Pin mapping if configurable
- USB CDC vs hardware UART behavior
- Buffering requirements

Do not assume every board maps `Serial` to a hardware UART.

For continuous streams, prefer buffered parsing rather than long blocking reads.

Design parsers to tolerate partial packets when serial data can arrive asynchronously.

---

## 14. I2C

Verify:

- Correct SDA/SCL pins
- Logic voltage
- Pull-up resistors
- Bus speed
- Device address
- Multiple-device address conflicts
- Board-specific `Wire` instance if multiple buses exist

Use an I2C scanner only as a diagnostic aid, not as proof that the device is configured correctly.

Do not assume all boards use A4/A5 for I2C.

---

## 15. SPI

Verify:

- MOSI/MISO/SCK pins
- Chip-select pin
- SPI mode
- Maximum bus frequency
- Bit order
- Multiple-device chip-select behavior

Use transactions where supported when multiple SPI devices require different configurations.

Do not leave multiple chip-select lines active simultaneously unless the hardware explicitly requires it.

---

## 16. Networking, Wi-Fi and BLE

For boards with networking support, verify the exact core and library APIs before implementation.

Avoid blocking reconnection loops that prevent the rest of the application from operating.

Where practical, model connection handling as states such as:

```text
DISCONNECTED
CONNECTING
CONNECTED
ERROR
```

Do not hardcode credentials into public repositories unless the user explicitly requests that behavior and understands the consequence.

Prefer a separate ignored secrets/config file or runtime provisioning where appropriate.

---

## 17. Memory

Resource limits vary widely across Arduino-compatible targets.

For constrained boards inspect:

- SRAM usage
- Global/static buffers
- String handling
- Stack depth
- Large local arrays
- Dynamic allocation

On small AVR targets, avoid unnecessary heap fragmentation from repeated dynamic `String` growth when long-running reliability matters.

Do not apply AVR memory rules mechanically to larger ESP32/RP2040/SAMD-class boards.

---

## 18. Implementation Rules

Prefer simple, testable firmware.

- Preserve existing naming and project style.
- Keep pin definitions centralized when the project already follows that pattern.
- Avoid unnecessary classes for tiny sketches.
- Use helper functions/classes when they improve separation of real responsibilities.
- Use constants instead of unexplained magic numbers.
- Avoid blocking loops when responsiveness matters.
- Use timeouts for hardware waits where failures are possible.
- Check return/status values when APIs provide them.
- Avoid introducing a library when a few clear lines using the core API are sufficient, unless the user has chosen to build that custom library intentionally.

Do not over-engineer a small sketch into a framework.

---

## 19. Debugging Order

When an Arduino project fails, debug in this order unless evidence suggests otherwise:

```text
1. Exact board and selected board profile
2. Power and wiring
3. Build errors
4. Upload / programmer / port
5. Pin mapping
6. Library compatibility
7. Peripheral initialization
8. Bus/protocol configuration
9. Timing / state logic
10. Interrupt interactions
11. Memory / buffer behavior
12. Application algorithm
13. Signal integrity / external hardware
```

Do not rewrite the algorithm before confirming basic hardware communication.

---

## 20. Debugging Evidence

Use available evidence rather than guessing.

Useful tools include:

- Compiler output
- Upload logs
- Serial Monitor
- Serial Plotter
- Logic analyzer
- Oscilloscope
- Multimeter
- I2C scanner
- Known-good loopback tests
- LED/GPIO test points
- Core debug facilities where supported

For communication failures, inspect actual bus traffic when possible.

---

## 21. Build and Upload Verification

A code edit is not verified merely because it looks correct.

Use the actual project toolchain where available.

Examples may include:

```text
Arduino IDE
arduino-cli
PlatformIO
```

Verify:

- Compilation succeeds for the exact target board
- Required libraries resolve
- No new relevant warnings
- Firmware fits available memory
- Upload succeeds when hardware access exists

Do not declare hardware behavior fixed from compilation alone.

---

## 22. Runtime Verification

Match runtime checks to the task.

Examples:

```text
GPIO       -> confirm expected voltage/state
PWM        -> measure frequency and duty cycle
ADC        -> compare raw reading with known input
UART       -> verify transmitted/received bytes
I2C        -> verify device ACK and expected registers/data
SPI        -> inspect transaction and returned data
Sensor     -> compare output against known physical condition
Motor      -> test at safe low-power condition first
Interrupt  -> verify event count/timing without ISR overload
Network    -> verify connect, disconnect and recovery behavior
```

If hardware is unavailable, clearly distinguish:

```text
Static verification
Build verification
Hardware/runtime verification not performed
```

---

## 23. Completion Report

At completion provide:

```text
Goal
Board / MCU
Library choice: existing or custom
Libraries affected
Files changed
Pins / peripherals affected
Build result
Upload result
Runtime verification
Remaining warnings / hardware checks
```

If a custom library was created, also report:

```text
Library purpose
Public API
Supported hardware/boards
Example sketch
Known limitations
```

Do not claim success beyond the evidence available.
