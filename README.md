# CMOS-circuit-design-Spice-simulation

### 🔹 What is SPICE?

**SPICE (Simulation Program with Integrated Circuit Emphasis)** is a tool used to **simulate electronic circuits** at the transistor, gate, or system level. It numerically solves circuit equations to tell us how voltages and currents behave over time.

---

### 🔹 Why do we need SPICE simulations?

1. **Real-world behavior ≠ ideal Boolean logic**

   * Boolean equations (like $Y = AB + BC + CA$) assume **ideal 1s and 0s**.
   * In reality: transistors have **threshold voltages, resistances, parasitics, and delays**.
   * SPICE helps us see how the circuit **really behaves**.

---

2. **Check timing and delays**

   * CMOS gates take time to charge/discharge capacitors.
   * SPICE shows **rise time, fall time, propagation delay**, and helps optimize for **high-speed circuits**.

---

3. **Power consumption**

   * Modern chips (mobile, IoT, AI) are **power-limited**.
   * SPICE gives **dynamic power (switching), leakage power, short-circuit current**.
   * Helps in **low-power design**.

---

4. **Noise and reliability**

   * Crosstalk, supply noise, process variations, temperature changes → can disturb logic.
   * SPICE helps check **noise margins** and **robustness**.

---

5. **Before fabrication (tape-out)**

   * Fabricating a chip costs **lakhs to crores 💸**.
   * SPICE ensures the design works before sending it to fab → avoids **very expensive mistakes**.

---

### 🔹 Summary (one-liner)

We need SPICE because **mathematical logic tells us what should happen, but SPICE shows us what actually happens in silicon**—including speed, power, and real-world imperfections.

---

Do you want me to also show you an **example SPICE simulation result** (like a CMOS inverter waveform) so you can visually see why it’s important?

<img width="1257" height="712" alt="image" src="https://github.com/user-attachments/assets/7db0adbe-91f3-410b-9ecf-f485c4c96f66" />

Perfect 👍 Let’s break this diagram step by step — it’s about **Power Aware Clock Tree Synthesis (CTS)** and how buffer trees are built.

---

## 🔹 What is shown?

* **Two Delay Tables** (for buffer `CBUF1` and buffer `CBUF2`)
* **Buffer tree structure** (two levels of buffering)
* **Capacitance loading analysis** at each node

---

## 🔹 1. Delay Tables (top part)

* Delay of a buffer depends on:

  * **Input slew** (how fast the input transition is, in ps → 20ps, 40ps, 60ps, 80ps)
  * **Output load** (the capacitance it drives, in fF → 10fF, 30fF … 110fF)

* Each entry `x1, x2 … y24` represents the **propagation delay** under that condition.

  * Left table = buffer type `CBUF1`
  * Right table = buffer type `CBUF2`

This information comes from **SPICE simulation & library characterization**.

---

## 🔹 2. Buffer Tree (bottom diagram)

We are building a **2-level clock buffer tree**:

* **Level 1**:
  Input drives 2 buffers (`Cbuf1` and `Cbuf2`) in parallel → nodes **A** and **C**.

* **Level 2**:
  Each buffer (`Cbuf1` and `Cbuf2`) drives another buffer (shown as “2”) which then drives capacitors $C1, C2, C3, C4$.

So, the signal is **fanned out gradually** instead of one buffer trying to drive everything.

---

## 🔹 3. Observations (yellow box)

* **2 levels of buffering**
* At each level → each node drives the **same load** (balanced tree).
* Identical buffers are used at the same level (to reduce skew and simplify design).

This ensures the **clock signal arrives everywhere at nearly the same time** (low skew).

---

## 🔹 4. Capacitance Calculation (right side equations)

We assume:

* $C1 = C2 = C3 = C4 = 25 \, \text{fF}$
* $Cbuf1 = Cbuf2 = 30 \, \text{fF}$ (input capacitance of the buffer)

Now calculate effective capacitances:

* **Node A (output of first buffer, drives buffer 2 + load C1 + C2)**
  → total = $Cbuf2 + C1 + C2 = 30 + 25 + 25 = 60 \, \text{fF}$

* **Node B (output of second-level buffer connected to A, drives C1 + C2)**
  → total = $25 + 25 = 50 \, \text{fF}$

