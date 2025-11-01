# Bandgap Reference Design

This project documents my work on the Bandgap Reference circuit using the Sky130 PDK.

## Objective
To design a stable voltage reference independent of temperature and process variations.

# INTRODUCTION
Bandgap Reference (BGR) Circuit

A Bandgap Reference (BGR) is a core circuit used in analog and mixed-signal ICs.
Its main role is to generate a stable reference voltage that stays nearly constant — regardless of temperature changes, supply voltage variations, or manufacturing differences.

Because of its reliability, it’s a key part of systems like:

1. ADCs (Analog-to-Digital Converters)
2. DACs (Digital-to-Analog Converters)
3. Voltage regulators
4. Biasing and sensor circuits

*How It Works*
The Bandgap Reference operates by combining two temperature-dependent voltages so that their effects cancel each other out:
1. CTAT Voltage (Complementary to Absolute Temperature):
 - Derived from the base-emitter voltage (V₍BE₎) of a bipolar transistor.
 - As temperature increases, V₍BE₎ decreases, giving it a negative temperature coefficient.

2. PTAT Voltage (Proportional to Absolute Temperature):
 - Created from the difference in base-emitter voltages (ΔV₍BE₎) of two transistors running at different current densities.
 - This voltage increases with temperature, meaning it has a positive temperature coefficient.

*Final Output*
By properly scaling and summing the PTAT and CTAT voltages, their opposing temperature behaviors balance out.
This produces a temperature-independent reference voltage, typically around 1.2 V — which corresponds to the bandgap voltage of silicon at absolute zero (0 K).

---

### Key Features
* Gives a **steady, temperature-independent voltage output**
* **Hardly affected** by changes in power supply or manufacturing differences
* Can be **built using either bipolar or CMOS technology**
* Commonly used in **high-precision analog and mixed-signal circuits**




###  Working Principle 
At its core, a Bandgap Reference circuit works like this:

1. It creates a CTAT (Complementary to Absolute Temperature) voltage using a diode-connected BJT — this is basically the base-emitter voltage, V₍BE₎, which decreases as temperature increases.
2. Then it generates a PTAT (Proportional to Absolute Temperature) voltage by using two BJTs with different emitter area ratios — this voltage increases with temperature.
3. Next, it combines the PTAT and CTAT voltages in the right ratio so that their temperature effects cancel each other out.
4. The result is a stable, temperature-independent reference voltage, which remains nearly constant across a wide temperature range.


## Features of Bandgap Reference (BGR)

1. A voltage reference circuit that stays stable across temperature changes, commonly used in Integrated Circuits (ICs).
2. Delivers a constant output voltage, unaffected by variations in power supply, temperature, or load.
3. Typically provides about 1.2 V, which corresponds to the bandgap energy of silicon at absolute zero (0 K).
4. Found in nearly all circuit types — analog, digital, mixed-signal, RF, and System-on-Chip (SoC) designs.


---

## Applications of Bandgap Reference
- **Low Dropout Regulators (LDOs)**  
- **DC-to-DC Buck Converters**  
- **Analog-to-Digital Converters (ADCs)**  
- **Digital-to-Analog Converters (DACs)**

## Contents

## 1. Tool and PDK Setup
### 1.1 Tools Setup

For the design, simulation, and verification of the Bandgap Reference (BGR) circuit, the following open-source EDA tools are used:

---

### **Ngspice – Circuit Simulation**

**Ngspice** is an open-source, SPICE-based simulator used for **analyzing analog and mixed-signal circuits**.
It reads a **SPICE netlist** that defines the circuit’s components and their interconnections, then computes key electrical parameters such as **node voltages, currents, and transfer characteristics**.

In this project, **Ngspice** is used to:

* Simulate the **schematic-level design** of the **Bandgap Reference (BGR)** circuit.
* Perform **DC**, **AC**, and **transient analyses** to study circuit performance.
* Evaluate **temperature effects** and verify the **stability of the reference output voltage**.


---

### **Magic – VLSI Layout Editor**

**Magic** is an open-source **VLSI layout editor** developed at **UC Berkeley**, widely used for **IC layout design** in open-source PDKs such as **Sky130**.
It provides an intuitive, interactive environment for **drawing transistors, interconnects, and layers** according to the process design rules.

In this project, **Magic** is used to:

* Design the **physical layout** of the **Bandgap Reference (BGR)** circuit.
* Perform **Design Rule Check (DRC)** to ensure the layout meets all **fabrication constraints** of the **Sky130 process**.
* **Extract** the layout to generate a **SPICE netlist** for **post-layout simulation and verification**.


---

### **Netgen – Layout Verification Tool**

**Netgen** is a layout verification tool used for **Layout Versus Schematic (LVS)** checks.
It compares two netlists:

