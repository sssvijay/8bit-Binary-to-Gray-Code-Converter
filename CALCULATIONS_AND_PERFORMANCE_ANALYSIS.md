# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

## 1. Exact values from the schematics, layout and waveforms

| Parameter | Result | Calculation / source |
|---|---:|---|
| Transistor sizing | \(W=120\ nm,\ L=45\ nm,\ m=1\) | g45n1svt / g45p1svt devices in inverter, buffer and XOR schematics |
| Inverter cell | 2 transistors | 1 PMOS + 1 NMOS |
| XOR cell | 12 transistors | 4 PMOS + 4 NMOS + 2 inverters |
| Buffer cell | 4 transistors | 2 PMOS + 2 NMOS |
| Total transistor count | **88** | \(7\times12+4=88\) |
| PMOS count | 44 | From the seven XOR cells and buffer |
| NMOS count | 44 | From the seven XOR cells and buffer |
| Total transistor width | **10.56 µm** | \(88\times0.12=10.56\ \mu m\) |
| Total transistor area | **0.475 µm²** | \(88\times(0.12\times0.045)=0.4752\ \mu m^2\) |
| Individual transistor \(W/L\) | **2.67** | \(120/45=2.67\) |
| Current _lay cell size | Approximately 7.25 × 7.25 µm | Estimated from pin-label coordinates; confirm the boundary in Virtuoso |
| Current _lay cell area | Approximately **52.6 µm²** | \(7.25\times7.25=52.56\ \mu m^2\) |
| Current _lay aspect ratio | Approximately **1.0** | \(7.25/7.25=1.0\) |
| Earlier layout size | Approximately 30.9 × 30.9 µm | Estimated using the same coordinate method |
| Earlier layout area | Approximately **956 µm²** | \(30.9\times30.9\approx955\ \mu m^2\) |
| Transistor area / current layout area | Approximately **0.9%** | \((0.475/52.6)\times100\approx0.9\%\) |

### Input stimulus and switching activity

The stimulus applies one binary state every 10 ns:

$$
T_{vector}=10\ ns
$$

$$
f_{vector}=\frac{1}{10\times10^{-9}}
$$

$$
\boxed{f_{vector}=100\ MHz}
$$

A complete 8-bit input sequence contains 256 states:

$$
N=2^8=256
$$

$$
T_{full}=256\times10\ ns
$$

$$
\boxed{T_{full}=2.56\ \mu s}
$$

All eight inputs start HIGH at \(t=0\), corresponding to binary 11111111 (decimal 255). The sequence counts down from 255.

For the analysed 2 µs waveform interval:

| Switching quantity | Count |
|---|---:|
| Total input transitions | **393** |
| Total Gray-output transitions | **199** |

Per-bit Gray-output transition counts:

| Output | Number of transitions |
|---|---:|
| G1 | 100 |
| G2 | 50 |
| G3 | 25 |
| G4 | 12 |
| G5 | 6 |
| G6 | 3 |
| G7 | 2 |
| G8 | 1 |
| **Total** | **199** |

The Gray output changes one bit at a time between consecutive states. In the analysed interval, the inputs make 393 transitions while the Gray outputs make 199 transitions.

Setup time, hold time and clock skew are not parameters of this standalone combinational converter because it has no clocked storage elements.

## 2. Derived calculations using stated assumptions

The following estimates use the stated assumptions:

| Assumption | Value |
|---|---:|
| Supply voltage, \(V_{DD}\) | 1.2 V |
| Gate capacitance per unit transistor width | Approximately 1.2 fF/µm |
| Gate capacitance per transistor | 0.144 fF |
| Total gate capacitance for 88 transistors | Approximately 12.7 fF |
| Output load capacitance | 2 fF |
| Internal-node capacitance per output transition | 2 fF |
| Effective capacitance per output transition | 4 fF |
| Effective input capacitance per input transition | 0.58 fF |
| Estimated total leakage current | 0.1 µA |

### 2.1 Dynamic energy of Gray outputs

The energy per output transition is estimated using:

$$
E=\frac{1}{2}CV_{DD}^{2}
$$

For 199 output transitions, with \(C=4\ fF\) and \(V_{DD}=1.2\ V\):

