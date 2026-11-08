# ActronAir LR7 RS-485 协议笔记

[English](README.md) · **简体中文**

本文档整理了 ActronAir Classic Series 2 CRA130S/EVA130S 和 LR7-1W 墙控的总线观测。该配置使用 Modbus RTU 帧格式，经 RS-485 以 **9600 波特、8N1** 通信。

- [协议参考](docs/protocol.zh-CN.md) 说明帧格式、CRC、主机轮询和已观测寄存器。
- [已实现功能与接线](docs/implementation.md) 说明 LR7 与 ESP32 的并联接法，并区分功能实现和验证范围。
- [合成示例帧](examples/synthetic-frames.json) 提供主机轮询、墙控回复和状态广播，可用于离线开发解析器。

项目已实现制冷开关、16–30 °C 统一调温、三档风速、五个分区开关、手机 PWA 和 Apple「家庭」配件。室内机原墙控主线在分线点一分二，LR7 和微雪板共用 `POWER/GND` 供电及 `485 A/B` 总线。微雪板通过 `VCC/GND` 电源螺丝端子取电。

这些观测来自上述设备组合。原墙控操作时捕获了请求标志，后续主机广播回报了对应设定。其他型号的兼容性和第三方控制器主动发送的效果需要单独验证。

原创文档和合成示例采用 [CC BY 4.0](LICENSE) 许可。ActronAir 和相关产品名称用于识别文档涉及的设备。
