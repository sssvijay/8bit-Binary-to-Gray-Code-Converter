# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

This report presents converter-specific equations, worked calculations and the Cadence measurement procedure for the 45 nm, 8-bit combinational Binary-to-Gray converter.

> **Measurement integrity:** The supplied transient PDFs are overview plots covering 0–2 µs. They do not contain cursor coordinates or exported numerical samples precise enough to calculate picosecond delays. The supplied workbook also labels some delay and current entries as placeholders. Placeholder values are not presented as measured project results.

## 1. Design equations

For binary input \(B_8B_7\ldots B_1\) and Gray output \(G_8G_7\ldots G_1\):

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

The supplied workbook describes seven XOR cells and one MSB buffer cell. The seven XOR cells operate on parallel paths; they are not seven gates connected in series.

### XOR truth table

| A | B | A XOR B |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Verify the final row in the XOR testbench: inputs 1 and 1 must produce output 0. The workbook flags this as a verification item, not as a confirmed circuit fault.

## 2. Supplied transient plots

- \`results/prelayout_transient_G1_G8_B1_B4.pdf\` — B1–B4 and G1–G8, page 1.
- \`results/prelayout_transient_B1_B8_G5_G8.pdf\` — B1–B8 and G5–G8, page 1.

Both supplied plots show transient response over 0–2 µs. The input traces appear around 0–1 V, while the output axes extend to approximately −0.2–2.0 V. Confirm the actual supply voltage and output logic levels in the testbench and PDK; do not infer the supply voltage from graph axes alone.

These screenshots document transient plots. They do not establish exact picosecond delay, power, PEX completion, post-layout simulation or physical area.

## 3. Input-stimulus frequency calculations

The workbook records the input pulse half-periods. For each input:

$$
T=2T_H
$$

$$
f=\frac{1}{T}
$$

### Example: input B1

Given:

$$
T_H=0.01\ \mu s
$$

Calculate the period:

$$
T=2\times0.01=0.02\ \mu s
$$

Calculate the frequency:

$$
f=\frac{1}{0.02\times10^{-6}}
$$

$$
\boxed{f=50\ MHz}
$$

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

These are input stimulus frequencies, not the converter's maximum operating frequency.

## 4. Parameters to collect from Cadence

| Parameter | Symbol | Cadence source |
|---|---|---|
| Supply voltage | \(V_{DD}\) | Testbench supply source |
| Ground voltage | \(V_{SS}\) | Testbench |
| Process corner and temperature | — | ADE model/corner setup |
| Output load | \(C_L\) | Testbench/load cells |
| Input rise and fall times | \(t_r,t_f\) | VPULSE properties |
| Supply current | \(I_{DD}(t)\) | Current through VDD source |
| Physical width and height | \(W,H\) | Layout boundary/bounding box |
| Extracted parasitics | \(R,C\) | PEX extracted view/netlist |

The workbook records input low/high levels of 0 V/1 V and a 100 ps rise time. Verify these settings in VPULSE. Resolve the supply-voltage/output-swing discrepancy before calculating logic thresholds and power.

## 5. Propagation delay calculations

First calculate the midpoint of the actual signal swing:

$$
V_{mid}=\frac{V_{HIGH}+V_{LOW}}{2}
$$

For an input rising transition that causes the output to fall:

$$
t_{PHL}=t_{out,fall}-t_{in,rise}
$$

For an input falling transition that causes the output to rise:

$$
t_{PLH}=t_{out,rise}-t_{in,fall}
$$

Measure all four crossing times at the same midpoint voltage.

Average propagation delay:

$$
t_{PD}=\frac{t_{PHL}+t_{PLH}}{2}
$$

### Worked numerical example — illustrative only

Suppose hypothetical ADE measurements give:

$$
t_{in,rise}=1.000\ ns
$$

$$
t_{out,fall}=1.180\ ns
$$

Then:

$$
t_{PHL}=1.180-1.000
$$

$$
\boxed{t_{PHL}=0.180\ ns=180\ ps}
$$

For the other transition, suppose:

$$
t_{in,fall}=2.000\ ns
$$

$$
t_{out,rise}=2.240\ ns
$$

Then:

$$
t_{PLH}=2.240-2.000
$$

$$
\boxed{t_{PLH}=0.240\ ns=240\ ps}
$$

Average delay:

$$
t_{PD}=\frac{0.180+0.240}{2}
$$

$$
\boxed{t_{PD}=0.210\ ns=210\ ps}
$$

These values demonstrate the calculation method only. They are not extracted from your supplied waveform PDFs.

Measure every sensitized input-to-output path and both transition directions. Record the path, transition, threshold, input slew, output load, process corner and temperature.

## 6. Contamination delay

Contamination delay is the minimum time from a valid input transition to the first corresponding output departure from its previous valid level.

$$
t_{CD}=\min(t_{cd,HL},t_{cd,LH})
$$

It is not average propagation delay and not the time required for the output to finish settling. Define a consistent output departure threshold and inspect the minimum across the relevant paths. A numerical value is not available from the supplied overview PDFs.

## 7. Setup time, hold time and clock skew

For this standalone combinational converter:

$$
t_{setup}=\text{N/A}
$$

$$
t_{hold}=\text{N/A}
$$

$$
t_{skew}=\text{N/A}
$$

The converter has no internal flip-flops or clock network. Setup, hold and clock skew become relevant when the block is included between external registers in a synchronous system.

## 8. Maximum-frequency estimate

A combinational block has no intrinsic clock frequency. After measuring the worst-case input-to-output delay, a rough reciprocal-delay estimate can be calculated:

$$
f_{delay,est}\approx\frac{1}{t_{path,max}}
$$

When delay is expressed in ns:

$$
f_{delay,est}(MHz)\approx\frac{1000}{t_{path,max}(ns)}
$$

Using the illustrative path delay of 0.210 ns:

$$
f_{delay,est}=\frac{1}{0.210\times10^{-9}}
$$

$$
\boxed{f_{delay,est}\approx4.762\ GHz}
$$

This is an illustrative delay-limited estimate, not sign-off maximum frequency. A synchronous system limit must also account for register setup time, clock skew, clock uncertainty and other path delays.

## 9. Power calculations

### 9.1 Average power

For a constant supply voltage:

$$
P_{avg}=V_{DD}\times I_{DD,avg}
$$

For time-varying supply voltage and current:

$$
P_{avg}=\frac{1}{T}\int_{t_0}^{t_0+T}V_{DD}(t)I_{DD}(t)\,dt
$$

Measure current through the actual VDD source over a stated steady-state interval. Check the simulator's current sign convention.

### 9.2 Static (leakage) power

Hold the inputs at a fixed vector, allow transients to settle, and measure leakage current:

$$
P_{static}=V_{DD}\times I_{leak}
$$

Illustrative substitution only:

$$
V_{DD}=1.8\ V,\qquad I_{leak}=2\ \mu A
$$

$$
P_{static}=1.8\times2
$$

$$
\boxed{P_{static}=3.6\ \mu W}
$$

The voltage and current above are example values, not confirmed project measurements.

### 9.3 Dynamic power

A switching-power estimate from measured average power and static power is:

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
\alpha=0.2
$$

$$
C_{eff}=20\times10^{-15}\ F
$$

$$
V_{DD}=1.8\ V,\qquad f=100\times10^{6}\ Hz
$$

Substitute:

$$
P_{dynamic}=0.2\times(20\times10^{-15})\times(1.8)^2\times(100\times10^{6})
$$

Square the supply voltage:

$$
(1.8)^2=3.24
$$

Multiply the numerical factors:

$$
P_{dynamic}=0.2\times20\times3.24\times100\times10^{-9}
$$

$$
P_{dynamic}=1296\times10^{-9}\ W
$$

Therefore:

$$
\boxed{P_{dynamic}=1.296\times10^{-6}\ W=1.296\ \mu W}
$$

This arithmetic is correct for the illustrative values above. It is not a measured result from the converter. Use measured effective capacitance, activity, supply voltage and frequency for your design.

### 9.4 Total power

When the components are separately resolved:

$$
P_{total}=P_{dynamic}+P_{static}+P_{short-circuit}
$$

If dynamic power is calculated as average power minus static power, do not add short-circuit power again when it is already included in the measured average current.

## 10. PDP and EDP calculations

Power-delay product:

$$
PDP=P_{avg}\times t_{PD}
$$

Energy-delay product:

$$
EDP=P_{avg}\times t_{PD}^{2}
$$

Using illustrative values only, \(P_{avg}=216\ \mu W\) and \(t_{PD}=0.210\ ns\):

$$
PDP=216\times0.210
$$

$$
\boxed{PDP=45.36\ \mu W\cdot ns=45.36\ fJ}
$$

Calculate EDP:

$$
EDP=216\times(0.210)^2
$$

$$
\boxed{EDP=9.5256\ \mu W\cdot ns^2}
$$

The example is not a measured project result. Use average power and delay measured under the same operating conditions.

## 11. Logical effort and path delay

For a single gate:

$$
d=gh+p
$$

Electrical effort:

$$
h=\frac{C_{out}}{C_{in}}
$$

For a path containing \(N\) stages:

$$
D=\sum_{i=1}^{N}(g_i h_i+p_i)
$$

The documented converter architecture has one XOR logic-cell level per Gray output. The seven XOR cells are parallel paths. Exact logical-effort and parasitic-delay values depend on the actual XOR topology, transistor sizing and 45 nm PDK. Logical effort is a theoretical estimate, not a value read directly from a transient graph.

## 12. Layout area and aspect ratio

Measure the physical layout boundary width and height:

$$
A=W\times H
$$

$$
AR=\frac{W}{H}
$$

Illustrative example only, for \(W=100\ \mu m\) and \(H=80\ \mu m\):

$$
A=100\times80
$$

$$
\boxed{A=8000\ \mu m^2}
$$

$$
AR=\frac{100}{80}
$$

$$
\boxed{AR=1.25}
$$

These are not measured layout dimensions. Read the cell boundary or bounding box in Virtuoso and confirm that the dimensions are in µm.

## 13. Parasitic RC and Elmore delay

A lumped RC time constant is:

$$
\tau=RC
$$

For an RC tree, the Elmore delay to a selected node is:

$$
t_{Elmore}=\sum_i R_iC_{downstream,i}
$$

These equations estimate interconnect contributions. The preferred post-layout result is obtained by simulating the PEX-extracted netlist.

## 14. Pre-layout versus post-layout comparison

After PEX, simulate the extracted netlist using the same testbench, input slew, load, supply voltage, corner, temperature and transient settings.

Delay difference:

$$
\Delta t=t_{post}-t_{pre}
$$

Percentage change:

$$
\%\Delta t=\frac{t_{post}-t_{pre}}{t_{pre}}\times100
$$

Use the same comparison method for power. A layout-only view is not a post-layout simulation unless extracted parasitic resistance and capacitance are included.

## 15. Evidence-based results status

| Parameter | Current evidence/status |
|---|---|
| Technology | 45 nm project setting; confirm exact PDK corner |
| Architecture | 7 XOR + 1 MSB buffer, as recorded in workbook |
| Input low/high levels | 0 V / 1 V recorded in workbook; confirm VPULSE |
| Input rise time | 100 ps recorded in workbook; confirm VPULSE |
| Transient interval | 0–2 µs visible on supplied graphs |
| Input stimulus rates | B1 50 MHz to B8 0.390625 MHz, calculated from workbook periods |
| Propagation delay | Requires ADE crossing-time measurements |
| Contamination delay | Requires minimum-delay measurements |
| Static/dynamic/average power | Requires supply-current simulations |
| PDP/EDP | Calculate after actual power and delay are measured |
| Setup/hold/clock skew | Not applicable to standalone combinational block |
| Maximum frequency | Not established; requires worst-case path delay and system constraints |
| Area/aspect ratio | Requires physical boundary dimensions from Virtuoso |
| Post-layout metrics | Requires PEX and extracted-netlist simulation |

## 16. Final measurement checklist

- [ ] Confirm actual supply voltage and PDK corner.
- [ ] Verify the XOR truth table, especially the input case 1,1 producing output 0.
- [ ] Export high-resolution transient waveform or ADE measurement data.
- [ ] Measure worst-case \(t_{PLH}\), \(t_{PHL}\), \(t_{PD}\) and minimum contamination delay.
- [ ] Measure average switching current over a stated interval.
- [ ] Measure static leakage current with fixed input vectors.
- [ ] Capture layout boundary width and height in µm.
- [ ] Save DRC and LVS reports.
- [ ] Run PEX and verify extracted resistance and capacitance.
- [ ] Run post-layout simulation under matching conditions.
- [ ] Calculate PDP, EDP and pre/post-layout differences from measured results.

## Reference

The section organization is inspired by the supplied 12T-SRAM example. SRAM-only butterfly/SNM/read/write analysis is not applicable to this combinational converter; the equations and measurement procedure above are converter-specific.

[Friend's 12T-SRAM calculation format](https://github.com/226ec1006-Bhargav/12T-SRAM-Cadence-Virtuoso/blob/main/CALCULATIONS_AND_PERFORMANCE_ANALYSIS.md)
