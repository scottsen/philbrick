# Platform Architecture: Layers & Interfaces

## The Core Principle
**Modules should be substrate-agnostic black boxes.**

A module can be:
- Analog circuitry (op-amps, transistors, filters)
- Digital DSP (ARM, FPGA, specialized ICs)
- Analog neural inference (crossbar arrays, memristors)
- Hybrid (analog + digital compute)
- Future: biological, optical, or other exotic substrates

**The interface makes them interchangeable.**

---

## The Seven Layers

### Layer 0: Physical Essence (de Forest)
**Substrate:** Electrons, transistors, tubes, resistors, capacitors, silicon

**In Your System:**
- Discrete analog electrical behavior
- Low-noise signal paths
- Transistor/op-amp "character"
- Physical continuous-time dynamics

**Modules:** Gain, clip, buffer, saturation, tone stacks

---

### Layer 1: Stability & Refinement (Black)
**Principle:** Negative feedback and linearization

**In Your System:**
- Controlled feedback loops
- Distortion control
- Gain structure
- Predictable behavior across modules

**Modules:** Feedback blocks, dynamic shapers, compression primitives

---

### Layer 2: Functional Analog Blocks (Philbrick)
**Principle:** Modular plug-in analog computation

**In Your System:**
- Standardized 10-pin connector interface
- Composable building blocks
- Routing between blocks
- Physical signal chain composition

**Critical:** This is your revolution - standardized analog function modules with professional-grade isolation that can be rearranged at will.

---

### Layer 3: Digital Control & Logic (Shannon)
**Principle:** Information, switching, and discrete signals

**In Your System:**
- Module identification (I²C/SPI)
- Digital modulation and clocking
- Routing matrices
- Presets, memory, states
- Clean analog/digital separation

**Modules:** LFO controllers, sequencers, MIDI blocks, routing matrices

---

### Layer 4: Voltage-Controlled Expression (Moog)
**Principle:** Musical modularity and human interface

**In Your System:**
- Knobs, sliders, footswitches
- Expression pedals
- Performance macro controllers
- CV interaction
- MIDI/USB integration

---

### Layer 5: Routing & Orchestration (Turing)
**Principle:** Programmable composition and scheduling

**In Your System:**
- Motherboard routing
- Multi-module coordination
- Scene/preset memory
- Topology switching
- Real-time control changes

---

### Layer 6: Experience & Feel (Neve, Moog, Buchla)
**Principle:** Aesthetic experience and musical interface

**In Your System:**
- CNC enclosures
- Modular faceplates
- Tactile workflow
- Visual feedback
- Touring reliability

---

## Power Architecture

### The 48V Decision

**Why 48V at the top tier:**
1. **Massive analog headroom** - critical for clean analog computation
2. **Lower noise floor** (better SNR)
3. **Lower current draw** for same power (less heat, thinner traces)
4. **Industry standard** (telecom, studio phantom power)
5. **Safe** (below shock hazard ~50-60V)

**Three-Tier Power Distribution:**

```
Tier 1: 48V Backbone (power distribution)
  ↓
Tier 2: ±12V Analog Rails (on-module DC-DC conversion)
  ↓
Tier 3: +5V, +3.3V Digital Rails (on-module regulators)
```

**Key Insight:** Each module generates its own operating rails from 48V, preventing cross-module noise.

**Grounding Strategy:**
- AGND (analog ground)
- DGND (digital ground)
- Separate domains with local regulation
- Prevent digital switching noise from polluting analog

---

## Module Pin Standard

### 10-Pin Standard (FINALIZED)

**Philbrick uses a unified 10-pin connector standard for all modules.** This decision prioritizes professional-grade signal integrity, proper analog/digital isolation, and future-proofing over initial simplicity.

**Pin Assignments:**
1. **AIN** - Analog Input
2. **AOUT** - Analog Output
3. **AVDD** - Analog Supply (+12V from 48V backbone)
4. **DVDD** - Digital Supply (+5V or +3.3V from 48V backbone)
5. **AGND** - Analog Ground (isolated ground domain)
6. **DGND** - Digital Ground (isolated ground domain)
7. **CV** - Control Voltage (0-5V analog control)
8. **CTRL** - Digital Control (I²C SDA or GPIO)
9. **CLK** - Clock/Sync (I²C SCL or shared timing reference)
10. **ID/RESET** - Module identification or reset signal

