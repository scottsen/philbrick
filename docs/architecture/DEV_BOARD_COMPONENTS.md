# Universal Dev Board - Component Selection
**Philbrick Modular Analog Computing Platform**

Version: 0.1 (Draft)
Date: 2025-11-20
Status: Component Research Phase

---

## Component Selection Philosophy

1. **Availability First**: Prefer JLCPCB/LCSC stock for easy assembly
2. **Cost Effective**: Target <$50 total BOM at qty 10
3. **Hand Solderable**: No BGA, prefer TQFP/QFN/SOIC packages
4. **Proven Technology**: Use well-documented, reliable parts
5. **Performance Balance**: Good enough for education/prototyping, not overspec'd

---

## 1. Microcontroller (MCU)

### Option A: Raspberry Pi RP2040 (RECOMMENDED)

**Part Number**: RP2040
**Package**: QFN-56 (7mm x 7mm)
**Cost**: ~$1.00 (qty 10+)

**Specifications**:
- Dual ARM Cortex-M0+ @ 133MHz
- 264KB SRAM
- 2MB external flash (via QSPI)
- 30x GPIO pins
- 2x I2C, 2x SPI, 2x UART
- 12x PWM channels
- 4x 12-bit ADC channels (internal, for monitoring)
- USB 1.1 device/host

**Pros**:
- Extremely low cost ($1 vs $5+ for STM32)
- Excellent documentation and community support
- Built-in USB bootloader (no programmer needed)
- Sufficient performance for analog monitoring/control
- PIO state machines (can implement custom protocols)

**Cons**:
- No built-in high-resolution ADC/DAC (must use external)
- Lower processing power than STM32F4 (but sufficient for this application)

**Justification**: The RP2040's low cost and excellent USB support make it ideal for a dev board. Since we're using external ADC/DAC for precision analog work anyway, the lack of high-res internal peripherals doesn't matter.

---

### Option B: STM32F405RGT6 (Alternative)

**Part Number**: STM32F405RGT6
**Package**: LQFP-64
**Cost**: ~$5.00 (qty 10+)

**Specifications**:
- ARM Cortex-M4F @ 168MHz
- 192KB SRAM, 1MB Flash
- Hardware floating-point unit (FPU)
- 3x I2C, 3x SPI, 6x USART
- 2x 12-bit DAC (internal)
- 3x 12-bit ADC (internal)
- USB OTG

**Pros**:
- More processing power
- Hardware FPU (faster math)
- Internal DACs (could save external DAC cost)

**Cons**:
- 5x more expensive than RP2040
- More complex to program initially
- Internal ADC/DAC only 12-bit (we want 16-bit for precision)

**Decision**: Use RP2040 unless testing shows performance issues.

---

## 2. Analog-to-Digital Converter (ADC)

### Selected: Texas Instruments ADS1115

**Part Number**: ADS1115IDGSR
**Package**: VSSOP-10
**Cost**: ~$3.50 (qty 10+)

**Specifications**:
- 16-bit resolution
- 4 single-ended or 2 differential inputs
- I2C interface (up to 4 devices on one bus = 16 channels total)
- Programmable gain amplifier (PGA): ±0.256V to ±6.144V
- Sample rate: 8 to 860 SPS
- Internal voltage reference
- Low power: 150µA

**Usage Plan**:
- Use 2x ADS1115 for 6 operator channels (3 channels per IC)
- Configure for ±6.144V full-scale range
- Interface via I2C (addresses 0x48 and 0x49)

**Alternatives Considered**:
- **MCP3424** (18-bit, 4-ch, I2C): Similar specs, good alternative
- **AD7606** (16-bit, 8-ch, parallel): Higher throughput but more complex interface

**Justification**: ADS1115 is a proven, low-cost solution with I2C interface that simplifies MCU connections. 16-bit resolution provides ~94µV steps at ±6.144V range, which translates to ~0.2mV at ±10V after scaling.

---

## 3. Digital-to-Analog Converter (DAC)

### Selected: Microchip MCP4728

**Part Number**: MCP4728-E/UN
**Package**: MSOP-10
**Cost**: ~$2.50 (qty 10+)

**Specifications**:
- 12-bit resolution (4096 steps)
- 4 independent channels
- I2C interface
- Internal voltage reference (2.048V) or external Vref
- Rail-to-rail output
- Low power: 210µA per channel
- Non-volatile memory (stores settings on power-down)

**Usage Plan**:
- 1x MCP4728 provides 4 DAC outputs
- Use internal 2.048V reference for stability
- Output 0-2.048V, then amplify/shift to ±10V externally
- Configure via I2C from RP2040

