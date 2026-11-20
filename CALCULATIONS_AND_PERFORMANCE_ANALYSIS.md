# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

This report presents the equations and worked calculations for the 45 nm, 8-bit combinational Binary-to-Gray converter. Display equations use GitHub's math rendering, so they appear as formatted mathematics rather than code-style text.

> **Measurement integrity:** The supplied waveform PDFs are overview transient plots over 0–2 µs. They do not provide cursor coordinates or exported samples precise enough for picosecond delay extraction. The accompanying workbook marks several delay/current values as placeholders. Such placeholders are not reported as measured results.

## 1. Design equations

For binary input (B_8B_7\ldots B_1) and Gray output (G_8G_7\ldots G_1):

$$
G_8=B_8
$$

$$
G_7=B_8\oplus B_7
$$

$$
G_6=B_7\oplus B_6
$$

$$
G_5=B_6\oplus B_5
$$

$$
\vdots
$$

$$
G_1=B_2\oplus B_1
$$

The workbook describes seven XOR cells and one MSB buffer cell. The seven XOR paths are parallel; they are not seven XOR gates connected in series.

### XOR truth table

| A | B | A XOR B |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Verify the (1,1\rightarrow0) case in the XOR cell testbench before treating functional operation as fully validated. The workbook contains this as a verification item, not as a confirmed fault.

## 2. Supplied transient plots

- `results/prelayout_transient_G1_G8_B1_B4.pdf` — B1–B4 and G1–G8, page 1.
- `results/prelayout_transient_B1_B8_G5_G8.pdf` — B1–B8 and G5–G8, page 1.

Both plots show transient response over 0–2 µs. The input traces appear around 0–1 V, while the output axes extend to approximately −0.2–2.0 V. Confirm the true VDD and output logic levels in the testbench and PDK; do not infer VDD only from graph axes.

These screenshots demonstrate that transient waveforms were plotted. They do not establish exact ps-level delay, power, PEX completion, post-layout simulation, or physical area.

## 3. Input-stimulus frequency calculations

The workbook records these input pulse half-periods. For each bit:

$$
T=2T_H
$$

$$
f=\frac{1}{T}
$$

Example for B1:

$$
T_{B1}=2\times0.01\ \mu s=0.02\ \mu s
$$

$$
f_{B1}=\frac{1}{0.02\ \mu s}=50\ MHz
$$

| Input | Half-period (T_H) (µs) | Period (T) (µs) | Stimulus frequency |
|---|---:|---:|---:|
| B1 | 0.01 | 0.02 | 50 MHz |
| B2 | 0.02 | 0.04 | 25 MHz |
| B3 | 0.04 | 0.08 | 12.5 MHz |
| B4 | 0.08 | 0.16 | 6.25 MHz |
| B5 | 0.16 | 0.32 | 3.125 MHz |
| B6 | 0.32 | 0.64 | 1.5625 MHz |
| B7 | 0.64 | 1.28 | 0.78125 MHz |
| B8 | 1.28 | 2.56 | 0.390625 MHz |

These are applied stimulus rates, **not** the converter's maximum operating frequency.

## 4. Values to obtain from Cadence

| Quantity | Symbol | Where to obtain it |
|---|---|---|
| Supply voltage | (V_{DD}) | Testbench supply source |
| Ground | (V_{SS}) | Testbench |
| Process corner / temperature | — | ADE model/corner setup |
| Output load | (C_L) | Testbench |
| Input rise/fall time | (t_r,t_f) | VPULSE properties |
| Supply current | (I_{DD}(t)) | Current through VDD source |
| Physical width/height | (W,H) | Layout boundary/bBox |
| Extracted parasitics | (R,C) | PEX extracted view/netlist |

The workbook records input low/high levels of 0 V/1 V and a 100 ps rise time; verify these settings in VPULSE. Resolve the VDD/output-swing discrepancy before calculating logic thresholds and power.

## 5. Propagation delay calculations

Use the actual signal low and high levels to define the midpoint:

$$
V_{50\%}=\frac{V_{HIGH}+V_{LOW}}{2}
$$

For input rising and output falling:

$$
t_{PHL}=t_{out,fall,50\%}-t_{in,rise,50\%}
$$

For input falling and output rising:

$$
t_{PLH}=t_{out,rise,50\%}-t_{in,fall,50\%}
$$

Average propagation delay:

$$
t_{PD}=\frac{t_{PHL}+t_{PLH}}{2}
$$

### Worked numerical example — illustrative only

Suppose ADE measurements (hypothetical) give:

$$
t_{in,rise,50\%}=1.000\ ns
$$

$$
t_{out,fall,50\%}=1.180\ ns
$$

