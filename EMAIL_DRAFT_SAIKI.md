# Academic Outreach Email Draft

**To:** Prof. Kazuto Saiki (Osaka University / JAXA SLIM MBC Principal Investigator)  
**Cc:** Mr. Shen Lim  
**From:** Atharv  
**Subject:** Research Proposal & Prototype: Bio-Inspired Neuromorphic Edge Vision for Lunar Lander / Micro-Rover Proximity Sensing (Indo-Japan Context)

---

Dear Prof. Saiki and Mr. Lim,

I hope this email finds you well.

I have been following with immense admiration the remarkable success of the JAXA SLIM mission and your pioneering work with the Multi-Band Camera (MBC) in characterizing lunar mantle olivine near the Shioli crater. As the Indo-Japan planetary exploration collaboration progresses towards future lunar surface initiatives (such as LUPEX), low-power autonomous hazard detection in the final touchdown meters remains one of the most critical challenges for micro-landers and micro-rovers.

Over the past months, I have developed **FREX (Fly-Inspired Neuromorphic Lunar Exploration)**: an ultra-compact edge computing architecture that maps the newly sequenced adult *Drosophila melanogaster* visual connectome (FlyWire, Nature 2024) onto real-time planetary proximity sensing.

### Key Technical Achievements of the FREX Prototype:
1. **Connectome-Derived SNN Architecture:** We isolated the biological looming-detection visual pathway (LC4 lobula columellar neurons to Giant Fiber escape circuitry) from the 138,000-neuron FlyWire connectome, preserving biological excitatory-inhibitory balance (3:1 ratio).
2. **Trained on Lunar Topography:** Using 2,000 descent sequences generated from NASA Lunar Reconnaissance Orbiter (LRO) LOLA terrain renders (excluding sensor anomalies), the network was trained via surrogate gradient BPTT to detect approaching boulder hazards from compound-eye retinotopic scanlines.
3. **Ultra-Compact Edge Deployment (ESP32):** The trained network is quantized to INT8 and compiles into just **404 bytes** of firmware—utilizing less than **0.005%** of the ESP32 microcontroller's flash memory, executing in **185 microseconds** per step, and drawing ~71 mA.
4. **Hardware-in-the-Loop Validation:** Across simulated steep-plunge landing profiles, the biological SNN achieves **50 ms detection latency** (a 700% speedup over classical moving-average thresholds) while maintaining **100% false-alarm immunity** against single-sample electronic sensor noise.

I have compiled our technical methodology, mathematical formulations, and benchmarking charts into a formal proposal document: **"Bio-Inspired Neuromorphic Edge Vision for Lunar Hazard Detection & Descent Collision Avoidance"** (attached). Furthermore, an interactive real-time mission control telemetry dashboard and the complete open-source codebase are ready for demonstration.

I would be deeply honored if you might review this work, and I would welcome the opportunity to discuss how this bio-inspired neuromorphic paradigm could contribute to future JAXA/ISRO lunar surface payloads, particularly regarding spectral reflectance integration with MBC data or edge computing on micro-rovers.

Thank you very much for your time, consideration, and pioneering contributions to planetary exploration.

With highest regards,

**Atharv**  
Project Lead — FREX Neuromorphic Lunar Proximity System  
Email: [Your Email]  
Repository / Project Link: [GitHub / Portfolio Link]