**Alternatives Considered**:
- **DAC8554** (16-bit, quad, SPI): Higher resolution but 4x cost
- **MCP4725** (12-bit, single, I2C): Cheaper but would need 4 ICs

**Justification**: MCP4728 provides 4 channels in one package at reasonable cost. 12-bit resolution (±5mV at ±10V) is sufficient for function generation and DC bias. Non-volatile memory is useful for saving calibration.

---

## 4. Precision Voltage Reference

### Selected: Texas Instruments REF5050

**Part Number**: REF5050ID
**Package**: SOIC-8
**Cost**: ~$2.00 (qty 10+)

**Specifications**:
- Output: 5.000V ±0.05% (±2.5mV)
- Temperature coefficient: 3ppm/°C (typical)
- Low noise: 3µVpp (0.1Hz to 10Hz)
- Output current: 10mA
- Supply voltage: 5.4V to 18V

**Usage**:
- Power from +5V rail (pre-regulator output)
- Use as ADC external reference (improves accuracy)
- Provide test point for calibration
- May also use for DAC external reference

**Alternatives Considered**:
- **ADR4550** (5V, 0.02%, lower noise): $3.50, diminishing returns
- **LM4040** (Zener-based): Cheaper but higher noise and drift

**Justification**: REF5050 provides excellent stability and low noise at reasonable cost. Critical for accurate ADC measurements.

---

## 5. Operational Amplifiers

### For Input Buffers & Signal Conditioning

**Selected**: Texas Instruments OPA4192

**Part Number**: OPA4192ID
**Package**: SOIC-14 (Quad op-amp)
**Cost**: ~$2.50 (qty 10+)

**Specifications (per amplifier)**:
- Rail-to-rail input and output
- Supply: 2.7V to 36V (works with ±15V or single +5V)
- Input offset voltage: ±25µV (max)
- Input bias current: ±0.2pA (typical)
- Bandwidth: 10MHz
- Slew rate: 20V/µs
- Noise: 5.3nV/√Hz @ 1kHz

**Usage**:
- Unity-gain buffers on ADC inputs (high impedance)
- Level shifting stages (±10V to 0-5V)
- Anti-alias filters (active or passive)
- Need ~3 ICs for 6 input channels + conditioning

**Why this part**:
- Very low input bias current (high impedance)
- Rail-to-rail operation (maximizes signal swing)
- Low noise (critical for precision analog)
- Can operate from ±15V supply

---

### For Output Buffers & Amplification

**Selected**: Texas Instruments TLV9064

**Part Number**: TLV9064IDR
**Package**: SOIC-14 (Quad op-amp)
**Cost**: ~$1.00 (qty 10+)

**Specifications**:
- Rail-to-rail input/output
- Supply: 1.8V to 5.5V (use with +5V or ±2.5V)
- Output current: ±60mA (can drive loads directly)
- Bandwidth: 10MHz
- Slew rate: 5V/µs

**Usage**:
- Initial stage for DAC outputs (0-2V to 0-5V)
- Low-impedance output buffers

**Justification**: Lower cost than OPA4192, sufficient performance for output stages. High output current capability allows driving operator inputs without additional buffering.

---

### For ±10V Output Amplifiers

**Selected**: Texas Instruments OPA2134

**Part Number**: OPA2134PA
**Package**: DIP-8 (Dual op-amp) or SOIC-8
**Cost**: ~$2.00 (qty 10+)

**Specifications**:
- Supply: ±2.5V to ±18V (use ±15V)
- Output current: ±30mA
- Bandwidth: 8MHz
- Slew rate: 20V/µs
- Very low distortion: 0.00008% THD
- Low noise: 8nV/√Hz

**Usage**:
- Final output stage (0-5V to ±10V)
- Powered from ±15V rails
- Drives operator input terminals
- Need 2x ICs for 4 output channels

**Justification**: Audio-grade op-amp with excellent linearity and low distortion. Can swing nearly rail-to-rail on ±15V supply, achieving full ±10V output range.

---

## 6. Power Supply Components

### Main Switching Regulator (12-24V → +5V)

**Selected**: Texas Instruments TPS54331

**Part Number**: TPS54331DR
**Package**: SOIC-8
**Cost**: ~$1.50 (qty 10+)

**Specifications**:
- Input: 3.5V to 28V
- Output: Adjustable (set to 5.0V)
- Current: 3A continuous
- Switching frequency: 570kHz
- Efficiency: ~90% (typical)
- Built-in protection: Over-current, thermal shutdown

