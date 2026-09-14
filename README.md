# Arduino Programmer Skill

[![Skill Spec](https://img.shields.io/badge/AI--Skill-v2.0-green.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Arduino](https://img.shields.io/badge/Arduino-Embedded%20Development-00878F.svg)](#)

An AI agent skill for **Arduino and Arduino-compatible embedded development**. It guides coding agents through board/core discovery, hardware compatibility checks, library decisions, custom library design, peripheral integration, debugging, build/upload verification, and hardware runtime testing.

---

## Key Behavior: Library Decision Gate

Before adding a new non-core dependency or writing a component driver, the agent must ask:

```text
Do you want to use an existing library, or build a custom library?
```

If you choose **existing library**, the agent researches suitable libraries, checks board/core compatibility, verifies the API, and uses the smallest appropriate dependency.

If you choose **custom library**, the agent must next ask:

```text
What exactly should the custom library do?
```

Only after you define the required behavior will it design the API and implementation.

---

## Features

- **Board & Core Discovery** — Identifies the exact board, MCU/SoC, architecture, Arduino core, voltage level, toolchain, upload method, and relevant pin capabilities.
- **Mandatory Library Choice** — Never silently chooses between a third-party library and a custom implementation.
- **Existing Library Research** — Checks hardware compatibility, architecture support, maintenance, API usage, license, and dependencies.
- **Custom Library Development** — Defines the required behavior first, then builds a minimal Arduino library with a clean public API and example sketch.
- **Hardware Investigation** — Verifies sensor/actuator datasheets, logic levels, power requirements, pinouts, pull resistors, level shifting, and driver stages.
- **Peripheral Engineering** — Covers GPIO, ADC, PWM, interrupts, UART, I2C, SPI, sensors, actuators, networking, Wi-Fi, BLE, and memory concerns.
- **Non-Blocking Firmware** — Uses `millis()`, state machines, interrupts, and buffering where concurrency or responsiveness requires them.
- **Cross-Architecture Awareness** — Avoids assuming Uno/AVR behavior applies to ESP32, RP2040, STM32, SAMD, Renesas, and other Arduino-compatible boards.
- **Debugging Workflow** — Uses a deterministic hardware-to-software debugging order rather than random code rewrites.
- **Verification** — Requires exact-target compilation and distinguishes build verification from upload and real hardware runtime validation.

---

## Custom Library Workflow

```text
User requests feature
        ↓
Agent inspects project + board
        ↓
Agent asks:
Existing library or custom library?
        ↓
   ┌───────────────┴───────────────┐
   ↓                               ↓
Existing                      Custom
   ↓                               ↓
Research compatible           Ask what the
libraries                     library must do
   ↓                               ↓
Verify API                    Define smallest API
   ↓                               ↓
Implement                     Implement library
   ↓                               ↓
Build + test                  Example + build + test
```

A typical custom library may use:

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

The skill avoids unnecessary files and abstractions unless the requirement actually needs them.

---

## Supported Development Workflows

The rules are designed to work with projects using:

```text
Arduino IDE
Arduino CLI
PlatformIO
```

and Arduino-compatible targets based on architectures such as:

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

---

## Repository Structure

```text
Arduino_Programmer/
├── SKILL.md       # Complete Arduino engineering workflow
├── README.md      # Overview and usage
└── LICENSE        # MIT License
```

---

## Usage

Place the `Arduino_Programmer` directory inside your AI agent's skills directory, for example:

```bash
~/.gemini/config/skills/Arduino_Programmer/
```

When Arduino firmware work is detected, the agent can use `SKILL.md` as its domain-specific development and verification procedure.

---

## License

Distributed under the MIT License. See `LICENSE` for details.
