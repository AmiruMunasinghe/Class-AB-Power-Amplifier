# Discrete Class AB Power Amplifier Design and Implementation

This repository contains the design, simulation, and hardware implementation details of a high-performance **Discrete Class AB Power Amplifier** with complementary Darlington output pairs. 

This circuit was designed as the power amplification stage of an active audio system. It is designed to provide high voltage gain, high current gain, low distortion, and sufficient output power to drive low-impedance loudspeaker loads efficiently from a ±15 V dual power supply.

---

## 1. Circuit Architecture

The power amplifier features a multi-stage discrete topology, optimized for linearity, stability, and drive capability. Below is the schematic of the designed circuit:

![Power Amplifier Circuit Schematic](assets/circuit_schematic.png)

### Stage-by-Stage Breakdown

#### A. Differential Input Stage (Input Error Amplifier)
* **Transistors:** Q7 (2SA733) and Q6 (2SA733) PNP pair.
* **Function:** Compares the input audio signal with the feedback signal returned from the output. It establishes high input impedance, ensures low offset, and provides excellent common-mode rejection.
* **Degeneration:** Resistors R14 ($6.8\text{ k}\Omega$) and R15 ($6.8\text{ k}\Omega$) provide emitter degeneration to improve linearity, widen the input dynamic range, and ensure thermal stability.
* **Biasing:** Biased through R6 ($47\text{ k}\Omega$) and R9 ($47\text{ k}\Omega$) to the $\pm 15\text{ V}$ rails.

#### B. Active Load & Current Mirror Stage
* **Transistors:** Q2, Q3, Q5, and Q9 (2SA733) PNP transistors.
* **Function:** Serves as active loads and current mirrors for the differential input pair. The active load configuration converts the differential output into a single-ended signal, boosting the open-loop voltage gain substantially while improving circuit symmetry and reducing distortion.

#### C. Voltage Amplification Stage (VAS)
* **Transistors:** Q10 (2SD400) NPN transistor.
* **Function:** Provides the bulk of the voltage swing required to drive the output stage. 
* **Degeneration:** Emitter degeneration resistor R19 ($120\ \Omega$) improves linearity and stabilizes the VAS bias current.
* **Miller Compensation:** Capacitor C7 ($100\text{ pF}$) provides local dominant-pole frequency compensation (Miller compensation), ensuring high-frequency stability, preventing parasitic oscillations, and defining the closed-loop bandwidth.

#### D. Class AB Biasing Network
* **Diodes:** D1 (1N4148) and D2 (1N4148) series diodes.
* **Function:** Establishes the correct quiescent bias voltage (approximately $1.2\text{ V}$ to $1.4\text{ V}$) to keep the driver and output transistors slightly conducting under idle conditions. This minimizes **crossover distortion** (inherent in Class B) while maintaining high power efficiency.

#### E. Driver Stage
* **Transistors:** Q1 (2SD400) NPN and Q8 (2SB560) PNP complementary pair.
* **Function:** Provides initial current gain and isolates the high-impedance VAS from the heavy current demands of the output stage. 
* **Biasing:** Biased and stabilized by resistors R20 ($1\text{ k}\Omega$) and R18 ($1\text{ k}\Omega$).

#### F. Darlington Output Stage
* **Transistors:** 
  * *Upper Darlington:* Q1 (2SD400) driving Q4 (2SB507) PNP power transistor.
  * *Lower Darlington:* Q8 (2SB560) driving Q11 (2SD313) NPN power transistor.
* **Function:** Delivers high output currents to the speaker load. The complementary Darlington configuration achieves a very high current gain product ($\beta_{\text{driver}} \times \beta_{\text{output}}$), allowing minimal drive current to control amperes of output current.
* **Emitter Resistors:** R21 ($0.22\ \Omega$) and R22 ($0.22\ \Omega$) provide local negative feedback. They improve thermal stability, ensure equal current sharing, and prevent **thermal runaway** by countering the negative temperature coefficient of the base-emitter junctions ($V_{BE}$).
* **Output Load:** Designed to drive a low-impedance speaker load (modeled as R24, $4\ \Omega$ or $8\ \Omega$ load).

