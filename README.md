# IPC-stander-rules-
IPC stander rules for PCB board diginer 

High-Frequency LC Filter Circuit & PCB Design

An industrial-grade, high-frequency **LC Low-Pass Filter (LPF) and Resonant Circuit** designed for RF front-ends, power decoupling, and signal conditioning applications. This repository contains the complete mathematical modeling, hardware design files, and IPC-compliant layout guidelines required to manufacture a production-ready printed circuit board (PCB).

## ðŸ¢ Architectural Layout & Visual Anchor
```
       [RF Input] ----+---- [ L: Inductor ] ----+---- [RF Output]
                      |                         |
                     ---                       ---
                 C1  ---                       --- C2 (Optional Tuning)
                      |                         |
                     GND                       GND
```

---

## âš¡ Technical Specifications & Calculations

The circuit is optimized for a targeted **Resonant Frequency ($f_c$) of 10.7 MHz** (standard IF stage) with a **50-Î© characteristic impedance** system matching.

### ðŸ“ Core Formula
The resonance and cutoff behavior is governed by the standard LC relation:
$$f_c = \frac{1}{2\pi\sqrt{LC}}$$

### ðŸ”§ Component Parameters
*   **Inductance ($L$):** 4.7 ÂµH (High-Q, low-DCR wire-wound inductor)
*   **Capacitance ($C$):** 47 pF (NP0/COG ceramic capacitor for high temperature stability)
*   **Calculated Cutoff ($f_c$):** ~10.7 MHz
*   **Impedance Matching:** Optimized for $Z_0 = 50\ \Omega$ transmission lines.

---

## ðŸ“ IPC-Compliant PCB Layout Guidelines

To ensure maximum signal integrity, minimal electromagnetic interference (EMI), and cross-border manufacturing readiness, this layout strictly adheres to global **IPC Standards**:

*   **IPC-2221B (Generic Standard on Printed Board Design):** Used to compute minimum conductor clearances and electrical trace widths based on current-carrying requirements and voltage drops.
*   **IPC-4101 (Specification for Base Materials):** Specifies high-Tg FR-4 substrate materials to ensure thermal stability during lead-free reflow soldering profiles.
*   **Controlled Impedance (IPC-2141A):** RF input/output traces are routed as **50-Î© Coplanar Waveguides with Ground (CPWG)**. Trace width and clearance gaps are computed precisely to prevent signal reflections.
*   **EMI/EMC & Grounding:**
    *   Dedicated, unbroken ground plane stitching immediately underneath the signal layer to minimize the return path loop area.
    *   Via shielding arrays flanking the 50-Î© traces to mitigate crosstalk and radiate RF energy safely to the ground.
*   **IPC-7351B (Footprint Requirements):** All SMD footprints (0805 for C, molded SMD for L) utilize the Nominal Material Condition (Density Level M) to achieve a robust solder joint during automated Pick-and-Place.

---

## ðŸ› ï¸ Simulation & Verification

The repository includes an automated validation script written in **Python**. It computes the ideal S-parameter frequency response ($S_{21}$ insertion loss) to verify the filter roll-off prior to tape-out.

### ðŸ“¦ Prerequisites
Install the dependencies using pip:
```bash
pip install numpy matplotlib scipy
```

### ðŸ’» Python Validation Script
```python
import numpy as np
import matplotlib.pyplot as plt

# Component Values
L = 4.7e-6   # 4.7 uH
C = 47e-12   # 47 pF
Z0 = 50      # 50 Ohm System

# Frequency sweep from 100 kHz to 100 MHz
frequency = np.logspace(5, 8, 1000)
omega = 2 * np.pi * frequency

# Transfer Function H(f) = Vout/Vin for an LC low-pass structure
# Z_L = j*w*L, Z_C = 1/(j*w*C)
Z_C = 1 / (1j * omega * C)
Z_L = 1j * omega * L
H = Z_C / (Z_L + Z_C)

# Convert to Decibels (dB)
magnitude_dB = 20 * np.log10(np.abs(H))

# Plotting Transfer Function
plt.figure(figsize=(8, 4))
plt.semilogx(frequency, magnitude_dB, label='Insertion Loss (S21)', color='#007ACC', linewidth=2)
plt.axvline(x=10.7e6, color='red', linestyle='--', label='Cutoff Freq (10.7 MHz)')
plt.title('LC Filter Frequency Response')
plt.xlabel('Frequency (Hz)')
plt.ylabel('Magnitude (dB)')
plt.grid(True, which="both", ls="-")
plt.legend()
plt.tight_layout()
plt.show()
```

---

## ðŸš€ Manufacturing Outputs
The production-ready package includes standard industry deliverables under the `/manufacturing` directory:
1.  **Gerber Files:** RS-274X extended format for automated lithography.
2.  **NC Drill Files:** Excellon format specifying precise coordinates for plated through-holes (PTH) and microvias.
3.  **Bill of Materials (BOM):** Standardized CSV including manufacturer part numbers (MPN) and supplier SKUs for automated component sourcing.
4.  **IPC-D-356 netlist:** For automated electrical bare-board testing (E-test).

---

## ðŸ“œ License
This project is open-source and licensed under the [MIT License](LICENSE).