**Why 10 pins is the right choice:**

1. **Proper Analog/Digital Isolation** - Separate power rails (AVDD/DVDD) and ground domains (AGND/DGND) prevent digital switching noise from polluting analog signals. This is critical for high-fidelity audio and precise analog computation.

2. **Future-Proof** - Supports all module types from day one:
   - Pure analog modules (use AVDD/AGND only)
   - Pure digital/DSP modules (use DVDD/DGND only)
   - Hybrid modules (use both domains with proper isolation)
   - Analog-neural inference chips (require separate domains)

3. **Professional-Grade Signal Integrity** - Dual ground domains are standard practice in professional audio and precision analog design. We're not building toys.

4. **Simplified Ecosystem** - One connector standard means:
   - No "basic vs advanced" module tiers
   - No compatibility confusion
   - Consistent mechanical design
   - Universal motherboard routing

5. **BOM Cost Negligible** - 10-pin JST-PH or similar connectors add ~$0.10-0.20 per module vs 6-pin. Worth it for proper engineering.

6. **Educational Value** - Teaches proper analog/digital domain separation from the start, not as an afterthought.

**Simplified Modules:** Pure analog modules can leave digital pins unconnected. Simple modules only populate AVDD/AGND and signal pins. The 10-pin standard doesn't force complexity - it enables it when needed.

---

## ADC/DAC Strategy

### Where Converters Live: **Inside Each Module**

**NOT on the main board.** Each module handles its own domain crossing.

**Why:**
- Modules are self-contained
- Optimal ADC/DAC selection per use case
- Simplest backplane
- Best signal isolation
- Least noise
- Future-proof

### Converter Types by Use Case

| Type | Speed | Resolution | Latency | Use Case |
|------|-------|-----------|---------|----------|
| SAR | 50kS-5MS/s | 12-24 bit | ~µs | Audio→DSP, CV, sensors |
| Delta-Sigma | 20-200kHz | 16-32 bit | 1-4ms | High-fidelity audio |
| Flash | GHz | 6-8 bit | ~ns | Chaos capture, neuromorphic |
| Pipeline | 10-200MS/s | 12-16 bit | ~10ns | DSP-grade audio, FFT |

**The Domain Crossing Contract:**

All modules MUST declare:
- `AD_latency`
- `DA_latency`
- `Internal_latency`
- `Bandwidth`
- `Precision`
- `Signal_ground_domain`

This prevents analog feedback loops from being broken by unexpected digital delays.

---

## Latency Protocol

### Automatic Latency Measurement & Propagation

**Core Idea:** Each module carries a "latency fingerprint" that propagates through the signal chain.

**Implementation:**
1. **Controller embeds timestamp in every packet**
   ```
   CMD + T_issue (global microseconds)
   ```

2. **Module measures internal processing time**
   - Simple: Host measures round-trip time
   - Advanced: Module reports `t_in_local`, `t_out_local`

3. **Controller computes latency:**
   ```
   module_latency = T_response - T_issue - bus_overhead
   ```

4. **Accumulate along chain:**
   ```
   Total_latency = Σ(module_latencies) + routing_overhead
   ```

**Benefits:**
- No distributed clock sync needed
- Works with analog-only, digital-only, and hybrid modules
- Controller knows full path delay
- Can block unsafe feedback loops
- Can warn about "laggy feel"

**Latency Classes:**
- Ultra-low: < 0.5 ms (pure analog, feedback-safe)
- Live: 0.5-5 ms (playable, real-time performance)
- High: 5-20 ms (acceptable for non-critical paths)
- Offline: > 20 ms (rendering, heavy processing)

---

## Module Self-Description Protocol

### Borrowed from USB Descriptors

Every module exposes:

```c
struct ModuleDescriptor {
  uint16 vendor_id;
  uint16 product_id;
  uint8 module_type;  // ANALOG, DSP, HYBRID, NEURAL
  uint16 firmware_version;
  uint8 num_modes;
  uint8 num_interfaces;
}

struct ModeInfo {
  uint8 mode_id;           // 0=eco, 1=live, 2=hq
  float latency_us;
  uint8 quality_level;
  float power_mw;
  uint8 flags;             // CAN_FEEDBACK, REALTIME_SAFE, etc.
}

struct ModuleTimingInfo {
  float latency_nominal_us;
  float latency_measured_us;
  uint8 confidence;
  uint8 latency_type;      // CONTINUOUS_ANALOG, DSP_BLOCK, etc.
  float sample_rate_hz;
}
```

