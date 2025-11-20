# Universal Dev Board - Block Diagram
**Philbrick Modular Analog Computing Platform**

Version: 0.1 (Draft)
Date: 2025-11-20

---

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          UNIVERSAL DEV BOARD                                 │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                         POWER SUBSYSTEM                               │  │
│  │                                                                        │  │
│  │  12-24V DC Input (USB-C or Barrel Jack)                              │  │
│  │       │                                                                │  │
│  │       ├─► [Protection] ─► [Switching Reg] ─► +5V (2A)                │  │
│  │       │                         │                                      │  │
│  │       │                         ├─► [LDO] ─► +3.3V Digital (500mA)   │  │
│  │       │                         │                                      │  │
│  │       │                         ├─► [LDO] ─► +3.3V Analog (100mA)    │  │
│  │       │                         │                                      │  │
│  │       │                         └─► [Dual Track Reg] ─► ±15V (500mA) │  │
│  │       │                                                                │  │
│  │       └─► [Protection] ─► [Precision Ref] ─► +5.000V Reference       │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                      OPERATOR INTERFACE (6 Channels)                  │  │
│  │                                                                        │  │
│  │   CH1  CH2  CH3  CH4  CH5  CH6                                        │  │
│  │    │    │    │    │    │    │                                         │  │
│  │   [10-pin connector x 6]  ◄──── ±15V Power Distribution              │  │
│  │    │    │    │    │    │    │                                         │  │
│  │    │    │    │    │    │    └─► LED Indicators (Activity)            │  │
│  │    │    │    │    │    │                                              │  │
│  │    └────┴────┴────┴────┴────┴─► Signal Lines to Analog Input         │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                  │                                           │
│                                  ▼                                           │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                    ANALOG INPUT SUBSYSTEM (6 Channels)                │  │
│  │                                                                        │  │
│  │  Operator Signal (±10V) from each 10-pin connector                    │  │
│  │       │                                                                │  │
│  │       ├─► [TVS Diode] ─► [Series R] ─► Protection                    │  │
│  │       │                                                                │  │
│  │       ├─► [Unity Gain Buffer] ───► High Impedance Input (>100kΩ)     │  │
│  │       │      (Op-amp)                                                 │  │
│  │       │                                                                │  │
│  │       ├─► [Divider/Level Shift] ──► ±10V to 0-3.3V or 0-5V          │  │
│  │       │      (Resistor network + offset)                              │  │
│  │       │                                                                │  │
│  │       ├─► [Anti-Alias Filter] ──► RC Low-pass (10kHz cutoff)         │  │
│  │       │                                                                │  │
│  │       └─► [ADC Input Channel] ──► To 16-bit Multi-channel ADC        │  │
│  │                                                                        │  │
│  │  All 6 channels ─► [16-bit ADC] ─► I2C/SPI ─► MCU                    │  │
│  │                    (ADS1115 or similar)                                │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                  │                                           │
│                                  ▼                                           │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                    MCU & DIGITAL CONTROL                              │  │
│  │                                                                        │  │
│  │  ┌──────────────────────────────────────────────────────┐            │  │
│  │  │  Microcontroller (RP2040 or STM32F4)                │            │  │
│  │  │                                                       │            │  │
│  │  │  - GPIO: ADC control, DAC control, LEDs, buttons    │            │  │
│  │  │  - I2C: ADC, DAC, OLED, EEPROM                      │            │  │
│  │  │  - SPI: Optional SD card                             │            │  │
│  │  │  - USB: Device mode for host communication           │            │  │
│  │  │  - Flash: Calibration data storage                   │            │  │
│  │  │  - Timers: PWM, timing-critical operations           │            │  │
│  │  │                                                       │            │  │
│  │  └──────────────────────────────────────────────────────┘            │  │
│  │       │        │        │        │        │                           │  │
│  │       │        │        │        │        └──► USB-C (Data + Power)  │  │
│  │       │        │        │        └──► SWD/JTAG Debug Header           │  │
│  │       │        │        └──► I2C OLED Display (128x64, optional)      │  │
│  │       │        └──► SD Card (SPI, optional data logging)              │  │
│  │       └──► EEPROM (I2C, calibration data)                             │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                  │                                           │
│                                  ▼                                           │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                    ANALOG OUTPUT SUBSYSTEM (2-4 Channels)             │  │
│  │                                                                        │  │
│  │  MCU ─► I2C/SPI ─► [12-bit Quad DAC]                                 │  │
│  │                    (MCP4728 or similar)                                │  │
│  │                         │                                              │  │
│  │                         ├─► CH1: 0-3.3V output                        │  │
│  │                         ├─► CH2: 0-3.3V output                        │  │
│  │                         ├─► CH3: 0-3.3V output                        │  │
│  │                         └─► CH4: 0-3.3V output                        │  │
│  │                              │                                         │  │
│  │    ┌─────────────────────────┴─────────────────────────┐             │  │
│  │    │ Per-channel processing (identical for each):       │             │  │
│  │    │                                                     │             │  │
│  │    │  [Level Shift] ──► 0-3.3V to ±10V                 │             │  │
│  │    │    (Op-amp gain/offset stage)                      │             │  │
│  │    │         │                                           │             │  │
│  │    │         ├─► [Output Buffer] ──► Low-Z drive       │             │  │
│  │    │         │     (Op-amp, >10mA capability)           │             │  │
│  │    │         │                                           │             │  │
│  │    │         ├─► [Current Limit] ──► Protection         │             │  │
│  │    │         │     (Series R + feedback)                │             │  │
│  │    │         │                                           │             │  │
│  │    │         └─► Output to operator input terminals      │             │  │
│  │    │                                                     │             │  │
│  │    └─────────────────────────────────────────────────────┘             │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                    INSTRUMENTATION & DEBUG                            │  │
│  │                                                                        │  │
│  │  ● Test Points:                                                       │  │
│  │    - All power rails (+3.3V, +5V, ±15V, GND)                         │  │
│  │    - Precision reference (+5.000V)                                    │  │
│  │    - ADC inputs (post-protection, post-buffer)                        │  │
│  │    - DAC outputs (pre- and post-amplification)                        │  │
│  │                                                                        │  │
│  │  ● LED Indicators:                                                    │  │
│  │    - Power Good (green)                                               │  │
│  │    - USB Activity (blue)                                              │  │
│  │    - Per-channel operator activity (6x amber)                         │  │
│  │                                                                        │  │
│  │  ● User Interface:                                                    │  │
│  │    - Reset button (MCU reset)                                         │  │
│  │    - Boot button (firmware update mode)                               │  │
│  │    - Optional: Mode select DIP switches                               │  │
│  │                                                                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Signal Flow Diagrams

