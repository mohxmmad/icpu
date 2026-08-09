# Memory Layer

The objective is to build sequential logic from first principles.

## Learning Path

Primitive Gates -> SR Latch -> Clocked SR Latch -> Edge Triggered SR Latch -> D Flip Flop -> Register -> RAM -> ROM

## Clock Semantics

iHDL simulates sequential circuits using a **two-phase clock model**.

Each clock cycle consists of two half-steps:

- **Half-step 1:** The master latch is active.
- **Half-step 2:** The slave latch is active.

This allows the internal behavior of master-slave latches and D flip-flops to be observed during simulation.

## SR Latch Implementation

The SR latch is the first sequential component implemented in iHDL.

Unlike real hardware, iHDL currently uses **wire remembrance** to support feedback loops. Wires can temporarily retain their previous values during circuit evaluation, allowing the cross-coupled NOR implementation to stabilize.

This behavior is specific to the current iHDL simulation model and is how sequential logic is implemented without native storage primitives.

Note: iHDL currently does not support inline comments inside HDL source files. Design explanations and implementation details are documented in the project's Markdown files instead.

### D Flip-Flop Behavior

A newly created D flip-flop starts in an **ERROR** state because no value has been stored yet. This is equivalent to the undefined (gray) startup state commonly shown in digital logic textbooks.

After the first complete clock cycle, the flip-flop stores the input value and behaves normally.

For synchronous circuits, inputs should only be changed once per **full clock cycle**. Although intermediate values can be observed during half-steps for debugging purposes, the architectural state of the circuit should be considered only after a complete clock cycle.

This behavior is compatible with the synchronous execution model used by the Hack Computer architecture while also exposing the internal operation of the master-slave implementation for educational purposes.

## FLOAT

`FLOAT` is the floating-gate-based memory cell used by the memory subsystem.

It provides persistent storage of a single bit and supports:

- Loading/writing the stored bit.
- Reading the stored bit.
- Resetting the stored bit.

Multiple `FLOAT` cells are combined to construct larger memory modules.

The floating-gate storage allows the memory state to be retained independently of the normal register-based datapath.

## RAM8

`RAM8` is an 8-word memory module composed of eight 16-bit registers.

### Inputs

| Name | Width | Description |
|------|------:|-------------|
| `IN` | 16 | Data to be written. |
| `LOAD` | 1 | When `1`, writes `IN` to the selected address on the next clock cycle. When `0`, performs a read operation. |
| `RESET` | 1 | Used to INITIALIZE the registers. |
| `ADDR` | 3 | Selects one of the eight registers (`000`–`111`). |
| `CLK` | 1 | Clock input. |

### Output

| Name | Width | Description |
|------|------:|-------------|
| `OUT` | 16 | Contents of the selected register. |

### Initialization

Each register powers up in an undefined (`ERROR`) state. Before using the RAM, initialize every register to a known value.

1. Set:
   - `RESET = 1`
   - `CLK = 0`
2. Perform one half clock cycle.
3. Set:
   - `RESET = 0`

The RAM is now fully initialized and can be used for normal read and write operations without producing `ERROR` values from uninitialized registers.

> **Note:** iHDL explicitly represents uninitialized storage elements as `ERROR`. Initializing all registers before use ensures deterministic behavior.

## VRAM4K

`VRAM4K` is a 4K-word video memory module used to store the framebuffer for the display.

Each word is 16 bits wide.

```text
4K × 16 bits = 65,536 bits
```

The module supports writing 16 bits at a time and outputs all 65,536 stored bits.

Two `VRAM4K` modules can be combined to provide:

```text
65,536 + 65,536 = 131,072 bits
```

The display resolution is:

```text
256 × 512 = 131,072 pixels
```

Therefore, two `VRAM4K` modules provide exactly enough bits for one complete 256 × 512 framebuffer.

```text
VRAM4K ──┐
         ├── 131,072-bit framebuffer ──> Display
VRAM4K ──┘
```

VRAM is separate from the normal RAM subsystem and is dedicated to storing display data.

## Implemented

- SR Latch
- Clocked SR Latch
- D Latch
- Edge-triggered SR Latch
- D Flip-Flop
- 1-bit Register
- 16-bit Register
- RAM8
- Program Counter
- RAM64
- RAM512
- RAM4K
- RAM32K
- VRAM (Display Memory)
- ROM

## Philosophy

The memory system is implemented from primitive logic gates without relying on built-in storage components, making every level of abstraction explicit and educational.
