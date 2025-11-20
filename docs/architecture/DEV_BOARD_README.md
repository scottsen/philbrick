# Universal Dev Board - Design Documentation

**Status**: Design Phase Complete ✅
**Date**: 2025-11-20
**Next Phase**: Schematic Capture (KiCad)

---

## What We've Completed

The universal dev board design documentation is complete. You now have everything needed to begin schematic capture and PCB layout.

### 📚 Documentation Set

1. **[DEV_BOARD_READING_GUIDE.md](DEV_BOARD_READING_GUIDE.md)**
   - Start here if you're new to the project
   - Lists which docs to read and in what order
   - Time estimates: ~2 hours total

2. **[DEV_BOARD_SPECIFICATION.md](DEV_BOARD_SPECIFICATION.md)**
   - Complete requirements specification
   - 5 major subsystems defined
   - Success criteria and design constraints
   - Open questions to resolve

3. **[DEV_BOARD_BLOCK_DIAGRAM.md](DEV_BOARD_BLOCK_DIAGRAM.md)**
   - System architecture with ASCII diagrams
   - Signal flow (measurement and generation paths)
   - Power distribution topology
   - Ground architecture (AGND/DGND separation)
   - 10-pin connector pinout specification

4. **[DEV_BOARD_COMPONENTS.md](DEV_BOARD_COMPONENTS.md)** ← You are here
   - Specific part numbers for all major components
   - Alternatives considered with justifications
   - Preliminary BOM: **~$48** (qty 10)
   - Passive components summary

---

## Quick Summary

### Core Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| **Operator Channels** | 6 | Via 10-pin connectors |
| **Operator Power** | ±15V @ 500mA | Per rail, total 3W |
| **ADC Resolution** | 16-bit | 2x ADS1115 |
| **DAC Resolution** | 12-bit | 1x MCP4728 (quad) |
| **Signal Range** | ±10V | Standard for analog computing |
| **MCU** | RP2040 | Dual Cortex-M0+, $1 |
| **Connectivity** | USB-C | Power + data |
| **BOM Cost** | ~$48 | Qty 10, excluding PCB |
| **PCB Size** | 100x100mm | Target (may adjust) |

### Key Design Decisions

1. **RP2040 over STM32**: $1 vs $5, sufficient performance, better USB support
2. **External ADC/DAC**: 16-bit ADC for accuracy, I2C interface simplifies wiring
3. **±15V for operators**: Standard analog computing voltage, allows ±10V signals
4. **TPS65131 for ±15V**: Boost+inverter IC, 200mA capacity, compact
5. **6 channels initially**: Balance between capability and cost/complexity
6. **10-pin connector**: Matches architecture standard (power + signals + I2C)

---

## What This Board Does

The universal dev board serves as the **bridge between analog operators and the digital world**:

```
┌─────────────┐       ┌──────────────┐       ┌──────────────┐
│  Operator   │◄─────►│  Dev Board   │◄─────►│  Computer    │
│  Modules    │       │  (This PCB)  │       │  (USB Host)  │
│             │       │              │       │              │
│ - Summer    │  ±15V │ - ADC: Read  │  USB  │ - Monitor    │
│ - Integr.   │  ±10V │ - DAC: Write │  Data │ - Control    │
│ - Nonlinear │ Signal│ - MCU: Think │       │ - Log        │
│ - Trigger   │       │ - Power: Feed│       │ - Visualize  │
└─────────────┘       └──────────────┘       └──────────────┘
```

### Primary Functions

1. **Power Distribution**: Provide ±15V to up to 6 operator modules
2. **Measurement**: Read operator outputs (±10V) via 16-bit ADC
3. **Stimulus**: Generate signals (±10V) for operator inputs via 12-bit DAC
4. **Communication**: USB connection to host computer for control/data
5. **Protection**: Safeguard operators and itself from electrical faults
6. **Instrumentation**: Built-in monitoring, LEDs, test points

---

## Development Phases

### ✅ Phase 1: Design Documentation (COMPLETE)
- [x] Requirements specification
- [x] Block diagrams and signal flows
- [x] Component selection with part numbers
- [x] Preliminary BOM with cost estimate
- [x] Design constraints and success criteria

### 🔄 Phase 2: Schematic Capture (NEXT)
- [ ] Set up KiCad project
- [ ] Draw power supply schematic
- [ ] Draw analog input chains (6 channels)
- [ ] Draw analog output chains (4 channels)
- [ ] Draw MCU and support circuitry
- [ ] Draw protection circuits
- [ ] Schematic review and iteration