* one **extracted from the layout** (usually using **Magic**)
* and the other **generated from the schematic** (used for **Ngspice** simulation).

The goal is to ensure that both netlists match in terms of **connectivity** and **device parameters**.
When the LVS check passes, it confirms that the **layout is an accurate physical representation of the schematic**, ensuring the **electrical integrity of the design before fabrication**.


### 1.2 PDK Setup 

The SkyWater **sky130** PDK provides process design data (layers, device rules, models) required for layout, extraction and simulation.  
Below are typical steps to obtain and prepare the SkyWater-130 PDK on a Linux development environment.
**Steps:**
- Create a directory for the PDK.  
- Clone the SkyWater PDK repository.  
- Initialize submodules (if required).  
- Build or install PDK libraries (optional).  
- Set the PDK path so tools like Magic, Ngspice, and Netgen can locate it easily. 


## 2. BGR Introduction
Got it! Here’s the **restructured version** of the entire section — now starting with the **Principle** first, followed by **Key Concepts**, and then **CTAT** and **PTAT** generation.
The layout is neat, hierarchical, and ideal for technical documentation or reports:

---

## **2. Principle of Bandgap Reference**

The **Bandgap Reference (BGR)** circuit generates a **temperature-independent reference voltage** by **combining two temperature-dependent voltages** — one that decreases with temperature (**CTAT**) and one that increases with temperature (**PTAT**).

By carefully adjusting their proportions, these opposing effects **cancel each other out**, resulting in a **stable reference voltage** (typically around **1.2 V**, corresponding to the **bandgap energy of silicon at 0 K**).

This reference voltage is crucial in ensuring **consistent circuit performance** across temperature, supply, and process variations.

---

## **2.1 Key Concepts**

### **CTAT (Complementary to Absolute Temperature)**

* A voltage that **decreases** as temperature **increases**.
* Commonly observed in the **base-emitter voltage (V₍BE₎)** of a **bipolar transistor** or a **forward-biased diode**.
* As temperature rises, carrier mobility increases, causing the **forward voltage drop** to reduce — this forms the **CTAT behavior**.

### **PTAT (Proportional to Absolute Temperature)**

* A voltage that **increases** with **temperature**.
* Generated by taking the **difference between two V₍BE₎ voltages** of transistors operating at **different current densities**.
* This difference is directly **proportional to absolute temperature**, forming the **PTAT component** of the Bandgap Reference circuit.

---

## **2.2 CTAT Voltage Generation**

* Semiconductor **diodes** naturally exhibit **CTAT characteristics**.
* When a **constant current** flows through a **forward-biased diode**, the **forward voltage decreases** as **temperature increases**.
* Experimentally, the forward voltage of a silicon diode decreases by approximately **–2 mV/°C**.

This temperature-dependent drop in voltage serves as the **CTAT voltage** component in the Bandgap Reference circuit.

---

## **2.3 PTAT Voltage Generation**

From the **diode current equation**, the forward voltage depends on two temperature-sensitive terms:

1. **Thermal Voltage (Vₜ):**

   * Directly **proportional to absolute temperature (T)**.

2. **Reverse Saturation Current (Iₛ):**

   * Increases with temperature approximately as **~T²·⁵**.

Since **Iₛ** appears in the denominator of the logarithmic term `ln(I₀/Iₛ)`, an increase in temperature reduces this term, leading to the **CTAT behavior** of the diode voltage.

To design a **PTAT voltage**, we isolate the **Vₜ-dependent component** while minimizing the effect of **Iₛ**.
This is typically achieved by **taking the difference between two V₍BE₎ voltages** of transistors biased at **different current densities**, resulting in a voltage that **increases linearly with temperature**.

---

## **2.4 Bandgap Voltage Derivation**

The **Bandgap Reference (BGR)** voltage is obtained by **adding a CTAT voltage** (which decreases with temperature) and a **PTAT voltage** (which increases with temperature) in such a way that their **temperature coefficients cancel each other out**.

---

### **Combining CTAT and PTAT Components**

By scaling and summing these two components, we can form a voltage that is **temperature-independent**:

[
V_{REF} = V_{BE} + k \cdot \Delta V_{BE}
]

where (k) is a scaling constant chosen such that the **negative temperature coefficient** of (V_{BE}) cancels out the **positive coefficient** of ( \Delta V_{BE} ).

At the **cancellation point**, (V_{REF}) becomes almost constant over temperature:

[
\frac{dV_{REF}}{dT} = 0
]

Resulting in a reference voltage approximately equal to the **silicon bandgap voltage**:

[
V_{REF} \approx 1.2 , \text{V}
]

---

### **Summary**

