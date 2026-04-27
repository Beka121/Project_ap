# 🚀 Mini-AGC — Apollo Guidance Computer in Logisim

> A simplified model of the Apollo 11 flight computer (Luminary099), built in Logisim.  
> The real AGC guided astronauts to the Moon in 1969. This project captures its core architecture.

---

## For Reader

I have written comments, and I didn't use difficult concepts.  
If you have questions, you can contact me:

<p align="center">
    <a href="https://github.com/Beka121">
        <img src="https://img.shields.io/badge/GitHub-090909?style=for-the-badge&logo=github&logoColor=white">
    </a>
    <a href="mailto:bekturemilev@gmail.com">
        <img src="https://img.shields.io/badge/Email-090909?style=for-the-badge&logo=gmail&logoColor=red">
    </a>
    <a href="https://t.me/Kaka_short">
        <img src="https://img.shields.io/badge/Telegram-090909?style=for-the-badge&logo=telegram&logoColor=26A5E4">
    </a>
</p>

---

## Inspiration

This project is inspired by [Luminary099](https://github.com/chrislgarry/Apollo-11/tree/master/Luminary099) — the actual source code of the Apollo 11 guidance computer.  
The real AGC used **15-bit words**, rope core ROM, and 36 instructions.  
My Mini-AGC uses the same **15-bit architecture** and the same fetch → decode → execute cycle.

---

## Architecture (3 Blocks)

### Block 1 — 15-Bit Data Bus
All components operate on **15-bit wide** signals — identical to the real AGC word length.  
This includes the ALU, ROM, Program Counter, and Accumulator register.

### Block 2 — Instruction Decoder
Each 15-bit instruction is split by a **Splitter** into two fields:

| Bits  | Field   | Width  | Purpose                  |
|-------|---------|--------|--------------------------|
| 14–11 | Opcode  | 4 bits | Which operation to run   |
| 10–0  | Operand | 11 bits| Data value or address    |

A **Decoder** component reads the 4-bit opcode and activates one control line at a time.

### Block 3 — Program Counter + ROM
- **Counter (PC)** increments on every clock tick → addresses the next instruction  
- **ROM** stores the program (like rope core memory in the real AGC — read-only, cannot be changed during execution)  
- ROM output → Decoder → ALU

---

## Instruction Set

| Mnemonic | Opcode | Hex    | Operation         | AGC Equivalent |
|----------|--------|--------|-------------------|----------------|
| NOP      | 0000   | 0000   | No operation      | TC 0           |
| ADD      | 0001   | 08xx   | A = A + operand   | AD             |
| SUB      | 0010   | 10xx   | A = A − operand   | SU             |
| AND      | 0011   | 18xx   | A = A AND operand | MASK           |
| OR       | 0100   | 20xx   | A = A OR operand  | —              |
| JMP      | 0101   | 28xx   | PC = operand      | TC             |

---

## Test

Clock stepped manually using `Ctrl+T` in Logisim.  
ROM loaded with program: `0805 0803 0000 0000`

```
Cycle 1:
  PC  = 0x0000
  ROM = 0x0805  →  Decoder: ADD = 1
  ALU = 0 + 5 = 5
  REG A = 5

Cycle 2:
  PC  = 0x0001
  ROM = 0x0803  →  Decoder: ADD = 1
  ALU = 5 + 3 = 8
  REG A = 8  ✅

Cycle 3:
  PC  = 0x0002
  ROM = 0x0000  →  Decoder: NOP = 1
  REG A = 8  (unchanged)
```

---

## Problems

1. JMP instruction is decoded but not fully connected to PC load input — jump execution is not yet complete.
2. No ZERO or CARRY flag output in the current version.
3. Only 4 ROM slots are used in the demo program — the rest are empty (NOP).

---

## References

- [Luminary099 — Apollo 11 Source Code](https://github.com/chrislgarry/Apollo-11)
- [AGC Technical Manual — MIT, 1966](https://www.ibiblio.org/apollo/)
- [Logisim Evolution](https://github.com/logisim-evolution/logisim-evolution)
