[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# STT-MRAM Compact Model

This repository provides a comprehensive description and compact modeling, via Verilog-A and OpenVAF chain, of a STT-MRAM Cell. The base version objective consist in creating a model of a single cell, using standard literature parameters and materials, with the derivation of foundamental equation done separately. Future version will elaborate a more complex model (see [Additional Improvements](#additional-improvements) section for more indications).

---

## Equations used

In this sections are formalized the equations used in the verilog-A model for the STT-MRAM cell's simulation. For more information about the assumptions and procedures used to derive the formualae, go to the [tecnical documentation](docs/images/STT_MRAM_thecnical_document.pdf).

### LLG

**Implicit:**

$$\frac{d\mathbf{M}}{dt} = -\gamma \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) + \frac{\alpha}{M_s} \left( \mathbf{M} \times \frac{d\mathbf{M}}{dt} \right)$$

**Explicit:**

$$\frac{d\mathbf{M}}{dt} = -\frac{\gamma}{1 + \alpha^2} \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) - \frac{\gamma \alpha}{(1 + \alpha^2) M_s} \left[ \mathbf{M} \times \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) \right]$$

**Gilbert parameter:**

$$\alpha$$

### Giant Tunnel Magnetoresitance

$$\text{GMR} = \frac{R_{AP} - R_P}{R_P}$$

### Thermal activation effect simulation

$$\frac{d\mathbf{m}}{dt} = -\frac{\gamma}{1 + \alpha^2} \left[ \mathbf{m} \times \left( \mathbf{H}_{\text{eff}} + \mathbf{H}_{\text{th}} \right) \right] - \frac{\gamma \alpha}{1 + \alpha^2} \left\{ \mathbf{m} \times \left[ \mathbf{m} \times \left( \mathbf{H}_{\text{eff}} + \mathbf{H}_{\text{th}} \right) \right] \right\}$$

**Discrete:**

$$\mathbf{H}_{\text{th}} = \boldsymbol{\eta} \sqrt{\frac{2 \alpha k_B T}{\gamma \mu_0 M_s V \Delta t}}$$

---

## Quick Start

### Prerequisites

- OpenVAF
- ngspice with OSDI support
- A suitable C/C++ build toolchain

### Build Command

```sh
openvaf stt_mram.va
```

### Run a simulation

```sh
ngspice -b example.cir
```

---

## Modeling constants used

The constant used in the verilog-A model follow standard literature values, described below:

### 1. Magnetic and Geometrical Parameters (LLG Dynamics)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Nanopillar diameter | `d` | `40e-9` | $\text{m}$ | Valid in the macrospin regime (`d < 50 nm`) |
| Free layer thickness | `t_fl` | `1.3e-9` | $\text{m}$ | Thickness of the CoFeB free layer |
| Saturation magnetization | `Ms` | `1.257e6` | $\text{A/m}$ | Equivalent to $\mu_0 M_s = 1.58\text{ T}$ |
| Interfacial anisotropy | `Ki` | `1.3e-3` | $\text{J/m}^2$ | CoFeB/MgO interfacial energy density |
| Gilbert damping | `alpha` | `0.027` | — | Effective damping constant |
| Gyromagnetic ratio | `GAMMA` | `1.76e11` | $\text{rad}/(\text{s}\cdot\text{T})$ | Electron gyromagnetic constant |

### 2. Electrical and Transport Parameters (Jullière & STT)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Zero-bias TMR | `TMR0` | `1.2` | — | Tunnel magnetoresistance ratio (120%) |
| Resistance-Area product | `RA_P` | `5.0e-12` | $\Omega\cdot\text{m}^2$ | Equivalent to $5\ \Omega\cdot\mu\text{m}^2$ (parallel state) |
| TMR half-bias voltage | `Vh` | `0.5` | $\text{V}$ | Bias voltage at which $\text{TMR}(V_h) = \text{TMR}_0 / 2$ |
| Oxide barrier thickness | `t_ox` | `0.85e-9` | $\text{m}$ | Thickness of the MgO tunnel barrier |
| Spin polarization | `P` | `0.58` | — | Tunneling current spin polarization factor |

### 3. Universal Constants (`localparam`)

| Constant | Verilog-A Symbol | Value (SI) | Units |
| :--- | :--- | :--- | :--- |
| Vacuum permeability ($\mu_0$) | `MU0` | `1.256637061e-6` | $\text{H/m}$ |
| Reduced Planck constant ($\hbar$) | `HBAR` | `1.054571817e-34` | $\text{J}\cdot\text{s}$ |
| Elementary charge ($e$) | `Q_E` | `1.602176634e-19` | $\text{C}$ |
| Boltzmann constant ($k_B$) | `KB` | `1.380649e-23` | $\text{J/K}$ |

---

## Additional Improvements

- Improve thermal-effect modeling
- Extend the model to support cell-to-cell coupling
- Add complete simulation examples
- Validate parameters using experimental data

---

## References & Acknowledgments

### Theoretical Foundation
- **Prof. Aurélien Manchon — *Introduction to Spintronics* (Lecture Series):** Primary reference for the physical derivation of magnetostatics, interfacial perpendicular magnetic anisotropy (PMA), the Landau-Lifshitz-Gilbert (LLG) equation, spin-dependent tunneling, and Spin-Transfer Torque (STT) / Spin-Orbit Torque (SOT) dynamics.

### Physical & Compact Modeling Equations
- **Magnetization Dynamics:** Landau-Lifshitz-Gilbert-Slonczewski (LLGS) macrospin formulation in Cartesian coordinates.
- **Tunnel Magnetoresistance (TMR):** Jullière model extended with bias-voltage dependence for parallel ($R_P$) and antiparallel ($R_{AP}$) states.
- **Thermal Effects:** Néel-Brown thermal activation model ($\Delta = E_b / k_B T$) and Langevin stochastic thermal field.

### Toolchain & Reference Frameworks
- **OpenVAF:** Verilog-A compiler generating OSDI-compliant device binaries (`.osdi`).
- **ngspice:** Open-source circuit simulator used for DC, transient, and bit-cell simulations via the OSDI interface.
- **ARM MRAM Simulation Framework:** Referenced for simulation architecture and benchmarking structure (`https://github.com/ARM-software/mram_simulation_framework`).

### Material & Device Parameters
- Physical, technological and geometrical parameters used in the simulation are calibrated following standard literature values, reported in:

> **Y. Zhang, W. Zhao, Y. Lakys, J.-O. Klein, J.-V. Kim, D. Ravelosona, and C. Chappert**,  
> *"Compact Modeling of Perpendicular-Anisotropy CoFeB/MgO Magnetic Tunnel Junctions"*,  
> **IEEE Transactions on Electron Devices**, vol. 59, no. 3, pp. 819–826, Mar. 2012.  
> DOI: `10.1109/TED.2011.2178416`

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
