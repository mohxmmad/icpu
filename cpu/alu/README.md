# Hack ALU

The ALU (Arithmetic Logic Unit) is an implementation of the **Hack Computer ALU** described in the **Nand2Tetris** course.

It is written in **iHDL** and constructed hierarchically from primitive logic gates rather than using built-in arithmetic operators.

---

## Interface

### Inputs

| Name | Width | Description |
|------|------:|-------------|
| X | 16 bits | First operand |
| Y | 16 bits | Second operand |
| CONTROLBITS | 6 bits | ALU control bits |

### Outputs

| Name | Width | Description |
|------|------:|-------------|
| OUT | 16 bits | ALU result |
| ZR | 1 bit | High when OUT equals zero |
| NG | 1 bit | High when OUT is negative (MSB = 1) |

---

## Control Bits

The ALU follows the Hack Computer specification.

```
zx nx zy ny f no
```

| Bit | Meaning |
|------|----------|
| zx | Zero the X input |
| nx | Negate X |
| zy | Zero the Y input |
| ny | Negate Y |
| f | Function selector (0 = AND, 1 = ADD) |
| no | Negate output |

These six control bits determine the operation performed by the ALU.

---

## Supported Operations

The Hack ALU can generate the standard 18 computations defined by the Hack architecture.

Examples include:

- 0
- 1
- -1
- X
- Y
- !X
- !Y
- -X
- -Y
- X + 1
- Y + 1
- X - 1
- Y - 1
- X + Y
- X - Y
- Y - X
- X & Y
- X | Y

These operations are produced entirely by different combinations of the six control bits.

---

## Design

The ALU is implemented hierarchically in iHDL using reusable hardware modules.

The design is based directly on the Hack Computer architecture from Nand2Tetris.

No built-in arithmetic operators are used inside the ALU implementation.

---

## Status Flags

### ZR

The Zero flag becomes high when

```
OUT == 0
```

### NG

The Negative flag is simply the most significant bit of the output.

```
NG = OUT[15]
```

using two's complement representation.

---

## Purpose

The Hack ALU serves as the arithmetic and logical core of the iCPU project.

It will later be integrated with the memory subsystem, registers, program counter, and CPU control logic to build a complete Hack-compatible processor.

---

## References

- Nand2Tetris
- The Elements of Computing Systems
- Hack Computer Architecture