* **(V_{BE})** → CTAT component (decreases with temperature)
* **(\Delta V_{BE})** → PTAT component (increases with temperature)
* **Proper combination** → Stable reference voltage (~1.2 V)

Thus, the **Bandgap Reference circuit** provides a **temperature-independent voltage**, making it an essential element in **precision analog and mixed-signal integrated circuits**.

---



####  Summary

- **Diode / BJT junction** provides the **CTAT** component.  
- **Difference in V<sub>BE</sub>** between transistors provides the **PTAT** component.  
- When both are combined properly, the **temperature variations cancel**, producing a **constant reference voltage (~1.2 V)** close to the **bandgap voltage of silicon**.

---

## **2.2 Types of Bandgap Reference (BGR)**

The **Bandgap Reference (BGR)** circuit provides a temperature-independent reference voltage, typically close to the silicon bandgap energy (~1.2 V).
Different implementations exist, each balancing **accuracy**, **power**, and **complexity** depending on design requirements.

---

### **2.2.1 Architecture-Based Classification**

| **Architecture Type**                        | **Description**                                                                                             | **Advantages**                                                                |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Self-Biased Current Mirror BGR**           | Employs internal current mirrors for biasing, eliminating the need for external voltage or current sources. | Compact design, low power consumption, and good stability.                    |
| **Operational Amplifier (Op-Amp) Based BGR** | Utilizes an op-amp to equalize node voltages and improve matching between branches.                         | High precision, superior temperature compensation, and reduced offset errors. |

---

### **2.2.2 Application-Based Classification**

Depending on the target application and performance goals, Bandgap Reference circuits are further optimized as follows:

* **Low-Voltage BGR:** Designed to operate reliably at supply voltages below 1 V.
* **Low-Power BGR:** Focused on minimizing static and dynamic power for portable or battery-driven devices.
* **High-PSRR / Low-Noise BGR:** Enhances rejection of supply noise and fluctuations, critical for analog and RF circuits.
* **Curvature-Compensated BGR:** Introduces additional compensation networks to counteract non-linear temperature effects for enhanced stability.

---

### **2.2.3 Chosen Architecture**

In this project, a **Self-Biased Current Mirror-Based Bandgap Reference** is implemented.
This configuration offers an excellent balance between **simplicity**, **power efficiency**, and **temperature stability**, making it particularly suitable for **integrated analog systems** fabricated using open-source PDKs such as **Sky130**.

---

## **2.3 Self-Biased Current Mirror-Based BGR**

A **Self-Biased Current Mirror Bandgap Reference** is a fully self-contained circuit that generates a stable reference voltage without requiring any external bias source.
It achieves temperature independence by **summing CTAT and PTAT voltages** in the correct ratio, thereby cancelling their opposite temperature dependencies.

---

### **2.3.1 Functional Blocks**

| **Block**                      | **Purpose**                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------- |
| **CTAT Voltage Generator**     | Produces a voltage that decreases with temperature (typically V<sub>BE</sub> of a BJT).     |
| **PTAT Voltage Generator**     | Produces a voltage that increases linearly with temperature (derived from ΔV<sub>BE</sub>). |
| **Self-Biased Current Mirror** | Provides automatic biasing by mirroring current through feedback.                           |
| **Reference Branch**           | Combines PTAT and CTAT voltages to generate a temperature-stable output.                    |
| **Start-Up Circuit**           | Ensures the circuit powers up correctly and avoids a zero-current state.                    |

---

### **2.3.2 Principle of Operation**

The BGR circuit operates by **balancing two opposing temperature effects**:

1. **CTAT Voltage:**
   The base-emitter voltage (V<sub>BE</sub>) of a BJT decreases with temperature at a rate of roughly **–2 mV/°C**.
   This voltage alone is temperature-dependent and unsuitable as a reference.

2. **PTAT Voltage:**
   The difference in base-emitter voltages (ΔV<sub>BE</sub>) between two BJTs operating at different current densities increases linearly with temperature.
   This voltage can be scaled and added to the CTAT component.

3. **Summation:**
   By adding the PTAT voltage to the CTAT voltage in the correct ratio, the temperature dependencies cancel each other, resulting in a **constant reference voltage** approximately equal to **1.2 V**, which corresponds to the silicon bandgap at 0 K.

---

### **2.3.3 Reference Branch Circuit**

The **Reference Branch** forms the core of the Bandgap Reference circuit and is responsible for producing the final stable voltage.

#### **Structure**

* Comprises a **diode-connected BJT** and a **resistor** in series.
* A **mirror transistor** ensures current symmetry between the PTAT and CTAT branches.
* The current through the mirror and the reference branch remains equal, ensuring consistent operation.

#### **Operation**

