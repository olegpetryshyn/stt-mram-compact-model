[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# STT-MRAM Compact Model

This repository provides a comprehensive description and compact modeling, via Verilog-A and OpenVAF chain, of a STT-MRAM Cell. The base version objective consist in creating a model of a single cell, using standard literature parameters and materials, with the derivation of foundamental equation done separately. Future version will elaborate a more complex model (see [Additional Improvements](#additional-improvements) section for more indications).

---

## Equations used

In this sections are formalized the equations used in the verilog-A model for the STT-MRAM cell's simulation. For more information about the assumptions and procedures used to derive the formualae, go to the [technical documentation](docs/images/STT_MRAM_thecnical_document.pdf).

### LLG

**Implicit:**

$$\frac{d\mathbf{M}}{dt} = -\gamma \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) + \frac{\alpha}{M_s} \left( \mathbf{M} \times \frac{d\mathbf{M}}{dt} \right)$$

**Explicit:**

$$\frac{d\mathbf{M}}{dt} = -\frac{\gamma}{1 + \alpha^2} \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) - \frac{\gamma \alpha}{(1 + \alpha^2) M_s} \left[ \mathbf{M} \times \left( \mathbf{M} \times \mathbf{H}_{\text{eff}} \right) \right]$$

**Gilbert parameter:**

### Giant Tunnel Magnetoresistance

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