Then:

$$
t_{PHL}=1.180-1.000=0.180\ ns
$$

Suppose the second transition gives:

$$
t_{in,fall,50\%}=2.000\ ns
$$

$$
t_{out,rise,50\%}=2.240\ ns
$$

Then:

$$
t_{PLH}=2.240-2.000=0.240\ ns
$$

Average delay:

$$
t_{PD}=\frac{0.180+0.240}{2}=0.210\ ns
$$

$$
\boxed{t_{PD}=210\ ps}
$$

These numbers demonstrate the arithmetic only. They are **not** extracted from your supplied overview plots.

Measure each sensitized input-to-output path and both edge directions. Record path, edge, threshold, input slew, output load, process corner and temperature.

## 6. Contamination delay

Contamination delay is the minimum time from a valid input transition to the first corresponding output departure from its previous valid level.

$$
t_{CD}=\min\left(t_{cd,HL},t_{cd,LH}\right)
$$

It is not the average propagation delay and not the time for the output to finish settling. Use an explicit departure threshold and inspect the minimum across relevant paths. A numerical value is not available from the overview PDFs.

## 7. Setup time, hold time and clock skew

For the standalone combinational converter:

$$
t_{setup}=\text{N/A}
$$

$$
t_{hold}=\text{N/A}
$$

$$
t_{skew}=\text{N/A}
$$

There are no internal flip-flops or clock network. These constraints become relevant only when the converter is placed between external registers in a synchronous system.

## 8. Maximum-frequency estimate

A combinational block has no intrinsic clock frequency. A rough reciprocal-delay estimate can be computed after the worst-case path delay is measured:

$$
f_{delay,est}\approx\frac{1}{t_{path,max}}
$$

If the path delay is expressed in ns:

$$
f_{delay,est}(MHz)\approx\frac{1000}{t_{path,max}(ns)}
$$

Using the illustrative delay above:

$$
f_{delay,est}=\frac{1}{0.210\ ns}=4.762\ GHz
$$

This is an illustrative delay-limited estimate, **not sign-off Fmax**. A synchronous system limit must include register setup, clock skew, clock uncertainty and other path delays.

## 9. Power calculations

### 9.1 Average power

For constant supply voltage:

$$
P_{avg}=V_{DD}\times I_{DD,avg}
$$

For time-varying voltage and current:

$$
P_{avg}=\frac{1}{T}\int_{t_0}^{t_0+T}V_{DD}(t)I_{DD}(t)\,dt
$$

Use the current through the actual VDD source over a stated steady-state interval. Check the simulator's current sign convention.

### 9.2 Static (leakage) power

Hold inputs at a fixed vector, allow transients to settle, and measure leakage current:

$$
P_{static}=V_{DD}\times I_{leak}
$$

Hypothetical substitution:

$$
P_{static}=1.8\ V\times2\ \mu A
$$

$$
\boxed{P_{static}=3.6\ \mu W}
$$

The (1.8\ V) and (2\ \mu A) values are an arithmetic example only; they are not confirmed design values.

### 9.3 Dynamic power

A switching-power estimate from measured average and static power is:

$$
P_{dynamic}=P_{avg}-P_{static}
$$

The first-order activity-based equation is:

$$
P_{dynamic}=\alpha C_{eff}V_{DD}^{2}f
$$

#### Worked numerical example — illustrative only

Given:

$$
\alpha=0.2,\quad C_{eff}=20\ fF
$$

$$
V_{DD}=1.8\ V,\quad f=100\ MHz
$$

Substitute:

$$
P_{dynamic}=0.2\times(20\times10^{-15})\times(1.8)^2\times(100\times10^6)
$$

Square the supply voltage:

$$
(1.8)^2=3.24
$$

Combine the powers of ten:

$$
P_{dynamic}=0.2\times20\times3.24\times100\times10^{-9}\ W
$$

Multiply:

$$
P_{dynamic}=129.6\times10^{-9}\ W
$$

Therefore:

$$
\boxed{P_{dynamic}=1.296\times10^{-7}\ W=0.1296\ \mu W}
$$

**Arithmetic note:** for exactly these stated inputs, the result is (0.1296\ \mu W), not (1.296\ \mu W). The latter is ten times too large. Use actual measured (C_{eff}), activity, VDD and frequency for your converter.

### 9.4 Total power

When components are separately resolved:

$$
P_{total}=P_{dynamic}+P_{static}+P_{short-circuit}
$$

If dynamic power is calculated as (P_{avg}-P_{static}), do not add short-circuit power again if it is already included in the measured average current.

## 10. PDP and EDP calculations

Power-delay product:

$$
PDP=P_{avg}\times t_{PD}
$$

