# Universal Dev Board Specification
**Philbrick Modular Analog Computing Platform**

Version: 0.1 (Draft)
Date: 2025-11-20
Status: Initial Specification

---

## 1. Purpose

The Universal Dev Board serves as the bridge between digital control/measurement and the analog operator modules in the Philbrick platform. It enables:

- **Development**: Test and validate operator module designs
- **Integration**: Connect analog operators to digital instrumentation
- **Education**: Make analog computing accessible and observable
- **Experimentation**: Rapid prototyping of analog computing circuits

---

## 2. High-Level Requirements

### 2.1 Operator Interface
- **Connector Standard**: 10-pin headers (per architecture/00-OVERVIEW.md)
- **Number of Channels**: 4-8 operator connection points (initial target: 6)
- **Operator Power**: ±15V supply to each connected module
- **Signal Levels**: Compatible with ±10V analog computing standard
- **Input Impedance**: High impedance (>100kΩ) to avoid loading operators

### 2.2 Analog Measurement
- **ADC Resolution**: Minimum 12-bit, target 16-bit
- **ADC Channels**: One per operator connection (6 minimum)
- **Sampling Rate**: 10 kSPS minimum per channel
- **Voltage Range**: ±10V (matched to operator output range)
- **Accuracy**: ±0.5% or better

### 2.3 Signal Generation
- **DAC Resolution**: Minimum 12-bit
- **DAC Channels**: At least 2 independent outputs
- **Output Range**: ±10V (matched to operator input range)
- **Built-in Waveforms**: DC, sine, square, triangle, ramp

### 2.4 Power Requirements
- **Input**: 12-24V DC, USB-C or barrel jack
- **Operator Power**: ±15V @ 500mA per rail (3W total for operators)
- **Digital Power**: +5V and +3.3V for MCU and peripherals
- **Efficiency**: >75% for switching regulators
- **Noise**: <10mVpp on analog rails

### 2.5 Digital Control & Connectivity
- **MCU**: ARM Cortex-M class or RP2040
- **USB**: USB-C for power, serial console, firmware updates
- **Debug**: SWD/JTAG header for development
- **Storage**: Optional SD card for data logging
- **Display**: Header for I2C OLED (128x64 minimum)

### 2.6 Protection & Safety
- **Overvoltage**: TVS diodes on all operator connections
- **Overcurrent**: Per-channel current limiting
- **Reverse Polarity**: Protection on power input
- **ESD**: ESD protection on all external connectors
- **Thermal**: Thermal shutdown on power regulators

### 2.7 Instrumentation & Debug
- **Test Points**: At all critical nodes (power rails, references, ADC inputs)
- **LED Indicators**: Power status, per-channel activity
- **Monitoring**: Real-time voltage/current display capability
- **Calibration**: On-board precision voltage reference accessible via test point

---

## 3. Major Subsystems

### 3.1 Power Subsystem
**Function**: Convert input power to all required rails with low noise

Components:
- Input protection and filtering
- Switching regulator (12-24V → +5V)
- Linear regulators (+5V → +3.3V digital)
- Dual tracking regulator (+5V → ±15V analog)
- Power sequencing and monitoring

**Key Requirements**:
- Analog and digital grounds isolated, star topology
- Separate LDOs for analog reference and ADC power
- Ferrite beads between digital/analog domains

### 3.2 Analog Input Subsystem
**Function**: Measure voltages from operator outputs

Components:
- Input protection (TVS + series resistor)
- Unity-gain buffer (high impedance, low noise op-amp)
- Voltage divider/level shifter (±10V → 0-3.3V or 0-5V)
- Anti-aliasing filter
- Multi-channel ADC (I2C/SPI)

**Key Requirements**:
- Input protection withstands ±20V
- Buffer bandwidth >100kHz
- ADC reference stability <50ppm/°C

### 3.3 Analog Output Subsystem
**Function**: Generate signals for operator inputs

Components:
- Multi-channel DAC (I2C/SPI)
- Level shifter/amplifier (0-3.3V → ±10V)
- Output buffer (low impedance drive)
- Protection (current limiting)

**Key Requirements**:
- Output accuracy ±1% or better
- Drive >10mA into 1kΩ load
- Slew rate >1V/µs

### 3.4 MCU & Digital Control
**Function**: Coordinate all subsystems, communicate with host

Components:
- MCU (RP2040 or STM32F4 series)
- USB-C transceiver
- Debug connector (SWD/JTAG)
- Boot configuration (buttons/jumpers)
- EEPROM for calibration data

**Key Requirements**:
- Sufficient GPIO for all ADC/DAC/control signals
- Hardware I2C and SPI peripherals
- USB device support
- Firmware update via USB (bootloader)

### 3.5 Operator Interface
**Function**: Physical and electrical connection to operator modules

Components:
- 10-pin headers (2x5, 0.1" pitch)
- Per-channel power distribution
- Signal routing to ADC inputs
- LED indicators per channel

**Layout Requirements**:
- Headers along one edge for clean cable routing
- Silkscreen labeling of all pins
- Adequate spacing between headers (15mm minimum)

---

## 4. Success Criteria

The dev board will be considered successful if it can:

1. ✓ Power and interface with at least 4 operator modules simultaneously
2. ✓ Accurately measure ±10V signals with <1% error
3. ✓ Generate arbitrary waveforms for operator stimulus
4. ✓ Stream real-time data to host computer via USB
5. ✓ Operate continuously without damage from reasonable misuse
6. ✓ Cost less than $75 in components (qty 10)

---

## 5. Design Constraints

- **PCB Size**: Maximum 100mm x 150mm (fits standard enclosures)
- **Component Availability**: Prefer JLCPCB/LCSC in-stock parts
- **Assembly**: Hand-solderable (0805 passives minimum, TQFP/QFN ICs acceptable)
- **Cost**: Target <$50 BOM at qty 10
- **Power Budget**: <5W total power consumption

---

## 6. Next Steps

1. Create detailed block diagram with signal flow
2. Select specific components (MCU, ADC, DAC, power ICs)
3. Design power supply schematic
4. Design analog input chain schematic
5. Design analog output chain schematic
6. Create preliminary PCB layout (component placement)
7. Generate detailed BOM with costs

---

## 7. References

- Architecture Overview: `/docs/architecture/00-OVERVIEW.md`
- Operator Primitives: `/docs/primitives/OPERATOR_PRIMITIVES.md`
- Project Roadmap: `/ROADMAP.md` (Phase 1, Week 2)
- Reading Guide: `/docs/architecture/DEV_BOARD_READING_GUIDE.md`

---

## 8. Open Questions

- [ ] Final decision on MCU: RP2040 ($1, 264kB RAM) vs STM32F405 ($5, 192kB RAM, more peripherals)?
- [ ] SD card logging: Required or optional?
- [ ] Display: I2C OLED or save cost and rely on USB serial?
- [ ] Number of operator channels: 4, 6, or 8?
- [ ] ADC architecture: Single multi-channel IC or individual per-channel ADCs?

---

*This specification will evolve as design decisions are made and prototypes are tested.*
