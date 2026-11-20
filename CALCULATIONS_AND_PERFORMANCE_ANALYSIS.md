# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

This report uses the structure of the supplied 12T-SRAM example, but applies only converter-specific metrics. The design is a 45 nm combinational 8-bit Binary-to-Gray converter.

> **Measurement integrity:** The replacement Cadence PDFs are overview transient plots for 0–2 µs. They do not contain cursor coordinates or exported numerical samples sufficient for ps-level delay extraction. The supplied spreadsheet contains example placeholders for VDD, delays and currents. Placeholders are not project results and are excluded from all measured-results claims.

## 1. Architecture and equations

The source metrics workbook records seven XOR cells and one MSB buffer cell (eight cells total). The conversion is:
`G8 = B8`
`G7 = B8 XOR B7`
`G6 = B7 XOR B6`
...
`G1 = B2 XOR B1`

Each output has one active logic-cell level. The seven XOR cells operate in parallel; do not add seven XOR delays as though they were in series.

XOR truth table (verify all four cases):
| A | B | A XOR B |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

The uploaded workbook flags a verification item: check whether an XOR output remains high for inputs 1,1. This is not declared a confirmed fault; run a focused four-vector cell test and fix the schematic if 1,1 does not produce 0.

## 2. Replacement transient waveform evidence

- `results/prelayout_transient_G1_G8_B1_B4.pdf`: B1–B4 and G1–G8, transient response, 0–2 µs.
- `results/prelayout_transient_B1_B8_G5_G8.pdf`: B1–B8 and G5–G8, transient response, 0–2 µs.

The plots show input swings around 0–1 V and output axes extending around −0.2–2.0 V. Confirm actual VDD and output logic levels from the testbench/PDK; do not infer VDD from graph axes alone. These are schematic/transient plots and do not prove PEX or post-layout simulation.

## 3. Stimulus rates recorded in the uploaded workbook

These are input stimulus frequencies, not converter Fmax.

| Input | Half-period (µs) | Period (µs) | Stimulus frequency |
|---|---:|---:|---:|
| B1 | 0.01 | 0.02 | 50 MHz |
| B2 | 0.02 | 0.04 | 25 MHz |
| B3 | 0.04 | 0.08 | 12.5 MHz |
| B4 | 0.08 | 0.16 | 6.25 MHz |
| B5 | 0.16 | 0.32 | 3.125 MHz |
| B6 | 0.32 | 0.64 | 1.5625 MHz |
| B7 | 0.64 | 1.28 | 0.78125 MHz |
| B8 | 1.28 | 2.56 | 0.390625 MHz |

The workbook records input low/high levels as 0/1 V and rise time as 100 ps. Confirm the VPULSE properties. Resolve the VDD/signal-swing discrepancy before reporting logic-level margins.

## 4. Parameters to collect from Cadence

| Parameter | Symbol | Cadence source |
|---|---|---|
| Supply/ground | VDD/VSS | Testbench sources |
| Corner/temperature | - | ADE model/corner setup |
| Transistor dimensions | W/L | Schematic instance properties |
| Output loading | CL | Testbench/load cells |
| Input slew/period | tr/tf/T | VPULSE properties |
| Supply current | IDD(t) | Current through actual VDD source |
| Layout boundary | W,H | Virtuoso boundary/bBox, physical units |
| Extracted parasitics | R,C | PEX extracted view/netlist |

## 5. Propagation delay

Use the 50% point of each signal's actual measured swing:
`V50 = (VHIGH + VLOW)/2`

For input rising and output falling:
`tPHL = t(output crosses 50% falling) - t(input crosses 50% rising)`

For input falling and output rising:
`tPLH = t(output crosses 50% rising) - t(input crosses 50% falling)`

Average delay:
`tPD = (tPHL + tPLH)/2`

Measure each sensitized input-to-output path and both edge directions. Report worst-case path delay. Do not estimate ps values from the printed 0–2 µs overview; use ADE delay/cross measurements or exported waveform data with a sufficiently small transient maximum step relative to the 100 ps input edge. Record path, edge, thresholds, input slew, load, corner and temperature.

## 6. Contamination delay

Contamination delay is the minimum time from a valid input transition to the first corresponding output change:
`tCD = min(tcd,HL, tcd,LH)`

Use a documented departure threshold and inspect all sensitized paths/transitions. It is not average delay and not the time until the output fully settles. Not numerically available from the overview PDFs.

## 7. Setup, hold and clock skew

For this standalone combinational converter, setup time, hold time and internal clock skew are **N/A**: there are no clocked storage elements or internal clock network. If embedded between external registers, use the surrounding register-to-register timing constraints; those are not intrinsic converter parameters.

## 8. Maximum-frequency estimate

A combinational block has no intrinsic clock. After measuring worst-case path delay:
`f_delay_estimate ≈ 1/tpath,max`
If delay is in ns: `f_delay_estimate(MHz) ≈ 1000/tpath,max(ns)`.

This is a rough reciprocal-delay estimate, not sign-off Fmax. A synchronous system limit must include launch/capture registers, setup, skew, uncertainty and downstream delay. Do not confuse this estimate with the B1–B8 input stimulus frequencies.

## 9. Power

For constant VDD:
`Pavg = VDD × IDD,avg`
More generally:
`Pavg = (1/T) × integral[VDD(t) × IDD(t)] dt`

Measure supply current through the actual supply source over a stated steady-state interval; check simulator current sign convention.

Static/leakage power with all inputs held at a fixed vector:
`Pstatic = VDD × Ileak`