1. The **PTAT generator** provides a current proportional to temperature.
2. This current flows through the **resistor** and the **CTAT diode**, producing both voltage components.
3. The **resistor value** is adjusted so that the PTAT voltage slope compensates for the CTAT slope.
4. The resulting voltage across the branch becomes **temperature-independent**, providing a **stable reference output**.



---

## **2.4 Advantages and Limitations of Self-Biased Current Mirror (SBCM) BGR**

The **Self-Biased Current Mirror (SBCM)** topology is a popular choice for implementing **Bandgap Reference (BGR)** circuits due to its simplicity and self-sustaining biasing behavior.
However, while it offers design elegance, it also presents some practical trade-offs in performance and power handling.

---

###  **Advantages**

1. **Simple and Compact Design**

   * The SBCM architecture minimizes circuit complexity, making it highly suitable for integration in analog and mixed-signal ICs.
   * Requires fewer components, reducing silicon area and overall design effort.

2. **Ease of Biasing**

   * The circuit achieves internal biasing automatically without the need for external bias currents or reference voltages.
   * This makes it inherently robust and self-starting (once initiated by a start-up circuit).

3. **Stable Operation**

   * Once active, the feedback loop establishes a steady equilibrium current.
   * Ensures consistent reference voltage even under moderate process or temperature variations.

4. **Low Power Consumption**

   * The absence of high-gain amplifiers or external bias circuitry allows for ultra-low power operation — ideal for portable and battery-powered systems.

---

### **Limitations**

1. **Limited PSRR (Power Supply Rejection Ratio)**

   * The design is sensitive to fluctuations in supply voltage.
   * Noise from the power line can couple into the reference voltage, affecting precision.
   * **Improvement Tip:** Cascoding or op-amp-assisted biasing can be added to improve PSRR.

2. **Voltage Headroom Constraints**

   * Since all transistors must stay in the saturation region, the circuit requires a minimum operating voltage.
   * This limits its performance in sub-1V or low-voltage systems.

3. **Start-up Requirement**

   * SBCM can naturally settle at a **zero-current metastable state** after power-up.
   * A dedicated **start-up circuit** is required to “kick-start” current flow into the bias loop.

4. **Process Sensitivity**

   * The circuit performance may slightly vary across different fabrication corners (TT, SS, FF).
   * Careful transistor sizing and layout matching are essential to maintain consistent behavior.

---

## **3. Design and Pre-Layout Simulation**

The Bandgap Reference (BGR) circuit is designed and simulated using the **SkyWater SKY130 (130 nm)** open-source PDK.
This section outlines the **design targets**, **device models**, and **methodology** used for schematic-level design and verification before layout implementation.

---

### ⚙️ **3.1 Design Specifications**

| **Parameter**                         | **Target Value** |
| ------------------------------------- | ---------------- |
| Supply Voltage (VDD)                  | 1.8 V            |
| Temperature Range                     | –40°C to +125°C  |
| Power Consumption                     | < 60 µW          |
| Standby Current                       | < 2 µA           |
| Start-up Time                         | < 2 µs           |
| Temperature Coefficient (Vref Tempco) | < 50 ppm/°C      |

> These specifications ensure the BGR performs reliably across wide operating conditions while maintaining low power and high temperature stability.

---

### 🔩 **3.2 Device Models (from SKY130 PDK)**

#### **1. MOSFET Devices**

| **Parameter**                      | **NFET**                    | **PFET**                    |
| ---------------------------------- | --------------------------- | --------------------------- |
| Type                               | LVT (Low Threshold)         | LVT (Low Threshold)         |
| Voltage Rating                     | 1.8 V                       | 1.8 V                       |
| Threshold Voltage (V<sub>t0</sub>) | ~0.4 V                      | ~–0.6 V                     |
| Model                              | sky130_fd_pr__nfet_01v8_lvt | sky130_fd_pr__pfet_01v8_lvt |

> LVT transistors are selected to ensure reliable operation at low voltages and to maintain fast response during transient conditions.

---

#### **2. Bipolar Junction Transistor (BJT – PNP)**

| **Parameter**   | **Value**                         |
| --------------- | --------------------------------- |
| Current Density | 1 µA – 10 µA/µm²                  |
| β (Beta)        | ≈ 12                              |
| Area            | 11.56 µm²                         |
| Model           | sky130_fd_pr__pnp_05v5_W3p40L3p40 |

> PNP BJTs serve as the key devices for generating CTAT and PTAT voltages. Their predictable V<sub>BE</sub> behavior over temperature enables precise reference formation.

---

#### **3. Resistor (RPOLYH Type)**

