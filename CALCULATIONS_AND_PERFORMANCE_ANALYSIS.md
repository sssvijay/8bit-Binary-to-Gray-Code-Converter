# 8-bit Binary-to-Gray Code Converter in Cadence Virtuoso (45 nm, gpdk045)

> **How to read the status tags in this document**
> - **[Known]** read directly from my schematics, layout or testbench
> - **[Calc]** computed exactly from known data
> - **[Est]** hand-calculated estimate with stated assumptions, *not a simulation result*
> - **[TBM]** to be measured in Virtuoso ADE (value will replace the placeholder)
>
> No number marked [Est] or [TBM] is a measured result.

---

## 1. Design Specification

| Parameter | Value / Characteristic | Notes |
|---|---|---|
| Function | 8-bit binary to Gray code converter | `G8 = B8`, `Gn = B(n+1) XOR Bn` for n = 1..7 |
| Technology | 45 nm CMOS (Cadence GPDK045) [Known] | Devices `g45n1svt` / `g45p1svt` |
| Supply voltage (VDD) | **[TBM] confirm in testbench** (1.2 V nominal in the template) | Must be confirmed before power numbers are used |
| Device size | W = 120 nm, L = 45 nm, m = 1 [Known] | W/L = 2.67 for every transistor |
| Architecture | 7 x 2-input XOR + 1 buffer cell [Known] | Fully combinational, no clock |
| Transistor count | **88** (44 PMOS + 44 NMOS) [Calc] | 7 x 12T XOR + 4T buffer |
| Total gate width | 10.56 um [Calc] | 88 x 0.12 um |
| Total gate area | 0.475 um2 [Calc] | 88 x (0.12 x 0.045) um2 |
| Test stimulus | 10 ns per input state (100 MHz vector rate) [Known] | Inputs 0 to 1 V, tr = 100 ps |
| Simulation | Transient, 0 to 2 us (200 of 256 states) [Known] | 2.56 us needed for all 256 states |
| EDA toolchain | Cadence Virtuoso, Assura | DRC / LVS |

### Cell composition

| Cell | Transistors | Notes |
|---|---|---|
| `cmosinv` | 2 (1P + 1N) | W/L = 120n/45n |
| `xor` | 12 (4P + 4N + 2 `cmosinv`) | 2-input XOR |
| `Cmos_buffer` | 4 (2P + 2N) | See known issues (Section 8) |
| `8bit_Bin_To_Gra_simu` | 88 | 7 XOR + 1 buffer |

### Input stimulus (read from the transient waveforms) [Known]

| Input | Period | Frequency | Duty cycle |
|---|---|---|---|
| B1 | 20 ns | 50 MHz | 50% |
| B2 | 40 ns | 25 MHz | 50% |
| B3 | 80 ns | 12.5 MHz | 50% |
| B4 | 160 ns | 6.25 MHz | 50% |
| B5 | 320 ns | 3.125 MHz | 50% |
| B6 | 640 ns | 1.5625 MHz | 50% |
| B7 | 1.28 us | 781.25 kHz | 50% |
| B8 | 2.56 us | 390.625 kHz | 50% |

---

## 2. Switching Activity (0 to 2 us) [Calc]

All inputs start high at t = 0, so the stimulus counts down from 255. Each step changes exactly one Gray-code bit.

| Signal | B1 | B2 | B3 | B4 | B5 | B6 | B7 | B8 | Total |
|---|---|---|---|---|---|---|---|---|---|
| Input toggles | 199 | 99 | 49 | 24 | 12 | 6 | 3 | 1 | **393** |

| Signal | G1 | G2 | G3 | G4 | G5 | G6 | G7 | G8 | Total |
|---|---|---|---|---|---|---|---|---|---|
| Output toggles | 100 | 50 | 25 | 12 | 6 | 3 | 2 | 1 | **199** |

Gray code halves the output activity compared with the binary input (199 vs 393 transitions).

---

## 3. Timing Performance Breakdown (tPLH, tPHL, tpd, tcd)

Thresholds: 50% of VDD at input and output.

### 3.1 Measured results (fill in from ADE Calculator)

| Hierarchy block | tPLH | tPHL | tpd (average) | tcd (contamination) |
|---|---|---|---|---|
| CMOS inverter | [TBM] | [TBM] | [TBM] | [TBM] |
| 2-input XOR | [TBM] | [TBM] | [TBM] | [TBM] |
| Buffer cell | [TBM] | [TBM] | [TBM] | [TBM] |
| 8-bit top (worst path) | [TBM] | [TBM] | [TBM] | [TBM] |

### 3.2 Hand-calculation estimate (top level)

