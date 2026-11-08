# ActronAir LR7-1W protocol reference

**English** · [简体中文](protocol.zh-CN.md) · [Repository home](../README.md)

This independently observed profile covers the LR7-1W wall controller and the Classic Series 2 CRA130S/EVA130S. It describes the first register page and the cooling, setpoint, fan speed, and zone-selection changes verified through original wall controller operations.

## Physical layer and framing

The observed wall controller bus uses RS-485, **9600 baud, eight data bits, no parity, one stop bit**. Its frames use Modbus RTU addressing, function codes, and CRC-16. The register map and request flags below are application-specific.

Multi-byte addresses, counts, and register words are big-endian. The CRC is transmitted **low byte first**. Register numbers in this document are zero-based addresses as carried on the wire. Hexadecimal numbers use the `0x` prefix.

Use inter-frame gaps when reliable byte timing is available. For byte chunks without reliable timing, check the expected frame shape and byte count, validate the CRC, and match responses to preceding requests. Drop request context across capture gaps and resets.

| Frame | Layout in wire order | Observed length |
| --- | --- | ---: |
| Host poll | `[unit] 03 00 02 00 7C [CRC low] [CRC high]` | 8 bytes |
| Controller response | `[unit] 03 F8 [124 words] [CRC low] [CRC high]` | 253 bytes |
| Host state broadcast | `00 10 00 04 00 7A F4 [122 words] [CRC low] [CRC high]` | 253 bytes |

- The host polls addresses including `0x66`, `0x67`, and `0x68`. These are observed bus addresses. A missing response does not establish that an address is available.
- Function `0x03` reads holding registers. The poll requests 124 words, from register 2 through 125. Its response carries 248 data bytes and omits the start register. Recover the start from a preceding matching request for the same unit, function, and word count.
- Function `0x10` at unit `0x00` broadcasts 122 words, from register 4 through 125. Its header carries the start, count, and 244-byte payload length. Unit zero is the Modbus broadcast address. The observed state broadcast has no individual reply.
- For a zero-indexed frame, register `r` begins at byte `3 + 2*(r-2)` in the matched response and at `7 + 2*(r-4)` in the broadcast.

### CRC-16/Modbus

Initialize the accumulator to `0xFFFF`. XOR each byte from the unit through the end of the payload into the low eight bits. For each byte, repeat eight times: shift right by one and XOR `0xA001` if the bit shifted out was one. Append the low eight bits of the accumulator followed by its high eight bits. The ASCII test string `123456789` produces accumulator `0x4B37` and wire bytes `37 4B`.

## First-page register map

| Register | Meaning | Evidence and limits |
| ---: | --- | --- |
| 10 | Cooling on/off setting | `0x0000` and `0x8000` accompanied off. `0x0001` and `0x8001` accompanied cooling enabled. Bit 15 can vary. These observations describe the selected operating state. Compressor activity and other modes are unresolved. |
| 11 | Fan selection | Low, medium, and high appear as `0x0981`, `0x0982`, and `0x0983`. The `0x0901` through `0x0903` forms also occur. The low two bits distinguish these three speeds. Other bits vary and have no confirmed independent mapping here. |
| 12 | Shared target temperature | The observed integer value is in tenths of a degree Celsius. For example, a synthetic value of 235 represents 23.5 °C. Changes of 0.5 °C produced changes of five in the register. |
| 13 | Possible room temperature | The location matches a candidate in the related NEO notes. An independent temperature comparison is still needed for this profile. |
| 14 | Wall controller request flag | The active values below were correlated with original controller actions. Zero was observed in steady-state replies. |
| 15 | Unknown | Changes in some action replies. Preserve as raw data when decoding. |
| 55–58 | Unresolved auxiliary words | These words stayed unchanged during observed setpoint changes. The NEO setpoint mirror interpretation remains unverified for LR7. |
| 85 | Zone selection mask | Bits 0 through 4 correspond to zone indices 1 through 5, with masks `0x01`, `0x02`, `0x04`, `0x08`, and `0x10`. A set bit indicates a selected zone. The bit does not measure physical damper position. |

Zone labels are installation-defined. Per-zone setpoints, heating, fan-only operation, continuous fan flags, and higher zone bits are outside the verified map.

## Original controller action flags

| Operation | Register 14 | Associated registers |
| --- | ---: | --- |
| Cooling on or off | `0x0007` | 10 and 11 changed together in the observed power transitions. |
| Shared temperature adjustment | `0x0004` | 12 carries the requested setpoint. |
| Fan speed adjustment | `0x0002` | 11 carries the requested fan word. |
| Zone selection | `0x0040` | 85 carries the requested mask. |

During a wall controller action, a matched `0x03` reply from unit `0x66` carried the transient flag and changed fields. A later `0x10` broadcast reflected the new system setting. Subsequent idle replies returned to zero. Repeated key presses sometimes kept a nonzero flag across several polls. Treat the reply as an observed request and the later broadcast as the system's reported setting. These observations do not establish a supported external write procedure, exact timing limits, or a complete handshake.

## Offline examples

[Synthetic frames](../examples/synthetic-frames.json) contains valid CRCs, consistent request and reply lengths, and expected decoded values. All payloads are constructed for documentation. Unassigned words are zero-filled for the examples and have no inferred default on a real bus.

## References

- Modbus Organization application protocol and serial line specifications. [Specifications and implementation guides](https://www.modbus.org/modbus-specifications)
- jordonsc's NEO Touch research, which describes a related register layout with model-specific differences. [shq-suite / actron-sniffer findings](https://github.com/jordonsc/shq-suite/blob/bee5664ea14ecc3f55203e19c1a56e274558fcc1/actron-sniffer/FINDINGS.md)
- awulf's Actron485 research, a reference for other ActronAir message families. Its documented native message formats and default serial rate are a separate profile. [Actron485 source and notes](https://github.com/awulf/Actron485/tree/dc2617c919ff57a16e39255e7391a811ec53dc3e)