| **Parameter**    | **Value**                          |
| ---------------- | ---------------------------------- |
| Sheet Resistance | ~350 Ω/sq                          |
| Tempco           | 2.5 Ω/°C                           |
| Available Widths | 0.35 µm, 0.69 µm, 1.41 µm, 5.37 µm |
| Model            | sky130_fd_pr__res_high_po          |

> High-resistivity poly resistors (RPOLYH) are chosen for their low noise, good matching, and minimal temperature drift.

---

###  **3.3 Circuit Design Process**

#### **Step 1: Bias Current Estimation**

Given:
[
P_{max} = 60~\mu W, \quad V_{DD} = 1.8~V
]
[
I_{total} = \frac{P_{max}}{V_{DD}} = \frac{60~\mu W}{1.8~V} = 33.3~\mu A
]

* Allocated per branch: **~10 µA**
* Start-up circuit current: **1–2 µA**

> The chosen current ensures low power while maintaining sufficient transconductance for stable biasing.

---

#### **Step 2: BJT Count in Branch 2**

* Fewer BJTs → lower resistance but poor current matching.
* More BJTs → improved matching but increased area and resistance.

 **Final Choice:** **8 parallel BJTs**
→ Provides a good balance between matching, area, and temperature linearity.

---

#### **Step 3: Calculation of R₁**

[
R_1 = \frac{V_T \cdot \ln(8)}{I} = \frac{26~mV \cdot \ln(8)}{10.7~\mu A} \approx 5~k\Omega
]

**Resistor Layout:**

* Width = 1.41 µm, Length = 7.8 µm
* Unit Resistance = 2 kΩ
* Configuration: 2 resistors in series, 2 sets in parallel → **effective ≈ 5 kΩ**

---

#### **Step 4: Calculation of R₂**

[
I_3 = I_2 = \frac{V_T \cdot \ln(8)}{R_1}
]
[
V_{R2} = I_3 \cdot R_2 = \frac{R_2}{R_1} \cdot (V_T \cdot \ln(8))
]

To achieve **zero temperature coefficient**:
[
Slope(V_{R2}) + Slope(V_{BE}) = 0
]

Given:

* Slope(V<sub>R2</sub>) ≈ (R₂ / R₁) × (ln(8) × 115 µV/°C)
* Slope(V<sub>BE</sub>) ≈ –1.6 mV/°C

→ **R₂ ≈ 33 kΩ**

**Resistor Layout:** 16 in series, 2 in parallel.

---

#### **Step 5: Self-Biased Current Mirror Design**

**PMOS Pair (MP1, MP2):**

* Operate in **saturation region**.
* Increase channel length to minimize λ (channel-length modulation).
* Final: L = 2 µm, W = 5 µm, M = 4

**NMOS Pair (MN1, MN2):**

* Operate in **deep subthreshold** for improved linearity.
* Increase L for better stability and reduced leakage.
* Final: L = 1 µm, W = 5 µm, M = 8

---

###  **3.3.1 Final Circuit Schematic**

<img width="1027" height="647" alt="BGR Final Circuit" src="https://github.com/user-attachments/assets/bcb02bda-5746-41d8-a6f6-3e4952a4ed85" />

---

###  **3.4 SPICE Netlist and Pre-Layout Simulation**

#### **Steps to Create a Netlist**

1. Create a new file with `.sp` extension using `vim`, `nano`, or `gvim`.
2. Add a title (first line is treated as a comment).
3. Include the **PDK library paths** and specify the **simulation corner** (e.g., TT, SS, FF).