**Discovery Process:**
1. Controller scans all module addresses on bus
2. Reads descriptors from known offset (I²C/SPI)
3. Builds routing graph and capability map
4. Optionally runs calibration to measure actual latency

**Mode Switching:**
- Controller can ask modules to switch modes based on global latency budget
- Example: "We're over budget → go to lower-latency 'live' mode"
- Modules adjust: smaller buffers, simpler algorithms, bypass extras

---

## Dev Board Architecture

**Note**: This section describes two different types of boards:
1. **Universal Dev Board** - Development platform for testing operator modules (~$48, see [DEV_BOARD_README.md](DEV_BOARD_README.md))
2. **Operator Module Boards** - Individual function modules that plug into the dev board (~$2.50-5 each, described below)

### Universal Development Platform (Completed Design)

**Purpose**: Test and develop operator modules

**Key Specs**:
- RP2040 MCU (dual Cortex-M0+, $1)
- 16-bit ADC (6 channels) + 12-bit DAC (4 channels)
- ±15V power generation for operators
- 6x 10-pin operator connectors
- USB-C connectivity
- **Total BOM: ~$48** (qty 10)

**See**: [DEV_BOARD_README.md](DEV_BOARD_README.md) for complete design documentation

---

### Individual Operator Module Boards (Future Design)

**Core Component:** Cheap Cortex-M MCU ($0.10 - $1.50)
- Cortex-M0+/M3/M4 @ 48-120 MHz
- 64-256 KB flash, 16-64 KB RAM
- Optional USB for debugging
- Multiple ADCs (10-12 bit)
- Timers, PWMs, SPI/I²C/UART

**Built-in Features:**
- Power regulation: ±15V → ±12V/+5V/+3.3V ($0.30)
- Input/output buffer op-amps ($0.20)
- Protection (overvoltage, reverse polarity, ESD) ($0.20)
- Auto-discovery EEPROM ($0.11)
- 10-pin connector ($0.20)
- Programming header (SWD/UART)

**Total BOM: ~$2.50 in volume, sell for $5-10**

**What This Enables:**
- Every module can be semi-smart
- Self-calibration, health reporting, preset storage
- Digital + analog modules look identical
- Kids can build boutique-quality modules with zero EE background
- Firmware becomes the differentiator

---

## Signal Flow Example

### "Crappy Guitar → Sounds Like Symphony"

```
User Input (guitar pickup)
  ↓
[Analog Buffer Block] - pure analog (AVDD/AGND only), 0.01ms
  ↓
[ML Body Model Block] - hybrid (analog I/O + neural inference, both domains), 3ms
  ↓
[Analog Color Block] - pure analog (transformer saturation), 0.02ms
  ↓
[DSP Reverb Block] - digital (DVDD/DGND + control), 8ms
  ↓
Main Output

Total Latency: ~11ms (acceptable for non-feedback path)
Controller knows this, tags chain as "high-latency, not for monitoring"
```

**Key Point:** All modules use the same 10-pin connector. Some use only analog domain, some only digital, some both. The standard accommodates all use cases without requiring different connector types.

---

## Key Design Principles

1. **Substrate Agnostic:** Modules can be anything that obeys the interface
2. **Deterministic Latency:** Every path has known, measurable delay
3. **Safe Composition:** System prevents unsafe feedback loops
4. **Progressive Complexity:** Start simple (analog), add smarts as needed
5. **Open Ecosystem:** Third parties can build modules easily
6. **Unified Time:** Shared clock/timestamp enables global coordination
7. **Self-Describing:** Modules advertise capabilities like USB devices

---

## Next Steps

See:
- `03-PRIMITIVES.md` - The four core operations everything builds from
- `04-MODULES.md` - Specific module types and designs
- `05-PROTOCOL.md` - Detailed bus protocol specification
- `06-KAIRO-BRIDGE.md` - How this connects to Kairo
