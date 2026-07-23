# Memory Layer

The memory subsystem is currently under development.

The objective is to build sequential logic from first principles.

## Learning Path

Primitive Gates -> SR Latch -> Clocked SR Latch -> Edge Triggered SR Latch -> D Flip Flop -> Register -> RAM

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

## RAM8

`RAM8` is an 8-word memory module composed of eight 16-bit registers.

### Inputs

| Name | Width | Description |
|------|------:|-------------|
| `IN` | 16 | Data to be written. |
| `LOAD` | 1 | When `1`, writes `IN` to the selected address on the next clock cycle. When `0`, performs a read operation. |
| `ADDR` | 3 | Selects one of the eight registers (`000`–`111`). |
| `CLK` | 1 | Clock input. |

### Output

| Name | Width | Description |
|------|------:|-------------|
| `OUT` | 16 | Contents of the selected register. |

### Initialization

Each register powers up in an undefined (`ERROR`) state. Before using the RAM, initialize every register to a known value.

1. Set:
   - `IN = 0000000000000000`
   - `LOAD = 1`
   - `CLK = 0`
2. Starting with `ADDR = 000`, perform one complete clock cycle.
3. Increment `ADDR` and repeat for all addresses up to `111`.
4. After all eight registers have been initialized, set:
   - `LOAD = 0`
5. Perform one additional complete clock cycle.

The RAM is now fully initialized and can be used for normal read and write operations without producing `ERROR` values from uninitialized registers.

> **Note:** iHDL explicitly represents uninitialized storage elements as `ERROR`. Initializing all registers before use ensures deterministic behavior.

## Implemented

- SR Latch
- Clocked SR Latch
- D Latch
- Edge-triggered SR Latch
- D Flip-Flop
- 1-bit Register
- 16-bit Register
- RAM8

## Planned

- Program Counter
- RAM64
- RAM512
- RAM4K
- RAM16K

## Philosophy

The memory system is implemented from primitive logic gates without relying on built-in storage components, making every level of abstraction explicit and educational.
