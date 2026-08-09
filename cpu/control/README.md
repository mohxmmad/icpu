# Control Unit

The Control Unit decodes a 16-bit instruction and generates a 16-bit control output for the CPU.

## Interface

```text
INPUT  IN[16]
INPUT  ZR
INPUT  NG

OUTPUT OUT[16]
```

- `IN[16]`: 16-bit instruction.
- `ZR`: ALU zero flag.
- `NG`: ALU negative flag.
- `OUT[16]`: 16-bit control word.

## Instruction Types

### A Instruction

```text
0xxxxxxxxxxxxxxx
```

If the first bit is `0`, the instruction is an A instruction.

The remaining 15 bits are used as the A-instruction value.

### C Instruction

```text
11xxxxxxxxxxxxxx
```

If the first two bits are `11`, the instruction is a C instruction.

The C instruction controls the ALU, register loads, RAM writes, and jumps.

### F Instruction

```text
10xxxxxxxxxxxxxx
```

If the first two bits are `10`, the instruction is an F instruction.

The defined F operation is:

```text
ROM[D] = RAM[A]
```

The last 13 bits of the F instruction are currently reserved.

## Control Output

```text
M0 M1 M2 FC ZX NX ZY NY F NO LD LA WRAM WROM W PC_LOAD
```

| Signal | Function |
|---|---|
| `M0` | Selects A-register input: instruction or ALU output |
| `M1` | Selects ALU Y input: A register or RAM |
| `M2` | Selects the address source for the instruction/address path |
| `FC` | Selects the instruction source |
| `ZX` | ALU zero-X control |
| `NX` | ALU negate-X control |
| `ZY` | ALU zero-Y control |
| `NY` | ALU negate-Y control |
| `F` | ALU function control |
| `NO` | ALU negate-output control |
| `LD` | D-register load |
| `LA` | A-register load |
| `WRAM` | RAM write enable |
| `WROM` | ROM write enable |
| `W` | General write-operation flag |
| `PC_LOAD` | Program-counter load |

## M0

Selects the source for the A-register input.

```text
0 → instruction
1 → ALU output
```

## M1

Selects the source for the ALU Y input.

```text
0 → A register
1 → RAM
```

## M2

Selects the source used by the instruction/address path.

Normal execution uses the program counter.

The F instruction uses the special address path required for ROM programming.

## FC

Controls the instruction source.

Normal execution uses ROM.

The F instruction switches the instruction source to the RAM path.

## ALU Controls

The six ALU control signals are:

```text
ZX NX ZY NY F NO
```

These are controlled by the C instruction.

## Register Loads

### LD

Loads the D register when asserted.

### LA

Loads the A register when asserted.

## Memory Writes

### WRAM

Enables a RAM write.

For an F instruction:

```text
WRAM = 0
```

### WROM

Enables a ROM write.

For an F instruction:

```text
WROM = 1
```

### W

Indicates a write operation.

```text
W = WRAM OR WROM
```

## Program Counter

### PC_LOAD

Controls loading of the program counter.

The signal is generated from the decoded jump condition together with the ALU flags `ZR` and `NG`.

## Jump Logic

The three jump bits of a C instruction are decoded into:

```text
JMP
JLE
JNQ
JLT
JGE
JEQ
JGT
NJMP
```

The jump decision uses:

```text
ZR
NG
```

The resulting decision controls `PC_LOAD`.

## F Instruction

The F instruction performs:

```text
ROM[D] = RAM[A]
```

- `A` provides the RAM source address.
- `RAM[A]` provides the data.
- `D` provides the ROM destination address.

During F:

```text
WRAM = 0
WROM = 1
W = 1
```

## Internal Components

The Control Unit uses:

- `MUX`
- `ADDR_DECODER`
- `AND`
- `OR`
- `NOT`
- `BUF`
- `SPLIT`
- `JOIN`

## Instruction Classification

```text
0...  → A
10..  → F
11..  → C
```

## Output Ordering

The final output is joined as:

```text
M0 M1 M2 FC ZX NX ZY NY F NO LD LA WRAM WROM W PC_LOAD
```

Therefore:

```text
OUT[16] =
M0 M1 M2 FC ZX NX ZY NY F NO LD LA WRAM WROM W PC_LOAD
```

## Purpose

The Control Unit converts the 16-bit machine instruction and ALU status flags into the hardware control signals required by the CPU.

It controls:

- A-register input
- D-register loading
- ALU inputs
- ALU operation
- RAM writes
- ROM writes
- Instruction source
- Program-counter loading
- Jump conditions
