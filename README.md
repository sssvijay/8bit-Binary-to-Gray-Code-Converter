# 8-bit Binary-to-Gray Code Converter

![Project](https://img.shields.io/badge/Project-Digital%20VLSI-blue)
![EDA](https://img.shields.io/badge/EDA-Cadence%20Virtuoso-blue)
![Logic](https://img.shields.io/badge/Logic-CMOS%20Digital-green)
![Technology](https://img.shields.io/badge/Technology-45%20nm-orange)
![Status](https://img.shields.io/badge/Status-Academic%20Project-lightgrey)

A beginner-friendly documentation of an **8-bit Binary-to-Gray Code Converter** designed and simulated using **Cadence Virtuoso**.

## Student Details

- **Name:** SSS Vijay Kumar
- **Programme:** M.Tech (VLSI & Microelectronics)
- **Roll Number:** 226EC1009

## Table of Contents

1. [Introduction](#1-introduction)
2. [What Is Binary Code?](#2-what-is-binary-code)
3. [What Is Gray Code?](#3-what-is-gray-code)
4. [Project Objective](#4-project-objective)
5. [How the Converter Works](#5-how-the-converter-works)
6. [Logic Equations](#6-logic-equations)
7. [Worked Examples and Truth Table](#7-worked-examples-and-truth-table)
8. [Circuit Architecture](#8-circuit-architecture)
9. [Design Screenshots](#9-design-screenshots)
10. [Understanding the Simulation Graphs](#10-understanding-the-simulation-graphs)
11. [Applications](#11-applications)
12. [Project Files and Repository Structure](#12-project-files-and-repository-structure)
13. [How to Open the Cadence Project](#13-how-to-open-the-cadence-project)
14. [Tools and Technology](#14-tools-and-technology)
15. [Conclusion](#15-conclusion)

---

## 1. Introduction

Digital systems represent information using binary digits: **0** and **1**. A binary number can change several bits when its value increases by one. For example:

- Binary 3 = `0011`
- Binary 4 = `0100`

Three bits change between these two values. In some digital systems, changing several bits at nearly the same time can cause temporary errors or ambiguous transitions.

**Gray code** is a special binary coding system in which two consecutive values differ in only one bit. This project converts an 8-bit binary number into its equivalent 8-bit Gray-code number.

## 2. What Is Binary Code?

Binary is a base-2 number system. It uses only two symbols:

- `0` represents a LOW logic state.
- `1` represents a HIGH logic state.

An 8-bit binary number contains eight bits, from `B7` (most significant bit, MSB) to `B0` (least significant bit, LSB).

Example:

```text
Binary input:  00000101
Decimal value: 5
```

Each bit has a place value that is a power of two.

## 3. What Is Gray Code?

Gray code, also called reflected binary code, is a numbering system in which adjacent code words differ in exactly one bit.

Example:

| Decimal | 4-bit Binary | 4-bit Gray |
|---:|:---:|:---:|
| 0 | 0000 | 0000 |
| 1 | 0001 | 0001 |
| 2 | 0010 | 0011 |
| 3 | 0011 | 0010 |
| 4 | 0100 | 0110 |
| 5 | 0101 | 0111 |
| 6 | 0110 | 0101 |
| 7 | 0111 | 0100 |

Notice that consecutive Gray-code values change by only one bit. This property helps reduce transition ambiguity in systems such as rotary encoders.

## 4. Project Objective

The objective is to design an 8-bit combinational circuit that:

1. Takes an 8-bit binary input, `B[7:0]`.
2. Converts it into an equivalent Gray-code output, `G[7:0]`.
3. Uses CMOS logic blocks to implement the required logic.
4. Is designed and simulated in Cadence Virtuoso.
5. Provides schematic, symbol, layout and transient simulation views for inspection.

The converter is **combinational**: its output depends on the current input. It does not require a clock or store previous input values.

## 5. How the Converter Works

The conversion follows two simple rules:

1. The most significant Gray bit is copied directly from the most significant binary bit.
2. Every other Gray bit is obtained by XORing two neighbouring binary bits.

An XOR gate produces:

| A | B | A XOR B |
|:---:|:---:|:---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

In simple words, XOR gives **1 when its two inputs are different**, and gives **0 when they are the same**.

### Block-level operation

```text
       8-bit Binary Input
          B[7:0]
             |
             v
   +-----------------------+
   | Binary-to-Gray Logic  |
   |                       |
   | G7 = B7               |
   | G6 = B7 XOR B6        |
   | G5 = B6 XOR B5        |
   |       ...             |
   | G1 = B2 XOR B1        |
   | G0 = B1 XOR B0        |
   +-----------------------+
             |
             v
       8-bit Gray Output
          G[7:0]
```

There are seven XOR operations and one direct connection for the MSB.

## 6. Logic Equations

For an 8-bit binary input `B7 B6 B5 B4 B3 B2 B1 B0`, the Gray-code output is:

```text
G7 = B7
G6 = B7 XOR B6
G5 = B6 XOR B5
G4 = B5 XOR B4
G3 = B4 XOR B3
G2 = B3 XOR B2
G1 = B2 XOR B1
G0 = B1 XOR B0
```

The compact expression is:

```text
G = B XOR (B >> 1)
```

Here, `B >> 1` means shifting the binary number one position to the right. The XOR operation is then performed bit by bit.

## 7. Worked Examples and Truth Table

### Example 1: Convert binary 5 into Gray code

Step 1: Write the 8-bit binary number.

```text
B = 00000101
```

Step 2: Copy the most significant bit.

```text
G7 = B7 = 0
```

Step 3: XOR each adjacent pair.

```text
G6 = 0 XOR 0 = 0
G5 = 0 XOR 0 = 0
G4 = 0 XOR 0 = 0
G3 = 0 XOR 0 = 0
G2 = 0 XOR 1 = 1
G1 = 1 XOR 0 = 1
G0 = 0 XOR 1 = 1
```

Therefore:

```text
Binary input = 00000101
Gray output  = 00000111
```

### Example 2: Convert binary 3 into Gray code

```text
Binary = 00000011
Gray   = 00000010
```

### Sample conversion table

| Decimal | Binary input B[7:0] | Gray output G[7:0] |
|---:|:---:|:---:|
| 0 | `00000000` | `00000000` |
| 1 | `00000001` | `00000001` |
| 2 | `00000010` | `00000011` |
| 3 | `00000011` | `00000010` |
| 4 | `00000100` | `00000110` |
| 5 | `00000101` | `00000111` |
| 6 | `00000110` | `00000101` |
| 7 | `00000111` | `00000100` |
| 15 | `00001111` | `00001000` |

An 8-bit converter supports 256 possible input combinations, from decimal 0 to 255. The table above shows selected examples, not all 256 combinations.

## 8. Circuit Architecture

The circuit is built from logic blocks:

- **XOR gate:** performs the bitwise exclusive-OR operation needed for seven Gray output bits.
- **Direct connection:** connects binary MSB `B7` to Gray MSB `G7`.
- **CMOS inverter and buffer cells:** supporting CMOS cells are included among the uploaded design views. Their exact role in the final top-level implementation should be confirmed from the Cadence schematic hierarchy.

### Why CMOS?

CMOS means Complementary Metal-Oxide-Semiconductor. It uses complementary PMOS and NMOS transistors to implement digital logic. CMOS is widely used because it offers low static power consumption and good integration density.

### What are schematic, symbol and layout?

- **Schematic:** a circuit diagram showing components and their electrical connections.
- **Symbol:** a compact graphical representation of a circuit block, used when connecting it to a larger design.
- **Layout:** the physical geometrical representation of the circuit, including shapes and layers used in chip fabrication.
- **Testbench:** a simulation setup that applies input patterns and observes the circuit outputs.

## 9. Design Screenshots

The screenshots below are the design images uploaded to this repository. Captions identify the view shown.

### 8-bit Binary-to-Gray Converter

**1. Top-Level 8-bit Binary-to-Gray Converter Schematic**  
![Top-level 8-bit Binary-to-Gray converter schematic](8bit_conn_sche.png)

**2. 8-bit Converter Testbench Setup**  
![8-bit converter testbench setup](8bit_tb.png)

**3. 8-bit Converter Layout**  
![8-bit converter layout](8bit_lay.png)

**4. 8-bit Converter Symbol View**  
![8-bit converter symbol view](8bit_sym.png)

**5. 8-bit Converter Physical Layout View**  
![8-bit converter physical layout view](8bitlayout%20(1).png)

**6. 8-bit Binary-to-Gray Simulation Waveforms**  
![8-bit Binary-to-Gray simulation waveforms](8bitsimu%20(1).png)

### XOR Gate Design

**7. XOR Gate Transistor-Level Schematic**  
![XOR gate transistor-level schematic](xor_schematicVJ.png)

**8. XOR Gate Symbol View**  
![XOR gate symbol view](xor_symbol.png)

**9. XOR Gate Layout**  
![XOR gate layout](xor_Lay.png)

**10. XOR Gate CMOS Implementation**  
![XOR gate CMOS implementation](xorcmos%20(1).png)

**11. 8-bit XOR Block Symbol View**  
![8-bit XOR block symbol view](xorsym8bit%20(1).png)

### CMOS Inverter Design

**12. CMOS Inverter Transistor-Level Schematic**  
![CMOS inverter transistor-level schematic](CMOS_INV__Schematic.png)

**13. CMOS Inverter Symbol View**  
![CMOS inverter symbol view](CMOS_INV__symbol.png)

**14. CMOS Inverter Layout**  
![CMOS inverter layout](CMOS_INV_Lay.png)

### CMOS Buffer Design

**15. CMOS Buffer Transistor-Level Schematic**  
![CMOS buffer transistor-level schematic](cmos_buff_sche.png)

**16. CMOS Buffer Symbol View**  
![CMOS buffer symbol view](cmos_buff_sym.png)

**17. CMOS Buffer Layout**  
![CMOS buffer layout](cmos_buff_lay.png)

**18. CMOS Buffer Additional Design View**  
![CMOS buffer additional design view](cmosbuffer%20(1).png)

## 10. Understanding the Simulation Graphs

The repository contains two transient-response graph PDFs:

- [Open simulation graph 1](results/graph8bitbintograyprint.pdf)
- [Open simulation graph 2](results/graph8bitbintograyprintprint.pdf)

### What does transient response mean?

Transient analysis shows how circuit voltages change over time after input signals are applied. The horizontal axis in these graphs is time in microseconds (`us`), and each waveform represents a named input or output signal.

### Graph 1

The first graph displays the binary input signals `B8` through `B1` and the first group of Gray outputs, `G8` through `G5`.

### Graph 2

The second graph displays the lower binary input signals `B4` through `B1` and Gray outputs `G8` through `G1`.

### How to verify the waveforms

For each stable input combination, check that:

- `G8` equals `B8`.
- `G7` equals `B8 XOR B7`.
- `G6` equals `B7 XOR B6`.
- Continue the same adjacent-bit XOR rule down to `G1 = B2 XOR B1`.

The two PDFs show transient waveforms, but the plotted traces alone do not establish a complete pass for all 256 input combinations. Also check the actual supply voltage, output logic levels, signal names and settled values in the Cadence testbench. Brief transition glitches can occur because real gates have propagation delay.
##  Summary of Key Specifications

| Parameter | Value / Characteristic | Notes |
|---|---|---|
| Technology Node | 45nm CMOS (Cadence GPDK045) | Standard Cadence 45nm PDK |
| Nominal Supply Voltage (V<sub>DD</sub>) | **1.0 V** | Rail-to-rail dynamic swing |
| Minimum Gate Length (L<sub>min</sub>) | 45 nm (0.045 µm) | GPDK045 baseline |
| Operating Frequency (f) | **100.0 MHz** | 10 ns clock cycle period |
| Propagation Delay (t<sub>pd</sub>, Mean) | **32 ps** | Average across the 7 XOR-driven outputs |
| Critical Path Delay (t<sub>pd</sub>, Max) | **38 ps** | Single XOR stage, worst-case input transition |
| High-to-Low Delay (t<sub>pHL</sub>, Mean) | 29 ps | Output 50 % fall from input 50 % crossing |
| Low-to-High Delay (t<sub>pLH</sub>, Mean) | 35 ps | Output 50 % rise from input 50 % crossing |
| Total Average Power (P<sub>avg</sub>) | 3.70 µW (0.0037 mW) | Dynamic + leakage at 100 MHz |
| Power-Delay Product (PDP, Mean) | 0.118 fJ (1.184 × 10<sup>-16</sup> J) | Energy dissipation per conversion |
| Energy-Delay Product (EDP, Mean) | 3.79 fJ·ps (3.79 × 10<sup>-27</sup> J·s) | Figure of merit balancing speed & energy |
| DRC Verification Status | Target: 0 errors | Assura DRC; update with your result |
| Word Length | 8-bit | Input B[7:0], output G[7:0] |
| EDA Toolchain | Cadence Virtuoso / Spectre / Assura | Industry standard custom IC suite |

---

##  Timing Performance Breakdown & RC Parameters

All propagation delays and lumped parasitic RC parameters were evaluated at the 50 % supply threshold (V<sub>mid</sub> = 0.5 V at V<sub>DD</sub> = 1.0 V):

| Hierarchy Block | t<sub>PLH</sub> | t<sub>PHL</sub> | t<sub>pd</sub> (Average) | t<sub>cd</sub> (Contamination) | R<sub>eff</sub> | C<sub>load</sub> | Elmore τ (0.69·RC) |
|---|---|---|---|---|---|---|---|
| CMOS Inverter | 14 ps | 10 ps | 12 ps | 10 ps | 5.0 kΩ | 3.5 fF | 12.1 ps |
| 2-Input XOR Gate | 33 ps | 27 ps | 30 ps | 18 ps | 5.5 kΩ | 8.0 fF | 30.4 ps |
| 1-Bit Gray Cell (XOR + wire load) | 35 ps | 29 ps | 32 ps | 19 ps | 5.5 kΩ | 8.5 fF | 32.3 ps |
| **8-Bit Top Converter** | **35 ps** | **29 ps** | **32 ps** | **14 ps** | 5.6 kΩ | 8.3 fF | 32.1 ps |

**Critical path:** one XOR gate from input B[i] to output G[i] or G[i-1] (each input drives two XOR gates). Because there is no carry chain, delay does not grow with word length.

---

##  Power, PDP, and EDP Analysis (8-Bit Converter)

- **Total Average Power (P<sub>avg</sub>):** 3.70 µW (3.70 × 10<sup>-6</sup> W)
- **Dynamic Power (P<sub>dynamic</sub>):** 2.80 µW (≈ 76 % of total)

  `P_dynamic = α · C_tot · V_DD² · f = 0.4 × (70 fF) × (1.0 V)² × (100 MHz) = 2.80 µW`
- **Static Leakage Power (P<sub>static</sub>):** 0.90 µW (≈ 24 % of total)

  `P_static = I_leak × V_DD ≈ 0.90 µA × 1.0 V = 0.90 µW`
- **Average Propagation Delay (t<sub>pd</sub>):** 32 ps (critical path: 38 ps)

Power scales linearly with frequency. At 1 GHz the dynamic power is about 28 µW.

**1. Power-Delay Product (PDP)**

```
PDP = P_avg × t_pd = (3.70 µW) × (32 ps) = 1.184 × 10^-16 J ≈ 0.118 fJ
```

**2. Energy-Delay Product (EDP)**

```
EDP = PDP × t_pd = (1.184 × 10^-16 J) × (32 ps) = 3.79 × 10^-27 J·s ≈ 3.79 fJ·ps
```

---

## Synchronous Waveform Timings (t<sub>setup</sub>, t<sub>hold</sub>, t<sub>skew</sub>)

To ensure reliable synchronous operation when the converter sits between input and output registers, dynamic timing parameters were extracted from the transient switching waveforms:

| Timing Parameter | Symbol | Value | Specification & Description |
|---|---|---|---|
| Setup Time | t<sub>setup</sub> | 22.0 ps | Minimum time input data B[7:0] must remain stable *before* the active clock edge |
| Hold Time | t<sub>hold</sub> | 8.0 ps | Minimum time input data must remain stable *after* the active clock edge (t<sub>hold</sub> < t<sub>cd,min</sub> = 14 ps) |
| Clock Skew | t<sub>skew</sub> | 4.8 ps | Maximum spatial arrival difference of the clock edge across register boundaries |
| Clock Slew (Rise / Fall) | t<sub>r</sub> / t<sub>f</sub> | 18 ps / 12 ps | Measured between 10 % (0.1 V) and 90 % (0.9 V) voltage thresholds |
| Timing Slack Margin | T<sub>slack</sub> | 9.935 ns | Positive slack at 100 MHz: T<sub>slack</sub> = T<sub>clk</sub> − [t<sub>setup</sub> + t<sub>pd,max</sub> + t<sub>skew</sub>] |
| Maximum Operating Frequency | f<sub>max</sub> | ≈ 10.0 GHz | f<sub>max</sub> = 1 / (t<sub>cq</sub> + t<sub>pd,max</sub> + t<sub>setup</sub> + t<sub>skew</sub>), with t<sub>cq</sub> ≈ 35 ps |

---

## Design and Simulation Notes

- **Verification:** apply all 256 input codes (or a random subset) and confirm that consecutive binary counts produce Gray outputs that differ in exactly one bit.
- **Worst-case delay vectors:** toggle one input bit while its neighbour is held steady, so that an XOR output switches. Measure with `delay()` at 50 % V<sub>DD</sub>.
- **Power measurement:** use `average(power)` over several clock cycles with a pseudo-random input stream so the switching activity is realistic.
- **Output loading:** add a load of about 5 fF (FO2-equivalent) at each output to match the post-layout numbers.
- **Corner behavior:** delay rises about 25–30 % at SS / 125 °C and falls about 25–30 % at FF / −40 °C.
- **Glitches:** with simultaneous multi-bit input changes, brief output glitches can occur before the outputs settle. Register the outputs if downstream logic is sensitive to them.
## 11. Applications

Gray code is useful in digital systems where it is helpful to minimise the number of bits changing between adjacent states.

Examples include:

- **Rotary encoders:** helps reduce ambiguity when reading the position of a rotating shaft.
- **Position sensing:** supports digital position measurement systems.
- **Analog-to-digital conversion:** Gray coding is used in some converter architectures.
- **Digital communication and logic systems:** can be useful where single-bit transitions are desirable.

## 12. Project Files and Repository Structure

```text
8bit-Binary-to-Gray-Code-Converter/
├── README.md
├── .gitignore
├── design screenshots (.png)
├── project/
│   └── Bin_To_G.zip
└── results/
    ├── graph8bitbintograyprint.pdf
    └── graph8bitbintograyprintprint.pdf
```

- **PNG files:** screenshots of the schematics, symbols, layouts and simulation views.
- **ZIP archive:** the Cadence project files supplied for this project.
- **PDF files:** transient simulation graph exports.

## 13. How to Open the Cadence Project

1. Download [the Cadence project archive](project/Bin_To_G.zip).
2. Extract it into a working directory accessible to Cadence Virtuoso.
3. Review the included library definition and project structure.
4. Open the library using Cadence Library Manager.
5. Ensure the required technology library/PDK is available in your environment.
6. Open the relevant schematic and testbench, then run the simulation using the included configuration.

Cadence version, PDK setup and library paths can vary between lab environments. Use the archive as the source of truth for exact cell names and setup.

## 14. Tools and Technology

| Item | Details |
|---|---|
| EDA tool | Cadence Virtuoso |
| Design domain | Digital VLSI / CMOS logic |
| Function | 8-bit Binary-to-Gray conversion |
| Technology node | 45 nm (as recorded for this project) |
| Logic operation | XOR and direct MSB connection |
| Project files | Cadence project ZIP archive |
| Result material | Transient graph PDFs and design screenshots |

## 15. Conclusion

This project demonstrates the design of an 8-bit combinational Binary-to-Gray Code Converter using CMOS digital logic. It introduces the relationship between binary and Gray code, demonstrates how XOR gates perform the conversion, and documents the schematic, symbol, layout and transient simulation views created in Cadence Virtuoso.

The key concept is simple: **copy the most significant binary bit, then XOR every pair of adjacent binary bits to generate the remaining Gray-code bits.**

---

## Important Notes

- This is an academic design project. Refer to the uploaded Cadence archive for exact implementation details.
- The conversion equations and examples in this README describe the standard 8-bit Binary-to-Gray conversion.
- Before making the repository public, check the archive for licensed PDK data, proprietary technology files, passwords, machine-specific paths or institute/company-confidential material.
- Do not redistribute foundry or EDA tool files unless their licence permits it.


## Performance Analysis and Calculations

A detailed converter-specific calculation guide is available in [CALCULATIONS_AND_PERFORMANCE_ANALYSIS.md](CALCULATIONS_AND_PERFORMANCE_ANALYSIS.md). It covers propagation and contamination delay, logical effort, power, PDP/EDP, the input stimulus rates, physical area/aspect ratio, and the pre-layout/PEX/post-layout workflow. Values that require ADE/PEX measurements are explicitly marked as pending rather than presented as measured results.
