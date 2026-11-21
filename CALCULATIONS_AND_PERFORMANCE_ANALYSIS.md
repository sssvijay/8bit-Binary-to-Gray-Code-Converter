# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

## 1. Exact values from the schematics, layout and waveforms

| Parameter | Result | Calculation / source |
|---|---:|---|
| Transistor sizing | W = 120 nm, L = 45 nm, m = 1 | g45n1svt / g45p1svt devices in inverter, buffer and XOR schematics |
| Inverter cell | 2 transistors | 1 PMOS + 1 NMOS |
| XOR cell | 12 transistors | 4 PMOS + 4 NMOS + 2 inverters |
| Buffer cell | 4 transistors | 2 PMOS + 2 NMOS |
| Total transistor count | **88** | 7 × 12 + 4 = 88 |
| PMOS count | 44 | From the seven XOR cells and buffer |
| NMOS count | 44 | From the seven XOR cells and buffer |
| Total transistor width | **10.56 µm** | 88 × 0.12 = 10.56 µm |
| Total transistor area | **0.475 µm²** | 88 × (0.12 × 0.045) = 0.4752 µm² |
| Individual transistor W/L | **2.67** | 120 / 45 = 2.67 |
| Current _lay cell size | Approximately 7.25 × 7.25 µm | Estimated from pin-label coordinates |
| Current _lay cell area | Approximately **52.6 µm²** | 7.25 × 7.25 = 52.56 µm² |
| Current _lay aspect ratio | Approximately **1.0** | 7.25 / 7.25 = 1.0 |
| Earlier layout size | Approximately 30.9 × 30.9 µm | Same coordinate method |
| Earlier layout area | Approximately **956 µm²** | 30.9 × 30.9 ≈ 955 µm² |
| Transistor area / current layout area | Approximately **0.9%** | (0.475 / 52.6) × 100 ≈ 0.9% |

### Input stimulus and switching activity

The stimulus applies one binary state every 10 ns:

<p align="center"><strong>T<sub>vector</sub> = 10 ns</strong></p>

<p align="center"><strong>f<sub>vector</sub> = 1 / (10 × 10<sup>−9</sup>)</strong></p>

<p align="center"><strong>f<sub>vector</sub> = 100 MHz</strong></p>

A complete 8-bit input sequence contains 256 states:

<p align="center"><strong>N = 2<sup>8</sup> = 256</strong></p>

<p align="center"><strong>T<sub>full</sub> = 256 × 10 ns = 2.56 µs</strong></p>

All eight inputs start HIGH at t = 0, corresponding to binary 11111111 (decimal 255). The sequence counts down from 255.

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
| Supply voltage, V<sub>DD</sub> | 1.2 V |
| Gate capacitance per unit transistor width | Approximately 1.2 fF/µm |
| Gate capacitance per transistor | 0.144 fF |
| Total gate capacitance for 88 transistors | Approximately 12.7 fF |
| Output load capacitance | 2 fF |
| Internal-node capacitance per output transition | 2 fF |
| Effective capacitance per output transition | 4 fF |
| Effective input capacitance per input transition | 0.58 fF |
| Estimated total leakage current | 0.1 µA |

### 2.1 Dynamic energy of Gray outputs

Energy per output transition:

<p align="center"><strong>E = ½ × C × V<sub>DD</sub><sup>2</sup></strong></p>

For 199 output transitions, C = 4 fF and V<sub>DD</sub> = 1.2 V:

<p align="center"><strong>E<sub>Gray</sub> = 199 × ½ × 4 × 10<sup>−15</sup> × (1.2)<sup>2</sup></strong></p>

<p align="center"><strong>E<sub>Gray</sub> = 573.12 × 10<sup>−15</sup> J</strong></p>

<p align="center"><strong>E<sub>Gray</sub> ≈ 573 fJ</strong></p>

### 2.2 Dynamic energy of binary inputs

For 393 input transitions, with effective input capacitance C = 0.58 fF:

<p align="center"><strong>E<sub>inputs</sub> = 393 × ½ × 0.58 × 10<sup>−15</sup> × (1.2)<sup>2</sup></strong></p>

<p align="center"><strong>E<sub>inputs</sub> ≈ 163 × 10<sup>−15</sup> J</strong></p>

<p align="center"><strong>E<sub>inputs</sub> ≈ 163 fJ</strong></p>

