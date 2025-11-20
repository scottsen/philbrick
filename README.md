# Philbrick

**Modular Analog/Digital Hybrid Computing Platform**

> *"LEGO for continuous-time computation."*

---

![Status](https://img.shields.io/badge/status-design%20phase-blue)
![License](https://img.shields.io/badge/license-CERN--OHL--S%20%2B%20MIT-green)
![Version](https://img.shields.io/badge/version-0.1.0--alpha-orange)

---

## What This Is

**Philbrick** is a universal substrate for **modular analog computation** that enables emergent complexity from simple continuous-time primitives.

Build systems where:
- **Simple primitives** (sum, integrate, nonlinearity, events) compose into infinite complexity
- **Analog and digital** coexist seamlessly through clean abstractions
- **Modules are substrate-agnostic** - op-amps, DSP chips, neural accelerators, or future exotic tech
- **Computation becomes composition** - emergent behavior from modular building blocks

### Who This Appeals To

- **Musicians & Guitarists** - Modular pedals with infinite reconfigurability
- **Synth Users** - Eurorack-adjacent but cleaner, more modern
- **Makers & Hackers** - Accessible dev boards ($5-10), open ecosystem
- **ML Researchers** - Analog inference accelerators, neuromorphic substrates
- **Educators** - Teaching analog + digital + DSP from one platform
- **Scientists** - Research substrate for continuous-time computation

---

## The Vision

> *"The universe is analog. Digital is a hack."*

We're building a platform where continuous-time dynamics meet modular composition - bridging the gap between pure analog elegance and digital precision.

**Started as** "modular guitar pedals"
**Evolved into** a universal substrate for analog computation
**Destined to become** the Arduino of continuous-time systems

---

## Why "Philbrick"?

Named after **George A. Philbrick (1913-1974)**, who invented **commercial modular analog computing blocks** in 1952.

Philbrick created:
- The first plug-in operational amplifier modules (K2-W op-amp)
- Standardized form factors for analog computation
- Modular systems **decades before Moog**
- The philosophy that electronics should be **compositional blocks**, not fixed circuits

**We are literally reviving his vision** for modern makers, musicians, and researchers.

---

## The Four Primitives

Everything in signal processing reduces to **four core operations**:

### 1. **Summation**
Linear combination, mixing, gain staging, feed-forward networks

### 2. **Integration**
Dynamics over time, filters, envelopes, oscillators, physical modeling

### 3. **Nonlinearity**
Squashing, clipping, distortion, activation functions, character

### 4. **Events**
Sampling, triggers, discrete transitions, gates, clocks

**Just as DNA uses 4 bases (A, C, G, T) to create all life, Philbrick uses 4 primitives to create all signal processing.**

Everything else emerges through composition.

---

## What Makes This Different

### Not Incremental - Paradigm Shift

**Existing Solutions:**
- **Eurorack** - Analog-only, cable-heavy, 1000s of redundant modules
- **Guitar Pedals** - Fixed-function, non-modular
- **DSP Platforms** - Digital-only, latency issues
- **Neuromorphic Chips** - Research-only, inaccessible

**Philbrick:**
- ✅ **First professional-grade modular analog computation platform**
- ✅ **Seamless analog/digital hybrid** with automatic latency management
- ✅ **Substrate-agnostic modules** (analog, digital, neural, hybrid)
- ✅ **Software ↔ Hardware bridge** with [Morphogen](https://github.com/scottsen/morphogen)
- ✅ **Accessible but sophisticated** ($5-10 dev boards, open ecosystem)
- ✅ **Historical legitimacy** (Philbrick → Moog → Us)

---

## The Architecture

### Seven Layers, Each Honoring a Pioneer

| Layer | Domain | Patron | Contribution |
|-------|--------|--------|--------------|
| 0 | Physical substrate | Lee de Forest | Amplification (triode, 1906) |
| 1 | Stability & feedback | Harold Black | Negative feedback (1927) |
| 2 | **Modular blocks** | **George A. Philbrick** | **Modular analog computing (1952)** |
| 3 | Logic & routing | Claude Shannon | Information theory (1948) |
| 4 | Expression | Robert Moog | Voltage-controlled modularity (1964) |
| 5 | Orchestration | Alan Turing | Universal computation (1936) |
| 6 | Aesthetics | Rupert Neve | Musical electronics |

**Creates "computational mythology" rooted in real history.**

---

## Technical Highlights

### Power Architecture
- **48V system backbone** (for large installations with many modules)
- **12-24V dev board input** (USB-C or barrel jack for prototyping)
- On-module regulation to ±15V analog, +5V, +3.3V
- Separate analog/digital ground domains with star topology

### Pin Standard (Finalized)
- **10-pin universal connector** (2x5, 0.1" pitch header)
- **Pinout**: +15V, -15V, GND, INPUT_A, INPUT_B, OUTPUT, SENSE, ENABLE, I2C_SDA, I2C_SCL
- Proper analog/digital isolation from day one
- Supports pure analog, pure digital, and hybrid modules
- Standard IDC ribbon cable compatible (~$0.10-0.20 per connector)

### Latency Protocol
- **Automatic latency measurement** along signal chains
- Controller embeds timestamps in packets
- Modules report processing time
- System prevents unsafe feedback loops
- Mode switching (eco/live/HQ) based on budget

### Module Self-Description
- **USB descriptor-inspired** protocol
- Plug-and-play discovery
- Modules declare vendor, type, modes, latency, capabilities
- Controller builds routing graph automatically

### Universal Dev Board (Design Complete ✅)
- **RP2040 MCU** (dual Cortex-M0+, ~$1)
- **16-bit ADC** (ADS1115, 6 channels) + **12-bit DAC** (MCP4728, 4 channels)
- **±15V power** for operators, power regulation, USB-C connectivity
- **Total BOM ~$48** (qty 10, complete dev board with 6 operator channels)
- See [Dev Board Documentation](docs/architecture/DEV_BOARD_README.md)
- Makes analog computing accessible to **software people** and **makers**

---

## Example: "Crappy Guitar → Symphony"

```
User Input (guitar pickup)
  ↓
[Analog Buffer] - pure analog, 0.01ms
  ↓
[ML Body Model] - hybrid (analog I/O, neural inference), 3ms
  ↓
[Analog Color] - transformer saturation, 0.02ms
  ↓
[DSP Reverb] - digital, 8ms
  ↓
Main Output

Total Latency: ~11ms (acceptable for non-feedback path)
```

**Some modules analog, some digital, some neural. User doesn't care. System manages it automatically.**

---

## The Morphogen Connection

**Philbrick** (hardware) and **[Morphogen](https://github.com/scottsen/morphogen)** (software) are **sister projects** - two reflections of the same deep architecture in different media.

| Aspect | Morphogen (Software) | Philbrick (Hardware) |
|--------|---------------------|---------------------|
| Purpose | Digital simulation of continuous phenomena | Physical embodiment of continuous dynamics |
| Primitives | Streams, fields, transforms | Sum, integrate, nonlinearity, events |
| Safety | Type system (domain/rate/units) | Pin contracts (voltage/impedance) |
| Execution | Multirate deterministic scheduler | Latency-aware routing fabric |
| Philosophy | **Computation = composition** | **Computation = composition** |

**They are the software and hardware halves of one vision.**

---

## Getting Started

### Documentation

**Start Here:**
- [Vision & Philosophy](docs/vision/00-VISION.md) - The big picture and why this matters
- [Architecture Overview](docs/architecture/00-OVERVIEW.md) - Technical design and seven layers
- [The Four Primitives](docs/primitives/OPERATOR_PRIMITIVES.md) - Core operations explained

**Dev Board Design (Complete):**
- [Dev Board Overview](docs/architecture/DEV_BOARD_README.md) - Start here for hardware
- [Reading Guide](docs/architecture/DEV_BOARD_READING_GUIDE.md) - What to read and when
- [Specifications](docs/architecture/DEV_BOARD_SPECIFICATION.md) - Requirements and subsystems
- [Block Diagram](docs/architecture/DEV_BOARD_BLOCK_DIAGRAM.md) - System architecture
- [Components & BOM](docs/architecture/DEV_BOARD_COMPONENTS.md) - Parts list with costs

**Additional Documentation:**
- [The Pantheon](docs/vision/01-PANTHEON.md) - Tooling and ecosystem vision
- [Morphogen Bridge](docs/vision/03-MORPHOGEN-BRIDGE.md) - Software/hardware integration

### Quick Links

- 📖 [Full Documentation](docs/) - All documentation organized by topic
- 🗺️ [Roadmap](ROADMAP.md) - Development timeline and current status
- 🔧 [Dev Board Docs](docs/architecture/DEV_BOARD_README.md) - Hardware design (complete)
- 🧠 [Vision Docs](docs/vision/) - Philosophy and long-term goals
- ⚡ [Operator Primitives](docs/primitives/) - Core building blocks

---

## Roadmap

### Phase 1: Prove the Concept (Weeks 1-4) - **IN PROGRESS**
- [x] Define final pin standard (10-pin universal standard adopted)
- [x] **Dev board design documentation complete** (specs, block diagram, BOM)
- [ ] Dev board schematics (KiCad) - **NEXT STEP**
- [ ] Build 4 primitive modules (sum, integrate, nonlinearity, trigger)
- [ ] Implement basic latency protocol
- [ ] Demonstrate composition: simple modules → complex behavior

See [ROADMAP.md](ROADMAP.md) for detailed timeline and weekly breakdown.

### Phase 2: Establish Ecosystem (Months 2-3)
- [ ] Release dev board as open hardware
- [ ] Publish module descriptor spec
- [ ] Build 10-12 reference modules
- [ ] Create firmware templates
- [ ] Developer documentation
- [ ] First third-party modules

### Phase 3: Morphogen Integration (Months 3-6)
- [ ] Map Morphogen operators → hardware primitives
- [ ] Prototype Morphogen → firmware compilation
- [ ] Bidirectional testing (Morphogen validates hardware)
- [ ] Shared descriptor language

### Phase 4: Advanced Modules (Months 6-12)
- [ ] Analog-neural inference chips
- [ ] ML body modeling modules
- [ ] High-fidelity hybrid modules
- [ ] Performance controllers

### Phase 5: Platform Maturity (Year 2+)
- [ ] Educational curriculum
- [ ] Research partnerships (neuromorphic, analog ML)
- [ ] Commercial ecosystem
- [ ] Industry standard adoption

---

## The Pantheon: Standing on Giants' Shoulders

Every layer of Philbrick honors a pioneer:

- **Lee de Forest** (1873-1961) - Invented the triode vacuum tube, enabling electronic amplification
- **Harold Black** (1898-1983) - Invented negative feedback, stabilizing analog systems
- **George A. Philbrick** (1913-1974) - Invented modular analog computing blocks
- **Claude Shannon** (1916-2001) - Created information theory, digital logic design
- **Alan Turing** (1912-1954) - Founded computation theory, created universal machines
- **Robert Moog** (1934-2005) - Pioneered voltage-controlled modular synthesis
- **Rupert Neve** (1926-2021) - Defined musical analog electronics

**We stand on their shoulders. This is their legacy, continued.**

---

## Key Insights

### 1. DNA-Level Simplicity
Just as DNA uses 4 bases to create all life, Philbrick uses **4 operations** to create all signal processing.

### 2. Substrate Agnostic
A "module" can be **anything** that obeys the interface:
- Pure analog (op-amps, transistors)
- Pure digital (DSP, FPGA)
- Analog neural (crossbar arrays)
- Hybrid (analog + digital)
- Future: biological, optical, quantum-inspired

**The abstraction makes them interchangeable.**

### 3. Philbrick and Morphogen Are Mirrors
Software (Morphogen) and hardware (Philbrick) implement the same vision in different substrates. They will eventually compile to each other.

### 4. Emergence IS the Architecture
Morphogenesis perfectly describes what we do: **4 primitives → infinite complexity** through composition.

### 5. Historical Grounding
We're not inventing from scratch - we're reviving Philbrick's profound vision with modern technology.

---

## Design Principles

1. **Substrate Agnostic** - Modules can be any technology that obeys the interface
2. **Deterministic Latency** - Every signal path has known, measurable delay
3. **Safe Composition** - System prevents unsafe feedback loops automatically
4. **Progressive Complexity** - Start simple (analog), add sophistication as needed
5. **Open Ecosystem** - Third parties can build modules easily
6. **Unified Time** - Shared clock/timestamp enables global coordination
7. **Self-Describing** - Modules advertise capabilities like USB devices

---

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for:
- How to contribute hardware designs
- Firmware development guidelines
- Documentation standards
- Community expectations

### Areas We Need Help

- **Hardware Design** - Reference module schematics
- **Firmware** - Module firmware implementations
- **Documentation** - Guides, tutorials, examples
- **Testing** - Validation procedures
- **Education** - Curriculum development
- **Research** - Analog ML, neuromorphic computing

---

## License

**Hardware**: [CERN Open Hardware License v2 - Strongly Reciprocal](LICENSE-HARDWARE)
**Firmware/Software**: [MIT License](LICENSE-SOFTWARE)

We believe in open hardware and open source. Build on our work, but share improvements back with the community.

---

## Community

- **Discussions**: [GitHub Discussions](https://github.com/scottsen/philbrick/discussions)
- **Issues**: [GitHub Issues](https://github.com/scottsen/philbrick/issues)
- **Project Showcase**: [community/showcase/](community/showcase/)
- **Third-Party Modules**: [community/third-party-modules/](community/third-party-modules/)

---

## Related Projects

- **[Morphogen](https://github.com/scottsen/morphogen)** - Sister project, digital simulation platform
- **Eurorack** - Modular synthesis standard (analog-only inspiration)
- **Neuromorphic Computing** - Research field for brain-inspired analog compute
- **Analog ML Accelerators** - Mythic AI, IBM RRAM, etc.

---

## The Elevator Pitch

### Long Version
> **"We're building the first modular analog/digital hybrid computing platform - a universal substrate where guitar pedals, neural accelerators, DSP blocks, and analog circuits seamlessly compose into emergent systems. It's Eurorack meets Morphogen meets Philbrick, with modern tech and accessible dev boards."**

### Short Version
> **"LEGO for continuous-time computation."**

### One Sentence
> **"Modular analog computing platform where simple primitives compose into complex continuous-time systems."**

---

## Status

**Current Phase**: Design & Documentation (v0.1.0-alpha)

**Key Areas for Input**:
- 10-pin connector selection (JST-PH, Molex, or custom)
- Protocol specification review
- Reference module designs
- Dev board BOM optimization
- Morphogen integration strategy

---

## Acknowledgments

**George A. Philbrick** - For inventing modular analog computing in 1952 and showing us the way.

**The Pantheon** - de Forest, Black, Shannon, Turing, Moog, Neve - for creating the foundations we build on.

**The Community** - For believing in the vision of accessible, modular, continuous-time computation.

---

## Contact

- **GitHub**: [@scottsen](https://github.com/scottsen)
- **Project**: [Philbrick](https://github.com/scottsen/philbrick)
- **Sister Project**: [Morphogen](https://github.com/scottsen/morphogen)

---

*"The universe computes in analog. We model it in Morphogen. We embody it in Philbrick. This is the full circle."*

---

**Philbrick** - Modular Analog/Digital Hybrid Computing Platform
**Version**: 0.1.0-alpha
**License**: CERN-OHL-S (hardware) + MIT (software)
**Status**: Design Phase
**Named After**: George A. Philbrick (1913-1974)
