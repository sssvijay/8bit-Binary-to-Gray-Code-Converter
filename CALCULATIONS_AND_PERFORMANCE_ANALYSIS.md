# 8-bit Binary-to-Gray Converter: Calculations and Performance Analysis

This document presents the design equations and calculation methods for the 45 nm, 8-bit combinational Binary-to-Gray converter implemented in Cadence Virtuoso.

## 1. Binary-to-Gray conversion equations

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
G_4=B_5\oplus B_4
$$

$$
G_3=B_4\oplus B_3
$$

$$
G_2=B_3\oplus B_2
$$

$$
G_1=B_2\oplus B_1
$$

The converter uses seven XOR logic cells and one MSB buffer path. The XOR cells operate in parallel, with one logic-cell stage per Gray output.

### XOR truth table

| A | B | A XOR B |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

## 2. Transient simulation waveform

The transient simulation is shown over a time interval of 0 to 2 µs. The input signals are B1–B8 and the output signals are G1–G8.

The supplied input stimulus settings are:

$$
V_{LOW}=0\ V,\qquad V_{HIGH}=1\ V
$$

$$
t_r=100\ ps
$$

The input waveforms use different pulse periods so that the binary input combinations are exercised during the transient simulation.

## 3. Input-stimulus frequency calculations

For a pulse signal, the period is twice its half-period:

$$
T=2T_H
$$

The input stimulus frequency is:

$$
f=\frac{1}{T}
$$

### Calculation for input B1

$$
T_H=0.01\ \mu s
$$

$$
T=2\times0.01=0.02\ \mu s
$$

$$
f=\frac{1}{0.02\times10^{-6}}
$$

$$
\boxed{f_{B1}=50\ MHz}
$$

### Input stimulus frequency table

| Input | Half-period (µs) | Period (µs) | Frequency (MHz) |
|---|---:|---:|---:|
| B1 | 0.01 | 0.02 | 50 |
| B2 | 0.02 | 0.04 | 25 |
| B3 | 0.04 | 0.08 | 12.5 |
| B4 | 0.08 | 0.16 | 6.25 |
| B5 | 0.16 | 0.32 | 3.125 |
| B6 | 0.32 | 0.64 | 1.5625 |
| B7 | 0.64 | 1.28 | 0.78125 |
| B8 | 1.28 | 2.56 | 0.390625 |

## 4. Propagation delay calculations

The 50% threshold is calculated from the signal's measured high and low levels:

$$
V_{50}=\frac{V_{HIGH}+V_{LOW}}{2}
$$

For an input rising transition that causes the output to fall:

$$
t_{PHL}=t_{out,fall}-t_{in,rise}
$$

For an input falling transition that causes the output to rise:

$$
t_{PLH}=t_{out,rise}-t_{in,fall}
$$

The average propagation delay is:

$$
t_{PD}=\frac{t_{PHL}+t_{PLH}}{2}
$$

### Delay calculation sequence

1. Measure the input crossing time at the 50% threshold.
2. Measure the corresponding output crossing time at the 50% threshold.
3. Subtract the input crossing time from the output crossing time.
4. Repeat for the rising and falling output transitions.
5. Calculate the average propagation delay using the equation above.

For the complete converter, evaluate each sensitized input-to-output path and identify the largest propagation delay.

## 5. Contamination delay

Contamination delay is the minimum time between a valid input transition and the first corresponding output transition.

$$
t_{CD}=\min(t_{cd,HL},t_{cd,LH})
$$

The minimum value is obtained by evaluating the relevant input-to-output paths and both transition directions.

## 6. Setup time, hold time and clock skew

The Binary-to-Gray converter is a combinational logic circuit and does not contain clocked storage elements.

$$
t_{setup}=\text{N/A}
$$

$$
t_{hold}=\text{N/A}
$$

$$
t_{skew}=\text{N/A}
$$

## 7. Maximum-frequency calculation

The critical path of the converter contains one XOR logic-cell stage. The delay-limited frequency is calculated from the worst-case path delay:

$$
f_{max,est}=\frac{1}{t_{path,max}}
$$

When the path delay is expressed in nanoseconds:

$$
f_{max,est}(MHz)=\frac{1000}{t_{path,max}(ns)}
$$

This is the delay-based frequency estimate for the combinational logic path.

## 8. Average power calculation

Average power is calculated from the supply voltage and average current drawn from the supply:

$$
P_{avg}=V_{DD}\times I_{DD,avg}
$$

For a time-varying supply voltage and current:

