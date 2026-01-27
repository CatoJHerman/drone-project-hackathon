# 🧠 NeuroFlight: Brain-Controlled Drone Interface

**A closed-loop BCI (Brain-Computer Interface) system that pilots a Crazyflie drone using real-time EEG data.**

This project interfaces a **BioAmp EEG sensor** with a **Crazyflie 2.1 drone**. It features a custom Python flight controller that replaces standard manual piloting with a "Hybrid Stabilization" engine—combining hardcoded hardware trims with a Fuzzy Logic algorithm to actively fight air currents and drift without user input.

---

## 🚀 Features

* **Mind-Triggered Flight:** Uses EOG (Electrooculography) blink detection to arm and launch the drone hands-free.
* **Live Visualization:** Real-time Matplotlib graph showing raw BrainWave signals against a dynamic trigger threshold.
* **Hybrid Stabilization Engine:**
    * **Feedforward:** Hardcoded roll/pitch trims to compensate for physical hardware weight imbalances.
    * **Feedback:** Active "Fuzzy Logic" controller that reads onboard Gyroscope data 50x/second to fight wind and drift.
* **Safety Protocols:** Auto-landing sequences, connection timeout handling, and emergency kill switches.

---

## 🛠️ Hardware Stack

* **Drone:** Bitcraze Crazyflie 2.1
* **Microcontroller:** Arduino Uno R4 (or ESP32)
* **Sensor:** BioAmp EXG Pill (or compatible EEG/EOG sensor)
* **Connectivity:**
    * Crazyradio PA (2.4GHz) for Drone telemetry.
    * USB Serial for Arduino-to-Python communication.

## 💻 Software Stack

* **Python 3.10+**
* **Libraries:** `cflib` (Crazyflie SDK), `matplotlib`, `pyserial`, `numpy`
* **Arduino IDE** (C++)

---

## ⚙️ Installation & Setup

### 1. Prerequisites
Clone the repository and install the Python dependencies:
```bash
git clone [https://github.com/yourusername/neuroflight.git](https://github.com/yourusername/neuroflight.git)
cd neuroflight
pip install cflib matplotlib pyserial
```
### 2. Arduino Setup
Connect the BioAmp sensor to Pin A0.

Open biofeedback.ino in Arduino IDE.

Upload the code to your board.

Note the COM Port (e.g., `COM15` or `/dev/ttyUSB0`).

### 3. Drone Setup
Ensure your Drone is powered on and placed on a **flat surface**.

Update the DRONE_URI in main.py to match your drone's address (e.g., `udp://192.168.43.42`).

### How to Run
**Connect Hardware**: Plug in the Arduino and the Crazyradio dongle.

### Start the Interface:

```Bash
python main.py
Wait for Calibration: The system will connect to the Arduino and the Drone. Wait for the graph to appear.
```
### Launch:

Focus or Blink hard to spike the signal above the Green Threshold line.

The graph will turn RED, and the drone will launch automatically.

**Auto-Pilot**: The drone will hover for a preset duration using the Fuzzy Logic stabilizer, then auto-land.

### The "Hybrid" Logic Explained
Standard calibration wasn't enough for this specific hardware setup due to weight distribution issues. This project uses a custom control loop:

# Pseudo-code logic
def control_loop():
    # 1. Read Gyroscope (Feedback)
    drift = get_sensor_data()
    
    # 2. Calculate Active Correction (Fuzzy Logic)
    if drift > small_wobble:
        correction = apply_gentle_force()
    elif drift > big_tilt:
        correction = apply_strong_force()
        
    # 3. Apply Feedforward Trim (Hardware Fix)
    final_command = HARDCODED_TRIM + correction
    
    send_to_motors(final_command)```
This allows the drone to fly straight even if its physical center of gravity is off-center (e.g., drifting left).

## Tuning
If the drone drifts, adjust the variables at the top of main.py:

**HARDCODED_ROLL**: Increase if drifting Left, Decrease if drifting Right.

**HARDCODED_PITCH**: Increase if drifting Back, Decrease if drifting Forward.

**roll_gain**: Increase if the drone reacts too slowly to wind. Decrease if it wobbles/oscillates.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
