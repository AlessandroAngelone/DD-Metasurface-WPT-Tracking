# Double-Layer Dual-Band (DD) Metasurface for Sensorless Tracking & WPT in Active Implantable Medical Devices

**Author:** Alessandro Angelone (M.Sc. in Biomedical Engineering, 110/110 — University of Pisa)  
**Supervisors:** Prof. Danilo Brizi, Prof. Agostino Monorchio, Dr. Alessandro Dellabate | **Reviewer:** Prof. Mimma Nardelli  

> **Academic & IP Notice:**  
> This repository contains the system architecture, signal processing pipelines, embedded control logic, and experimental validation protocols developed for my Master's Thesis (Oct. 2026). As this research is currently being prepared for peer-reviewed journal submission, specific unit-cell geometric dimensions, raw Altair FEKO simulation models, and manufacturing Gerber files are omitted to preserve publication novelty.

---

## 1. Project Overview & Engineering Challenge
Active Implantable Medical Devices (AIMDs) such as leadless pacemakers and motile capsules suffer from severe inductive coupling degradation ($k < 0.1$) caused by anatomical separation and dynamic load misalignment. Existing reconfigurable Wireless Power Transfer (WPT) solutions typically require active RF telemetry and sensing electronics onboard the implant, creating a power overhead and a "dead-battery trap."

This project introduces and experimentally validates a **Double-Layer Dual-Band (DD) Magnetic Metasurface** capable of simultaneously achieving:
1. **Purely Sensorless Spatial Tracking (160 MHz):** Passively localizing the receiver via magneto-inductive (MI) wave perturbation without any active electronics or telemetry onboard the implant.
2. **Dynamic Near-Field Hotspotting (13.56 MHz ISM Band):** Focusing the magnetic flux toward the detected implant position in real time via a voltage-controlled varactor array.

 📄 **[Read the Full Technical Synthesis (PDF)](./docs/Masters_Synthesis.pdf)**

---

## 2. System Architecture & Hardware Implementation
The prototype physically decouples sensing and power transfer onto two independent FR4 PCB layers (0.8 mm thickness, 35 µm copper) to minimize parasitic resistive losses on the power link:

* **Layer 1 — Magneto-Inductive Sensing Array (160 MHz):** $5 \times 5$ planar resonator array excited by two orthogonal Cartesian "pivot" cells. The passive implant acts as an inductive defect, inducing position-dependent frequency splitting measured via the transmission magnitude $\vert{}S_{12}\vert{}$.
* **Layer 2 — Reconfigurable WPT Array (13.56 MHz):** $5 \times 5$ unit-cell array loaded with reverse-biased varactor diodes (calibrated at 12.8 MHz at 0 V bias with 330 pF fixed parallel capacitance), driven by an **Arduino microcontroller** and a **25-channel DAC** to synthesize a localized $3 \times 3$ focal hotspot.

### Closed-Loop Control Pipeline
`Layer 1 (160 MHz MI Waves)` $\rightarrow$ `2-Port Keysight FieldFox VNA (|S12| Sweep)` $\rightarrow$ `12-Class Spatial LUT Classifier (0.2 ms)` $\rightarrow$ `UART (115200 baud) to Arduino + 25-ch DAC` $\rightarrow$ `Layer 2 Varactor Bias (3x3 Hotspot @ 13.56 MHz)`

---

## 3. Experimental Validation & Key Results
* **Localization Accuracy (Test T1):** Evaluated over 625 experimental acquisitions (25 grid positions $\times$ 25 repetitions) using 10-repeated 5-fold cross-validation. Achieved **~92.6% classification accuracy** across 12 spatial classes (and **~100% effective functional accuracy** within the $3 \times 3$ hotspot boundaries), remaining invariant under spectral decimation down to 10 points per sweep.
* **Power Transfer Efficiency Gain (Test T1):** At 30 mm separation distance, active dynamic focusing increased measured PTE ($\eta = \vert{}S_{21}\vert{}^2 / (1 - \vert{}S_{11}\vert{}^2)$) from **0.4% (unfocused baseline) to 2.4% (focused)**, demonstrating a **+530% relative efficiency improvement**.
* **Dosimetric Safety & SAR Compliance (Test T2):** Modeled a dispersive 4-pole Cole-Cole muscle tissue phantom ($160 \times 30\text{ mm}$, $\varepsilon_r = 143.5$, $\sigma = 0.623\text{ S/m}$) per ASME V&V 40-2018. Computed 10-g averaged SAR at 1 W input power is **0.76 W/kg** (well below the **2.0 W/kg** ICNIRP / IEEE C95.1 limit). Validated experimentally on a custom **agar-based tissue phantom** (2% w/v agar, 0.34% w/v NaCl per IEC/IEEE 62209-1528).

---

## 4. Repository Structure
* `/docs` — Full 5-page Technical Synthesis PDF and system figures.
* `/matlab_processing` — Scripts for S-parameter parsing, PTE calculation, spatial LUT clustering, and 5-fold cross-validation.
* `/firmware` — C/C++ microcontroller routines for serial communication and 25-channel DAC varactor bias actuation.