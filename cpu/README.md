# CPU

The CPU integrates the Control Unit, ALU, A register, D register, and Program Counter into a single processor datapath.

## Interface

```text
INPUT  INSTRUCTION[16]
INPUT  DATA[16]
INPUT  RESET
CLOCK  CLK

OUTPUT OUT[16]
OUTPUT LRAM
OUTPUT LROM
OUTPUT DATA_ADDR[15]
OUTPUT INSTRUCTION_ADDR[15]
OUTPUT INSTRUCTION_SELECTOR[16]
OUTPUT WRITE
```

## Main Components

The CPU is built from:

- `CONTROL`
- `ALU`
- `COUNTER`
- `REGISTER`
- `MUX16`

The CPU currently does not directly include the keyboard, display, RAM module, or ROM module. Those are connected at the computer level.

## Registers

### A Register

The A register stores the address/value selected by the instruction or ALU output.

It is used for:

- RAM addressing
- Immediate values
- Jump targets
- ROM addressing during an F instruction

### D Register

The D register stores the ALU output when `LD` is asserted.

For the F instruction, the D register holds the data that will be written to ROM.

### Program Counter

The Program Counter stores the address of the next instruction.

It can load a new address when `PC_LOAD` is asserted.

The PC uses the A register as its load value for jumps.

## ALU

The ALU receives:

```text
X = D register
Y = A register or RAM
```

The six ALU control signals are:

```text
ZX NX ZY NY F NO
```

The ALU produces:

```text
OUT
ZR
NG
```

`OUT` is the CPU's ALU result.

`ZR` and `NG` are fed back into the Control Unit for jump-condition evaluation.

## Control Unit

The Control Unit receives:

```text
INSTRUCTION
ZR
NG
```

and produces:

```text
M0 M1 M2 FC ZX NX ZY NY F NO LD LA WRAM WROM W PC_LOAD
```

These signals control the CPU datapath.

## Instruction Flow

The instruction is supplied through `INSTRUCTION[16]`.

The Control Unit decodes the instruction and generates the required control signals.

The CPU then:

1. Selects the required register and ALU inputs.
2. Performs the ALU operation.
3. Loads A or D when required.
4. Writes to RAM or ROM when required.
5. Evaluates jump conditions using `ZR` and `NG`.
6. Loads the Program Counter when required.

## A Instruction

Format:

```text
0xxxxxxxxxxxxxxx
```

An A instruction loads its 15-bit value into the A register.

The A register value is also used as the normal RAM address.

## C Instruction

Format:

```text
11xxxxxxxxxxxxxx
```

A C instruction controls:

- ALU operation
- ALU inputs
- A register loading
- D register loading
- RAM writes
- Jump conditions

## F Instruction

Format:

```text
101xxxxxxxxxxxxx
```

The F instruction is the CPU's ROM programming instruction.
It performs a ROM write and temporarily switches instruction fetching to RAM so the next instruction can be supplied from a preloaded RAM location.

The intended operation is:

```text
ROM[A] = D
```

**Current Limitation :** Because the next instruction must be prepared in RAM using the existing A/C instructions,
generating arbitrary 16-bit instruction values may require multiple instructions.

The sequence used to prepare an F instruction is:

```text
C instruction → D = instruction to be executed after F instruction 
A instruction → A = destination ROM address
C instruction → M = D
A instruction → A = source RAM address (where the instruction to be flashed is written)
C instruction → D = RAM[A] (instruction to be flashed)
A instruction → A = destination ROM address
F instruction → ROM[A] = D
A instruction → A = 0x0000 (this ROM instruction will be skipped and RAM[A] will be executed)
```

Therefore, the F instruction uses:

```text
A → ROM destination address
D → ROM data
```

During F:

```text
WRAM = 0
WROM = 1
W = 1
```

## Address Paths

### RAM Address

The A register provides the RAM address:

```text
A register → DATA_ADDR
```

`DATA_ADDR` is 15 bits wide.

### Instruction Address

The instruction address is selected from the Program Counter or the special address path controlled by `M2`.

`INSTRUCTION_ADDR` is 15 bits wide.

## Instruction Source

`FC` is exposed as the instruction-source selector:

```text
INSTRUCTION_SELECTOR[16]
```

The selector is represented as a 16-bit value because the `MUX16` interface expects a 16-bit select input.

## Memory Write Outputs

The Control Unit generates:

```text
WRAM
WROM
W
```

These are exposed by the CPU as:

```text
LRAM  = WRAM
LROM  = WROM
WRITE = W
```

## CPU Datapath

```text
                 INSTRUCTION
                      │
                      ▼
               ┌─────────────┐
               │   CONTROL   │
               └──────┬──────┘
                      │
        ┌─────────────┼──────────────┐
        │             │              │
        ▼             ▼              ▼
      A/D           ALU            PC
    registers         │              │
        │             │              │
        └─────────────┼──────────────┘
                      │
                      ▼
                    OUT
```

## Status Feedback

The ALU produces two status flags:

```text
ZR
NG
```

These are returned to the Control Unit.

The Control Unit uses them together with the C-instruction jump bits to determine `PC_LOAD`.

## Current CPU Structure

```text
CPU
├── Control Unit
├── ALU
├── A Register
├── D Register
├── Program Counter
└── MUX16 datapath
```

The CPU provides the processing core required by the larger computer module. RAM, ROM, keyboard, display, and other peripherals can be connected externally at the computer level.
