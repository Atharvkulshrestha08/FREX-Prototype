# Product Requirement Document (PRD)

## Project Title
**Autonomous Lunar Proximity & Collision Warning System**

## 1. Executive Summary & Objective
The objective of this project is to build an embedded edge-computing application that processes physical telemetry in real-time to mimic space exploration safety subsystems (such as a planetary lander or an autonomous rover). The system will ingest raw signals from an Analog Infrared (IR) Distance Sensor, run localized logic on an ESP32 microcontroller, and provide dynamic telemetry warnings via a piezo buzzer.

By prioritizing edge-based execution and noise filtering, this prototype addresses critical requirements of modern aerospace systems: latency elimination, autonomous hazard mitigation, and resilience against physical sensor noise.

---

## 2. Hardware Architecture & Pin Mapping
The prototype relies on low-power, commercially available embedded hardware components configured as follows:

| Component | Interface Type | Microcontroller Pin Connection | Functional Purpose |
| :--- | :--- | :--- | :--- |
| **ESP32 Development Board** | Main Compute Unit | - | Executes sensor filtering, state machines, and local inference logic. |
| **Analog Infrared (IR) Sensor** | Analog Input (ADC) | `GPIO 34 (ADC1_CH6)` | Measures real-time distance reflectivity data from physical surfaces. |
| **Piezo Buzzer** | Digital Output (PWM) | `GPIO 25` | Outputs multi-frequency auditory alarms based on the threat state. |
| **Push Button** | Digital Input | `GPIO 12` | Toggles system states via physical user interrupt. |

*Note on Button Configuration:* The Push Button must use the ESP32 internal pull-up resistor configuration (`INPUT_PULLUP`). The baseline digital state reads `HIGH`, pulling down to `LOW` when physically pressed.

---

## 3. Functional Requirements & State Machine

### 3.1 Mode Selection (Button Interrupt)
The system operates within a modal loop toggled by pressing the button connected to `GPIO 12`:
1. **ACTIVE DETECTION MODE (Default):** The system continuously samples data, calculates moving windows, evaluates safety states, and actively drives the buzzer.
2. **SILENT CALIBRATION MODE:** The system silences the buzzer completely. It streams raw sensor telemetry directly to the Serial interface at `115200 baud` for sensor optimization and environment profiling.

### 3.2 Signal Conditioning (Moving Average Filter)
Cheap infrared sensors exhibit high frequency electronic signal spikes (noise) which can cause critical false-alarm triggers. 
* The firmware must maintain a sequential array buffer of the last **10 consecutive sensor readings**.
* The sampling interval must be exactly **50 milliseconds**.
* Every loop iteration must drop the oldest data point, insert the newest sensor reading, and calculate a clean, noise-mitigated **Moving Average** to use for state verification.

### 3.3 Threat Evaluation & Output Behavior
The application updates its warning state based on the distance thresholds calculated from the filtered sensor moving average:

```
                  +-----------------------------------+
                  |      Calculate Moving Average     |
                  +-----------------------------------+
                                    |
            ________________________|________________________
           |                        |                        |
 [Below Caution Boundary] [Within Caution Boundary] [Rapid Time-Derivative Δ]
           |                        |                        |
           v                        v                        v
   State 0: CLEAR          State 1: CAUTION          State 2: HAZARD
   (Buzzer: SILENT)        (Buzzer: 1Hz Beep)        (Buzzer: CONSTANT ALARM)
```

* **State 0: CLEAR PATH**
  * *Condition:* The moving average indicates an obstacle is outside the critical monitoring zone (low analog value / far object).
  * *Action:* Keep the Piezo Buzzer completely silent.
* **State 1: STEADY CAUTION**
  * *Condition:* The moving average enters an intermediate zone indicating an obstacle is present but stable.
  * *Action:* Drive the buzzer with an intermittent warning pattern: pulse active for 100ms, silent for 900ms (**1 Hz frequency cycle**).
* **State 2: IMMINENT HAZARD / COLLISION**
  * *Condition:* Triggered by two distinct conditional vectors:
    1. **Threshold Breach:** The filtered moving average value exceeds the extreme absolute limit (object critically close).
    2. **Time-Derivative Velocity Delta ($\Delta$):** The current instantaneous reading is significantly larger than the historical moving average window ($CurrentReading - MovingAverage > \Delta\_Threshold$). This indicates a high-speed approach path even before the absolute proximity threshold is crossed.
  * *Action:* Drive the Piezo Buzzer continuously at a high frequency (`2000Hz - 2500Hz`) to sound an emergency alarm.

---

## 4. Technical Constraints & Design Considerations

* **Local Compute Only (Edge Rule):** The script must process everything locally. No active Wi-Fi, Bluetooth, or external cloud compute libraries may be invoked.
* **Non-Blocking Logic:** The state machine and buzzer beep cycles must avoid using the blocking `delay()` function. All time tracking must use the native hardware clock tracker `millis()` to ensure that high-speed hazard transitions are detected instantly even during a slow audio pulse.