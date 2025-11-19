# Philbrick Operator Primitives

**Foundation**: Every analog module is an operator `O: ℝ → ℝ` with a well-defined spectrum

**Last Updated**: 2025-11-18

---

## Core Philosophy

From [Operator Philosophy](../../kairo/docs/OPERATOR_PHILOSOPHY.md):

> **Systems are operators. Dynamics are spectra.**
>
> An analog module is not "a circuit that processes audio."
> An analog module is a **continuous-time operator** with:
> - Input dimension (number of input jacks)
> - Output dimension (number of output jacks)
> - Operator type (linear, nonlinear, time-invariant, etc.)
> - Spectrum (poles and zeros in the s-plane)
> - Latency (propagation delay in microseconds)

---

## The Four Fundamental Operator Types

Every analog computation decomposes into these four primitives:

### 1. Linear Combination (Mixer / Summation)

**Operator**: `O₁: ℝⁿ → ℝ`
**Mathematics**: `y(t) = Σᵢ αᵢ xᵢ(t)`
**Spectrum**: No poles or zeros (pure gain)
**Circuit**: Summing amplifier, mixer, attenuator

**Module Spec**:
```json
{
  "operator_type": "linear",
  "time_invariant": true,
  "spectrum": {
    "poles": [],
    "zeros": []
  },
  "input_dimension": 3,
  "output_dimension": 1,
  "parameters": {
    "gains": [1.0, 1.0, 1.0]  // Configurable mixing ratios
  }
}
```

**Physical Implementation**:
- Op-amp summing configuration
- Resistive mixer (passive)
- Variable gain amplifier (VCA)

**Uses**:
- Mixing multiple signals
- Gain staging
- Cross-fading
- Panning

---

### 2. Integration (Memory / Smoothing)

**Operator**: `O₂: ℝ → ℝ`
**Mathematics**: `y(t) = ∫₀ᵗ x(τ) dτ`
**Spectrum**: Pole at `s = 0` (infinite DC gain)
**Circuit**: RC integrator, capacitor

**Module Spec**:
```json
{
  "operator_type": "linear",
  "time_invariant": true,
  "spectrum": {
    "poles": [{"real": 0, "imag": 0}],  // Pure integrator
    "zeros": []
  },
  "input_dimension": 1,
  "output_dimension": 1,
  "parameters": {
    "time_constant": 0.001  // 1ms time constant
  }
}
```

**Physical Implementation**:
- Op-amp integrator
- RC lowpass filter (approximate integrator)
- Capacitor with controlled discharge

**Uses**:
- Smoothing / lowpass filtering
- Accumulation / memory
- Ramp generation
- Envelope followers

---

### 3. Nonlinearity (Saturation / Rectification / Threshold)

