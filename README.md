# 8-bit Binary-to-Gray Code Converter

![Project](https://img.shields.io/badge/Project-Digital%20VLSI-blue)
![EDA](https://img.shields.io/badge/EDA-Cadence%20Virtuoso-blue)
![Logic](https://img.shields.io/badge/Logic-CMOS%20Digital-green)
![Technology](https://img.shields.io/badge/Technology-45%20nm-orange)
![Status](https://img.shields.io/badge/Status-Academic%20Project-lightgrey)

An 8-bit Binary-to-Gray Code Converter designed and simulated as an academic digital VLSI project using **Cadence Virtuoso**.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Design Objective](#design-objective)
3. [Binary-to-Gray Conversion Theory](#binary-to-gray-conversion-theory)
4. [Logic Equations](#logic-equations)
5. [Truth Table](#truth-table)
6. [Design and Simulation](#design-and-simulation)
7. [Simulation Results](#simulation-results)
8. [Repository Contents](#repository-contents)
9. [How to Open the Cadence Project](#how-to-open-the-cadence-project)
10. [Tools and Technology](#tools-and-technology)
11. [Author](#author)

---

## Project Overview

This project implements an 8-bit combinational logic circuit that converts an 8-bit binary input into its equivalent 8-bit Gray-code output. Gray code is a unit-distance code in which two consecutive code words differ by only one bit.

## Design Objective

- Accept an 8-bit binary input: `B[7:0]`.
- Generate the corresponding 8-bit Gray-code output: `G[7:0]`.
- Implement the conversion using digital CMOS logic.
- Create and simulate the design in Cadence Virtuoso.
- Retain the Cadence project archive and simulation graph PDFs for reference.

## Binary-to-Gray Conversion Theory

The most significant Gray-code bit is identical to the most significant binary bit. Each remaining Gray-code bit is obtained by XORing two adjacent binary bits.

## Logic Equations

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

Equivalently, `G = B XOR (B >> 1)`, where the most significant bit is retained.

## Truth Table

The complete converter has 256 possible input combinations. The following examples illustrate the conversion:

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

## Design and Simulation

The design work was carried out in Cadence Virtuoso. The repository includes the original project archive and PDF graph outputs supplied for this project. The exact internal cell hierarchy, testbench settings and simulator configuration should be taken from the archive and the corresponding Cadence environment.

## Simulation Results

The following PDFs are provided as simulation graph records:

- [Simulation graph PDF 1](./results/graph8bitbintograyprint.pdf)
- [Simulation graph PDF 2](./results/graph8bitbintograyprintprint.pdf)

These files are included as supplied. Numerical performance values and pass/fail claims are not stated here because they have not yet been independently extracted and verified.

## Repository Contents

```text
8bit-Binary-to-Gray-Code-Converter/
├── README.md
├── Bin_To_G.zip
└── results/
    ├── graph8bitbintograyprint.pdf
    └── graph8bitbintograyprintprint.pdf
```

## How to Open the Cadence Project

1. Download `Bin_To_G.zip` from this repository.
2. Extract the archive in a working directory accessible to Cadence Virtuoso.
3. Review the included library definition and project structure.
4. Open the library using Cadence Library Manager.
5. Confirm that the required technology library/PDK is available in your environment.
6. Open the relevant schematic and testbench, then run the simulation using the configuration included with the project.

Cadence version, PDK setup and library paths can vary by lab environment. The project archive is the source of truth for the exact cell names and setup.

## Tools and Technology

| Item | Details |
|---|---|
| EDA tool | Cadence Virtuoso |
| Design domain | Digital VLSI / CMOS logic |
| Function | 8-bit Binary-to-Gray conversion |
| Technology node | 45 nm (as recorded for this project) |
| Result material | Simulation graph PDFs |

## Author

**SSS Vijay Kumar**  
M.Tech — VLSI & Microelectronics

---

## Important Notes

- This is an academic design project; use the uploaded archive for exact implementation details.
- Check the archive for licensed PDK data, proprietary technology files, passwords, machine-specific paths or institute/company-confidential material before making the repository public.
- Do not redistribute foundry or EDA tool files unless their licence permits it.