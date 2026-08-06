# Arduino Programmer Skill

[![Skill Spec](https://img.shields.io/badge/AI--Skill-v1.0-green.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

An AI agent skill for assisting with **Arduino microcontrollers and embedded systems development**. It guides AI coding assistants through board architecture identification, sensor research, documentation lookup, pinout hookups, and non-blocking C/C++ microcontroller programming.

---

## 📋 Features

- **Architecture & Board Identification**: Explicitly identifies target architectures (AVR, SAMD, ESP8266, ESP32, STM32, RP2040) and operating voltages (3.3V vs 5V).
- **Sensor & Peripheral Investigation**: Catalogs interface protocols (I2C, SPI, UART, PWM, Analog/Digital) and checks component power limits.
- **Documentation & Library Research**: Searches official Arduino APIs and reputable repositories for reliable hardware libraries.
- **Wiring & Hookup Scheme**: Documents clear wiring schematics including pull-up/pull-down resistor recommendations.
- **Embedded Best Practices**: Enforces non-blocking timing logic (`millis()` over `delay()`), clean `setup()` / `loop()` structure, and modular helper code.

---

## 📁 Repository Structure

```
Arduino_Programmer/
├── SKILL.md       # AI agent skill definition & execution protocol
├── README.md      # Documentation and overview
└── LICENSE        # MIT License
```

---

## 🚀 Usage & Integration

### With AI Agents (Gemini, Claude, Antigravity CLI)
Place the `Arduino_Programmer` directory inside your agent's skills configuration folder:
```bash
~/.gemini/config/skills/Arduino_Programmer/
```

When prompt instructions or project requirements mention Arduino development, the agent will load `SKILL.md` and follow the 5-step structured hardware and software development workflow.

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.