### Measurement Path (Operator → MCU)

```
Operator Module Output (±10V)
    │
    └─► 10-pin Connector
            │
            ├─► TVS Protection (±20V clamp)
            │
            ├─► Series Resistor (1kΩ, current limiting)
            │
            ├─► Unity-Gain Buffer (OPA4192, High-Z input)
            │        Input: >100kΩ impedance
            │        Bandwidth: >100kHz
            │
            ├─► Voltage Divider + Offset
            │        ±10V → 0-3.3V (or 0-5V if using 5V ADC)
            │        Example: (V+10)/20 * 3.3 = Vout
            │
            ├─► Anti-Alias Filter (RC, fc=10kHz)
            │
            ├─► ADC Input (ADS1115 or similar)
            │        16-bit resolution
            │        I2C interface to MCU
            │
            └─► MCU Processing
                    │
                    ├─► Apply calibration (gain/offset correction)
                    ├─► Convert to voltage (±10V scale)
                    └─► Send via USB to host computer
```

### Generation Path (MCU → Operator)

```
Host Computer (USB Command)
    │
    └─► MCU receives waveform parameters
            │
            ├─► Generate samples (sine, square, triangle, DC)
            │
            ├─► Apply calibration (pre-distortion)
            │
            └─► DAC Output (MCP4728 via I2C)
                    │ 12-bit value (0-3.3V)
                    │
                    ├─► Level Shift + Gain Stage (Op-amp)
                    │        0-3.3V → ±10V
                    │        Example: (Vin * 20/3.3) - 10 = Vout
                    │
                    ├─► Output Buffer (Low impedance driver)
                    │        Can drive >10mA into 1kΩ load
                    │
                    ├─► Current Limiting (Series R + sense)
                    │
                    └─► 10-pin Connector → Operator Module Input
```

---

## Power Distribution Topology

```
12-24V Input (USB-C or Barrel)
    │
    ├─► Reverse Polarity Protection (P-FET or diode)
    │
    ├─► Input Filter (Capacitor + ferrite bead)
    │
    ├─► Switching Regulator (Buck, TPS54331 or similar)
    │        Output: +5V @ 2A
    │        Efficiency: ~85%
    │
    └─► +5V Rail (Digital + Analog pre-regulator)
            │
            ├─────────────────────────┐
            │                         │
            ├─► Linear Reg (LDO)     ├─► Linear Reg (LDO)
            │   Output: +3.3V Digital│   Output: +3.3V Analog
            │   Load: MCU, USB       │   Load: ADC, Op-amps
            │   Current: 500mA       │   Current: 100mA
            │                         │   (Isolated with ferrite bead)
            │                         │
            ├─► Dual Tracking Reg     └─► Precision Reference
            │   (LM27761 or TPS65131)    Output: +5.000V ±0.05%
            │   Output: ±15V @ 500mA     Load: ADC reference, test point
            │   Load: Operator modules   (Ultra-low noise LDO: LT3042)
            │   Distribution to 6x
            │   10-pin connectors
            │
            └─► Power Monitoring
                ● Current sense on ±15V rails
                ● Overvoltage detection
                ● Thermal monitoring (MCU ADC input)
```

