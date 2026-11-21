# 8-bit Binary-to-Gray Converter — Calculations

**Project:** 8-bit Binary-to-Gray Code Converter  
**Technology:** 45 nm CMOS (Cadence GPDK045)  
**Tool:** Cadence Virtuoso  
**Device dimensions:** W = 120 nm, L = 45 nm, multiplicity = 1

> **Important:** Values marked **[Estimate]** are hand-calculated using assumptions; they are not Virtuoso simulation measurements. The calculations below include only quantities for which a value or result is available. No blank measurement fields or unmeasured parameters are listed.

---

## 1. Transistor count

The design contains seven 12-transistor XOR cells and one 4-transistor buffer cell.

```text
Total transistors = (7 × 12) + 4
                  = 88 transistors

PMOS count = 7 × 4 + 2 = 30? 
```

For the stated 12T XOR implementation (4 PMOS + 4 NMOS plus two 2-transistor inverters), each XOR contains 6 PMOS and 6 NMOS. Therefore:

- PMOS count = (7 × 6) + 2 = **44**
- NMOS count = (7 × 6) + 2 = **44**
- Total = 44 + 44 = **88 transistors**

## 2. Transistor sizing and total device area

**W/L ratio of each transistor**

```text
W/L = 120 nm / 45 nm
    = 2.67
```

**Total transistor width**

```text
Total width = 88 × 0.12 µm
            = 10.56 µm
```

**Total transistor area** (sum of individual W × L areas)

```text
Area = 88 × (0.12 µm × 0.045 µm)
     = 0.4752 µm²
     ≈ 0.475 µm²
```

## 3. Input stimulus calculations

One input state is applied every 10 ns.

```text
Vector frequency = 1 / 10 ns
                 = 100 MHz
```

An 8-bit input has 256 possible combinations.

```text
Number of states = 2⁸ = 256

Time for all states = 256 × 10 ns
                    = 2560 ns
                    = 2.56 µs
```

Input signal frequencies calculated from their periods:

| Input | Period | Frequency |
|---|---:|---:|
| B1 | 20 ns | 50 MHz |
| B2 | 40 ns | 25 MHz |
| B3 | 80 ns | 12.5 MHz |
| B4 | 160 ns | 6.25 MHz |
| B5 | 320 ns | 3.125 MHz |
| B6 | 640 ns | 1.5625 MHz |
| B7 | 1.28 µs | 781.25 kHz |
| B8 | 2.56 µs | 390.625 kHz |

## 4. Switching activity (0 to 2 µs)

From the supplied transient stimulus and waveform analysis:

| Signal | B1 | B2 | B3 | B4 | B5 | B6 | B7 | B8 | Total |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Input transitions | 199 | 99 | 49 | 24 | 12 | 6 | 3 | 1 | **393** |

| Signal | G1 | G2 | G3 | G4 | G5 | G6 | G7 | G8 | Total |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Output transitions | 100 | 50 | 25 | 12 | 6 | 3 | 2 | 1 | **199** |

**Transition-count comparison**

```text
Input transitions  = 393
Gray output transitions = 199
```

## 5. Layout area calculations

The approximate layout dimensions from the pin-coordinate estimate are 7.25 µm × 7.25 µm.

```text
Layout area = 7.25 × 7.25
            = 52.5625 µm²
            ≈ 52.6 µm²

Aspect ratio = Width / Height
             = 7.25 / 7.25
             = 1.0

Transistor-area percentage =
    (0.4752 / 52.5625) × 100
  ≈ 0.90%
```

These are approximate dimensions and area estimates, not a foundry area report.

## 6. Power calculations [Estimate]

**Assumptions used**

| Quantity | Assumed value |
|---|---:|
| Supply voltage (VDD) | 1.2 V |
| Effective capacitance per output transition | 4 fF |
| Effective capacitance per input transition | 0.58 fF |
| Leakage current | 0.1 µA |
| Observation interval | 2 µs |

These assumptions are used only for the following hand calculations.

### 6.1 Dynamic energy of Gray outputs

```text
E = Number of transitions × ½ × C × VDD²

EGray = 199 × ½ × 4 fF × (1.2 V)²
      = 573.12 fJ
      ≈ 573 fJ
```

### 6.2 Dynamic energy of binary inputs

```text
Einput = 393 × ½ × 0.58 fF × (1.2 V)²
       ≈ 163 fJ
```

### 6.3 Total dynamic energy and dynamic power

```text
Edynamic = EGray + Einput
         = 573 + 163
         = 736 fJ

Pdynamic = Edynamic / observation time
         = 736 fJ / 2 µs
         ≈ 0.37 µW
```

### 6.4 Static power

```text
Pstatic = VDD × Ileak
        = 1.2 × 0.1 µA
        = 0.12 µW
```

### 6.5 Average power

```text
Pavg = Pdynamic + Pstatic
     = 0.37 + 0.12
     = 0.49 µW
```

### 6.6 Energy per input vector

```text
Evector = Pavg × Tvector
        = 0.49 µW × 10 ns
        = 4.9 fJ
```

## 7. Delay, maximum-frequency estimate, PDP and EDP [Estimate]

The hand-estimated propagation-delay range is 50–100 ps. The midpoint used for the example is 70 ps. These are estimates, not measured ADE results.

```text
Maximum frequency estimate = 1 / propagation delay

For 50 ps:  1 / 50 ps  = 20 GHz
For 70 ps:  1 / 70 ps  ≈ 14.3 GHz
For 100 ps: 1 / 100 ps = 10 GHz
```

Using the estimated average power of 0.49 µW:

```text
PDP = Pavg × tPD
EDP = Pavg × (tPD)²
```

| Assumed tPD | 1/tPD | PDP | EDP |
|---:|---:|---:|---:|
| 50 ps | 20 GHz | 0.024 fJ | 1.2 × 10⁻²⁷ J·s |
| 70 ps | 14.3 GHz | 0.034 fJ | 2.4 × 10⁻²⁷ J·s |
| 100 ps | 10 GHz | 0.049 fJ | 4.9 × 10⁻²⁷ J·s |

---

**Author:** SSS Vijay Kumar  
**Programme:** M.Tech (VLSI & Microelectronics)  
**Roll Number:** 226EC1009
