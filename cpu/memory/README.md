# Memory Layer

The memory subsystem is currently under development.

The objective is to build sequential logic from first principles.

## Learning Path

Primitive Gates -> SR Latch -> Clocked SR Latch -> Edge Triggered SR Latch -> D Flip Flop -> Register -> RAM

## SR Latch Implementation

The SR latch is the first sequential component implemented in iHDL.

Unlike real hardware, iHDL currently uses **wire remembrance** to support feedback loops. Wires can temporarily retain their previous values during circuit evaluation, allowing the cross-coupled NOR implementation to stabilize.

This behavior is specific to the current iHDL simulation model and is how sequential logic is implemented without native storage primitives.

Note: iHDL currently does not support inline comments inside HDL source files. Design explanations and implementation details are documented in the project's Markdown files instead.

## Implemented

- SR Latch
- Clocked SR Latch
- D Latch
- Edge-triggered SR Latch
- D Flip-Flop

## Planned

- 1-bit Register
- 16-bit Register
- Program Counter
- RAM8
- RAM64
- RAM512
- RAM4K
- RAM16K

## Philosophy

The memory system is implemented from primitive logic gates without relying on built-in storage components, making every level of abstraction explicit and educational.