### Ground Architecture

```
                    Input GND (Chassis)
                          │
                          ├─► Analog Ground (AGND) ← Star point
                          │       │
                          │       ├─► Operator connectors return
                          │       ├─► Op-amp grounds
                          │       ├─► ADC/DAC analog grounds
                          │       └─► Precision reference ground
                          │
                          └─► Digital Ground (DGND)
                                  │
                                  ├─► MCU ground
                                  ├─► USB shield ground
                                  ├─► Digital IC grounds
                                  └─► [Ferrite bead] ←──┘
                                            (Single connection between AGND/DGND)
```

---

## Interface Pin Assignments

### 10-Pin Operator Connector (Standardized)

From architecture/00-OVERVIEW.md specification:

```
Pin  Signal       Direction  Description
---  ------       ---------  -----------
 1   +15V         Supply     Positive rail for operator
 2   -15V         Supply     Negative rail for operator
 3   GND          Supply     Analog ground return
 4   INPUT_A      To Op      Signal input A (from dev board DAC)
 5   INPUT_B      To Op      Signal input B (from dev board DAC or GND)
 6   OUTPUT       From Op    Operator output (to dev board ADC)
 7   SENSE        From Op    Optional secondary output or current sense
 8   ENABLE       To Op      Digital enable/disable signal (3.3V logic)
 9   I2C_SDA      Bidir      Optional I2C data for smart operators
10   I2C_SCL      To Op      Optional I2C clock for smart operators
```

### MCU Peripheral Allocation

Assuming RP2040 or STM32F4:

```
Peripheral   Function                    Pins
----------   --------                    ----
I2C0         ADC (ADS1115)               SDA, SCL
I2C1         DAC (MCP4728), OLED, EEPROM SDA, SCL
SPI0         SD Card (optional)          MOSI, MISO, SCK, CS
USB          Host communication          D+, D-
SWD          Debug                       SWDIO, SWCLK
GPIO         6x Operator ENABLE          GP0-GP5
GPIO         6x Activity LEDs            GP6-GP11
GPIO         Power LED, USB LED          GP12, GP13
GPIO         Reset button, Boot button   GP14, GP15
ADC          Temperature monitor         GP26 (ADC0)
```

---

## Critical Design Notes

### 1. Analog Signal Conditioning
- **Why ±10V?** Standard for analog computers (matches most op-amps with ±15V supply)
- **Why 16-bit ADC?** Provides ~1.5mV resolution over ±10V range (acceptable for most experiments)
- **Why unity-gain buffer?** Prevents loading of operator outputs (output impedance may be 1kΩ or higher)

### 2. Power Supply Sequencing
Recommended power-up sequence:
1. +5V switching regulator (first)
2. +3.3V digital LDO
3. ±15V tracking regulator for operators
4. +3.3V analog LDO (last, cleanest rail)

### 3. Protection Philosophy
- **Fail-safe**: Board should survive common mistakes (reversed operators, shorted outputs)
- **Observable**: LED indicators show what's happening
- **Recoverable**: Resettable fuses or current limiting (not destructive fuses)

### 4. Layout Considerations
- Keep analog and digital sections physically separated
- Route ±15V power traces wide (0.5mm minimum for 500mA)
- Place decoupling capacitors close to IC power pins
- Use ground pour on top and bottom layers
- Avoid routing digital signals under analog circuitry

---

## Next Steps

With this block diagram defined, we can now:

1. ✓ Select specific component part numbers
2. ✓ Design detailed schematics for each subsystem
3. ✓ Calculate power budget and thermal requirements
4. ✓ Create Bill of Materials (BOM) with costs
5. ✓ Begin PCB layout with component placement

---

## References

- Dev Board Specification: `/docs/architecture/DEV_BOARD_SPECIFICATION.md`
- Architecture Overview: `/docs/architecture/00-OVERVIEW.md` (10-pin standard)
- Operator Primitives: `/docs/primitives/OPERATOR_PRIMITIVES.md`