### 📋 Phase 3: PCB Layout (UPCOMING)
- [ ] Component placement (analog/digital separation)
- [ ] Power routing (wide traces for ±15V)
- [ ] Ground planes (AGND/DGND isolation)
- [ ] Signal routing (minimize noise coupling)
- [ ] Design rule check (DRC)
- [ ] Generate Gerber files

### 🛠️ Phase 4: Fabrication & Assembly (FUTURE)
- [ ] Order PCBs (JLCPCB or OSH Park)
- [ ] Order components (Mouser/Digikey/LCSC)
- [ ] Hand assembly (or JLCPCB SMT service)
- [ ] Bring-up testing (power first, then subsystems)
- [ ] Firmware development (basic ADC/DAC test)

### 🧪 Phase 5: Validation (FUTURE)
- [ ] Power supply noise measurements
- [ ] ADC linearity and accuracy testing
- [ ] DAC linearity and accuracy testing
- [ ] Full system integration test with operator modules
- [ ] Iterative improvements for v1.1

---

## Files You'll Need Next

For KiCad schematic capture, you'll need:

1. **Component Libraries**:
   - RP2040 symbol (available from Raspberry Pi)
   - ADS1115, MCP4728 symbols (create or find)
   - Standard op-amps, regulators, passives

2. **Reference Schematics**:
   - RP2040 minimal design (from RP2040 datasheet)
   - ADS1115 typical application (from TI datasheet)
   - MCP4728 typical application (from Microchip datasheet)
   - Power supply reference designs

3. **Datasheets** (download from manufacturer sites):
   - RP2040: Raspberry Pi website
   - ADS1115: Texas Instruments
   - MCP4728: Microchip
   - TPS54331, TPS65131, REF5050: Texas Instruments
   - OPA4192, OPA2134: Texas Instruments

---

## Open Questions & Decisions Needed

Before starting schematics, resolve:

1. **SD Card**: Include or skip in v1.0? (Adds ~$1 and complexity)
2. **OLED Display**: Include header or skip? (Useful for standalone operation)
3. **Barrel Jack**: Include alongside USB-C for higher power? (Recommended: yes)
4. **DAC Channels**: 2 or 4? (Current BOM has 4-channel MCP4728)
5. **Test Points**: How many? Where? (Recommend: all rails + critical signals)
6. **Enclosure**: Design for specific case or leave open? (Future consideration)

**Recommendation**: Start with minimal feature set (no SD, no OLED, yes barrel jack, 4 DAC channels). Add features in v1.1 based on testing.

---

## How to Proceed

### If you're ready to design schematics:

1. Read the three main docs in order (Spec → Block Diagram → Components)
2. Set up KiCad 7.x or 8.x project
3. Create or import component symbols
4. Start with power supply schematic (easiest to verify in isolation)
5. Refer to block diagram for connectivity
6. Check component datasheet typical applications

### If you need to modify the design:

1. Update the relevant document (Specification, Block Diagram, or Components)
2. Ensure changes are consistent across all three docs
3. Update BOM estimate if component changes affect cost
4. Document reasons for changes

### If you're building this board:

1. Wait for schematics and PCB layout to be complete
2. Review Gerber files before ordering
3. Order a small batch (5-10 boards) for testing
4. Have soldering equipment ready (hot air station recommended for QFN/WSON)
5. Budget ~$150-200 total for first prototype run (PCBs + components)

---

## Success Metrics

This design will be successful if:

- ✅ **BOM < $50** (target achieved: ~$48)
- ✅ **Hand solderable** (SOIC/TQFP/QFN packages, no BGA)
- ✅ **Measures ±10V** (16-bit ADC with proper scaling)
- ✅ **Generates ±10V** (12-bit DAC with op-amp output stage)
- ✅ **Powers 4-6 operators** (±15V @ 500mA available)
- ⏳ **Survives misuse** (protection circuits to be validated in testing)
- ⏳ **Easy to debug** (test points, LEDs - to be verified in layout)

---

## Questions?

- See [DEV_BOARD_READING_GUIDE.md](DEV_BOARD_READING_GUIDE.md) for documentation overview
- See [DEV_BOARD_SPECIFICATION.md](DEV_BOARD_SPECIFICATION.md) for requirements
- See [DEV_BOARD_BLOCK_DIAGRAM.md](DEV_BOARD_BLOCK_DIAGRAM.md) for architecture
- See [DEV_BOARD_COMPONENTS.md](DEV_BOARD_COMPONENTS.md) for parts list

---

**Ready for Phase 2: Schematic Capture** 🚀