$$
E_{Gray}=199\times\frac{1}{2}\times4\times10^{-15}\times(1.2)^2
$$

$$
E_{Gray}=573.12\times10^{-15}\ J
$$

$$
\boxed{E_{Gray}\approx573\ fJ}
$$

### 2.2 Dynamic energy of binary inputs

For 393 input transitions, with effective input capacitance \(C=0.58\ fF\):

$$
E_{inputs}=393\times\frac{1}{2}\times0.58\times10^{-15}\times(1.2)^2
$$

$$
E_{inputs}=164.1\times10^{-15}\ J
$$

$$
\boxed{E_{inputs}\approx164\ fJ}
$$

### 2.3 Total dynamic energy and dynamic power

$$
E_{dynamic}=E_{Gray}+E_{inputs}
$$

$$
E_{dynamic}=573+164
$$

$$
\boxed{E_{dynamic}\approx737\ fJ}
$$

For a 2 µs observation interval:

$$
P_{dynamic}=\frac{E_{dynamic}}{T}
$$

$$
P_{dynamic}=\frac{737\times10^{-15}}{2\times10^{-6}}
$$

$$
\boxed{P_{dynamic}\approx0.37\ \mu W}
$$

### 2.4 Static power

Using the assumed leakage current of 0.1 µA:

$$
P_{static}=V_{DD}\times I_{leak}
$$

$$
P_{static}=1.2\times0.1\times10^{-6}
$$

$$
\boxed{P_{static}=0.12\ \mu W}
$$

### 2.5 Average power

$$
P_{avg}=P_{dynamic}+P_{static}
$$

$$
P_{avg}=0.37+0.12
$$

$$
\boxed{P_{avg}\approx0.49\ \mu W}
$$

### 2.6 Energy per input vector

$$
E_{vector}=P_{avg}\times T_{vector}
$$

$$
E_{vector}=0.49\times10^{-6}\times10\times10^{-9}
$$

$$
\boxed{E_{vector}=4.9\ fJ}
$$

### 2.7 Propagation-delay estimate

Based on the minimum-size transistor devices, the estimated propagation-delay range is:

$$
\boxed{t_{PD}=50\text{–}100\ ps}
$$

The middle estimate used for the comparison is:

$$
\boxed{t_{PD,mid}=70\ ps}
$$

### 2.8 Maximum-frequency estimate, PDP and EDP

The delay-limited frequency estimate is:

$$
f_{max}=\frac{1}{t_{PD}}
$$

Power-delay product:

$$
PDP=P_{avg}\times t_{PD}
$$

Energy-delay product:

$$
EDP=P_{avg}\times t_{PD}^{2}
$$

Using \(P_{avg}=0.49\ \mu W\):

| Estimated \(t_{PD}\) | \(f_{max}=1/t_{PD}\) | PDP | EDP |
|---:|---:|---:|---:|
| 50 ps | 20 GHz | 0.024 fJ | \(1.2\times10^{-27}\ J\cdot s\) |
| 70 ps | 14.3 GHz | 0.034 fJ | \(2.4\times10^{-27}\ J\cdot s\) |
| 100 ps | 10 GHz | 0.049 fJ | \(4.9\times10^{-27}\ J\cdot s\) |

## 3. Parameters obtained through Virtuoso simulation

The following electrical performance values are obtained from Cadence Virtuoso measurements:

| Parameter | Symbol |
|---|---|
| Low-to-high propagation delay | \(t_{PLH}\) |
| High-to-low propagation delay | \(t_{PHL}\) |
| Contamination delay | \(t_{cd}\) |
| Average power | \(P_{avg}\) |
| Static power | \(P_{static}\) |
| Dynamic power | \(P_{dynamic}\) |
| Output rise time | \(t_r\) |
| Output fall time | \(t_f\) |
| Noise margins | \(NM_H,\ NM_L\) |
| Post-layout delay and power | Extracted-layout measurements |

For post-layout measurements, perform DRC and LVS and then use the extracted parasitic netlist for simulation.

---

**Author**  
**SSS Vijay Kumar**  
Department of ECE, IIITDM Kurnool

**Under the Guidance of**  
**Dr. P. Rangababu**  
Associate Professor, Department of ECE, IIITDM Kurnool
