# Master Implementation Plan: Bio-Inspired Neuromorphic Lunar Proximity & Collision Warning System

**Bridging Space Robotics (JAXA SLIM Context) and Edge Neuromorphic Intelligence (Drosophila-Inspired Spiking Neural Networks)**

---

## 🌌 Project Vision & Academic Context

* **Objective:** Design an ultra-low-power, bio-inspired edge computing subsystem for planetary landers and rovers (simulating architectures applicable to lunar missions like JAXA's SLIM).
* **Core Paradigm:** Space environments impose extreme power and radiation constraints. Instead of power-hungry deep learning models, we deploy **Neuromorphic Spiking Neural Networks (SNNs)** modeled after biological insect escape circuits (specifically the *Drosophila* / Locust **Lobula Giant Movement Detector - LGMD**).
* **Research Target:** Deliver a functioning, data-backed prototype and research proposal to **Professor Kazuto Saiki** and **Mr. Shen Lim** following the Indo-Japan Cultural & Academic Exchange.

---

## 🗺️ Multi-Phase Roadmap Overview

```
 [ Phase 1: ESP32 Firmware ] ──▶ [ Phase 2: Telemetry Dashboard ] ──▶ [ Phase 3: Drosophila SNN / NEST-GPU ]
             │                                │                                        │
             ▼                                ▼                                        ▼
   - 10-Point Filter Buffer          - Real-Time Serial Stream               - Insect LGMD Circuit
   - Non-Blocking millis()           - Membrane Potential & Spikes           - NEST-GPU Simulation
   - Embedded Spiking Dynamics       - Telemetry Dataset Collector           - Synaptic Weight Tuning
                                              │                                        │
                                              └────────────────────┬───────────────────┘
                                                                   ▼
                                                 [ Phase 4: Hardware-in-the-Loop ]
                                                                   │
                                                                   ▼
                                             [ Phase 5: Proposal & Outreach to Prof. Saiki ]
```

---

## 🛠️ Phase Breakdown

### Phase 1: Embedded Hardware & Neuromorphic Firmware (ESP32)
* **Goal:** Build the core real-time embedded firmware running on the ESP32.
* **Hardware Connections:**
  * `GPIO 34 (ADC1)`: Analog IR Distance Sensor
  * `GPIO 25 (PWM)`: Piezo Buzzer (1 Hz pulse for Caution, 2.2 kHz for Hazard)
  * `GPIO 12 (INPUT_PULLUP)`: Push Button (Mode toggle to GND)
* **Firmware Modules:**
  1. **Non-Blocking Execution Engine:** 50ms sampling loop (`millis()`), zero `delay()`.
  2. **Signal Conditioning:** 10-sample circular moving average buffer for electronic noise elimination.
  3. **Biomimetic Threat Engine (LIF / LGMD Model):**
     * Instantaneous velocity delta: $\Delta = \text{Raw} - \text{MovingAverage}$
     * Leaky Integrate-and-Fire membrane potential accumulator:
       $$V_m[t] = V_m[t-1] \cdot e^{-\Delta t / \tau} + I_{\text{stimulus}}[t]$$
     * Spike generation triggering immediate collision hazard alerts.
  4. **Dual-Mode Controller:**
     * **Mode 0 (Active Detection):** Full autonomous state machine and buzzer output.
     * **Mode 1 (Silent Calibration & Data Streaming):** Raw and filtered telemetry streamed over UART at `115200 baud`.

---

### Phase 2: Live Telemetry Dashboard & Data Ingestion Pipeline
* **Goal:** Create a visual control station that receives UART data from the ESP32 to visualize and log physical telemetry.
* **Key Features:**
  * **Real-time Live Graphing:** Distance, Moving Average, Instantaneous Velocity Delta ($\Delta$), and Simulated Membrane Potential ($V_m$).
  * **Visual State Indicators:** CLEAR (Green), CAUTION (Yellow), IMMINENT HAZARD (Flashing Red).
  * **Dataset Recorder:** One-click CSV/JSON logging of approach profiles (slow drift vs. rapid drop) for model training and simulation ground-truth.

---

### Phase 3: Drosophila (Fruit Fly) Connectome & NEST-GPU Neural Modeling
* **Goal:** Simulate large-scale biophysical spiking neural models inspired by insect visual collision avoidance.
* **Key Modules:**
  * **The LGMD Neuromorphic Model:**
    * Excitation path: Rapid localized expansion of approaching obstacles.
    * Lateral inhibition path: Suppression of wide-field slow background drift.
  * **Simulation Engine (NEST-GPU / Brian2):**
    * Model multi-compartment spiking neurons using Runge-Kutta ODE solvers.
    * Feed recorded experimental telemetry datasets to benchmark spike latency vs. conventional ML algorithms.
  * **Model Distillation:** Extract optimized synaptic weights and threshold parameters to flash back into the lightweight ESP32 C++ engine.

---

### Phase 4: Hardware-in-the-Loop (HIL) Lunar Approach Emulator
* **Goal:** Test and benchmark system reaction times against simulated planetary descent trajectories.
* **Components:**
  * Simulated lander / rover velocity profiles.
  * Quantitative benchmarking: Millisecond response latency, power consumption (milliwatts), and false-positive rejection rate under simulated electronic noise.

---

### Phase 5: Academic Research Proposal & Outreach to Prof. Kazuto Saiki
* **Goal:** Package the code, mathematical documentation, and benchmark results into an academic submission.
* **Deliverables:**
  * `PROJECT_PROPOSAL_SAIKI.md`: Formal research proposal detailing the Bio-Inspired Neuromorphic Edge Computing subsystem for Lunar Surface Missions.
  * Professional email draft connecting the Indo-Japan exchange, the SLIM mission context, and your working prototype.

---

## 🎯 Immediate Next Step: **Task 1 — Core ESP32 Firmware Build**

The foundational step is creating the complete, compiled, and tested ESP32 firmware sketch (`lunar_proximity_system.ino`) implementing the non-blocking state machine, 10-sample moving average filter, velocity delta math, and calibration serial streaming.
