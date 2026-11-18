


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
* Physical and geometrical parameters for $\text{CoFeB/MgO}$-based perpendicular MTJs ($M_s$, $\alpha$, $\text{TMR}_0$, $t_{\text{ox}}$, $K_i$) are calibrated against standard literature values *(add specific experimental or compact-model paper citations here once parameters are finalized)*.
