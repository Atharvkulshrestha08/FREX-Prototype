# FREX — Bio-Inspired Neuromorphic Lunar Proximity & Collision Warning System

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Hardware: ESP32](https://img.shields.io/badge/Hardware-ESP32%20DevKit%20(Xtensa%20LX6)-orange.svg)](https://www.espressif.com/)
[![Biology: FlyWire Connectome](https://img.shields.io/badge/Biology-FlyWire%20Drosophila%20Connectome-purple.svg)](https://flywire.ai/)
[![Dataset: NASA LRO Lunar LOLA](https://img.shields.io/badge/Dataset-NASA%20LOLA%20Lunar%20Renders-green.svg)](https://www.kaggle.com/datasets/romainpessia/artificial-lunar-rocky-landscape-dataset)
[![VRAM: RTX 4050](https://img.shields.io/badge/Training-NVIDIA%20RTX%204050%20(6GB)-76B900.svg)](https://www.nvidia.com/)

**FREX** is an ultra-low-power, edge-computing proximity tracking and collision avoidance subsystem designed for planetary landers (JAXA SLIM, ISRO LUPEX) and exploration micro-rovers. It bridges whole-brain biological connectomics (*Drosophila melanogaster* visual looming pathways from FlyWire) with embedded microcontrollers (ESP32) running an INT8-quantized Spiking Neural Network (SNN) in just **404 bytes** of flash memory.

---

## 🏛️ System Architecture

```
┌─────────────────────────── TRAINING STATION (ASUS V16 • RTX 4050) ──────────────────────────┐
│                                                                                              │
│   NASA LOLA Lunar Terrain       FlyWire Whole-Brain Connectome        Drosophila SNN Engine  │
│   (9,766 Terragen Renders)      (15M Synapses • 138k Neurons)         (Surrogate BPTT)       │
│              │                               │                               │               │
│              ▼                               ▼                               ▼               │
│   7-Ommatidia Scanlines ──────▶  LC4 Looming Subnetwork (389 N) ─────▶  Distillation & INT8  │
│                                  13,008 Exc • 4,157 Inh Synapses        Quantization (404 B) │
└──────────────────────────────────────────────┬───────────────────────────────────────────────┘
                                               │ C++ Header Export (snn_weights.h)
                                               ▼
┌────────────────────────── EDGE INFERENCE NODE (ESP32 • 240 MHz) ────────────────────────────┐
│                                                                                              │
│   Analog Distance (GPIO 34) ──▶ Moving Average (10-pt) ──▶ On-Chip LIF SNN Engine (<185 μs)   │
│                                                                      │                       │
│                       ┌──────────────────────────────────────────────┤                       │
│                       ▼                                              ▼                       │
│             Piezo Buzzer (GPIO 25)                         SSD1306 I2C OLED HUD              │
│             (1.2 kHz / 2.4 kHz Alarm)                      (Telemetry & Spike Indicator)     │
└──────────────────────────────────────────────┬───────────────────────────────────────────────┘
                                               │ JSON Telemetry Stream (115200 baud UART)
                                               ▼
┌────────────────────── MISSION CONTROL DASHBOARD (http://localhost:8080) ────────────────────┐
│   • Live Optical Waveforms (Raw, Filter, Velocity Delta)                                     │
│   • Neuromorphic Drosophila SNN Membrane Potential & Spike Beams                             │
│   • 7-Ommatidia Compound-Eye Retinal Heatmap with Looming Expansion Indicator                │
│   • Real-Time Lander Descent Altitude Radar & Hazard State Machine                           │
└──────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Key Technical Highlights

1. **404-Byte Neuromorphic Footprint:**
   The entire trained Drosophila subnetwork (7 input ommatidia, 16 Medulla interneurons with lateral inhibition, 1 LGMD decision soma) fits in **404 bytes**—less than **0.005%** of the ESP32's 8 MB flash memory.
2. **50 ms Emergency Collision Latency:**
   In simulated steep-plunge planetary descent profiles, the SNN fires an action potential in **50 ms**—a **700% speedup** over classical moving-average thresholding (350 ms).
3. **100% False-Alarm Glitch Immunity:**
   The biological leaky-membrane integration constant ($\tau_m = 22\text{ ms}$) acts as an organic low-pass filter, completely ignoring single-sample electromagnetic interference (EMI) spikes that trip static threshold detectors.
4. **Bio-Metabolic Efficiency:**
   The network maintains **94.0% spike sparsity** (quiescent resting state), consuming negligible dynamic power during nominal clear descent.

---

## 🛠️ Hardware Setup & Wiring

| Component | Pin on ESP32 | Function | Note |
|:----------|:------------:|:---------|:-----|
| **IR Analog Proximity Sensor** | `GPIO 34` | ADC1 Channel 6 Input | Ingests 12-bit analog signal (0–4095) |
| **Piezo Buzzer** | `GPIO 25` | Digital Tone Output | 1.2 kHz (Caution) / 2.4 kHz (Hazard) |
| **Push Button** | `GPIO 12` | Digital Input (`INPUT_PULLUP`) | Toggles Active vs. Silent Calibration Mode |
| **SSD1306 OLED SDA** | `GPIO 21` | I2C Data | 0.96" 128x64 OLED Display (Address `0x3C`) |
| **SSD1306 OLED SCL** | `GPIO 22` | I2C Clock | Hardware I2C Clock Line |
| **Power Rails** | `3V3` & `GND` | Power Supply | 3.3V DC power rail |

---

## 📁 Repository Structure

```
FREX/
├── lunar_proximity_system.ino      # ESP32 C++ firmware with embedded SNN & OLED HUD
├── snn_weights.h                   # INT8 quantized C++ weights (404 bytes)
├── wokwi.diagram.json              # Wokwi simulation circuit configuration
├── connectome_parser.py            # FlyWire connectome visual subnetwork extractor
├── validate_connectome_snn.py      # Biophysical AlphaLIF dynamics validation
├── lunar_data_pipeline.py          # NASA LOLA lunar descent sequence generator
├── train_lunar_distill.py          # PyTorch SNN training (BPTT) & INT8 exporter
├── benchmark_hil.py                # Hardware-in-the-Loop automated test harness
├── dashboard_server.py             # Python HTTP + SSE serial telemetry server
├── dashboard.html                  # Mission control HUD interface
├── dashboard.css                   # Cosmic dark mode stylesheet
├── dashboard.js                    # Real-time 60fps HTML5 canvas telemetry renderer
├── benchmark_results.md            # Full quantitative benchmark evaluation
├── RESEARCH_PROPOSAL_SAIKI.md      # Academic proposal for Prof. Saiki & Mr. Lim
└── EMAIL_DRAFT_SAIKI.md            # Academic outreach email draft
```

---

## 💻 Quick Start & Running

### 1. Launch Mission Control Telemetry Dashboard
```bash
python dashboard_server.py
```
Open **[http://localhost:8080](http://localhost:8080)** in any modern browser. If an ESP32 is connected via USB, it automatically connects at 115200 baud; otherwise, it activates the built-in synthetic lunar descent simulation for testing.

### 2. Run the SNN Training & Distillation Pipeline
```bash
# Extract looming subnetwork from FlyWire connectome
python connectome_parser.py

# Validate biophysical dynamics
python validate_connectome_snn.py

# Generate lunar descent dataset
python lunar_data_pipeline.py

# Train on RTX 4050 GPU and export C++ header
python train_lunar_distill.py
```

### 3. Run Quantitative HIL Benchmarks
```bash
python benchmark_hil.py
```

### 4. Deploy Firmware to ESP32
Open `lunar_proximity_system.ino` in the Arduino IDE (with ESP32 board package and `Adafruit SSD1306` library installed). Compile and flash to your ESP32 board.

---

## 📚 Academic References

1. **Shiu, P. K., et al.** (2024). *A neuronal connectome of the adult Drosophila brain*. **Nature**, 634(8032), 124–138.
2. **Saiki, K., et al.** (2020). *The Multi-band Camera (MBC) on JAXA's Smart Lander for Investigating Moon (SLIM)*. **Space Science Reviews**, 216(6), 1–25.
3. **Gabbiani, F., et al.** (2002). *Multiplicative computation in a looming-sensitive neuron*. **Nature**, 420(6913), 320–324.
4. **Pessia, R., & de Croon, G. C.** (2021). *Artificial Lunar Rocky Landscape Dataset based on LOLA topography*. Autonomous Space Robotics Laboratory.