* **Node C (output of second-level buffer connected to C, drives C3 + C4)**
  → total = $25 + 25 = 50 \, \text{fF}$

---

## 🔹 5. Why this matters (Power Aware CTS)?

* If one buffer tries to drive **too much load**, delay ↑ and power ↑.
* Buffer trees **split the load**, so each stage drives a reasonable capacitance.
* This reduces **delay, skew, and power consumption**.

---

✅ **In short:**
The figure shows how a **balanced two-level clock buffer tree** is designed. Delay tables give the timing behavior under different loads and slews, and by distributing load capacitance evenly, the clock reaches all sinks with **equal delay** and **lower power**.

<img width="927" height="183" alt="image" src="https://github.com/user-attachments/assets/7d51ddb5-03cb-4c68-8b86-28b9ded2a5d0" />

### L2 Introduction to basic element in Circuit design – NMOS

<img width="1172" height="477" alt="image" src="https://github.com/user-attachments/assets/080a2d69-13b8-4553-875d-e07d6d2a937e" />

<img width="1322" height="477" alt="image" src="https://github.com/user-attachments/assets/3eae77e5-7385-4099-a340-2c9c2efa3f84" />

### L3 Strong inversion and threshold voltage

<img width="1357" height="542" alt="image" src="https://github.com/user-attachments/assets/77d33baa-f710-45c4-914d-c188d31f4372" />

<img width="1220" height="526" alt="image" src="https://github.com/user-attachments/assets/0f61d448-9a2e-445c-af0e-4e3022d30b23" />
Great question 👍
This figure shows an **NMOS transistor** in two different scenarios, focusing on the **body (substrate) connection** and its effect on operation.

---

### **Scenario 1 (Left) – Body tied to Ground, Vsb = 0**

* **Source (S):** at ground (0 V).
* **Drain (D):** at some voltage (usually positive w\.r.t source).
* **Gate (G):** given positive bias $V_{GS}$.
* **Body (B, substrate):** tied to ground.

Here:

* Since **source and body are at the same potential**, $V_{SB} = V_S - V_B = 0$.
* The **threshold voltage** is the intrinsic threshold, i.e.

  $$
  V_T = V_{T0}
  $$

  (no body effect).
* When $V_{GS} > V_T$, an inversion layer (n-channel) forms under the gate, connecting the n+ source and drain → current flows if $V_{DS} > 0$.

**Conclusion:** This is the simplest case, no body bias → **normal NMOS operation**.

---

### **Scenario 2 (Right) – Body tied to Source**

* **Source (S):** connected to body (p-substrate).
* **Drain (D):** at positive voltage.
* **Gate (G):** still has positive bias.

Here:

* The **body and source are shorted**, so $V_{SB} = 0$ always.
* Since $V_{SB}=0$, again no body effect, and $V_T = V_{T0}$.
* Advantage: ensures that **source-body junction is not forward biased**.
* This connection is common in **practical IC design**, because it prevents the parasitic diode between source and body from turning on (since both are at same potential).

**Conclusion:** This ensures safe operation. The device behaves similarly to Scenario 1 but guarantees **no unintended forward-bias of body-source junction**.

---

✅ **Key Difference Between Both Scenarios**

* In **Scenario 1**, the body is fixed at ground, so body effect could appear if source rises above 0 V.
* In **Scenario 2**, body tied to source ensures $V_{SB} = 0$ always, eliminating threshold shift and avoiding diode conduction.

### L4 Threshold voltage with positive substrate potential

<img width="680" height="498" alt="image" src="https://github.com/user-attachments/assets/6ac59b47-c6e5-405b-9b66-50833c99e8b9" />

<img width="1387" height="650" alt="image" src="https://github.com/user-attachments/assets/3ef444c7-26b4-41d7-bf7b-0742f0ef08e4" />

<img width="683" height="328" alt="image" src="https://github.com/user-attachments/assets/00a4393a-8f2a-48c7-a066-718eadc72dba" />

<img width="523" height="412" alt="image" src="https://github.com/user-attachments/assets/3300940a-83ce-45b9-9270-31e62d7718e9" />

## NMOS resistive region and saturation region of operation

### L1 Resistive region of operation with small drain-source voltage

<img width="1333" height="580" alt="image" src="https://github.com/user-attachments/assets/92713634-2716-4380-9543-df89717ae975" />

