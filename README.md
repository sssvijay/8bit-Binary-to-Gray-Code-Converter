# 8-bit Binary-to-Gray Code Converter

An academic VLSI design project documenting an 8-bit Binary-to-Gray Code Converter using Cadence Virtuoso.

## Conversion logic

For binary input B7 B6 B5 B4 B3 B2 B1 B0, the Gray-code outputs are:

- G7 = B7
- G6 = B7 XOR B6
- G5 = B6 XOR B5
- G4 = B5 XOR B4
- G3 = B4 XOR B3
- G2 = B3 XOR B2
- G1 = B2 XOR B1
- G0 = B1 XOR B0

The MSB is passed directly; each remaining output is the XOR of two adjacent binary bits.

## Tools and technology

- Cadence Virtuoso
- CMOS digital circuit design
- Transient simulation
- Technology node: 45 nm (as specified for this project)

## Project files

The complete Cadence project archive and simulation graph PDFs are intended to be stored in this repository. Keep the original archive intact so the library/cell data can be restored in the Cadence environment.

Suggested repository layout:

- project/Bin_To_G.zip — original Cadence project archive
- graphs/ — transient simulation graph PDFs
- README.md — project overview and usage notes

## Opening the project

1. Download the project ZIP from this repository.
2. Extract it in a location accessible to your Cadence Virtuoso environment.
3. Open the extracted library from Library Manager (use the project's own library/cell names).
4. Reconnect the appropriate 45 nm technology library if your lab environment requires it.
5. Open the testbench and rerun transient simulation to reproduce the waveforms.

Exact setup steps may vary depending on the Cadence installation and the PDK available in your lab.

## Author

**SSS Vijay Kumar**  
M.Tech — VLSI & Microelectronics

## Note

This repository is currently private. Before making it public, check that the archive does not contain licensed PDK files, proprietary technology data, or confidential institute/company material.