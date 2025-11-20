# Dev Board Design - Reading Guide

If you want to plan and design the universal dev board for the Philbrick platform, read these documents in this order:

## Phase 1: Understand the Vision (30 minutes)

1. **Start Here**: `/README.md`
   - Overview of the entire Philbrick project
   - Understand the modular analog computing concept

2. **Vision Context**: `/docs/vision/00-VISION.md`
   - Core philosophy and goals
   - Why this project exists

3. **The Pantheon**: `/docs/vision/01-PANTHEON.md`
   - The operating environment and tooling vision
   - How the dev board fits into the larger ecosystem

## Phase 2: Technical Requirements (45 minutes)

4. **Architecture Overview**: `/docs/architecture/00-OVERVIEW.md`
   - System architecture and module interconnection
   - **CRITICAL**: 10-pin connector standard (Section: "Revised Interconnection Architecture")
   - Power distribution requirements
   - Signal levels and impedance standards

5. **Operator Primitives**: `/docs/primitives/OPERATOR_PRIMITIVES.md`
   - The analog operators that will connect to the dev board
   - Input/output signal characteristics
   - What the dev board must support electronically

## Phase 3: Implementation Context (30 minutes)

6. **Roadmap**: `/ROADMAP.md`
   - Phase 1, Week 2: "Dev board design" - see what's expected
   - Timeline and dependencies
   - Integration points with other milestones

## Key Dev Board Requirements (Quick Reference)

From the documents above, the dev board must:

- **Connector Standard**: 10-pin interface for all operator modules
- **Power**: Provide ±15V to operators, +5V/+3.3V for digital
- **Analog I/O**: ADC/DAC for measurement and signal injection
- **MCU**: Digital control and USB connectivity
- **Protection**: Safeguard operators and itself from misconfigurations
- **Instrumentation**: Built-in monitoring and debugging capabilities

## Optional Deep Dives

If designing specific subsystems, also read:

- **Morphogen Bridge** (`/docs/vision/03-MORPHOGEN-BRIDGE.md`): If implementing pattern generation or advanced signal synthesis
- **Primitives Overview** (`/docs/primitives/00-OVERVIEW.md`): Detailed operator taxonomy

---

**Estimated Total Reading Time**: ~2 hours

**Next Step After Reading**: Create block diagram showing:
- Power subsystem
- MCU and peripherals
- Analog I/O chain
- Protection circuits
- 10-pin operator connectors