### 2.3 Total dynamic energy and dynamic power

<p align="center"><strong>E<sub>dynamic</sub> = E<sub>Gray</sub> + E<sub>inputs</sub></strong></p>

<p align="center"><strong>E<sub>dynamic</sub> = 573 + 163 = 736 fJ</strong></p>

For a 2 µs observation interval:

<p align="center"><strong>P<sub>dynamic</sub> = E<sub>dynamic</sub> / T</strong></p>

<p align="center"><strong>P<sub>dynamic</sub> = (736 × 10<sup>−15</sup>) / (2 × 10<sup>−6</sup>)</strong></p>

<p align="center"><strong>P<sub>dynamic</sub> ≈ 0.37 µW</strong></p>

### 2.4 Static power

Using the assumed leakage current of 0.1 µA:

<p align="center"><strong>P<sub>static</sub> = V<sub>DD</sub> × I<sub>leak</sub></strong></p>

<p align="center"><strong>P<sub>static</sub> = 1.2 × 0.1 × 10<sup>−6</sup></strong></p>

<p align="center"><strong>P<sub>static</sub> = 0.12 µW</strong></p>

### 2.5 Average power

<p align="center"><strong>P<sub>avg</sub> = P<sub>dynamic</sub> + P<sub>static</sub></strong></p>

<p align="center"><strong>P<sub>avg</sub> = 0.37 + 0.12</strong></p>

<p align="center"><strong>P<sub>avg</sub> ≈ 0.49 µW</strong></p>

### 2.6 Energy per input vector

<p align="center"><strong>E<sub>vector</sub> = P<sub>avg</sub> × T<sub>vector</sub></strong></p>

<p align="center"><strong>E<sub>vector</sub> = 0.49 × 10<sup>−6</sup> × 10 × 10<sup>−9</sup></strong></p>

<p align="center"><strong>E<sub>vector</sub> = 4.9 fJ</strong></p>

### 2.7 Propagation-delay estimate

Based on the minimum-size transistor devices, the estimated propagation-delay range is:

<p align="center"><strong>t<sub>PD</sub> = 50–100 ps</strong></p>

The middle estimate used for the comparison is:

<p align="center"><strong>t<sub>PD,mid</sub> = 70 ps</strong></p>

### 2.8 Maximum-frequency estimate, PDP and EDP

The delay-limited frequency estimate is:

<p align="center"><strong>f<sub>max</sub> = 1 / t<sub>PD</sub></strong></p>

Power-delay product:

<p align="center"><strong>PDP = P<sub>avg</sub> × t<sub>PD</sub></strong></p>

Energy-delay product:

<p align="center"><strong>EDP = P<sub>avg</sub> × t<sub>PD</sub><sup>2</sup></strong></p>

Using P<sub>avg</sub> = 0.49 µW:

| Estimated t<sub>PD</sub> | f<sub>max</sub> = 1/t<sub>PD</sub> | PDP | EDP |
|---:|---:|---:|---:|
| 50 ps | 20 GHz | 0.024 fJ | 1.2 × 10<sup>−27</sup> J·s |
| 70 ps | 14.3 GHz | 0.034 fJ | 2.4 × 10<sup>−27</sup> J·s |
| 100 ps | 10 GHz | 0.049 fJ | 4.9 × 10<sup>−27</sup> J·s |

## 3. Parameters obtained through Virtuoso simulation

The following electrical performance values are obtained through Cadence Virtuoso simulation and analysis:

| Parameter | Symbol |
|---|---|
| Low-to-high propagation delay | t<sub>PLH</sub> |
| High-to-low propagation delay | t<sub>PHL</sub> |
| Contamination delay | t<sub>cd</sub> |
| Average power | P<sub>avg</sub> |
| Static power | P<sub>static</sub> |
| Dynamic power | P<sub>dynamic</sub> |
| Output rise time | t<sub>r</sub> |
| Output fall time | t<sub>f</sub> |
| Noise margins | NM<sub>H</sub>, NM<sub>L</sub> |
| Post-layout delay and power | Extracted-layout measurements |

For post-layout measurements, perform DRC and LVS, then use the extracted parasitic netlist for simulation.

---

**Author**  
**SSS Vijay Kumar**  
Department of ECE, IIITDM Kurnool

**Under the Guidance of**  
**Dr. P. Rangababu**  
Associate Professor, Department of ECE, IIITDM Kurnool
