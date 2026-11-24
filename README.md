[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# STT-MRAM Compact Model

This repository provides a comprehensive description and compact modeling, via Verilog-A and OpenVAF chain, of a STT-MRAM Cell. The base version objective consist in creating a model of a single cell, using standard literature parameters and materials, with the derivation of foundamental equation done separately. Future version will elaborate a more complex model (see [Additional Improvements](#additional-improvements) section for more indications).

---

## Equations used

This section explains the equations implemented in the model: `mtj_pma`. 

The model uses a cilindrical symmetry, which gives rise to a magnetization constant `m` only on the z axis. The free-layer magnetization is assumed to remain uniform (its state is described by the polar angle `theta`). 

The equations below describe the implemented model; in particular, the current version does not include stochastic thermal-field noise or full three-dimensional magnetization dynamics.

### Geometry and magnetization state

For a circular nanopillar of diameter `d`, the MTJ cross-sectional area is

$$A = \frac{\pi d^2}{4}.$$

The initial magnetization direction is chosen by `init_state`, with a small angular offset `theta_min` from either pole:
$$\theta_0 = \begin{cases}
\theta_{\min}, & \text{if init state} = 0,\\
\pi-\theta_{\min}, & \text{if init state} = 1.
\end{cases}$$

The Verilog-A state variable `V(n_theta)` represents an angular deviation in radians, so

$$\theta = \theta_0 + V(n_{\theta}), \qquad m_z = \cos(\theta).$$


### Bias-dependent TMR and MTJ conductance

The voltage across the MTJ is $V_{MTJ}=V(p,n)$. The bias-dependent tunnel magnetoresistance ratio used by the model is

$$\mathrm{TMR}_{eff}(V_{MTJ}) = \frac{\mathrm{TMR}_0}{1+(V_{MTJ}/V_h)^2}.$$

Thus the TMR equals `TMR0` at zero bias and falls to half that value when $|V_{MTJ}|=V_h$. With the parallel-state resistance-area product `RA_P`, the parallel conductance is

$$G_P = \frac{A}{RA_P},$$

and the antiparallel conductance is

$$G_{AP} = \frac{G_P}{1+\mathrm{TMR}_{eff}}.$$

The conductance is interpolated continuously according to the free-layer orientation:

$$G_{eff} = \frac{G_P+G_{AP}}{2} + \frac{G_P-G_{AP}}{2}m_z.$$

Consequently, $m_z=+1$ gives $G_{eff}=G_P$ (parallel state), while $m_z=-1$ gives $G_{eff}=G_{AP}$ (antiparallel state). The electrical current and current density are

$$I_{MTJ}=G_{eff}V_{MTJ}, \qquad J=\frac{I_{MTJ}}{A}.$$

The sign of $J$ follows the circuit's `p`-to-`n` current convention and determines the sign of the torque in the implemented equation.

### Effective anisotropy and spin-transfer torque fields

The effective perpendicular anisotropy field is calculated as

$$H_{k,eff}=\frac{2K_i}{\mu_0 M_s t_{fl}}-M_s.$$

The first term is the field associated with interfacial perpendicular anisotropy; subtracting $M_s$ represents the thin-film demagnetizing contribution in this simplified expression. A positive result corresponds to an out-of-plane easy axis. `H_keff` is expressed in A/m.

The effective field associated with spin-transfer torque is

$$H_{STT}=\frac{\hbar P J}{2e\mu_0 M_s t_{fl}},$$

where $P$ is the spin-polarization factor, $e$ is the elementary charge, and $\hbar$ is the reduced Planck constant. This expression converts the current density through the junction into the torque-driving field used by the one-dimensional dynamics.

### One-dimensional LLGS polar-angle dynamics

The implemented polar-angle rate is

$$\frac{d\theta}{dt}=\frac{\Gamma\mu_0}{1+\alpha^2}\sin(\theta)\left(H_{STT}-\alpha H_{k,eff}m_z\right).$$

`GAMMA` is the gyromagnetic ratio and `alpha` is the Gilbert damping parameter. The term proportional to $H_{STT}$ describes current-driven switching; the term proportional to $\alpha H_{k,eff}m_z$ describes damping associated with the perpendicular anisotropy. The factor $\sin(\theta)$ is part of the polar-coordinate reduction. This is a reduced, one-dimensional macrospin equation, not the full vector LLG/LLGS equation.

In Verilog-A, the internal electrical node `n_theta` is used as an analog state variable. Its branch equation

$$\frac{dV(n\_theta)}{dt}=\frac{d\theta}{dt}$$

is expressed through `ddt(V(n_theta)) - dtheta_dt`. A small leakage term (`1e-12 * V(n_theta)`) provides a DC path for numerical convergence. Additional boundary currents clamp the state variable when it would drive `theta` outside the allowed interval. The monitor output is `V(mz_out) = m_z`.

### Thermal activation

Although thermal activation and a stochastic Langevin thermal field are relevant to STT-MRAM modeling, no thermal-field term is present in this `mtj_pma` implementation. The model is therefore deterministic; thermal switching probabilities are not represented by the equations above.

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

The constants used in the Verilog-A model follow standard literature values, described below:

### 1. Magnetic and Geometrical Parameters (LLG Dynamics)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Nanopillar diameter | d | 40e-9 | $\text{m}$ | Valid in the macrospin regime ($d < 50\text{ nm}$) |
| Free layer thickness | t_fl | 1.3e-9 | $\text{m}$ | Thickness of the CoFeB free layer |
| Saturation magnetization | Ms | 1.257e6 | $\text{A/m}$ | Equivalent to $\mu_0 M_s = 1.58\text{ T}$ |
| Interfacial anisotropy | Ki | 1.3e-3 | $\text{J/m}^2$ | CoFeB/MgO interfacial energy density |
| Gilbert damping | alpha | 0.027 | — | Effective damping constant |
| Gyromagnetic ratio | GAMMA | 1.76e11 | $\text{rad}/(\text{s}\cdot\text{T})$ | Electron gyromagnetic constant |

### 2. Electrical and Transport Parameters (Jullière & STT)

| Parameter | Verilog-A Symbol | Nominal Value | Units (SI) | Description |
| :--- | :--- | :--- | :--- | :--- |
| Zero-bias TMR | TMR0 | 1.2 | — | Tunnel magnetoresistance ratio (120%) |
| Resistance-Area product | RA_P | 5.0e-12 | $\Omega\cdot\text{m}^2$ | Equivalent to $5\ \Omega\cdot\mu\text{m}^2$ (parallel state) |
| TMR half-bias voltage | Vh | 0.5 | $\text{V}$ | Bias voltage at which $\text{TMR}(V_h) = \text{TMR}_0 / 2$ |
| Oxide barrier thickness | t_ox | 0.85e-9 | $\text{m}$ | Thickness of the MgO tunnel barrier |
| Spin polarization | P | 0.58 | — | Tunneling current spin polarization factor |

### 3. Universal Constants (localparam)

| Constant | Verilog-A Symbol | Value (SI) | Units |
| :--- | :--- | :--- | :--- |
| Vacuum permeability ($\mu_0$) | MU0 | 1.256637061e-6 | $\text{H/m}$ |
| Reduced Planck constant ($\hbar$) | HBAR | 1.054571817e-34 | $\text{J}\cdot\text{s}$ |
| Elementary charge ($e$) | Q_E | 1.602176634e-19 | $\text{C}$ |
| Boltzmann constant ($k_B$) | KB | 1.380649e-23 | $\text{J/K}$ |

---
**Status:** Active Early Development

Physics-based compact model of an STT-MRAM cell targeting open-source SPICE simulators (`ngspice` via `OpenVAF`).

## Current Implementation
- [x] Static MTJ resistance model (`src/mtj_static.va`) with bias-dependent TMR ($V_h$) and Slonczewski conductance formulation.

## Roadmap
- [ ] OpenVAF compilation & `ngspice` DC sweep testbench (P vs AP I–V curves)
- [ ] LLGS magnetization dynamics (macrospin approximation)
- [ ] Thermal stability & switching current verification
- [ ] 
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
