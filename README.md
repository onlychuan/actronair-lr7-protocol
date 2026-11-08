# ActronAir LR7 RS-485 protocol notes

**English** · [简体中文](README.zh-CN.md)

Independent protocol notes for the ActronAir Classic Series 2 CRA130S/EVA130S with an LR7-1W wall controller. The documented profile uses Modbus RTU framing over RS-485 at **9600 baud, 8N1**.

- [Protocol reference](docs/protocol.md) covers frame layout, CRC, polling, and the observed register map.
- [Implemented features and wiring](docs/implementation.en.md) describes the parallel LR7/ESP32 connection and separates implementation from verification.
- [Synthetic frames](examples/synthetic-frames.json) provide a poll, a response, and a state broadcast for offline parser development.

The project implements cooling on/off, a shared 16–30 °C setpoint, three fan speeds, five zone switches, a mobile PWA, and Apple Home accessories. The indoor unit's wall controller cable splits into two branches. The LR7 and Waveshare board share the `POWER/GND` supply and the `485 A/B` bus. The Waveshare board draws power through its `VCC/GND` screw terminals.

The observations describe one equipment combination. Request flags were observed during original wall controller operations and matched subsequent system broadcasts. Compatibility with other models and transmission by a third-party controller require separate verification.

The original notes and synthetic examples are available under [CC BY 4.0](LICENSE). ActronAir and product names identify the equipment discussed.