#### G. Feedback Network
* **Components:** R26 ($40\text{ k}\Omega$ feedback), R25 ($1\text{ k}\Omega$ shunt), and C10 ($4.7\ \mu\text{F}$ AC coupling).
* **Function:** Stabilizes the closed-loop gain, flattens the frequency response, and reduces total harmonic distortion (THD). The series capacitor C10 blocks DC, reducing the DC gain of the feedback loop to unity ($0\text{ dB}$), which locks the output DC offset to a minimum.

---

## 2. Theoretical Analysis & Design Modifications

### Closed-Loop AC Voltage Gain ($A_v$)
The closed-loop voltage gain is established by the negative feedback ratio:
$$A_v \approx 1 + \frac{R_{26}}{R_{25}}$$

Based on the values in the schematics and documentation, we note the following comparisons:

| Parameter / Component | Schematic Design (This Repo) | Project Report Text |
| :--- | :--- | :--- |
| **Feedback Resistor ($R_{26}$)** | $40\text{ k}\Omega$ | $22\text{ k}\Omega$ |
| **Gain Shunt Resistor ($R_{25}$)** | $1\text{ k}\Omega$ | $1\text{ k}\Omega$ |
| **Theoretical AC Gain ($A_v$)** | $41\text{V/V}\ (32.26\text{ dB})$ | $23\text{V/V}\ (27.2\text{ dB})$ |
| **Miller Capacitor ($C_7$)** | $100\text{ pF}$ | $39\text{ pF}$ |
| **Emitter Resistors ($R_{21}, R_{22}$)** | $0.22\ \Omega$ | $0.5\ \Omega$ |
| **Speaker Load ($R_{24}$)** | $4\ \Omega$ | $8\ \Omega$ |



### Output Swing Limits
Although an ideal push-pull amplifier swings from rail to rail ($\pm 15\text{ V}$), the maximum unclipped peak output voltage is limited by:
$$V_{\text{peak,max}} = V_{CC} - V_{CE,\text{sat}}(Q_4) - V_{BE}(Q_1) - I_{\text{peak}} \cdot R_{22}$$
These semiconductor overheads reduce the maximum linear peak output voltage slightly below the supply rails.

---

## 3. Simulation & Performance Characteristics

The circuit was verified through extensive SPICE transient and AC simulations. The performance characteristics of the power amplifier are summarized below:

* **Supply Voltage:** $\pm 15\text{ V}$ Dual Supply
* **Closed-Loop Gain:** $\approx 40\text{ dB}$ (with configured feedback)
* **Frequency Response:** $20\text{ Hz}$ to $20\text{ kHz}$ (flat across the audio spectrum)
* **Maximum Output Voltage:** $14.5\text{ V}_{\text{peak}}$
* **Output Power:** $6.25\text{ W}$ (into $8\ \Omega$)
* **Power Efficiency ($\eta$):** $\approx 60.1\%$ to $60.5\%$

### Efficiency Calculation
The efficiency is evaluated using:
$$\eta = \frac{P_{\text{out}}}{P_{\text{in}}} \times 100\%$$
Using simulated values of $P_{\text{in}} = 10.4\text{ W}$ and $P_{\text{out}} = 6.25\text{ W}$:
$$\eta = \frac{6.25}{10.4} \times 100\% = 60.1\%$$
This efficiency is typical for a Class AB amplifier, representing an optimal balance between minimizing crossover distortion (compared to Class B) and maximizing efficiency (compared to Class A).

---

## 4. Hardware Implementation

The power amplifier was constructed and verified on a breadboard using discrete electronic components.

![Breadboard Implementation](assets/breadboard_implementation.jpg)

### Experimental Verification
The amplifier was tested under load using a function generator for input signal injection, and a digital oscilloscope to monitor output waveforms across the audible frequency range.

* **Low-Frequency Performance (400 Hz):** Verified clean sinusoidal amplification without distortion, showing solid bass frequency response.
* **Mid-Frequency Performance (9.02 kHz):** Symmetrical, stable, and low-noise output waveforms confirmed robust operation in the core mid-range.
* **High-Frequency Performance (15.1 kHz):** Sinusoidal integrity was maintained with minimal high-frequency roll-off, confirming the adequacy of the $100\text{ pF}$ Miller compensation capacitor.

The experimental testing validated the simulation results, confirming a stable and reliable discrete audio amplifier design.