**External Components**:
- Inductor: 10µH, 3A rated
- Input cap: 10µF ceramic
- Output cap: 22µF ceramic
- Feedback resistors: Set Vout = 5.0V

**Justification**: Proven buck regulator with good efficiency. 3A capacity is more than sufficient for entire board (~1.5A estimated load).

---

### Linear Regulators (LDOs)

#### +3.3V Digital Rail

**Selected**: AMS1117-3.3
**Package**: SOT-223
**Cost**: ~$0.15 (qty 10+)

**Specifications**:
- Input: 4.5V to 15V (use +5V input)
- Output: 3.3V fixed
- Current: 1A max
- Dropout: 1.1V @ 800mA
- Quiescent current: 5mA

**Usage**: Power RP2040, USB transceiver, digital support ICs

---

#### +3.3V Analog Rail (Low Noise)

**Selected**: Texas Instruments TPS7A4700

**Part Number**: TPS7A4700RGWR
**Package**: VQFN-20
**Cost**: ~$2.50 (qty 10+)

**Specifications**:
- Input: 2.2V to 18V
- Output: Adjustable (set to 3.3V)
- Current: 1A
- Noise: 4.17µVrms (ultra-low)
- PSRR: 72dB @ 100kHz
- Dropout: 250mV @ 1A

**Usage**: Power ADCs, precision op-amps, voltage reference. Isolated from digital noise via ferrite bead.

**Justification**: Worth the extra cost for analog rail. Ultra-low noise critical for 16-bit ADC performance.

---

### Dual ±15V Regulator

**Option A: Texas Instruments LM27761** (Charge Pump, lower current)

**Part Number**: LM27761DSGR
**Package**: WSON-10
**Cost**: ~$2.00 (qty 10+)

**Specifications**:
- Input: 2.7V to 5.5V (use +5V)
- Output: Adjustable ±Vout (set to ±15V)
- Current: ±25mA typical
- Method: Charge pump (no inductor needed)

**Pros**: Simple, no inductor, small footprint
**Cons**: Limited current (only ~25mA total for operators)

---

**Option B: Texas Instruments TPS65131** (Boost + Inverting, higher current)

**Part Number**: TPS65131RTER
**Package**: WQFN-16
**Cost**: ~$1.50 (qty 10+)

**Specifications**:
- Input: 2.7V to 5.5V
- Output: Adjustable (set to +15V and -15V)
- Current: +100mA, -100mA
- Method: Boost + inverting buck-boost

**Pros**: Higher current capacity
**Cons**: Requires inductors, more complex

---

**Option C: Discrete Boost + Inverter** (Maximum current)

Use:
- **MT3608** (Boost +5V → +15V): $0.50
- **LM2664** (Inverter +15V → -15V): $1.00
- Total: ~$1.50

**Pros**: High current (>500mA possible), very flexible
**Cons**: More board space, more components

---

**Decision**: Start with **TPS65131** as it provides best balance of current capacity (200mA total) and simplicity. If operators need more power, move to discrete solution in revision.

---

## 7. Protection Components

### TVS Diodes (Overvoltage Protection)

**Selected**: Littelfuse SMBJ Series

**Part Number**: SMBJ18CA (Bidirectional, 18V)
**Package**: SMB (DO-214AA)
**Cost**: ~$0.20 each (need 6 for inputs)

**Specifications**:
- Standoff voltage: 18V
- Breakdown voltage: 20V (min)
- Clamping voltage: 29V @ 10A
- Response time: <1ps

**Usage**: One per operator output connection (6 total), clamps voltage to safe range before reaching buffer op-amps.

---

### Input Protection Resistors

**Selected**: Standard thick-film resistors

**Value**: 1kΩ, 1/4W, 1%
**Cost**: ~$0.01 each

**Usage**: Series resistor after TVS diode, limits current into protection circuit and op-amp inputs.

---

### Reverse Polarity Protection (Power Input)

**Selected**: P-Channel MOSFET

**Part Number**: Si2301DS (or similar)
**Package**: SOT-23
**Cost**: ~$0.15

**Usage**: Protects against reversed power supply connection. If input polarity is correct, MOSFET conducts; if reversed, it blocks.

---

## 8. Connectors & Mechanical

### Operator Interface Connectors

**Selected**: 2x5 Pin Header, 0.1" pitch

**Part Number**: Generic dual-row pin header
**Cost**: ~$0.20 each (need 6)

