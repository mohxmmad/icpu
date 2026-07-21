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

## Implemented

- SR Latch
- Clocked SR Latch
- D Latch
- Edge-triggered SR Latch
- D Flip-Flop
- 1-bit Register
- 16-bit Register

## Planned

- Program Counter
- RAM8
- RAM64
- RAM512
- RAM4K
- RAM16K

## Philosophy

The memory system is implemented from primitive logic gates without relying on built-in storage components, making every level of abstraction explicit and educational.
