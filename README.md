
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


# STT-MRAM Compact Model
This repository provides a comprehensive description and compact modeling, via Verilog-A and OpenVAF chain, of a STT-MRAM Cell. The base version objective consist in creating a model of a single cell, using standard literature parameters and materials, with the derivation of foundamental equation done separately. Future version will elaborate a more complex model( See [additional improvements] (#Additional Improvements) section for more indications).



## Derivation of equations used
### LLG 

### Giant Tunnel Magnetoresitance

### Therma activation effect simulation

## Quick Start

### Prerequisites

- OpenVAF
- ngspice with OSDI support
- A suitable C/C++ build toolchain

### Build

```sh
openvaf stt_mram.va
```

### Run a simulation

```sh
ngspice -b example.cir
```
## Modeling constants used
the constant used in the verilog-A model follow standard literature values, described 

### 1. Magnetic and Geometrical Parameters (LLG Dynamics)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Nanopillar diameter | d | 40e-9 | m | Valid in the macrospin regime (`d < 50 nm`) |
| Free layer thickness | t_fl | 1.3e-9 | m | Thickness of the CoFeB free layer |
| Saturation magnetization | Ms | 1.257e6 | A/m | Equivalent to μ<sub>0</sub>M<sub>s</sub> = 1.58 T |
| Interfacial anisotropy | Ki | 1.3e-3 | J/m<sup>2</sup> | CoFeB/MgO interfacial energy density |
| Gilbert damping | alpha | 0.027 | — | Effective damping constant |
| Gyromagnetic ratio | GAMMA | 1.76e11 | rad/(s·T) | Electron gyromagnetic constant |

### 2. Electrical and Transport Parameters (Jullière & STT)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Zero-bias TMR | TMR0 | 1.2 | — | Tunnel magnetoresistance ratio (120%) |
| Resistance-Area product | RA_P | 5.0e-12 | Ω·m<sup>2</sup> | Equivalent to 5 Ω·μm<sup>2</sup> (parallel state) |
| TMR half-bias voltage | Vh | 0.5 | V | Bias voltage at which TMR(V<sub>h</sub>) = TMR<sub>0</sub> / 2 |
| Oxide barrier thickness | t_ox | 0.85e-9 | m | Thickness of the MgO tunnel barrier |
| Spin polarization | P | 0.58 | — | Tunneling current spin polarization factor |

### 3. Universal Constants (`localparam`)

| Constant | Verilog-A Symbol | Value (SI) | Units |
| :--- | :--- | :--- | :--- |
| Vacuum permeability (μ<sub>0</sub>) | MU0 | 1.256637061e-6 | H/m |
| Reduced Planck constant (ħ) | HBAR | 1.054571817e-34 | J·s |
| Elementary charge (e) | Q_E | 1.602176634e-19 | C |
| Boltzmann constant (k<sub>B</sub>) | KB | 1.380649e-23 | J/K |

---
## Additional Improvements
- Improve thermal-effect modeling
- Extend the model to support cell-to-cell coupling
- Add complete simulation examples
- Validate parameters using experimental data



## References & Acknowledgments

### Theoretical Foundation
* **Prof. Aurélien Manchon — *Introduction to Spintronics* (Lecture Series):** Primary reference for the physical derivation of magnetostatics, interfacial perpendicular magnetic anisotropy (PMA), the Landau-Lifshitz-Gilbert (LLG) equation, spin-dependent tunneling, and Spin-Transfer Torque (STT) / Spin-Orbit Torque (SOT) dynamics.

### Physical & Compact Modeling Equations
* **Magnetization Dynamics:** Landau-Lifshitz-Gilbert-Slonczewski (LLGS) macrospin formulation in Cartesian coordinates.
* **Tunnel Magnetoresistance (TMR):** Jullière model extended with bias-voltage dependence for parallel ($R_P$) and antiparallel ($R_{AP}$) states.
* **Thermal Effects:** Néel-Brown thermal activation model ($\Delta = E_b / k_B T$) and Langevin stochastic thermal field.

### Toolchain & Reference Frameworks
* **OpenVAF:** Verilog-A compiler generating OSDI-compliant device binaries (`.osdi`).
* **ngspice:** Open-source circuit simulator used for DC, transient, and bit-cell simulations via the OSDI interface.
* **ARM MRAM Simulation Framework:** Referenced for simulation architecture and benchmarking structure (`https://github.com/ARM-software/mram_simulation_framework`).

### Material & Device Parameters
* Physical, technological and geometrical parameters used in the simulation are calibrated following standard literature values, reported in :
> **Y. Zhang, W. Zhao, Y. Lakys, J.-O. Klein, J.-V. Kim, D. Ravelosona, and C. Chappert**,  
> *"Compact Modeling of Perpendicular-Anisotropy CoFeB/MgO Magnetic Tunnel Junctions"*,  
> **IEEE Transactions on Electron Devices**, vol. 59, no. 3, pp. 819–826, Mar. 2012.  
> DOI: `10.1109/TED.2011.2178416`


## License
This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