**Notes**:
- Standard 0.1" pitch for easy prototyping
- Use shrouded headers to prevent reversed connection
- Mate with 10-conductor IDC ribbon cables

---

### USB-C Connector

**Selected**: USB Type-C receptacle (USB 2.0)

**Part Number**: HRO-TYPE-C-31-M-12 (or similar)
**Package**: SMD
**Cost**: ~$0.50

**Configuration**: USB 2.0 (D+/D- only), configured for 5V power negotiation via CC resistors.

---

### Power Input (Alternative to USB)

**Selected**: Barrel Jack (5.5mm x 2.1mm)

**Part Number**: PJ-002A or similar
**Cost**: ~$0.30

**Usage**: Optional secondary power input for 12-24V DC supply if more power is needed than USB can provide.

---

## 9. Passive Components Summary

### Capacitors
- **Decoupling (0.1µF, 50V, X7R)**: ~40 pcs @ $0.02 = $0.80
- **Bulk (10µF, 25V, X7R)**: ~10 pcs @ $0.10 = $1.00
- **Power (22µF, 50V, ceramic)**: ~5 pcs @ $0.30 = $1.50
- **Filtering (1µF, 100nF various)**: ~20 pcs @ $0.05 = $1.00

### Resistors (1%, 0805 or through-hole)
- **Various values (1kΩ, 10kΩ, 100kΩ, etc.)**: ~50 pcs @ $0.01 = $0.50

### Inductors
- **10µH, 3A (for TPS54331)**: 1 pc @ $0.50 = $0.50
- **Small inductors for ±15V supply**: 2 pcs @ $0.30 = $0.60
- **Ferrite beads (analog/digital isolation)**: 5 pcs @ $0.10 = $0.50

### LEDs
- **Indicator LEDs (various colors)**: ~8 pcs @ $0.10 = $0.80

---

## 10. Preliminary Bill of Materials (BOM) Summary

| Category | Component | Qty | Unit Cost | Total |
|----------|-----------|-----|-----------|-------|
| **MCU** | RP2040 + flash + crystal | 1 | $2.50 | $2.50 |
| **ADC** | ADS1115 (16-bit, I2C) | 2 | $3.50 | $7.00 |
| **DAC** | MCP4728 (12-bit, quad) | 1 | $2.50 | $2.50 |
| **Voltage Ref** | REF5050 (5V, 0.05%) | 1 | $2.00 | $2.00 |
| **Op-Amps** | OPA4192 (input buffers) | 3 | $2.50 | $7.50 |
| **Op-Amps** | TLV9064 (output buffers) | 1 | $1.00 | $1.00 |
| **Op-Amps** | OPA2134 (±10V drivers) | 2 | $2.00 | $4.00 |
| **Power** | TPS54331 (buck regulator) | 1 | $1.50 | $1.50 |
| **Power** | TPS65131 (±15V supply) | 1 | $1.50 | $1.50 |
| **Power** | LDOs (3.3V digital) | 1 | $0.15 | $0.15 |
| **Power** | TPS7A4700 (3.3V analog) | 1 | $2.50 | $2.50 |
| **Protection** | TVS diodes (SMBJ18CA) | 6 | $0.20 | $1.20 |
| **Connectors** | 10-pin headers | 6 | $0.20 | $1.20 |
| **Connectors** | USB-C receptacle | 1 | $0.50 | $0.50 |
| **Connectors** | Barrel jack (power) | 1 | $0.30 | $0.30 |
| **Passives** | Capacitors (all) | - | - | $4.30 |
| **Passives** | Resistors (all) | - | - | $0.50 |
| **Passives** | Inductors & ferrites | - | - | $1.60 |
| **Misc** | LEDs, buttons, misc | - | - | $1.50 |
| **PCB** | 4-layer, 100x100mm | 1 | $5.00 | $5.00 |
| | | | **TOTAL** | **~$48.25** |

**Note**: This is a preliminary estimate. Actual costs may vary ±20% depending on suppliers and quantities.

---

## 11. Next Steps

- [ ] Finalize MCU choice (RP2040 vs STM32)
- [ ] Verify all parts are in stock at JLCPCB/LCSC
- [ ] Create detailed schematics in KiCad
- [ ] Calculate precise power budget
- [ ] Design PCB layout with proper analog/digital separation
- [ ] Order prototype boards (qty 5) for testing

---

## References

- Block Diagram: `/docs/architecture/DEV_BOARD_BLOCK_DIAGRAM.md`
- Specification: `/docs/architecture/DEV_BOARD_SPECIFICATION.md`
- Datasheets: (TBD - will maintain list of datasheet links)