$$
P_{avg}=\frac{1}{T}\int_{t_0}^{t_0+T}V_{DD}(t)I_{DD}(t)\,dt
$$

## 9. Static power calculation

Static power is calculated from the supply voltage and leakage current when the circuit inputs are held at a constant logic vector:

$$
P_{static}=V_{DD}\times I_{leak}
$$

## 10. Dynamic power calculation

Dynamic power is the switching component of the total average power:

$$
P_{dynamic}=P_{avg}-P_{static}
$$

The activity-based CMOS switching-power equation is:

$$
P_{dynamic}=\alpha C_{eff}V_{DD}^{2}f
$$

Where:

- \(\alpha\) is the switching activity factor.
- \(C_{eff}\) is the effective switched capacitance.
- \(V_{DD}\) is the supply voltage.
- \(f\) is the switching frequency.

## 11. Power-delay product (PDP)

Power-delay product is the product of average power and average propagation delay:

$$
PDP=P_{avg}\times t_{PD}
$$

Since µW × ns is equal to fJ:

$$
PDP(fJ)=P_{avg}(\mu W)\times t_{PD}(ns)
$$

## 12. Energy-delay product (EDP)

Energy-delay product is calculated by multiplying average power by the square of propagation delay:

$$
EDP=P_{avg}\times t_{PD}^{2}
$$

Using power in µW and delay in ns:

$$
EDP(\mu W\cdot ns^2)=P_{avg}(\mu W)\times[t_{PD}(ns)]^2
$$

## 13. Logical-effort delay calculation

The normalized delay of a logic gate is:

$$
d=gh+p
$$

Electrical effort is:

$$
h=\frac{C_{out}}{C_{in}}
$$

For a path containing \(N\) stages:

$$
D=\sum_{i=1}^{N}(g_i h_i+p_i)
$$

For this converter, each Gray output is generated through one XOR logic-cell stage. The seven XOR paths are parallel paths.

## 14. Layout area and aspect ratio

The layout area is calculated from the physical boundary width and height:

$$
A=W\times H
$$

The aspect ratio is:

$$
AR=\frac{W}{H}
$$

Where width and height are taken from the physical layout boundary in micrometres.

## 15. Parasitic RC and Elmore delay

The RC time constant is:

$$
\tau=RC
$$

For an RC tree, the Elmore delay to a selected node is:

$$
t_{Elmore}=\sum_i R_iC_{downstream,i}
$$

## 16. Pre-layout and post-layout comparison

The delay difference between schematic and extracted-layout simulations is:

$$
\Delta t=t_{post}-t_{pre}
$$

Percentage change in delay is:

$$
\%\Delta t=\frac{t_{post}-t_{pre}}{t_{pre}}\times100
$$

The same comparison can be applied to average power:

$$
\Delta P=P_{post}-P_{pre}
$$

$$
\%\Delta P=\frac{P_{post}-P_{pre}}{P_{pre}}\times100
$$

## 17. Summary of calculations

| Calculation | Expression |
|---|---|
| Binary-to-Gray conversion | Seven adjacent-bit XOR equations and direct MSB connection |
| Input stimulus frequency | \(f=1/(2T_H)\) |
| Propagation delay | \(t_{PHL}\), \(t_{PLH}\), and \(t_{PD}=(t_{PHL}+t_{PLH})/2\) |
| Contamination delay | Minimum input-to-output transition delay |
| Maximum-frequency estimate | \(f_{max,est}=1/t_{path,max}\) |
| Average power | \(P_{avg}=V_{DD}I_{DD,avg}\) |
| Static power | \(P_{static}=V_{DD}I_{leak}\) |
| Dynamic power | \(P_{dynamic}=P_{avg}-P_{static}\) |
| Power-delay product | \(PDP=P_{avg}t_{PD}\) |
| Energy-delay product | \(EDP=P_{avg}t_{PD}^{2}\) |
| Logical effort | \(d=gh+p\) |
| Layout area | \(A=W\times H\) |
| Aspect ratio | \(AR=W/H\) |
| RC time constant | \(\tau=RC\) |
| Elmore delay | \(t_{Elmore}=\sum_iR_iC_{downstream,i}\) |
| Pre-/post-layout delay difference | \(\Delta t=t_{post}-t_{pre}\) |
| Pre-/post-layout power difference | \(\Delta P=P_{post}-P_{pre}\) |

---

**Author**  
**SSS Vijay Kumar**  
Department of ECE, IIITDM Kurnool

**Under the Guidance of**  
**Dr. P. Rangababu**  
Associate Professor, Department of ECE, IIITDM Kurnool
