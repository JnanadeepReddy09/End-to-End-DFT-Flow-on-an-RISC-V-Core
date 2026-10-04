# End-to-End DFT Flow on a RISC-V Core and a Custom Digital Accelerator

A complete Design-for-Testability (DFT) flow, from RTL to verified test patterns, applied to an open-source RISC-V core (PicoRV32) and a custom 4×4 MAC-array accelerator. The project measures what testability costs (area, timing, power) and what it buys (fault coverage, pattern count, test time), and compares a control-dominated block (CPU) with a datapath-dominated block (accelerator).

> Capstone project for the *VLSI Testing and Design for Testability* lab.
> **Status:** work in progress. Results marked `TBD` are filled in as each stage completes.

---

## Goals

- Take a design from RTL through synthesis, scan insertion, ATPG, memory BIST, test access and compression.
- Verify with formal equivalence checking at every stage that DFT insertion did not change functional behaviour.
- Quantify DFT overhead against test benefit, per block and for the combined design.
- Document every stage so the whole flow is reproducible from scripts.

## Design under test

| Block | Description | Role in the study |
|---|---|---|
| **PicoRV32** | Open-source RV32I RISC-V core (ISC license) | Control-heavy logic with a large register file |
| **4×4 MAC array** | Custom accelerator: 8-bit inputs, memory-mapped register interface, control FSM | Datapath-heavy logic written to be testable from the start |
| **Memory** | Small SRAM wrapper | Target for memory BIST |

## Flow

```
RTL ──► Synthesis ──► Scan insertion ──► ATPG ──► Memory BIST ──► Test access (JTAG/IJTAG) ──► Compression
 │          │               │                                                                      │
 └──────────┴───── Formal equivalence check (RTL vs netlist, pre-DFT vs post-scan) ◄───────────────┘
```

| Stage | Tool | What is measured |
|---|---|---|
| Functional simulation | Questa | Testbenches for core and accelerator against a software reference |
| Synthesis | Cadence Genus | Baseline area, timing, power |
| Scan insertion | Tessent Scan | Chain count and length, DFT overhead |
| ATPG | Tessent FastScan | Stuck-at and transition-fault coverage, pattern count |
| Memory BIST | Tessent MemoryBIST | March algorithm coverage, area overhead |
| Test access | Tessent IJTAG | TAP and per-block access |
| Compression | Tessent TestKompress | Compression ratio, test time vs uncompressed |
| Equivalence | Cadence Conformal | Pass/fail for each DFT step |

**Technology:** SkyWater Sky130 `sky130_fd_sc_hd` standard cells. Slow and fast corners are used for setup and hold checks respectively.

## Results

Results are added as each stage is completed.

| Metric | PicoRV32 | MAC array | Combined |
|---|---|---|---|
| Baseline area (µm²) | TBD | TBD | TBD |
| Area overhead after scan | TBD | TBD | TBD |
| Stuck-at fault coverage | TBD | TBD | TBD |
| Transition fault coverage | TBD | TBD | TBD |
| Pattern count (uncompressed) | TBD | TBD | TBD |
| Pattern count (compressed) | TBD | TBD | TBD |
| Compression ratio | TBD | TBD | TBD |
| Untestable faults and cause | TBD | TBD | TBD |
| Conformal equivalence | TBD | TBD | TBD |

## Repository structure

```
.
├── rtl/            # PicoRV32 wrapper, MAC array, SRAM wrapper
├── tb/             # Questa testbenches and software reference models
├── synth/          # Genus scripts and constraints
├── dft/            # Tessent scan, ATPG, MBIST, IJTAG, compression scripts
├── lec/            # Conformal equivalence-check scripts
├── reports/        # Area, timing, power and coverage reports (summaries)
├── docs/           # Final report, block diagrams, notes
└── README.md
```

## Reproducing the results

Scripts are numbered in the order they run.

```bash
# 1. Functional simulation
make sim

# 2. Baseline synthesis
make synth

# 3. Scan insertion, then ATPG
make scan
make atpg

# 4. Memory BIST, test access, compression
make mbist
make ijtag
make compress

# 5. Equivalence checks
make lec
```

Tool versions and library paths are listed in `docs/setup.md`.

## Requirements

- Cadence Genus and Conformal
- Siemens Tessent (Scan/ATPG, TestKompress, MemoryBIST, IJTAG)
- Siemens Questa
- SkyWater Sky130 PDK, `sky130_fd_sc_hd` library
- Python 3 for software reference models and report scripts

## Notes on licensing

PicoRV32 is released under the ISC license and the Sky130 PDK under Apache 2.0. Proprietary tool outputs, vendor libraries and licensed scripts are **not** included in this repository. Only original RTL, scripts and summarised reports are published.

## Author

`Padi Jnanadeep Reddy` · `Indian Institute of Information Technology, Design & Manufacturing, Kancheepuram` · `EC23I2021`

## References

- Siemens EDA, *Hierarchical DFT in a RISC-V Processor* (white paper)
- Abdelatty, Gaber, Shalan, *Fault: Open-Source EDA's Missing DFT Toolchain*, IEEE Design & Test, 2021
- Rajski et al., *Embedded Deterministic Test*, IEEE TCAD, 2004