<img width="797" height="605" alt="image" src="https://github.com/user-attachments/assets/2bed6305-c7e3-44f0-9d36-80425b565edc" />

### L2 Drift current theory

<img width="1292" height="726" alt="image" src="https://github.com/user-attachments/assets/da9f1b3b-e9dd-4531-aa2f-831a59548b42" />

### L3 Drain current model for linear region of operation

refer EDC notes for current equation

### L4 SPICE conclusion to resistive operation

<img width="713" height="470" alt="image" src="https://github.com/user-attachments/assets/a484ae80-b761-46e3-8eb9-411bf01bc9b3" />

<img width="537" height="180" alt="image" src="https://github.com/user-attachments/assets/ea55987b-7df1-42e4-8530-0c52303f15b9" />

<img width="632" height="163" alt="image" src="https://github.com/user-attachments/assets/65cd4c8a-8aa4-4e3a-9763-19720ec1d9a6" />

<img width="1303" height="517" alt="image" src="https://github.com/user-attachments/assets/6f349409-fafa-469a-8243-ab93b1a8d0e7" />

<img width="496" height="126" alt="image" src="https://github.com/user-attachments/assets/18f7ab00-5c74-49ad-9603-fac03536c6a9" />

### L5 Pinch-off region condition

<img width="1276" height="648" alt="image" src="https://github.com/user-attachments/assets/376c91e6-3fc9-4767-9b9a-a9a3f210b93f" />

<img width="410" height="142" alt="image" src="https://github.com/user-attachments/assets/cad9e704-1639-497a-8712-a5a50654c483" />

### L6 Drain current model for saturation region of operation

<img width="1268" height="612" alt="image" src="https://github.com/user-attachments/assets/a0102ca2-7766-4a37-878a-8ca10e3db8b6" />

Perfect 👍 Let’s derive the **drain current model for an NMOS in saturation** (also called the **active region**).

---

### **Condition for Saturation**

For an NMOS:

$$
V_{DS} \geq V_{GS} - V_T
$$

* $V_{GS}$ = Gate-to-source voltage
* $V_{DS}$ = Drain-to-source voltage
* $V_T$   = Threshold voltage

---

### **Drain Current Expression**

1. **Without channel length modulation (ideal case):**

$$
I_D = \frac{1}{2}\,\mu_n C_{ox}\,\frac{W}{L}\,(V_{GS} - V_T)^2
$$

Where:

* $\mu_n$ = electron mobility
* $C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}$ = gate-oxide capacitance per unit area
* $W, L$ = channel width and length

This is the **square-law model**.

---

2. **With body effect included (threshold shift):**
   If source and body are not at the same potential:

$$
V_T = V_{T0} + \gamma \left(\sqrt{2\phi_F + V_{SB}} - \sqrt{2\phi_F}\right)
$$

where

* $V_{T0}$ = zero-bias threshold voltage
* $\gamma$ = body effect coefficient
* $\phi_F$ = Fermi potential
* $V_{SB}$ = Source-to-body bias

---

3. **With channel length modulation (real device):**
   When $V_{DS}$ increases further, the effective channel shortens. This is modeled by factor $(1 + \lambda V_{DS})$:

$$
I_D = \frac{1}{2}\,\mu_n C_{ox}\,\frac{W}{L}\,(V_{GS} - V_T)^2 \,(1 + \lambda V_{DS})
$$

where

* $\lambda$ = channel length modulation parameter
* This makes the $I_D$ vs $V_{DS}$ curve in saturation **slightly increasing**, not perfectly flat.

---

✅ **Final Model (General Saturation Region):**

$$
I_D = \frac{1}{2}\,\mu_n C_{ox}\,\frac{W}{L}\,(V_{GS} - V_{T})^2\,(1 + \lambda V_{DS})
$$

## Introduction to SPICE

### L1 Basic SPICE setup
Great question 👍 Let me break it down clearly.

**SPICE simulation** = **Simulation Program with Integrated Circuit Emphasis**

It’s a standard tool used in **electronics and semiconductor industries** to analyze and predict how circuits and devices will behave before they are fabricated.

---

### 🔹 What it does

* Uses **mathematical models** of electronic devices (MOSFETs, BJTs, diodes, resistors, capacitors, etc.)
* Simulates how a circuit behaves under different inputs, voltages, currents, and environmental conditions.
* Helps engineers verify **functionality, performance, and reliability** without costly fabrication.

