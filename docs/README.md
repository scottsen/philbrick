# Philbrick Documentation Index

**Welcome to the Philbrick documentation!**

This index helps you find what you need quickly.

---

## 🚀 New to Philbrick? Start Here

1. **[Project README](../README.md)** - High-level overview of the entire project
2. **[Vision & Philosophy](vision/00-VISION.md)** - Why we're building this, what it means
3. **[Architecture Overview](architecture/00-OVERVIEW.md)** - Technical foundation and seven layers
4. **[Roadmap](../ROADMAP.md)** - Development timeline and current status

**Time Investment**: ~1 hour to understand the big picture

---

## 🔧 Want to Build Hardware?

### Dev Board (Universal Development Platform)
**Status**: Design documentation complete ✅ | Schematics next

Start with the **[Dev Board README](architecture/DEV_BOARD_README.md)** - it guides you through everything.

#### Complete Dev Board Documentation Set:
1. **[DEV_BOARD_README.md](architecture/DEV_BOARD_README.md)** ⭐ START HERE
   - Overview of the dev board design
   - What's complete, what's next
   - How to proceed with schematics/PCB

2. **[DEV_BOARD_READING_GUIDE.md](architecture/DEV_BOARD_READING_GUIDE.md)**
   - Which docs to read and in what order
   - Time estimates for each section

3. **[DEV_BOARD_SPECIFICATION.md](architecture/DEV_BOARD_SPECIFICATION.md)**
   - Complete requirements specification
   - 5 major subsystems (power, analog I/O, MCU, protection)
   - Success criteria and constraints

4. **[DEV_BOARD_BLOCK_DIAGRAM.md](architecture/DEV_BOARD_BLOCK_DIAGRAM.md)**
   - System architecture diagrams
   - Signal flow (measurement and generation)
   - Power distribution topology
   - Ground architecture

5. **[DEV_BOARD_COMPONENTS.md](architecture/DEV_BOARD_COMPONENTS.md)**
   - Specific part numbers for all components
   - Alternatives considered with justifications
   - Preliminary BOM: ~$48 (qty 10)

**Next Steps**: KiCad schematic capture → PCB layout → Fabrication

---

## 🧠 Understanding the System

### Vision & Philosophy
- **[00-VISION.md](vision/00-VISION.md)** - Core philosophy and "why"
- **[01-PANTHEON.md](vision/01-PANTHEON.md)** - The tooling ecosystem vision
- **[03-MORPHOGEN-BRIDGE.md](vision/03-MORPHOGEN-BRIDGE.md)** - Software/hardware integration
- **[vision/README.md](vision/README.md)** - Vision docs overview

### Architecture & Design
- **[00-OVERVIEW.md](architecture/00-OVERVIEW.md)** - Seven layers, power architecture, pin standard
- **[Dev Board Docs](architecture/DEV_BOARD_README.md)** - See section above

### Operator Primitives (The Four Core Operations)
- **[OPERATOR_PRIMITIVES.md](primitives/OPERATOR_PRIMITIVES.md)** - Detailed explanation of sum, integrate, nonlinearity, events
- **[00-OVERVIEW.md](primitives/00-OVERVIEW.md)** - Primitives overview and taxonomy

---

## 📂 Documentation Structure

```
docs/
├── README.md (this file) - Documentation index
│
├── vision/ - Philosophy and long-term vision
│   ├── 00-VISION.md - Core philosophy
│   ├── 01-PANTHEON.md - Tooling ecosystem
│   ├── 03-MORPHOGEN-BRIDGE.md - Software/hardware bridge
│   └── README.md - Vision docs overview
│
├── architecture/ - Technical design
│   ├── 00-OVERVIEW.md - Seven layers, power, pins
│   ├── DEV_BOARD_README.md - Dev board overview ⭐
│   ├── DEV_BOARD_READING_GUIDE.md - Reading order
│   ├── DEV_BOARD_SPECIFICATION.md - Requirements
│   ├── DEV_BOARD_BLOCK_DIAGRAM.md - Architecture diagrams
│   └── DEV_BOARD_COMPONENTS.md - Parts list & BOM
│
└── primitives/ - Core operations
    ├── 00-OVERVIEW.md - Primitives overview
    └── OPERATOR_PRIMITIVES.md - Detailed explanations
```

---

## 🎯 Documentation by Goal

### "I want to understand the vision"
→ Read: [Vision](vision/00-VISION.md) → [Pantheon](vision/01-PANTHEON.md) → [Architecture](architecture/00-OVERVIEW.md)

### "I want to build the dev board"
→ Read: [Dev Board README](architecture/DEV_BOARD_README.md) (it links to everything you need)

### "I want to understand the operator primitives"
→ Read: [Operator Primitives](primitives/OPERATOR_PRIMITIVES.md)

### "I want to know what's been built so far"
→ Read: [Roadmap](../ROADMAP.md) → [Dev Board README](architecture/DEV_BOARD_README.md)

### "I want to contribute"
→ Read: [README](../README.md) → [Roadmap](../ROADMAP.md) → Choose an area → Start building!

---

## 📊 Documentation Status

| Category | Status | Docs Available |
|----------|--------|----------------|
| **Vision** | ✅ Complete | 3 docs + README |
| **Architecture** | ✅ Complete | 1 overview + 5 dev board docs |
| **Primitives** | ✅ Complete | 2 docs |
| **Dev Board Design** | ✅ Complete | 5 comprehensive docs |
| **Dev Board Schematics** | 🔄 Next | KiCad files (pending) |
| **Firmware** | ⏳ Planned | Templates (Phase 1, Week 4) |
| **Module Designs** | ⏳ Planned | 4 reference modules (Phase 1, Week 3) |
| **Protocol Spec** | ⏳ Planned | Phase 1, Week 1 |

---

## 🔍 Quick Reference

### Key Specs
- **10-pin connector**: +15V, -15V, GND, INPUT_A, INPUT_B, OUTPUT, SENSE, ENABLE, I2C_SDA, I2C_SCL
- **Signal range**: ±10V (analog computing standard)
- **Dev board**: RP2040 MCU, 16-bit ADC, 12-bit DAC, 6 operator channels
- **Power**: 48V system backbone, 12-24V dev board input

### Important Decisions Made
- ✅ 10-pin connector standard (finalized)
- ✅ RP2040 MCU for dev board (~$1, excellent USB)
- ✅ ±15V for operators (allows ±10V signals)
- ✅ External 16-bit ADC (ADS1115) for precision
- ✅ 6 operator channels on dev board

---

## 💡 Tips for Reading

- **If pressed for time**: Read the README files first (project README, dev board README)
- **If building hardware**: Follow the [Dev Board Reading Guide](architecture/DEV_BOARD_READING_GUIDE.md)
- **If philosophical**: Start with [Vision docs](vision/00-VISION.md)
- **If technical**: Start with [Architecture](architecture/00-OVERVIEW.md)

---

## 📝 Contributing to Docs

Found an error? Have a suggestion? Want to add documentation?

1. All docs use GitHub-flavored Markdown
2. Keep language clear and accessible
3. Include diagrams where helpful (ASCII art is fine!)
4. Cross-reference related docs
5. Update this index when adding new docs

---

**Last Updated**: 2025-11-20
**Current Phase**: Phase 1 (Design & Prototyping)
**Next Milestone**: Dev board schematic capture
