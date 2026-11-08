# ActronAir LR7 RS-485 protocol notes

Independent protocol notes for the ActronAir Classic Series 2 CRA130S/EVA130S with an LR7-1W wall controller. The documented profile uses Modbus RTU framing over RS-485 at **9600 baud, 8N1**.

- [Protocol reference](docs/protocol.md) covers frame layout, CRC, polling, and the observed register map.
- [Synthetic frames](examples/synthetic-frames.json) provide a poll, a response, and a state broadcast for offline parser development.

这是 ActronAir LR7-1W 墙控总线的公开协议笔记，整理了 9600/8N1 通信、帧格式、CRC、主机轮询，以及开关、风速、统一设定温度和五个分区的已观测字段。协议参考区分实测、候选解释和未知字段。示例是人工构造的测试数据，适合离线解码。

The observations describe one equipment combination. Request flags were observed during original wall controller operations and matched subsequent system broadcasts. Compatibility with other models and transmission by a third-party controller require separate verification.

The original notes and synthetic examples are available under [CC BY 4.0](LICENSE). ActronAir and product names identify the equipment discussed.