---

### 🔹 Types of SPICE Simulations

1. **DC Analysis** → Finds steady-state voltages/currents (e.g., (I_d)-(V_{ds}) curve of MOSFET).
2. **AC Analysis** → Frequency response, gain, bandwidth.
3. **Transient Analysis** → Time-domain behavior (e.g., inverter switching, delay).
4. **Noise Analysis** → Evaluates circuit noise.
5. **Monte Carlo / Parametric Analysis** → Variation effects (process, supply, temperature).

---

### 🔹 Why it’s important

* Saves **time and cost** by predicting behavior before fabrication.
* Essential in **VLSI design**, analog circuits, digital circuits, and semiconductor device characterization.
* Industry-standard for validating CMOS, analog ICs, RF circuits, and power electronics.

---

👉 In simple words:
**SPICE simulation is like a virtual lab for circuits — you test the design on computer before building it physically.**

<img width="1296" height="723" alt="image" src="https://github.com/user-attachments/assets/33e7e523-4da0-4579-b56d-c7f88254dadc" />
we have background knowledge of above picture.
<img width="497" height="556" alt="image" src="https://github.com/user-attachments/assets/a87ff4ed-d2c0-4d40-9b40-3b863f63648c" />
the one's highlighted in yellow are technology constants. these come costant from foundary.

<img width="918" height="587" alt="image" src="https://github.com/user-attachments/assets/6f9f5504-bffc-49b7-91ed-df2b1b196ea7" />

<img width="657" height="512" alt="image" src="https://github.com/user-attachments/assets/c9c4c4ee-df97-4f7a-ba9d-43af1609b77b" />
on feeding spice model parameter and spice netlist in software weget given graph.
<img width="1237" height="621" alt="image" src="https://github.com/user-attachments/assets/47a58055-8703-47d6-88a8-1c2dd5914b9d" />
it is a spice netlist as shown on left side. we need to write program like c or c++ something.


## L2 Circuit description in SPICE syntax

now the task is to write SPICE netlist in correct syntax.
1st step is to define the nodes.
<img width="657" height="457" alt="image" src="https://github.com/user-attachments/assets/bc7ab43a-df5a-4869-a083-c6267db6a48c" />

<img width="1187" height="538" alt="image" src="https://github.com/user-attachments/assets/cd54481a-3c6a-441c-a3a0-80744527e769" />

<img width="1188" height="402" alt="image" src="https://github.com/user-attachments/assets/c55294f1-26cb-4bd8-9787-c4402a69bd3b" />

<img width="1202" height="373" alt="image" src="https://github.com/user-attachments/assets/a9f7e00c-a499-4387-bc95-a94d964adc1f" />

<img width="1180" height="402" alt="image" src="https://github.com/user-attachments/assets/765fb9bc-3f63-4739-ad24-161f30618cd5" />

<img width="1171" height="413" alt="image" src="https://github.com/user-attachments/assets/bb760328-88fb-4d46-9ff7-eaa1aacdf883" />

<img width="1193" height="412" alt="image" src="https://github.com/user-attachments/assets/fc94a047-4a3f-4308-9139-bea73efab32b" />

<img width="1177" height="388" alt="image" src="https://github.com/user-attachments/assets/fc341131-491f-4de0-a31a-055fe765e8fc" />
if we notice L=1.2 micron. we are starting with long channel mosfet to bring out the difference clearly.

<img width="1171" height="438" alt="image" src="https://github.com/user-attachments/assets/4dda6a32-9742-48ac-bec8-7fcd4743d3ff" />

<img width="1167" height="417" alt="image" src="https://github.com/user-attachments/assets/548b9aaa-0a79-4845-9bfb-472b556ea538" />
anything starting with V is the voltage source.

<img width="1167" height="378" alt="image" src="https://github.com/user-attachments/assets/d9477edf-7318-42f0-8c6e-f2596fe94edf" />

<img width="1173" height="395" alt="image" src="https://github.com/user-attachments/assets/199eeafd-de9d-4fa3-9cdc-ad1a533a9368" />

This is the netlist for nmos which we are trying to simulate. Now we'll write technology file.it's basically adding all the constants in technology file.

## L3 Define technology parameters