![WhatsApp Image 2025-10-31 at 21 52 47_01e09db6](https://github.com/user-attachments/assets/ea25c29d-454e-40dc-a3c8-52f3d1f34c56)
![WhatsApp Image 2025-10-31 at 22 14 30_10c9c2d9](https://github.com/user-attachments/assets/f4120c83-86cb-47c6-8ad7-13a8bfe2764d)


### CTAT Voltage generation with Multiple BJT netlist

In this simulation we will check the CTAT voltage across the 8 parallel connected BJT
The slope is increasing in case of multiple BJTs.

### 3.4.2  PTAT Simulation

#### PTAT Voltage generation with VCVS
![WhatsApp Image 2025-10-31 at 22 24 55_e5268dd1](https://github.com/user-attachments/assets/49be7c29-981f-4593-95aa-51b5d0fce00a)
![WhatsApp Image 2025-10-31 at 22 32 19_525fb93e](https://github.com/user-attachments/assets/fbf28ae8-ad19-4675-a96b-c922f91f45fe)
![WhatsApp Image 2025-10-31 at 22 39 36_8a7ecc4e](https://github.com/user-attachments/assets/6e152c66-fa18-406c-b486-386559af4509)


## **3.4.3  Resistance Temperature Coefficient (Tempco)**

###  **Theory**

A resistor exhibits a **Positive Temperature Coefficient (PTC)**, meaning its resistance increases as temperature rises.
For a constant bias current ( I = 10~\mu A ), the voltage across the resistor can be expressed as:

[
V_R = I \times R(T)
]

where ( R(T) ) increases linearly with temperature. Hence, ( V_R ) also increases with temperature, forming a **Proportional-To-Absolute-Temperature (PTAT)** voltage.

In the **Bandgap Reference (BGR)** circuit, this PTAT voltage is combined with the **Complementary-To-Absolute-Temperature (CTAT)** voltage from the BJT to achieve **temperature compensation**, resulting in a nearly flat output reference voltage.

The simulation verifies the PTAT behavior of the resistor across temperature sweeps, confirming a **positive slope** for ( V_R ) vs. Temperature.

---

## **3.4.4  BGR Using Ideal Op-Amp**

After verifying the individual components, we simulate the complete **Bandgap Reference (BGR)** circuit using an **ideal Op-Amp**, implemented through a **Voltage-Controlled Voltage Source (VCVS)**.

###  **Theory**

* The **Op-Amp-based BGR** provides perfect feedback to ensure that the PTAT and CTAT voltages balance precisely.
* The resulting output curve should display the classic **“umbrella-shaped” behavior** — slightly rising with temperature before flattening around **1.2 V**.
* This ideal case helps in validating the correctness of our resistor ratios and transistor sizing before implementing the self-biased mirror.

###  **Expected Results**

* **Vref ≈ 1.20 V** (at ~27°C)
* **Minimal temperature drift**
* **Parabolic (umbrella-like) Vref vs Temperature curve**

### 🖼️ **Simulation Results**

<img width="600" src="https://github.com/user-attachments/assets/05041a0b-f0fc-482b-9a7d-f5b890645f5d" />  
<img width="600" src="https://github.com/user-attachments/assets/9ff6203a-208f-44a9-b8a4-546780f609c8" />  
<img width="600" src="https://github.com/user-attachments/assets/bc794b2c-1c02-4c65-899c-ea609da0e5a6" />  
<img width="600" src="https://github.com/user-attachments/assets/268dd8c9-dbed-4ca0-a683-5b4476feef49" />  
<img width="600" src="https://github.com/user-attachments/assets/fc181e1a-8162-4875-80dc-bcc43e602ccf" />  
<img width="600" src="https://github.com/user-attachments/assets/a5d2da18-1d79-467a-ac94-fe8c533f2b15" />  
*The simulation confirms ideal BGR behavior with the expected 1.2 V reference voltage and stable temperature response.*

---

## **3.4.5  BGR with Self-Biased Current Mirror**

After verifying ideal operation, the Op-Amp is replaced with the **Self-Biased Current Mirror (SBCM)** — our **proposed design**.
This configuration allows the circuit to generate its own biasing currents without external components, enhancing integration and reducing power consumption.

###  **Theory and Expectation**

* The SBCM replaces the ideal feedback amplifier by forming a **self-sustaining bias loop**.
* The design is expected to produce a similar **Vref vs. Temperature** characteristic as the ideal case, validating the internal biasing mechanism.
* The BGR is simulated across **different process corners (TT, SS, FF)** and **temperature ranges (–40°C to +125°C)** to ensure stability.

### **Corner Analysis Results**

| **Corner** | **Vref (Approx.)** | **Observation**                                |
| ---------- | ------------------ | ---------------------------------------------- |
| TT         | 1.203 V            | Nominal and stable output                      |
| SS         | 1.192 V            | Slight drop due to lower drive strength        |
| FF         | 1.214 V            | Slight increase due to higher transconductance |

### 🖼️ **Simulation Outputs**

<img width="600" src="https://github.com/user-attachments/assets/f9303e81-6a2c-4018-bfce-a52c592bc7ed" />  
<img width="600" src="https://github.com/user-attachments/assets/6189e9b8-e9fb-43f3-b931-5e414235491c" />  
<img width="600" src="https://github.com/user-attachments/assets/248220c0-914b-4dc0-a0d5-40b950ada458" />  
<img width="600" src="https://github.com/user-attachments/assets/b1057c56-df29-4e27-b523-7b7767d63d5e" />  
<img width="600" src="https://github.com/user-attachments/assets/c02da9e8-f50c-44bc-9c5b-0b1d1def0453" />  
<img width="600" src="https://github.com/user-attachments/assets/382df9ca-f271-4698-8789-de61d600fbea" />  
<img width="600" src="https://github.com/user-attachments/assets/e0ec7d2d-f7e7-47aa-adae-895bf468a1d1" />  
 *The self-biased current mirror–based BGR demonstrates a consistent 1.2 V reference across corners with excellent temperature stability and minimal drift.*

---

## **4. Layout Design**

After finalizing the pre-layout simulations, the next step is to **design the physical layout** of the Bandgap Reference (BGR) circuit using **Magic VLSI**.

Layout design translates the schematic into a **fabrication-ready geometry**, defining layers, interconnects, and device placement.

---

### **4.1 Getting Started with Magic**

**Magic** is an open-source VLSI layout tool used for **designing, editing, and verifying** IC layouts. It supports real-time **DRC (Design Rule Check)** and **LVS (Layout vs. Schematic)** verification.


Upon launching, two windows appear:

* **tkcon.tcl:** Command console
* **Toplevel window:** Layout canvas

---

###  **Essential Magic Commands**

| **Action**      | **Command / Shortcut** |
| --------------- | ---------------------- |
| Toggle Grid     | `g`                    |
| Zoom In / Out   | `z` / `Shift + z`      |
| Draw Box        | Left + Right Click     |
| Fill with Layer | `paint <layer_name>`   |
| Delete Layer    | `erase <layer_name>`   |
| Undo / Redo     | `u` / `Ctrl + r`       |
| Move / Copy     | `m` / `c`              |
| Rotate          | `r`                    |

---

### **4.2 Hierarchical Layout Design**

To improve modularity and reusability, the layout is organized into **three hierarchical levels:**

| **Hierarchy** | **Description**       | **Includes**                              |
| ------------- | --------------------- | ----------------------------------------- |
| **Level 1**   | Basic Cells           | NFET, PFET, BJT, Resistor                 |
| **Level 2**   | Functional Blocks     | NFETS, PFETS, PNP10, RESBANK, STARTERNFET |
| **Level 3**   | Top-Level Integration | Complete BGR layout (TOP)                 |

---

### **4.2.1 NFET Layout Design**

The **NFET array** is designed for compactness, symmetry, and current matching.

#### ⚙️ **Design Highlights**

* **Common Centroid** structure for mismatch reduction
* **Dummy devices** added at edges for uniform diffusion
* **P+ Guard Ring** for substrate noise isolation
* **Continuous diffusion regions** to reduce resistance
* **Aligned gate orientation** for consistent channel stress

---

### **4.2.2 PFET Layout Design**

The **PFET block** mirrors the same precision techniques to maintain accurate current mirroring.

####  **Design Highlights**

* **Symmetrical layout** with centroid pattern
* **N+ Guard Ring** to suppress coupling noise
* **Shared diffusion** to reduce area and parasitics
* **Dummy PFETs** to mitigate edge effects

---

### **4.2.3 Resistor Bank Layout (RESBANK)**

* Implemented using **high-resistivity poly resistors (RPOLYH)**.
* Arranged in **series-parallel combinations** to achieve precise R1 and R2 values.
* **Edge dummy resistors** improve uniformity and linearity.
* Tracks placed close to BJTs to ensure **thermal coupling**.

---

### **4.2.4 PNP10 Layout**

* **Eight PNP BJTs** arranged symmetrically for current density matching.
* **Dummy BJTs** used at array boundaries.
* **Guard ring** ensures substrate isolation and noise immunity.

---

### **4.2.5 Starter NFET Block**

Two long-channel NFETs (W = 1 µm, L = 7 µm) are used for the startup network.
They are surrounded by a **p+ guard ring** for isolation and noise control.

<img width="600" src="https://github.com/user-attachments/assets/2747a2f1-f13d-45aa-9444-ffa9f189ce3f" />  
<img width="600" src="https://github.com/user-attachments/assets/7529f8aa-cf34-4e58-a118-536652232e80" />  
<img width="600" src="https://github.com/user-attachments/assets/b7419784-b847-4b3e-924e-aaa920758e55" />  

---

## **4.3 Top-Level Layout Design**

The **top-level layout** integrates all submodules (NFET, PFET, resistor bank, BJT, and startup) into one coherent design.

#### **Key Features**

1. **Symmetrical Placement** – Ensures temperature uniformity and current matching.
2. **Compact Routing** – Reduces parasitic capacitances.
3. **Guard Ring Isolation** – Surrounds the entire circuit for noise protection.
4. **Proximity Grouping** – PTAT and CTAT elements placed close for thermal correlation.
5. **Hierarchical Instancing** – Subcells reused to maintain consistency.

####  **Layout Summary**

| Parameter   | Specification  |
| ----------- | -------------- |
| Tool Used   | Magic VLSI     |
| Technology  | SKY130         |
| DRC Status  | Clean          |
| File Name   | `bgr_top.mag`  |
| Layout Size | ~85 µm × 73 µm |

####  **Layout Visualization**

<img width="1163" height="774" src="https://github.com/user-attachments/assets/f8426b27-d2f7-4dee-bfe2-d1644d647747" />  

 *This top-level layout ensures electrical symmetry, thermal stability, and high reliability of the Bandgap Reference circuit.*

---

## 5. Post-Layout Simulation and Results

After completing the layout design using **Magic VLSI**, the final stage involves verifying the design through **post-layout simulations**.
These simulations include the effects of **parasitic resistances and capacitances** extracted from the layout to provide a realistic evaluation of the Bandgap Reference (BGR) circuit’s performance after fabrication.

---

### 5.1 Layout Extraction and Verification

Once the layout is completed, it is essential to ensure its **physical and electrical correctness** through a series of verification processes. The following verification steps are carried out using the **Magic** and **Netgen** tools from the SkyWater SKY130 PDK.

#### Design Rule Check (DRC)

The DRC verifies that all layout geometries adhere to the manufacturing design rules specified by the process technology.
A **clean DRC report** confirms that the layout is free from any violations and is suitable for fabrication.

#### Layout vs. Schematic (LVS)

The LVS verification compares the **layout-extracted netlist** (from Magic) with the **schematic-level netlist** used for Ngspice simulations.
A successful LVS ensures that the layout accurately reflects the schematic in terms of device connectivity and hierarchy.

#### Parasitic Extraction

After DRC and LVS, the layout is extracted to include parasitic resistances and capacitances.
These parasitics influence real-world circuit behavior and are necessary for **post-layout simulation**.

**Figure 5.1 – DRC and LVS Verification Outputs** 
<img width="1145" height="452" alt="Screenshot 2025-10-31 004007" src="https://github.com/user-attachments/assets/2ef1452c-77cc-42d6-a90c-89f782e5b1d0" /> <img width="942" height="705" alt="Screenshot 2025-10-31 004154" src="https://github.com/user-attachments/assets/ab5dfb95-d7f3-47c6-8e0f-69409b696b6d" />

---

### 5.2 Post-Layout Simulation Setup

The extracted netlist is simulated in **Ngspice** to verify the circuit’s electrical and thermal performance.
The same simulation conditions used during schematic analysis are applied here to facilitate direct comparison.

| **Parameter**      | **Simulation Condition**         |
| ------------------ | -------------------------------- |
| Supply Voltage     | 1.8 V                            |
| Temperature Range  | −40 °C to 125 °C                 |
| Process Corner     | Typical (TT)                     |
| Analyses Performed | DC, Transient, Temperature Sweep |
| Layout Tool        | Magic VLSI                       |
| Verification Tools | Netgen & Ngspice                 |
| Technology         | SkyWater SKY130                  |

**Figure 5.2 – Extracted BGR Layout (DRC Clean)** <img width="1163" height="774" alt="Screenshot 2025-10-31 003200" src="https://github.com/user-attachments/assets/f8426b27-d2f7-4dee-bfe2-d1644d647747" />

---

### 5.3 Post-Layout Simulation Results

#### (a) Output Reference Voltage

The **post-layout simulation** produces an output reference voltage of approximately **1.21 V**, closely matching the schematic simulation result.
The minor deviation observed is due to parasitic effects introduced by the physical interconnects and device layout.
---

#### (b) Temperature Stability

The **V<sub>REF</sub> vs Temperature** curve remains nearly flat over the entire operating range, confirming effective cancellation between CTAT and PTAT components.
The extracted **temperature coefficient (Tempco)** is approximately **45 ppm/°C**, indicating high thermal stability.

---

#### (c) Power Consumption

Even with post-layout parasitic effects, the total power consumption remains under **60 µW**, validating the design’s **low-power operation**.
This demonstrates that the self-biased current mirror architecture effectively maintains efficiency without sacrificing performance.

---

#### (d) Startup Behavior

The **startup circuit** ensures that the self-biased current mirror avoids the zero-current state during power-up.
Post-layout transient simulations confirm that the circuit stabilizes to its correct operating point within a few microseconds, ensuring robust startup under varying supply and temperature conditions.

---

### 5.4 Summary

The post-layout simulations confirm that the **Bandgap Reference (BGR)** circuit achieves its design objectives. The extracted and verified layout demonstrates:

* **Stable output voltage:** approximately 1.21 V
* **Excellent temperature compensation:** around 45 ppm/°C
* **Low power consumption:** below 60 µW
* **Reliable startup performance** under all corners

With successful **DRC** and **LVS** verification and consistent **post-layout results**, the design is now ready for **GDSII generation and tape-out**.

---


### AUTHOR- GYANVI NARAYAN UNDER GUIDANCE OF Prof. Santunu Sarangi in collaboration with VSD
















---