**Operator**: `O₃: ℝ → ℝ`
**Mathematics**: `y(t) = f(x(t))` where `f` is nonlinear
**Spectrum**: No spectrum (nonlinear operators don't have eigenvalues)
**Circuit**: Diode, transistor saturation, comparator

**Module Spec**:
```json
{
  "operator_type": "nonlinear",
  "time_invariant": true,
  "spectrum": null,  // Nonlinear operators have no global spectrum
  "local_linearization": {
    "gain_at_zero": 1.0,
    "saturation_threshold": 5.0  // Volts
  },
  "input_dimension": 1,
  "output_dimension": 1,
  "parameters": {
    "type": "soft_clip",  // Options: soft_clip, hard_clip, rectify, comparator
    "threshold": 5.0
  }
}
```

**Physical Implementation**:
- Diode clipper
- Transistor overdrive
- Comparator (hard threshold)
- Tanh/sigmoid approximation (differential pair)

**Uses**:
- Distortion / overdrive
- Waveshaping
- Rectification / detection
- Thresholding / binary decisions

---

### 4. Event / Clock (Discrete Timing)

**Operator**: `O₄: ℝ → ℝ`
**Mathematics**: `y(t) = Σₙ δ(t - tₙ)` (sum of impulses)
**Spectrum**: All frequencies (Dirac delta has flat spectrum)
**Circuit**: 555 timer, clock generator, trigger

**Module Spec**:
```json
{
  "operator_type": "event",
  "time_invariant": true,
  "spectrum": {
    "type": "periodic_impulse_train",
    "fundamental_frequency": 1.0  // Hz
  },
  "input_dimension": 0,  // Autonomous oscillator
  "output_dimension": 1,
  "parameters": {
    "frequency": 1.0,  // Hz
    "duty_cycle": 0.5
  }
}
```

**Physical Implementation**:
- 555 timer
- Schmitt trigger oscillator
- Comparator with hysteresis
- Crystal oscillator

**Uses**:
- Clocks / timing
- Rhythm generation
- Sample-and-hold triggers
- Sequencer steps

---

## Composite Operators (Built from Primitives)

### Lowpass Filter (O₂ variant)

**Composition**: Integration with feedback
**Mathematics**: `y(t) + τ(dy/dt) = x(t)`
**Spectrum**: Pole at `s = -1/τ`

```
O_lowpass = (1 + τs)⁻¹
Pole at s = -ω_c where ω_c = 1/τ
```

**Module Spec**:
```json
{
  "operator_type": "linear",
  "time_invariant": true,
  "spectrum": {
    "poles": [{"real": -628.3, "imag": 0}],  // 100Hz cutoff
    "zeros": []
  },
  "cutoff_frequency": 100.0  // Hz
}
```

---

### Resonant Filter (O₂ + feedback)

**Composition**: Two integrators in feedback loop
**Mathematics**: `d²y/dt² + 2ζω₀(dy/dt) + ω₀²y = ω₀²x`
**Spectrum**: Complex conjugate poles

```
O_resonant = ω₀² / (s² + 2ζω₀s + ω₀²)
Poles at s = -ζω₀ ± jω₀√(1-ζ²)
```

**Module Spec**:
```json
{
  "operator_type": "linear",
  "time_invariant": true,
  "spectrum": {
    "poles": [
      {"real": -31.4, "imag": 625.7},   // Upper pole
      {"real": -31.4, "imag": -625.7}   // Lower pole (conjugate)
    ],
    "zeros": []
  },
  "center_frequency": 100.0,  // Hz
  "q_factor": 10.0,
  "damping_ratio": 0.05
}
```

---

### Distortion / Waveshaper (O₃)

**Composition**: Nonlinear operator with optional pre/post filtering
**Mathematics**: `y = tanh(k·x)` or `y = clip(x, [-V, +V])`
**Spectrum**: None (nonlinear)

**Module Spec**:
```json
{
  "operator_type": "nonlinear",
  "time_invariant": true,
  "spectrum": null,
  "transfer_function": "tanh",
  "drive": 10.0,  // Pre-gain before nonlinearity
  "output_level": 0.5
}
```

---

## Operator Composition Rules

### Serial Composition (Cascading)

```
Module1 → Module2 → Module3
(O₃ ∘ O₂ ∘ O₁)(x)
```

**Spectrum of composition**:
- Poles: Union of all poles from all modules
- Zeros: Union of all zeros from all modules
- Order: Sum of orders

**Example**:
```
Lowpass (pole at -100 rad/s)
→ Resonant (poles at -31.4 ± j625.7)
→ Gain (no poles/zeros)

Combined spectrum:
Poles: {-100, -31.4+j625.7, -31.4-j625.7}
Zeros: {}
```

### Parallel Composition (Mixing)

```
       → Module1 →
x →  ↗             ↘
     → Module2 →    → Mixer → y
     → Module3 → ↗
```

**Spectrum of parallel composition**:
```
H_total(s) = α₁H₁(s) + α₂H₂(s) + α₃H₃(s)
```

Poles/zeros depend on the mix weights `αᵢ`.

---

## Module Descriptor Protocol

Every Philbrick module **must** provide this metadata:

```json
{
  "module_id": "lowpass-rc-001",
  "module_name": "RC Lowpass Filter",
  "version": "1.0.0",

  "operator": {
    "type": "linear",               // linear | nonlinear | event
    "time_invariant": true,
    "input_dimension": 1,
    "output_dimension": 1
  },

  "spectrum": {
    "poles": [
      {"real": -628.3, "imag": 0}  // 100Hz cutoff
    ],
    "zeros": []
  },

  "latency": {
    "propagation_delay_us": 2.4,   // Microseconds
    "group_delay_us": 1592.0        // At center frequency
  },

  "calibration": {
    "calibrated_at": "2025-11-18T12:00:00Z",
    "temperature_c": 25.0,
    "measured_spectrum": {
      "method": "white_noise_injection",
      "poles_measured": [{"real": -630.1, "imag": 0}],
      "tolerance": 0.03  // 3% pole deviation from spec
    }
  },

  "jacks": [
    {
      "id": "input",
      "type": "input",
      "impedance_ohms": 100000,
      "voltage_range": [-10, 10]
    },
    {
      "id": "output",
      "type": "output",
      "impedance_ohms": 100,
      "voltage_range": [-10, 10]
    }
  ],

  "parameters": [
    {
      "id": "cutoff",
      "name": "Cutoff Frequency",
      "type": "knob",
      "range": [10, 10000],  // Hz
      "default": 100,
      "scale": "logarithmic"
    }
  ]
}
```

---

## Validation & Testing

### Spectral Validation

**White Noise Test**:
1. Inject white noise at input
2. Measure output spectrum via FFT
3. Compare to expected `|H(jω)|²`
4. Validate poles/zeros within tolerance

**Impulse Response Test**:
1. Inject Dirac delta (short pulse)
2. Measure impulse response `h(t)`
3. Compute FFT → `H(jω)`
4. Extract poles/zeros via system identification

**Sine Sweep Test**:
1. Sweep sine wave from 0.1Hz to 20kHz
2. Measure amplitude and phase response
3. Plot Bode diagram
4. Verify pole/zero locations

### Composition Validation

**Series Test**:
```
Module1 → Module2
Expected spectrum: poles from both
Measure: white noise → FFT
Validate: combined transfer function matches prediction
```

**Parallel Test**:
```
Module1 ↘
         → Mixer
Module2 ↗
Expected: weighted sum of transfer functions
Measure: inject different frequencies in each path
Validate: superposition holds
```

---

## Connection to Morphogen

### Spectrum Matching

When a Philbrick module connects to Morphogen:

1. **Philbrick reports its spectrum** (poles/zeros in s-plane)
2. **Morphogen converts to z-plane** using bilinear transform:
   ```
   s = (2/T) * (z - 1)/(z + 1)
   ```
3. **Morphogen validates composition**:
   - Check dimension compatibility
   - Predict combined spectrum
   - Warn if instability detected (poles in right half-plane)

### Digital Twin

Morphogen can create a **digital twin** of Philbrick modules:

```python
# Philbrick module spectrum
poles_analog = [-628.3]  # s-plane
zeros_analog = []

# Convert to digital (assume 48kHz sample rate)
T = 1/48000
poles_digital = bilinear_transform(poles_analog, T)  # z-plane
zeros_digital = bilinear_transform(zeros_analog, T)

# Create digital filter matching analog module
digital_twin = create_filter(poles_digital, zeros_digital)

# Now Morphogen can simulate the Philbrick module
output_sim = digital_twin(input_signal)
```

### Calibration Feedback Loop

```
1. Morphogen generates test signal
2. Philbrick module processes it
3. Morphogen measures output
4. Compare to expected (from module descriptor)
5. Update calibration if drift detected
6. Store new spectrum in module metadata
```

---

## Future: Operator Learning

**Idea**: Learn module operators from data

```python
# Black-box module (unknown transfer function)
input_data = white_noise(duration=10s)
output_data = module.process(input_data)

# System identification
H_estimated = estimate_transfer_function(input_data, output_data)
poles, zeros = extract_poles_zeros(H_estimated)

# Store in module descriptor
module.spectrum = {"poles": poles, "zeros": zeros}
```

This enables:
- Automatic calibration
- Drift detection
- Anomaly detection (module malfunction)
- Digital twin creation without manual specs

---

## Summary

| Primitive | Operator Type | Spectrum | Use Case |
|-----------|---------------|----------|----------|
| Linear Combination | Linear | No poles/zeros | Mixing, gain |
| Integration | Linear | Pole at s=0 | Smoothing, memory |
| Nonlinearity | Nonlinear | None | Distortion, waveshaping |
| Event/Clock | Event | All frequencies | Timing, rhythm |

**All analog computation decomposes into these four operators.**

**All modules report their operator type and spectrum.**

**All compositions follow operator algebra rules.**

**Morphogen validates and simulates using the same mathematical framework.**

---

## References

- [Kairo Operator Philosophy](../../kairo/docs/OPERATOR_PHILOSOPHY.md) - Mathematical foundation
- [Philbrick Vision](../vision/00-VISION.md) - High-level philosophy
- [Morphogen Bridge](../vision/03-MORPHOGEN-BRIDGE.md) - Software/hardware connection

---

**Next**: See [Module Specifications](../modules/) for detailed circuit designs implementing these operators.