Energy-delay product:

$$
EDP=P_{avg}\times t_{PD}^{2}=PDP\times t_{PD}
$$

Using the **illustrative** (P_{avg}=216\ \mu W) and (t_{PD}=0.210\ ns):

$$
PDP=216\times0.210=45.36\ \mu W\cdot ns
$$

$$
\boxed{PDP=45.36\ fJ}
$$

$$
EDP=216\times(0.210)^2
$$

$$
\boxed{EDP=9.5256\times10^{-24}\ J\cdot s}
$$

The example is not your measured project result. Use one consistent measured power and delay pair from the same operating conditions.

## 11. Logical effort and path delay

For a single gate:

$$
d=gh+p
$$

Electrical effort:

$$
h=\frac{C_{out}}{C_{in}}
$$

For a path of (N) stages:

$$
D=\sum_{i=1}^{N}(g_i h_i+p_i)
$$

Since the documented converter has one XOR logic-cell level per Gray output, the seven XOR cells are parallel paths. Use the actual XOR topology, transistor sizing and PDK data for (g) and (p); do not label generic textbook values as measured results.

## 12. Layout area and aspect ratio

From the physical layout boundary:

$$
A=W\times H
$$

$$
AR=\frac{W}{H}
$$

Illustrative example only, for (W=100\ \mu m) and (H=80\ \mu m):

$$
A=100\times80=8000\ \mu m^2
$$

$$
AR=\frac{100}{80}=1.25
$$

These are not your measured layout dimensions. Read the cell boundary/bounding box in Virtuoso and confirm the units are µm.

## 13. Parasitic RC and Elmore delay

A lumped RC time constant is:

$$
\tau=RC
$$

For an RC tree, the Elmore delay to a selected node is:

$$
t_{Elmore}=\sum_i R_iC_{downstream,i}
$$

These equations estimate interconnect contributions. The preferred post-layout result is obtained by simulating the PEX extracted netlist.

## 14. Pre-layout versus post-layout comparison

After PEX, simulate the extracted netlist using the same testbench, input slew, load, VDD, corner, temperature and transient settings.

Delay difference:

$$
\Delta t=t_{post}-t_{pre}
$$

Percentage change:

$$
\%\Delta t=\frac{t_{post}-t_{pre}}{t_{pre}}\times100\%
$$

Use the same calculation for power. Do not call a layout-only simulation post-layout unless extracted parasitic R and C are included.

## 15. Evidence-based results status

| Parameter | Current evidence/status |
|---|---|
| Technology | 45 nm project setting; confirm exact PDK corner |
| Architecture | 7 XOR + 1 MSB buffer, as recorded in workbook |
| Input low/high levels | 0 V / 1 V recorded in workbook; confirm VPULSE |
| Input rise time | 100 ps recorded in workbook; confirm VPULSE |
| Transient interval | 0–2 µs visible on supplied graphs |
| Input stimulus rates | B1 50 MHz to B8 0.390625 MHz, calculated from workbook periods |
| (t_{PLH},t_{PHL},t_{PD}) | Not yet measured numerically |
| Contamination delay | Not yet measured numerically |
| Static/dynamic/average power | Requires supply-current simulations |
| PDP/EDP | Calculate after actual power and delay are measured |
| Setup/hold/clock skew | N/A for standalone combinational block |
| Maximum frequency | Not established; requires worst-case path delay and system constraints |
| Area/aspect ratio | Requires physical boundary dimensions from Virtuoso |
| Post-layout metrics | Requires PEX and extracted-netlist simulation |

## 16. Checklist before final submission

- [ ] Confirm actual VDD and PDK corner.
- [ ] Verify the XOR truth table, especially (1,1\rightarrow0).
- [ ] Export high-resolution transient waveform or ADE measurement data.
- [ ] Measure worst-case (t_{PLH}), (t_{PHL}), (t_{PD}) and minimum contamination delay.
- [ ] Measure average switching current over a stated interval.
- [ ] Measure static leakage current with fixed input vectors.
- [ ] Capture layout boundary width and height in µm.
- [ ] Save DRC and LVS results.
- [ ] Run PEX and verify extracted R/C.
- [ ] Run post-layout simulation under matching conditions.
- [ ] Calculate PDP, EDP and pre/post differences from measured results.

## Reference

The section organization is inspired by the supplied 12T-SRAM example. SRAM-only butterfly/SNM/read/write analysis is not applicable to this combinational converter, so the calculations above are converter-specific:

https://github.com/226ec1006-Bhargav/12T-SRAM-Cadence-Virtuoso/blob/main/CALCULATIONS_AND_PERFORMANCE_ANALYSIS.md