Leakage depends on input vector, process corner and temperature.

A measured switching-power estimate:
`Pdynamic = Pavg - Pstatic`

First-order activity-based estimate:
`Pdynamic ≈ alpha × Ceff × VDD² × f`

Define alpha convention and switching sequence. For this converter, activity depends on the input sequence and adjacent-bit correlations; do not assume alpha without deriving it. If separately resolved, short-circuit power may also be reported. Avoid double-counting it if already included in Pavg − Pstatic.

## 10. PDP and EDP

Using average total power and average propagation delay:
`PDP = Pavg × tPD`
`EDP = Pavg × tPD² = PDP × tPD`

Units: µW × ns = fJ for PDP; µW × ns² = 10⁻²⁴ J·s for EDP. State whether average power includes leakage/short-circuit components and use consistent conditions.

## 11. Logical effort

For a gate:
`d = g h + p`, with `h = Cout/Cin`.
For a path:
`D = sum(gi hi + pi)`.

Logical effort is a theoretical estimation/optimization technique, not a number directly read from the waveform. Exact XOR logical effort and parasitic delay depend on the actual topology, transistor sizing and 45 nm PDK. Each output path has one active cell stage according to the documented architecture.

## 12. Layout area and aspect ratio

Measure layout boundary dimensions in physical units:
`Area = W × H` (µm²)
`Aspect ratio = W/H`.

The source workbook's image pixel estimate (654 × 653 px; ratio about 1.0015) is only a visual screenshot estimate, not physical layout geometry. Do not report pixel-derived area or aspect ratio as a chip result.

## 13. Pre-layout and post-layout workflow

**Pre-layout:** verify XOR truth table; run schematic transient; measure delay and supply current with defined VDD, input slew, load, corner and temperature.

**Layout/PEX:** complete layout; run DRC and LVS; run the PDK-supported parasitic extraction flow (such as Quantus QRC or the lab-supported extractor); confirm extracted view/netlist contains parasitic R and C.

**Post-layout:** simulate extracted netlist with the same testbench, VDD, load, transient settings, corner and temperature; remeasure delay and power.

Comparison:
`Delta t = tpost − tpre`
`% change = ((tpost − tpre)/tpre) × 100%`

A lumped interconnect time constant is `tau = R C`. For an RC tree, Elmore delay to a node is `tElmore = sum(Ri × Cdownstream,i)`. This is an estimate, not a substitute for extracted-netlist transient simulation.

## 14. Current evidence-based status

| Item | Supported result/status |
|---|---|
| Technology | 45 nm stated project setting; confirm PDK/model corner |
| Architecture | 7 XOR + 1 MSB buffer (source workbook) |
| Logic depth | One active cell level per output, based on architecture |
| Input levels / rise | 0–1 V / 100 ps recorded in workbook; confirm VPULSE |
| Transient run | 0–2 µs visible in both supplied graph PDFs |
| Input stimulus | B1 50 MHz to B8 0.390625 MHz, calculated from workbook periods |
| tPLH/tPHL/tPD | Not numerically extracted; requires ADE crossing measurements |
| Contamination delay | Not numerically extracted |
| Power | Not numerically extracted; requires supply-current simulations |
| PDP/EDP | Compute only after actual power and delay measurements |
| Setup/hold/skew | N/A for standalone combinational block |
| Fmax | Not established; needs worst-case path delay and system assumptions |
| Area/aspect ratio | Needs physical layout boundary dimensions |
| Post-layout results | Not established until PEX and extracted-netlist simulation |

## 15. Illustrative arithmetic only (not project results)

If an example ADE run had tPHL = 0.18 ns and tPLH = 0.24 ns:
`tPD = (0.18 + 0.24)/2 = 0.21 ns = 210 ps`.

Then reciprocal-delay estimate:
`1000/0.21 = 4762 MHz ≈ 4.762 GHz` (not sign-off Fmax).

For an example only, with VDD = 1.8 V, Iavg = 120 µA, Ileak = 2 µA and tPD = 0.21 ns:
- Pavg = 1.8 × 120 = 216 µW
- Pstatic = 1.8 × 2 = 3.6 µW
- Pdynamic = 216 − 3.6 = 212.4 µW
- PDP = 216 × 0.21 = 45.36 fJ
- EDP = 216 × 0.21² = 9.5256 × 10⁻²⁴ J·s

All numbers in this subsection are hypothetical substitutions to demonstrate arithmetic. They must not be copied into the measured-results table.

## 16. Checklist before final submission

- [ ] Confirm VDD from testbench/PDK and resolve signal-swing mismatch.
- [ ] Verify XOR 00, 01, 10, 11 cases (especially 11 → 0).
- [ ] Export high-resolution waveform/ADE measurement data.
- [ ] Measure worst-case tPLH, tPHL, tPD and minimum contamination delay.
- [ ] Measure average switching current and static leakage current.
- [ ] Capture physical boundary W and H in µm.
- [ ] Save DRC and LVS reports.
- [ ] Run PEX and confirm extracted R/C netlist/view.
- [ ] Run post-layout simulation under the same conditions.
- [ ] Compute PDP, EDP and pre/post differences from measured values.

## Reference format

The structure is inspired by the friend's 12T-SRAM calculation document, but SRAM-specific butterfly/SNM/read/write metrics and its numerical examples are not used as converter results:
https://github.com/226ec1006-Bhargav/12T-SRAM-Cadence-Virtuoso/blob/main/CALCULATIONS_AND_PERFORMANCE_ANALYSIS.md