| Quantity | Estimate [Est] | Basis |
|---|---|---|
| tpd | 50 to 100 ps (about 70 ps) | Minimum-width (120 nm) devices, one XOR on the critical path |
| Maximum operating frequency | 10 to 20 GHz (1 / tpd) | Theoretical single-gate limit |

Logic depth is one gate (each Gn is a single XOR), so the critical path is one XOR delay.

---

## 4. Power, PDP and EDP Analysis

### 4.1 Hand-calculation estimate [Est], VDD = 1.2 V

Assumptions: gate capacitance 1.2 fF/um of width (0.144 fF per transistor), 2 fF output load plus 2 fF of internal capacitance per output toggle, total leakage current about 0.1 uA.

- Dynamic energy, outputs: 199 x (1/2) x 4 fF x (1.2 V)^2 = 573 fJ over 2 us
- Dynamic energy, inputs: 393 x (1/2) x 0.58 fF x (1.2 V)^2 = 163 fJ
- **P_dynamic** = (573 + 163) fJ / 2 us = **0.37 uW**
- **P_static** = I_leak x VDD = 0.1 uA x 1.2 V = **0.12 uW**
- **P_avg** = **0.49 uW**
- Energy per input vector = P_avg x 10 ns = **4.9 fJ**

| tpd assumed | f_max | PDP = P_avg x tpd | EDP = P_avg x tpd^2 |
|---|---|---|---|
| 50 ps | 20 GHz | 0.024 fJ | 1.2e-27 J.s |
| 70 ps | 14.3 GHz | 0.034 fJ | 2.4e-27 J.s |
| 100 ps | 10 GHz | 0.049 fJ | 4.9e-27 J.s |

### 4.2 Measured results

| Parameter | Value |
|---|---|
| Total average power | [TBM] |
| Dynamic power | [TBM] |
| Static leakage power | [TBM] |
| PDP | [TBM] |
| EDP | [TBM] |

---

## 5. Synchronous Timing Parameters

| Parameter | Value |
|---|---|
| Setup time | N/A (combinational circuit, no registers) |
| Hold time | N/A |
| Clock skew | N/A (no clock network) |

The converter has no sequential elements, so these parameters are not defined for it.

---

## 6. Physical Design and Verification

### 6.1 Layout dimensions

| Item | Value |
|---|---|
| Layout width x height | about 7.25 x 7.25 um [Calc, from pin coordinates; confirm in Virtuoso] |
| Layout area | about 52.6 um2 |
| Aspect ratio (W/H) | about 1.0 |
| Gate area / layout area | about 0.9% |

### 6.2 Verification status (Assura)

| Cell | LVS |
|---|---|
| `cmosinv` | Match |
| `Cmos_buffer` | Match |
| `xor` | **Mismatch** (two PMOS in the layout have drain, source and bulk tied to VDD) |
| `8bit_Bin_To_Gra_simu` | **Mismatch** (caused by the XOR cells) |

DRC on `8bit_Bin_To_Gra_simu_lay`: **not clean yet** (off-grid shapes, Nwell width/spacing, poly spacing and contact rules). This section will be updated when DRC and LVS pass.

---

## 7. Pre-Layout vs Post-Layout (RC-Extracted) Comparison

| Parameter | Pre-layout | Post-layout | Delta |
|---|---|---|---|
| tpd | [TBM] | [TBM] | |
| Rise time (10-90%) | [TBM] | [TBM] | |
| Fall time (90-10%) | [TBM] | [TBM] | |
| Total effective capacitance | [TBM] | [TBM] | |
| Dynamic power | [TBM] | [TBM] | |
| Static power | [TBM] | [TBM] | |
| Average power | [TBM] | [TBM] | |
| PDP | [TBM] | [TBM] | |
| EDP | [TBM] | [TBM] | |

Post-layout values require DRC and LVS clean, then parasitic extraction and a simulation on the extracted view.

---

## 8. Known Issues / Work in Progress

1. **XOR output at inputs (1,1):** in the schematic simulation, G1 to G7 stay high instead of going low when both XOR inputs are 1. Checking the PMOS network wiring in the XOR.
2. **Buffer cell:** `Cmos_buffer` is two inverters in parallel, which inverts. A non-inverting buffer needs two inverters in series.
3. **XOR layout:** LVS mismatch described in Section 6.2.
4. **VDD:** confirm the supply value used in the testbench.

---

## 9. How to Reproduce

1. Open the testbench `8bit_Bin_To_Gra_simu` in Virtuoso and run a 2.56 us transient.
2. In the ADE Calculator, measure tPLH / tPHL with `delay(...)` at 50% thresholds on `VT("/Bn")` and `VT("/Gn")`.
3. Measure average supply current with `average(-IT("/V0/PLUS"))`.
4. Run DRC and LVS with Assura, then extract parasitics and re-simulate.

---