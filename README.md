# ActronAir LR7 RS-485 protocol notes

Independent protocol notes for the ActronAir Classic Series 2 CRA130S/EVA130S with an LR7-1W wall controller. The documented profile uses Modbus RTU framing over RS-485 at **9600 baud, 8N1**.

- [Protocol reference](docs/protocol.md) covers frame layout, CRC, polling, and the observed register map.
- [Implemented features and wiring / 已实现功能与接线](docs/implementation.md) describes the parallel LR7/ESP32 connection and separates implementation from verification.
- [Synthetic frames](examples/synthetic-frames.json) provide a poll, a response, and a state broadcast for offline parser development.

这是 ActronAir LR7-1W 墙控总线的公开协议笔记，整理了 9600/8N1 通信、帧格式、CRC、主机轮询，以及开关、风速、统一设定温度和五个分区的已观测字段。协议参考区分实测、候选解释和未知字段。示例是人工构造的测试数据，适合离线解码。

项目已实现制冷开关、16–30 °C 统一调温、三档风速、五个分区开关、手机 PWA 和 Apple「家庭」配件。室内机原墙控主线在分线点一分二，LR7 和微雪板共用 `POWER/GND` 供电和 `485 A/B` 总线。微雪板通过 `VCC/GND` 电源螺丝端子取电。各项实测范围见[功能与接线说明](docs/implementation.md)。

The observations describe one equipment combination. Request flags were observed during original wall controller operations and matched subsequent system broadcasts. Compatibility with other models and transmission by a third-party controller require separate verification.

The original notes and synthetic examples are available under [CC BY 4.0](LICENSE). ActronAir and product names identify the equipment discussed.
