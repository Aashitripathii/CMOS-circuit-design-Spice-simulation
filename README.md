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
Good question 👍 Let’s go step by step.

---

## 🔹 Technology Parameters (in VLSI / Semiconductor Context)

Technology parameters are the **fundamental characteristics provided by a semiconductor technology node or process** that define how devices (like MOSFETs) and circuits behave.

They are part of the **Process Design Kit (PDK)** and are used in **SPICE models, design rules, and simulations**.

---

### 🔹 Common Technology Parameters

1. **Feature size (Technology node, e.g., 180 nm, 65 nm, 7 nm)**

   * Defines the minimum length of MOSFET gate (channel length).
   * Smaller nodes → higher speed, lower power, higher density.

2. **Oxide thickness (Tox)**

   * Thickness of the gate oxide.
   * Affects gate capacitance, threshold voltage, leakage.

3. **Threshold voltage (Vth)**

   * Minimum gate voltage needed to turn MOSFET ON.

4. **Mobility (μn, μp)**

   * Electron and hole mobilities in the channel.
   * Impacts current drive and speed.

5. **Supply voltage (Vdd)**

   * Nominal operating voltage of the technology (e.g., 1.8 V, 1.2 V).

6. **Junction depth (Xj)**

   * Depth of source/drain diffusion regions.

7. **Channel length modulation parameter (λ)**

   * Represents how current increases slightly in saturation due to finite output resistance.

8. **Capacitances (Cgs, Cgd, Cox, Csb)**

   * Intrinsic capacitances that affect delay, switching, and frequency response.

9. **Process variations / Corners**

   * TT, SS, FF, etc. (typical, slow, fast devices).
   * Used to check yield and robustness.

10. **Leakage currents (Ioff, sub-threshold slope, DIBL)**

* Define standby power and short-channel effects.

---

### 🔹 Why they matter

* Used in **SPICE models** to simulate transistors.
* Determine **speed, power, area, and yield** of chips.
* Critical for **device physics, circuit design, and fabrication planning**.

---

👉 In simple words:
**Technology parameters describe the physical and electrical properties of a given semiconductor process. They form the “rules of the game” for circuit designers.**

<img width="538" height="432" alt="image" src="https://github.com/user-attachments/assets/398bcde1-6642-4f32-afb6-a74504eedcb4" />
Both should be same.
<img width="1286" height="710" alt="image" src="https://github.com/user-attachments/assets/b7a72bbe-2bc3-46c3-8809-ecc91c0ab03e" />
<img width="1265" height="717" alt="image" src="https://github.com/user-attachments/assets/d6fc5ba0-998e-4ce3-8661-88c027d8a4bd" />

call the file in top level netlistas marked in below diagram.
<img width="630" height="497" alt="image" src="https://github.com/user-attachments/assets/2e2e2304-6c96-4ffd-baa5-fee32f08349c" />

<img width="1282" height="618" alt="image" src="https://github.com/user-attachments/assets/cd4c81e7-5256-45e9-8b45-5fb9bc99129f" />

## L4 First SPICE simulation
<img width="1095" height="476" alt="image" src="https://github.com/user-attachments/assets/9f107544-a774-416e-a8dd-0f0215504ca5" />

Nice — I’ll walk through **every command shown in your screenshot** and explain the purpose of each file and directory so you know exactly what everything means and what to do next.

---

# Terminal prompt

`vsduser@vsdsquadron:~$`

* **vsduser** = username on the VM.
* **vsdsquadron** = hostname of the machine.
* **`~`** = current working directory is the user’s home directory.
  The prompt format is `username@hostname:current_dir$`.

---

# Commands (step-by-step) and what they do

1. **`cd`**

   * With no argument: `cd` returns you to your home directory (`~`). It just ensures you are in your home folder.

2. **`git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop`**

   * This downloads (clones) the GitHub repository at that URL into a new folder named `sky130CircuitDesignWorkshop` in your current directory.
   * The output lines you saw mean:

     * **Enumerating/Counting objects** — git is listing all files/commits in the repo.
     * **Compressing objects / Receiving objects** — data is transferred and decompressed on your machine.
     * **Resolving deltas** — git applies compressed changes to rebuild file history.
   * After these messages finish, the repo is present locally.

3. **`cd sky130CircuitDesignWorkshop/design/`**

   * Change directory into the `design` folder inside the cloned repo. This folder contains the SPICE exercises and a local sky130 PDK folder used in the workshop.

4. **`ls`** (listing of files you saw)

   * The output (sample names visible) includes several `.spice` files and a directory `sky130_fd_pr`. Example file names from the screenshot:

     * `day1_nfet_idvds_L2_W5.spice`
     * `day2_nfet_idvgs_L015_W039.spice`
     * `day3_inv_tran_Wp084_Wn036.spice`
     * `day3_inv_vtc_Wp084_Wn036.spice`
     * `day4_inv_noisemargin_wp1_wn036.spice`
     * `day5_inv_devicevariation_wp7_wn042.spice`
     * `day5_inv_supplyvariation_Wp1_Wn036.spice`
     * `sky130_fd_pr/` (directory)

   **What these names mean (typical convention):**

   * `dayX_...` — exercises for day X of the workshop.
   * `nfet_idvds` — NFET drain current vs drain-source voltage characterization (`I_d` vs `V_ds`).
   * `nfet_idvgs` — NFET drain current vs gate-source voltage (`I_d` vs `V_gs`).
   * `inv_vtc` — inverter Voltage Transfer Characteristic (VTC) sweep.
   * `inv_tran` — transient switching simulation of an inverter (time-domain).
   * `noisemargin`, `devicevariation`, `supplyvariation` — simulations that test robustness (noise margins, process/device variation, supply voltage variation).
   * The `L` and `W` or `Wp/Wn` parts indicate transistor **length** and **width** values used in the netlist (units depend on the netlist — check the file to see whether L=2 means 2 µm or 2 units).

5. **`cd sky130_fd_pr/`**

   * You entered the folder named `sky130_fd_pr`. This is the PDK/library folder (SkyWater 130nm frontend/process resources) included for the workshop.

6. **`ls` inside `sky130_fd_pr`** -> output:

   * `cells`
   * `LICENSE`
   * `models`
   * `README.rst`

   **What these are:**

   * **`cells/`** — subcircuits or subckt files for standard cells or device-level subcircuits (e.g., NFET/PFET subckt definitions used in the `.spice` netlists).
   * **`models/`** — SPICE model files (transistor model cards, process corners, temperature tables). These are the `.model` or `.lib` files the netlists `.include` to get transistor electrical behavior.
   * **`LICENSE`** — legal/license text for the repo or PDK files (read this to know allowed usage).
   * **`README.rst`** — human-readable instructions for how to run the exercises, prerequisites, or notes for the PDK.

7. **`cd cells/`** then **`ls`** -> shows:

   * `nfet_01v8`
   * `pfet_01v8`

   **Meaning:**

   * These are files (or directories) containing the **NFET and PFET device/subckt definitions** for a 1.8 V device flavor. They usually contain `.subckt` definitions or netlists that the dayX `.spice` files call via `.include` so the netlists use the correct transistor models and device geometry.

---

# How to inspect these files (useful commands)

* `less README.rst` — read instructions for the repo/workshop.
* `head -n 40 day1_nfet_idvds_L2_W5.spice` — view first 40 lines of the netlist (quick peek).
* `grep -i ".include" -n *.spice` — find which model / subckt files the netlists include.
* `cat sky130_fd_pr/models/sky130.lib.spice | head -n 30` — view the model library header (example; actual filename may vary).
* `file nfet_01v8` or `ls -l nfet_01v8` — check whether `nfet_01v8` is a file or directory and view permissions.

---

# How to run a SPICE exercise (basic workflow)

1. **Install ngspice** (if not present):

   ```bash
   sudo apt update
   sudo apt install ngspice
   ```

2. **Run interactively**:

   ```bash
   ngspice day1_nfet_idvds_L2_W5.spice
   ```

   * ngspice will parse the netlist and run whatever `.dc`, `.tran`, or `.op` directives exist.
   * Inside ngspice you can `plot` or `display` node voltages / currents (for example `plot v(out)` or `plot i(vdd)` depending on netlist node names). Many provided netlists include `.measure` lines that print numeric results to stdout.

3. **Run in batch (non-interactive)**:

   ```bash
   ngspice -b day3_inv_tran_Wp084_Wn036.spice -o day3_out.log
   ```

   * `-b` runs ngspice in batch mode and `-o` writes a log file.

4. **View waveforms** (if the netlist writes a raw/VCD file):

   * Install GTKWave: `sudo apt install gtkwave`
   * Open the raw file: `gtkwave filename.raw`

> **Note:** The netlists usually `.include` model files (from `sky130_fd_pr/models/`) and `.include` subckt files (from `sky130_fd_pr/cells/`). If ngspice cannot find included files, adjust relative paths or run from the `design` directory.

---

# Typical contents inside a `.spice` file (what to expect)

Open any of the `.spice` files and you will typically see:

* `.title` — netlist title
* `.include ../sky130_fd_pr/models/sky130.lib.spice` — include model library
* Component lines: `M1 drain gate source bulk nfet_01v8 W=... L=...` or `.subckt inv ...`
* Voltage sources: `Vdd vdd 0 DC 1.8` or `Vg gate 0 PULSE(...)` for transient
* Analysis directives: `.dc Vd 0 1.8 0.01` or `.tran 1n 100n` or `.op`
* `.measure` statements to extract parameters (like threshold, Idsat)
* `.end`

Open the file to confirm units (µm vs. nm) and how L/W are defined.

---

# Quick interpretation of the day files (practical summary)

* **Day 1:** NFET Id vs Vds sweep — device output characteristics.
* **Day 2:** NFET Id vs Vgs sweep — transfer characteristics (threshold extraction).
* **Day 3:** CMOS inverter transient (switching) and VTC — measure delay and switching point.
* **Day 4:** Noise-margin evaluation — compute VIH, VIL, noise margins.
* **Day 5:** Robustness tests — device variation sweeps (different W/L), supply variation (Vdd changes) — used to see effect on performance/yield.

---

# Next steps I recommend (practical)

1. Open `README.rst` first — it usually tells required tools, order of exercises and commands to run.

   ```bash
   less README.rst
   ```
2. Check which files are included by netlists:

   ```bash
   grep -n ".include" -R .
   ```
3. Run one simple exercise to confirm the setup:

   ```bash
   ngspice day1_nfet_idvds_L2_W5.spice
   ```
4. If you want waveform GUI: ensure `gtkwave` is installed and check if the netlist produces `.raw` files, then open them.

---

<img width="1557" height="203" alt="image" src="https://github.com/user-attachments/assets/fca4cce6-95ab-4341-b596-60e192346c08" />
these are all the library files of nfet.
<img width="1018" height="105" alt="image" src="https://github.com/user-attachments/assets/4305cfed-aaa2-46de-8e44-0a91b7d6c4d2" />
it contains all the model parameters.
<img width="1055" height="857" alt="image" src="https://github.com/user-attachments/assets/8f1015f4-bd3b-4557-9e9f-4183762e4535" />
now corner spicify
<img width="346" height="117" alt="image" src="https://github.com/user-attachments/assets/c2919a88-92d8-4250-96e6-eaa5b66d7f26" />
<img width="1852" height="986" alt="image" src="https://github.com/user-attachments/assets/6a19841c-dfa6-4a7f-9671-f390b3e5a03c" />
<img width="1856" height="986" alt="image" src="https://github.com/user-attachments/assets/e65647a7-9c6a-450d-a188-21518dc22e2e" />

it contains different W and L values. 
<img width="1573" height="483" alt="image" src="https://github.com/user-attachments/assets/dab146ff-720f-415d-9e75-0a083b027d85" />

<img width="1847" height="996" alt="image" src="https://github.com/user-attachments/assets/892d7319-372e-4d31-9dc6-9668f904ea85" />

it contains all the lib files for nfet and pfet at different corners. 

<img width="1200" height="950" alt="image" src="https://github.com/user-attachments/assets/2a2143ce-1cc5-48a1-9671-d4d2c1b8443f" />
tt means typical corner
sky......is the model name. syntax is drain gate source and bulk. source and bilk is 0 0. drain is Vdd. selected w and L value. supply voltage is 1.8V.
H  ere we are sweeping Vds from 0 to 1.8 with step of 0.1. and Vgs as 0.2. So here we are doing Id vs Vds curve. now we'll run it. To run this type ngspice day1....
<img width="1456" height="220" alt="image" src="https://github.com/user-attachments/assets/98d8b0b9-b979-41f1-9275-0c7f1b0b5be2" />
<img width="1492" height="531" alt="image" src="https://github.com/user-attachments/assets/8ea90efd-ba08-4827-a0a1-c84c5f26f5e9" />
we have installed ngspice.
<img width="1860" height="1002" alt="image" src="https://github.com/user-attachments/assets/26871306-7323-4f73-8349-1d424b8f9eb6" />
<img width="1295" height="972" alt="image" src="https://github.com/user-attachments/assets/ed49e029-10a9-4616-b34b-23168c83efbe" />
to plot the graph give command as shown above.
<img width="482" height="427" alt="image" src="https://github.com/user-attachments/assets/ebb950a2-ee4a-42d8-b3cb-c0e44e406c90" />
<img width="1850" height="988" alt="image" src="https://github.com/user-attachments/assets/770933a5-3a1e-4577-990e-9b7300ff1ea3" />
this is Id Vs Vds at different Vgs values. 
It shows linear behaviour.
<img width="1137" height="1022" alt="image" src="https://github.com/user-attachments/assets/98a569c6-4591-4b0f-ad13-cb4ecc69ba22" />
to get the coordinate at specific point click on graph left click. 

## L5 SPICE Lab with sky130 models

same as day 4

# Day 2 - Velocity saturation and basics of CMOS inverter VTC
## SPICE simulation for lower nodes and velocity saturation effect
## L1 SPICE simulation for lower nodes

<img width="1368" height="713" alt="image" src="https://github.com/user-attachments/assets/1a9c89c6-0d0a-4e05-9b53-38d386246598" />
Read notes (EDC).
<img width="1432" height="767" alt="image" src="https://github.com/user-attachments/assets/68e9708b-bcd7-48d5-8704-33fa1fb796f4" />

## L2 Drain current vs gate voltage for long and short channel device

<img width="1397" height="713" alt="image" src="https://github.com/user-attachments/assets/2a66e8b6-bbca-4a2f-b282-3072acd32030" />
<img width="1391" height="727" alt="image" src="https://github.com/user-attachments/assets/fd81a87b-43f9-4bc2-a510-86eb5634eb2f" />
<img width="1407" height="717" alt="image" src="https://github.com/user-attachments/assets/7b7b0852-5bea-401d-8657-86faf85c0fae" />

## L3 Velocity saturation at lower and higher electric fields
<img width="932" height="458" alt="image" src="https://github.com/user-attachments/assets/e92d8840-1a8d-447d-926a-ba242bb37cd3" />
FOR short channel effect.
<img width="952" height="436" alt="image" src="https://github.com/user-attachments/assets/d917a085-36bc-4e76-9725-b77a37d371bb" />

<img width="1027" height="472" alt="image" src="https://github.com/user-attachments/assets/89f61828-3277-4a4f-a8ad-7b34be3bd5df" />
<img width="765" height="486" alt="image" src="https://github.com/user-attachments/assets/87782a66-f178-47c8-969e-0eeeddffc9fb" />

<img width="957" height="501" alt="image" src="https://github.com/user-attachments/assets/c2734726-20a3-403e-bfb0-8ff52b5b6adb" />

## L4 Velocity saturation drain current model

<img width="1067" height="660" alt="image" src="https://github.com/user-attachments/assets/61a2beb5-9194-4566-9460-19c0033e045d" />

<img width="958" height="560" alt="image" src="https://github.com/user-attachments/assets/c57fde23-075f-43bc-b25c-2094ad8c7ce1" />

## L5 Labs Sky130 Id-Vgs

<img width="1218" height="181" alt="image" src="https://github.com/user-attachments/assets/714e7eb2-226a-4cc0-8b69-4ff9d7d9d522" />
<img width="1210" height="933" alt="image" src="https://github.com/user-attachments/assets/e38f4c26-bf7c-42e0-8b42-210bfe779c31" />
doing dc simulation .dc
now run this. type ngspice file name.
<img width="1593" height="361" alt="image" src="https://github.com/user-attachments/assets/1de6f38c-b0fe-4dc9-9a0f-f90c51f26682" />
<img width="1593" height="830" alt="image" src="https://github.com/user-attachments/assets/c96501c5-7fc0-4bee-8cdb-1948864134ad" />
<img width="1846" height="1020" alt="image" src="https://github.com/user-attachments/assets/156834ee-477c-4136-84f5-cba015e8db11" />
<img width="1852" height="962" alt="image" src="https://github.com/user-attachments/assets/316b2cf7-16b3-403b-a807-f6315f870855" />
click on the graph we'll know the coordinates.
<img width="1850" height="260" alt="image" src="https://github.com/user-attachments/assets/7a361afe-cda7-439f-94e0-9e3ae5b750d0" />
<img width="1133" height="418" alt="image" src="https://github.com/user-attachments/assets/a38b9592-3902-431a-9071-7a82dc067a74" />
<img width="1780" height="907" alt="image" src="https://github.com/user-attachments/assets/0b57e7ee-81e2-407f-a0f5-c0a6b22dcf11" />
The above plot is Id vs Vds for different values of Vgs. We can see for lower values of Vgs it is showing quadratic behaviour and for higher values of Vgs it is showing Linear behaviour. Now if want to see the peak current for Vgs=1.8V, just 'press' left click on mouse at Vgs=1.8V.
Now let us observe Id vs Vgs
<img width="1245" height="292" alt="image" src="https://github.com/user-attachments/assets/6228086f-1198-446d-85e1-e2a4b4d43b17" />
<img width="665" height="120" alt="image" src="https://github.com/user-attachments/assets/41a536c1-dbda-400d-8085-bbfb35cf0a18" />
<img width="1810" height="1051" alt="image" src="https://github.com/user-attachments/assets/9bf78c3f-8c3f-482c-b461-a90041885da0" />
In the above graph we can see that, due to short channel effect we are seeing a linear behaviour for higher Vgs and Vds being constant.

## L6 Labs Sky130 Vt

Now we will calculate Threshold Voltage Vt for Id vs Vgs curve.
<img width="407" height="373" alt="image" src="https://github.com/user-attachments/assets/6bcbea19-1ba2-4d5d-8341-8619bc36979c" />

In the curve we can see that Vt is the value when current increases drastically for small change in Vgs. To calculate we will draw tangent on the curve and see where it touches.

# CMOS voltage transfer characteristics (VTC)

## L1 MOSFET as a switch
We will now look at the device parameters from the switch point of view.
<img width="1003" height="577" alt="image" src="https://github.com/user-attachments/assets/153f63cd-cf2d-4d28-b42b-eadf40fc04c4" />
The above shows MOSFET as a switch: * When |Vgs|Vt, device is ON and it acts as closed switch
<img width="1015" height="580" alt="image" src="https://github.com/user-attachments/assets/bb6bf43b-5d2e-4523-84d5-a1f292da595a" />

## L2 Introduction to standard MOS voltage current parameters

<img width="1247" height="663" alt="image" src="https://github.com/user-attachments/assets/3cbbccb5-7d06-47df-ac75-f5e7386b5e59" />

<img width="693" height="485" alt="image" src="https://github.com/user-attachments/assets/c349d7cf-5734-4540-9192-085ab8e80fae" />

<img width="1307" height="727" alt="image" src="https://github.com/user-attachments/assets/91e8b380-6b9b-4bf9-91dc-03cd81b0a7d0" />

## L3 PMOS/NMOS drain current v/s drain voltage

<img width="1327" height="721" alt="image" src="https://github.com/user-attachments/assets/ab4ed8ec-8d81-4e72-a37e-bcd26a892cd7" />
<img width="1436" height="736" alt="image" src="https://github.com/user-attachments/assets/952725c0-0ede-42c7-9fc5-b749b9df56ed" />

## L4 Step1 – Convert PMOS gate-source-voltage to Vin

We have seen various internal voltages, but actually in terms of user's perspective we can't see the internal voltages and only see the external Vin and Vout. From these we calculate the VTC and eventually we get to know the delay.

Now we will see the steps to obtain Voltage Transfer Characteristics(VTC) for static CMOS inverter: Assumption: Let us assume that it is a long channel device with Vdd=2V

We will fix the Vgs values as shown below image

We know that Vgsp= Vin-Vdd, So we get the above values.So we get Vin = Vgsp+Vdd, we are trying to convert all the voltages as function of Vin and Vout.

We will try to plot the graph of PMOS in terms of Idsn, the plot will be as shown below. We can see that the corresponding Vin value of Vgsp is being plotted as shown in the above table.
<img width="1366" height="752" alt="image" src="https://github.com/user-attachments/assets/48754897-c944-4a7a-bd74-6b5fb94b94d3" />

<img width="1340" height="730" alt="image" src="https://github.com/user-attachments/assets/7bd3e422-71bf-41d7-9ddb-189d99a4c8be" />

## L5 Step2 & Step3- Convert PMOS and NMOS drain-source-voltage to Vout

Now we be converting the Vdsp and function of output voltage Vin. We know Vdsp = Vout-Vdd.
Let us convert Vdsp into Vout. So to get Vout there is a shift of Vdd towards left hand side.
<img width="1033" height="302" alt="image" src="https://github.com/user-attachments/assets/d6d32731-b660-405d-a605-d918d1cf78af" />
We can see that whenever Vout=2V that means Vdsp=0V and Vdd=2V (given), then The current is zero and capacitor at the output is discharged. This is true only when PMOS is in combination with NMOS and forms a CMOS inverter.
Let us take another example, when Vout=0V, that means -Vdsp=2V and Vdd=2V, so at every gate voltage of Vin we will see a finite current whenever Vout=0V. As Vout=0V, the capacitor is completely discharged and we need to charge that, so that is the charging current required. So, here we get the load curve for PMOS
<img width="582" height="487" alt="image" src="https://github.com/user-attachments/assets/6d9fb4c5-b099-4f38-b85d-7ab590f536ef" />
Now we will try to get the "load curve" for NMOS transistor from this equations
It is actually simple as Vgsn = Vin and Vdsn = Vout, directly we can get the graphs.

<img width="420" height="220" alt="image" src="https://github.com/user-attachments/assets/025467f5-7e60-4f21-9675-56c9f26e50d0" />
<img width="498" height="352" alt="image" src="https://github.com/user-attachments/assets/e6351926-7f0a-4150-bbe0-a55a07ce8e21" />
<img width="1025" height="435" alt="image" src="https://github.com/user-attachments/assets/5422e024-8790-478e-b450-2177b59159b8" />

## L6 Step4- Merge PMOS-NMOS load curves and plot VTC

We will now merge the above two curves and obtain the voltage transfer characteristics(VTC) for CMOS inverter.
<img width="1352" height="431" alt="image" src="https://github.com/user-attachments/assets/721af904-5557-4637-98b6-1538226096dd" />
<img width="667" height="442" alt="image" src="https://github.com/user-attachments/assets/e5c91e60-33d1-49c7-8d5c-cf90d6325ea7" />

So the range of Vin and Vout is 0V-2V.

When Vin = 0V, Vout = 2V; NMOS is Cut Off and PMOS is in Linear region
When Vin = 0.5V, 1.5V<Vout<2V; NMOS is in Saturation region and PMOS is in Linear region.
When Vin = 1V, 0.5V<Vout<1.5V; NMOS and PMOS are in Saturation region.
When Vin = 1.5V, 0<Vout<0.5V; NMOS is Linear region and PMOS is in Saturation region.
When Vin = 2V, Vout = 0V; NMOS is in linear region and PMOS is Cut Off

<img width="987" height="526" alt="image" src="https://github.com/user-attachments/assets/b9be4f8c-4223-4b2e-87a5-ba2f5afe8ba4" />

# Day 3 - CMOS Switching threshold and dynamic simulations

## Voltage transfer characteristics – SPICE simulations
### L1 SPICE deck creation for CMOS inverter

We will now simulate the VTC. For that we need to create the SPICE deck. It is a connectivity information (Netlist). As there is information about substrate, the circuit is as shown below.Here M1 is PMOS and M2 is NMOS.
Great question 👍 Let me explain clearly:

---

### 🔹 What is a **SPICE Deck**?

A **SPICE deck** is basically the **input file** you give to the SPICE simulator (like Ngspice, HSPICE, etc.).

It is a **text file** written in SPICE syntax that describes:

1. **Circuit netlist** → components (transistors, resistors, capacitors, voltage sources, etc.) and how they are connected.
2. **Models** → device characteristics (e.g., NMOS, PMOS parameters from a technology file).
3. **Simulation commands** → what analysis you want to perform (DC, AC, Transient, etc.).
4. **Output instructions** → what voltages/currents to plot or save.

---

### 🔹 Structure of a SPICE Deck

Typical SPICE deck looks like this:

```spice
* CMOS Inverter Example (Comment line starts with *)
Vdd vdd 0 1.8         ; Power supply = 1.8V
Vin in 0 PULSE(0 1.8 0n 10p 10p 1n 2n)   ; Input pulse

M1 out in vdd vdd pmos L=0.15u W=0.5u   ; PMOS transistor
M2 out in 0   0   nmos L=0.15u W=0.25u  ; NMOS transistor

Cload out 0 5f      ; Load capacitor

.tran 0.1n 10n      ; Transient analysis (time-domain sim)
.plot tran v(in) v(out)
.end
```

---

### 🔹 Key points

* It’s called a “deck” because originally, engineers punched these instructions onto **cards (decks of punch cards)** for simulation.
* Each line is either a **component**, a **control statement**, or a **comment**.
* The **`.end`** statement finishes the deck.

---

✅ In short:
A **SPICE deck** = the **netlist + models + simulation setup** in a text file that tells SPICE how to simulate your circuit.

<img width="447" height="500" alt="image" src="https://github.com/user-attachments/assets/d44a8da6-0bec-48c1-b3df-76ed0748b8aa" />

Next we will write down the Component Vlaues, keeping W/L for both NMOS and PMOS same.
<img width="462" height="510" alt="image" src="https://github.com/user-attachments/assets/09995552-7cd3-42ce-8e1e-5b2ba6199e6c" />
<img width="602" height="481" alt="image" src="https://github.com/user-attachments/assets/cd47210d-1403-473b-ba65-b479870d01cb" />
as marked these are usually multiples.
<img width="1272" height="592" alt="image" src="https://github.com/user-attachments/assets/3d27243d-cf85-4c6f-b05d-aa60b077f9c0" />
after this name nodes.
Name the nodes In model file we will mention like, 2.5V input lies between Vin and 0, similarly Vdd lies between vdd and 0.
Now let us write the SPICE deck:
<img width="1288" height="587" alt="image" src="https://github.com/user-attachments/assets/536ff9b8-2193-4e93-b988-a7e46e1cbe18" />

## L2 SPICE simulation for CMOS inverter
We know for Mosfet the syntax is DGSS(Drain gate source and substrate)
<img width="1275" height="607" alt="image" src="https://github.com/user-attachments/assets/b6422313-b0a6-49a1-b786-250dfd95494c" />
<img width="1276" height="648" alt="image" src="https://github.com/user-attachments/assets/d78affa8-43d4-4f22-9d7d-80562c43179e" />
<img width="1287" height="603" alt="image" src="https://github.com/user-attachments/assets/c32d611e-7a2a-4e06-bda7-2ea2d666ffe6" />
<img width="1273" height="647" alt="image" src="https://github.com/user-attachments/assets/a914626a-b3d1-477f-a369-4391609d0234" />
Next comes the Simulation Commands
Here we will be sweeping the gate input voltage from 0 to 2.5V with steps of 0.05. We need to find the VTC, for this only we will be sweeping the input voltage and measuring the output voltage.
Final step is to describe the Model files, all the information about the technological parameteres is given inside the model files.
<img width="1247" height="638" alt="image" src="https://github.com/user-attachments/assets/6493fea3-3ec2-43a4-8ec5-82a0481e4f7d" />
Perfect 👌 you’ve shared a **SPICE deck** example of a **CMOS inverter**. Let’s carefully break down what each section and file line means:

---

## 🔹 SPICE Deck Breakdown

### 1. **Model Descriptions**

```spice
*** MODEL Descriptions ***
```

* Tells SPICE where to find the **transistor models** (e.g., NMOS, PMOS).
* These models contain physics-based parameters (mobility, threshold voltage, oxide thickness, etc.).
* In this case, they’re included from an external file:

  ```spice
  .LIB "tsmc_025um_model.mod" CMOS_MODELS
  ```

  → This file (`tsmc_025um_model.mod`) contains the technology data for 0.25µm CMOS transistors.

---

### 2. **Netlist Description**

This section defines the circuit connections (which node connects to which).

```spice
M1 out in vdd vdd pmos W=0.375u L=0.25u
M2 out in 0   0   nmos W=0.375u L=0.25u
cload out 0 10f
```

* **M1** = PMOS transistor

  * Drain = `out`
  * Gate = `in`
  * Source = `vdd`
  * Bulk = `vdd`
  * Type = `pmos`, Width = `0.375µm`, Length = `0.25µm`

* **M2** = NMOS transistor

  * Drain = `out`
  * Gate = `in`
  * Source = `0` (ground, Vss)
  * Bulk = `0` (ground)
  * Type = `nmos`, same W/L ratio

* **Cload** = capacitor (10 femtofarads)

  * Connected between output (`out`) and ground.
  * Represents load capacitance at inverter output.

So, this builds the CMOS inverter: PMOS pull-up, NMOS pull-down, and load capacitor.

---

### 3. **Voltage Sources**

```spice
Vdd vdd 0 2.5
Vin in 0 2.5
```

* **Vdd** = Power supply (2.5 V between node `vdd` and ground).
* **Vin** = Input voltage source (applied at node `in`).

Later, simulations sweep this input.

---

### 4. **Simulation Commands**

```spice
.op
.dc Vin 0 2.5 0.05
```

* **`.op`** = Operating point analysis (gives DC bias values at nodes).
* **`.dc Vin 0 2.5 0.05`** = Sweeps input `Vin` from 0 V to 2.5 V in 0.05 V steps.
  → This generates the **Voltage Transfer Characteristic (VTC)** of the inverter.

---

### 5. **Include Model File**

```spice
.include tsmc_025um_model.mod
```

or

```spice
.LIB "tsmc_025um_model.mod" CMOS_MODELS
```

* This line loads the **technology file** (`tsmc_025um_model.mod`).
* It defines the electrical behavior of the NMOS and PMOS used.
* Without it, SPICE won’t know the transistor parameters.

---

### 6. **End of File**

```spice
.end
```

* Marks the end of the SPICE deck.

---

## 🔹 Corresponding Circuit Diagram (Right Side)

* The schematic matches the netlist:

  * PMOS on top (M1).
  * NMOS on bottom (M2).
  * Input connected to both gates.
  * Output at node `out`.
  * Capacitor at the output.
  * Power supply Vdd = 2.5V.

---

✅ **Summary**:
This SPICE deck describes a **CMOS inverter** with W/L = 0.375µm/0.25µm, powered at 2.5V, simulated for DC transfer characteristics. The `.mod` file provides the technology parameters for realistic behavior.
Now we will do the SPICE simulation for Wn=Wp=0.375u, Ln=Lp=0.25u, Wn/ln=Wp/Lp=1.5. Below is the VTC we get for the above netlist.
<img width="962" height="712" alt="image" src="https://github.com/user-attachments/assets/aa285a44-8a1e-497b-826d-1fcad3487083" />
<img width="782" height="307" alt="image" src="https://github.com/user-attachments/assets/70d0b736-5b7c-45da-8d5e-334e47e505d1" />
<img width="795" height="622" alt="image" src="https://github.com/user-attachments/assets/a7a5524d-0e36-4d55-98af-b7188fc4127c" />
Next we will get the VTC for Wn= 0.375u, Wp= 0.9375u, Ln,p=0.25u; Wn/Ln=1.5, Wp/Lp=2.5 (PMOS width is 2.5 times more than NMOS)
<img width="912" height="705" alt="image" src="https://github.com/user-attachments/assets/ba2c7192-97a0-4534-8a4f-06fee2167e5d" />
If we observe the previous graph is left shifted slightly. This happens because NMOS is more stronger than PMOS in previous graph.

## L3 Labs Sky130 SPICE simulation for CMOS
We now get the VTC characteristics
<img width="1851" height="1023" alt="image" src="https://github.com/user-attachments/assets/6a8afe75-f775-43e3-b415-0dfd6dd12d7a" />
<img width="1827" height="301" alt="image" src="https://github.com/user-attachments/assets/1820c271-c52f-4a72-a5b4-3905a88a1896" />
<img width="1232" height="932" alt="image" src="https://github.com/user-attachments/assets/e961f748-10ef-4267-84b3-f6743bab27df" />
<img width="1711" height="872" alt="image" src="https://github.com/user-attachments/assets/6a967866-f658-41af-be0a-efd814bb87a2" />
<img width="1698" height="872" alt="image" src="https://github.com/user-attachments/assets/0fb6edd0-189c-4960-a411-12e96d571e82" />

We will now see the transient analysis:
For that we will go inside the tansient SPICE file for day3
<img width="1858" height="1026" alt="image" src="https://github.com/user-attachments/assets/c7d6457d-0eec-40e8-9b6f-88ba424f3b34" />
<img width="1753" height="870" alt="image" src="https://github.com/user-attachments/assets/8ec0f6b6-464c-4b46-80b0-4596efbcd4ac" />
So for rise delay and fall delay, we need to consider 50% of output curve i.e. at 0.9V; out-in.
<img width="376" height="80" alt="image" src="https://github.com/user-attachments/assets/605b452f-24ca-4022-8f48-53d02ec31ba2" />
Therefore Rise delay = 2.482ns-2.15ns = 0.333ns

For fall delay, consider while falling.
<img width="363" height="85" alt="image" src="https://github.com/user-attachments/assets/f73ef202-c378-47e1-80a5-9126732a1612" />
Therefore **Fall Delay = 4.334ns-4.050ns = 0.285ns**

## Static behavior evaluation – CMOS inverter robustness – Switching Threshold

## L1 Switching Threshold, Vm

Let us compare the two different CMOS inverters with different W/L ratios of PMOS and NMOS, we can see that the shape of the VTC is same in both the cases only the switching threshold is different. This shows the robustnesss of CMOS inverter.
<img width="1245" height="582" alt="image" src="https://github.com/user-attachments/assets/0f898db3-73ff-4c7a-844d-c2241a5b2d86" />
<img width="1203" height="637" alt="image" src="https://github.com/user-attachments/assets/4bbfee2a-eee5-40f2-b74e-08d96e5dbcae" />
<img width="1220" height="670" alt="image" src="https://github.com/user-attachments/assets/5579fc1b-ab32-48a1-965f-917c89f791c2" />

## L2 Analytical expression of Vm as a function of (W/L)p and (W/L)n

We will now calculate the value of Vm w.r.t the NMOS and PMOS width and length.
<img width="1167" height="715" alt="image" src="https://github.com/user-attachments/assets/4fde64d1-4d9b-47fb-a126-4496d2d35dd1" />
<img width="907" height="482" alt="image" src="https://github.com/user-attachments/assets/be595e97-bdf2-4be0-85cd-f7e478bb936b" />
## L3 Analytical expression of (W/L)p and (W/L)n as a function of Vm
Now here we will calculate the value of W/L for PMOS and NMOS when Vm is given.
We have to move in reverse fashion, as we need to calculate W/L ratio of PMOS and NMOS such that Switching threshold is exatly half of the power supply Vdd = 2.5V, therefore required Vm = 1.25V.
We will start from the current equation itself i.e. Idsn = -Idsp
<img width="1002" height="387" alt="image" src="https://github.com/user-attachments/assets/cadaf558-d6a5-4470-9965-ee9741ac4743" />
Expanding Kp and Kn (Gain factor)

<img width="627" height="107" alt="image" src="https://github.com/user-attachments/assets/08ca8890-234b-4741-9a4b-decf35dd5784" />

<img width="612" height="106" alt="image" src="https://github.com/user-attachments/assets/ab6d338b-f712-4ab2-8944-d50cb29d9290" />

Now here on the RHS all are constants and we will get the values from the model files except Vm, If we know Vm then we can get the W/L ratios.
So now this will allow us to find out for what value of W/L ratio of PMOS will be greater than NMOS based on values of Vm.
We will now see the behaviour of CMOS for below difference in W/L ratios of PMOS and NMOS.

<img width="347" height="285" alt="image" src="https://github.com/user-attachments/assets/7c95043f-08be-4cff-8d62-656e365ba9b1" />

<img width="1208" height="646" alt="image" src="https://github.com/user-attachments/assets/f3a697ac-9114-4056-a676-a6656013e926" />

## L4 Static and dynamic simulation of CMOS inverter

<img width="1190" height="695" alt="image" src="https://github.com/user-attachments/assets/bc559012-15a2-439a-ac2a-a01f77b234f2" />

## L5 Static and dynamic simulation of CMOS inverter with increased PMOS width

We will be doing the SPICE simulations for increased width of PMOS transistors and compare the results.

(W/L)p = 2(W/L)n
<img width="986" height="417" alt="image" src="https://github.com/user-attachments/assets/411dcb9e-7e5d-439a-9e48-c3cd2cb4b611" />
We can see that the Vm is now increased as the PMOS has become more stronger and it needs more current to charge the output load capacitor.
(W/L)p = 3(W/L)n
<img width="968" height="417" alt="image" src="https://github.com/user-attachments/assets/e411296e-7907-442a-8e14-e00c5671b905" />
<img width="988" height="415" alt="image" src="https://github.com/user-attachments/assets/406cc2dd-515d-4cb4-9302-aa5a0a5c5fe2" />
<img width="996" height="427" alt="image" src="https://github.com/user-attachments/assets/ef4cc22c-1c6f-419e-9c15-0da81846a4c7" />
Note: Rise delay decreases with increase in PMOS width, this shows the time required to charge the output capacitor decreases significantly this is because we have a bigger area.

## L6 Applications of CMOS inverter in clock network and STA

<img width="836" height="287" alt="image" src="https://github.com/user-attachments/assets/b2e3bfa5-2acf-4cc3-9e39-9be570001207" />

There are some conclusions we draw from this experiment:

During fabrication, there can be slight variation in sizes of PMOS and NMOS from the required one, but the robustness of CMOS inverter is such that, there is not much difference in the Vm with change in sizes.

When (W/L)p = 2(W/L)n, we see that RISE-FALL delay are approximately equal, if we simulate then we can get the ratio factor such that the Rise delay and fall delay are equal to each other. This shows "Symmetry" of CMOS inverter.

This is a typical characteristic of Clock Inverter/buffer where we want the rise delay and fall delay to be equal.
<img width="983" height="577" alt="image" src="https://github.com/user-attachments/assets/7028c919-636d-4fea-bc05-b0a4e0d3b1a3" />

# Day 4 - CMOS Noise Margin robustness evaluation
## Static behavior evaluation – CMOS inverter robustness – Noise margin
### L1 Introduction to noise margin

<img width="826" height="560" alt="image" src="https://github.com/user-attachments/assets/e6b9d574-4156-41e0-80db-f34f7aaa4ce1" />

Great question 👍 Let’s go step by step on **Noise Margin** in digital circuits, especially CMOS inverters.

---

## 🔹 What is Noise Margin?

Noise margin tells us **how much unwanted noise a logic circuit can tolerate** at its input or output without causing an error.

* Digital signals are supposed to be either **logic 0** (LOW) or **logic 1** (HIGH).
* In real life, due to **noise, crosstalk, supply variation, leakage, etc.**, voltages may not be perfectly 0V or Vdd.
* **Noise margin** defines the “safety cushion” between valid logic levels and invalid (uncertain) levels.

---

## 🔹 Key Voltage Levels

From the **Voltage Transfer Characteristic (VTC)** of an inverter (output vs. input voltage), we define:

1. **VOH** – Minimum output voltage recognized as HIGH.
2. **VOL** – Maximum output voltage recognized as LOW.
3. **VIH** – Minimum input voltage recognized as HIGH.
4. **VIL** – Maximum input voltage recognized as LOW.

Between `VIL` and `VIH`, the input is in an **undefined region** → not reliably HIGH or LOW.

---

## 🔹 Noise Margins

Two important metrics:

1. **NMH (Noise Margin High):**
   [
   NMH = VOH - VIH
   ]
   → Maximum noise voltage that can be tolerated on a HIGH signal before it’s misread as LOW.

2. **NML (Noise Margin Low):**
   [
   NML = VIL - VOL
   ]
   → Maximum noise voltage that can be tolerated on a LOW signal before it’s misread as HIGH.

---

## 🔹 Graphical View

On the CMOS inverter VTC curve:

* Draw the line `Vout = Vin` (45° line).
* Find the points where slope = –1 → defines **VIL** and **VIH**.
* From these, you compute NMH and NML.

---

## 🔹 Example (CMOS Inverter @ Vdd = 2.5V)

* VOH ≈ 2.5 V
* VOL ≈ 0 V
* VIL ≈ 0.8 V
* VIH ≈ 1.7 V

So:

* NML = VIL – VOL = 0.8 – 0 = **0.8 V**
* NMH = VOH – VIH = 2.5 – 1.7 = **0.8 V**

👉 This means the circuit can tolerate **up to 0.8 V of noise** without error.

---

## 🔹 Why Important?

* **Higher noise margin = more robust design.**
* Low noise margin → circuit easily fails due to small noise or supply variations.
* CMOS inverters typically have **good noise margins** compared to NMOS-only or PMOS-only logic.

---

✅ **Summary**:
Noise margin = tolerance of logic levels against noise. It’s calculated as:

* **NMH = VOH – VIH**
* **NML = VIL – VOL**
  It ensures that small disturbances don’t flip logic states incorrectly.
  
## L2 Noise margin voltage parameters
refer prev lec notes.
<img width="777" height="542" alt="image" src="https://github.com/user-attachments/assets/c91d616e-56e3-482b-9aa3-a10c307bffe2" />

## L3 Noise margin equation and summary
<img width="835" height="567" alt="image" src="https://github.com/user-attachments/assets/59972ea4-8056-45f1-97c3-52a9f930ac67" />

<img width="803" height="526" alt="image" src="https://github.com/user-attachments/assets/0a263d7e-1a62-443f-8a57-b93068d4ca63" />

## L4 Noise margin variation with respect to PMOS width
Nice one 👍 Let’s connect **noise margin** with **PMOS width variation** in a CMOS inverter.

---

## 🔹 CMOS Inverter Basics

* In a CMOS inverter, **NMOS pulls output LOW** and **PMOS pulls output HIGH**.
* The **relative strengths (W/L ratio)** of NMOS and PMOS determine the **switching threshold (VM)** of the inverter.

[
V_M \approx \frac{V_{thn} + \sqrt{\frac{\mu_p W_p}{\mu_n W_n}} (V_{DD} - |V_{thp}|)}{1 + \sqrt{\frac{\mu_p W_p}{\mu_n W_n}}}
]

where:

* (W_p, W_n) = widths of PMOS and NMOS
* (\mu_p, \mu_n) = carrier mobilities
* (V_{thp}, V_{thn}) = threshold voltages

---

## 🔹 Effect of Increasing PMOS Width (Wp)

1. **PMOS gets stronger** (more current-driving ability).
2. Switching point (V_M) shifts **to the right** (towards VDD).

   * Because PMOS pulls up more strongly.
3. **Noise Margins change:**

   * **NMH (Noise Margin High = VOH – VIH)** → **increases**, since VOH stays ≈ VDD and VIH shifts upward.
   * **NML (Noise Margin Low = VIL – VOL)** → **decreases**, since VIL shifts upward.

---

## 🔹 Effect of Decreasing PMOS Width (Wp)

1. **PMOS gets weaker** compared to NMOS.
2. Switching point (V_M) shifts **to the left** (towards 0).
3. **Noise Margins change:**

   * **NMH decreases** (HIGH level noise tolerance reduces).
   * **NML increases** (LOW level noise tolerance improves).

---

## 🔹 Trade-off

* Ideally, designers aim for **symmetric noise margins (NMH ≈ NML)**.
* This happens when **PMOS strength ≈ NMOS strength**.
* Since **hole mobility (µp)** is ~2–3× lower than electron mobility (µn), PMOS width is usually made **2–3× wider** than NMOS to balance.

---

✅ **Summary:**

* **Increase PMOS width → NMH ↑, NML ↓**.
* **Decrease PMOS width → NMH ↓, NML ↑**.
* Optimum ratio ( W_p/W_n ) is chosen so that both margins are roughly equal.

<img width="1250" height="686" alt="image" src="https://github.com/user-attachments/assets/cfb8afbe-b6d4-4f88-8c12-9c8197971342" />

<img width="1287" height="705" alt="image" src="https://github.com/user-attachments/assets/d4d5348d-e187-4d26-9883-546f549af86f" />

<img width="743" height="232" alt="image" src="https://github.com/user-attachments/assets/75fea204-1871-4879-ade9-594d028b58d8" />
<img width="807" height="583" alt="image" src="https://github.com/user-attachments/assets/143b9eba-5687-4ebf-8588-0d6616c61008" />

<img width="1095" height="613" alt="image" src="https://github.com/user-attachments/assets/016bcf36-4962-4064-b717-d87570119968" />

## L5 Sky130 Noise margin labs

To plot noise margin we need to look at vtc curve.
<img width="1325" height="922" alt="image" src="https://github.com/user-attachments/assets/71599c8c-9ebf-4e9b-8618-ff6dbe9e1b57" />
<img width="1626" height="368" alt="image" src="https://github.com/user-attachments/assets/9756c8a0-8b05-4ce7-ba94-7c9967522d59" />
<img width="678" height="161" alt="image" src="https://github.com/user-attachments/assets/ad45b20d-fa17-460b-b241-f13cd77f2405" />
<img width="1728" height="857" alt="image" src="https://github.com/user-attachments/assets/91bcc614-0fee-4b2a-a201-75a965cca2d6" />
To plot noise margin we have to consider slope where it is -1.
<img width="272" height="95" alt="image" src="https://github.com/user-attachments/assets/739ce5ec-7c92-4c3f-8ff8-612f6f412b5a" />
x= Vil, Y= Voh

<img width="263" height="37" alt="image" src="https://github.com/user-attachments/assets/9ff1d068-3d16-4220-8583-01d8396320d4" />

# Day 5 - CMOS power supply and device variation robustness evaluation
## Static behavior evaluation – CMOS inverter robustness – Power supply variation

### L1 Smart SPICE simulation for power supply variations

While evaluating the robustness of CMOS inverter another factor is Power Supply Scaling. On reducing the gate length, the operating power is also reduced. On power scaling the Cmos characteristics should not change.

We will check by simulation, taking two cases.
<img width="906" height="738" alt="image" src="https://github.com/user-attachments/assets/934d6d5f-1cbf-4e8a-b557-f28f0bdc196d" />
<img width="891" height="552" alt="image" src="https://github.com/user-attachments/assets/b6bc2e39-4da3-4565-bbb2-a7f2d390ee9b" />
We will now plot the VTC charactersitics for Vdd= 2.5V, 2V, 1.5V, 1V, 0.5V;
<img width="910" height="698" alt="image" src="https://github.com/user-attachments/assets/a91f35e2-d214-4de5-a2ea-465bacba6208" />

### Sky130 Supply variation Labs

We will calculate the supply variation.
<img width="873" height="525" alt="image" src="https://github.com/user-attachments/assets/a957540e-fc4d-4bb5-8e8a-3661bd0650e6" />
<img width="876" height="452" alt="image" src="https://github.com/user-attachments/assets/231c31c1-3219-43e1-9b67-c3c8250a1828" />
The initial supply voltage is 1.8V and we are reducing it with the step of 0.2V, so there will be 6 iterations.
<img width="966" height="490" alt="image" src="https://github.com/user-attachments/assets/eaf727a3-7557-4511-91a7-04068a08cc13" />
We will calculte the Gain

### Static behaviour evaluation-CMOS inverter robustness-Device variation

We will see the sources of variation of VTC characteristics in a CMOS inverter.
First is Etching Process
If we see a single inverter layout, we will see the length of gate, the width(common area between polysilicon and diffusion). Due to etching process there can be a variation in length and width of CMOS.
Considering the inverter chain, the variation can differ with different inverter.

<img width="1002" height="476" alt="image" src="https://github.com/user-attachments/assets/90202b96-e861-492b-b5f9-be66e3064d62" />
<img width="975" height="501" alt="image" src="https://github.com/user-attachments/assets/d0fe4962-c260-4ddc-8328-dfa11dc34749" />
<img width="956" height="547" alt="image" src="https://github.com/user-attachments/assets/61c952fa-0dc1-46e7-8869-81b73a08bf6a" />
The variation is more at the edges or sides than at the center.

<img width="992" height="472" alt="image" src="https://github.com/user-attachments/assets/270b58d2-3ccf-4284-a39f-6628f2f9c649" />
Therefore the variation in L and W can change the drain current of CMOS inverter.
<img width="943" height="542" alt="image" src="https://github.com/user-attachments/assets/e3a73cbd-65a4-41ea-a450-c62363944ca3" />

### Sky130 device variations labs
We will now do the SPICE simulations for the device variations
<img width="882" height="528" alt="image" src="https://github.com/user-attachments/assets/164eada3-17c2-4568-bc4e-7f5a196aa4ca" />
<img width="730" height="408" alt="image" src="https://github.com/user-attachments/assets/d05327cd-95b1-47a5-8c26-6b949d1b53dd" />
We can see that the width of PMOS is quite large than that of NMOS. SO it is clearly strong PMOS and weak NMOS case. The Vm will be right shifted.
<img width="712" height="489" alt="image" src="https://github.com/user-attachments/assets/4e8221dc-4616-4845-be09-b5e86864c855" />
<img width="1003" height="485" alt="image" src="https://github.com/user-attachments/assets/04065b2d-3ffe-41ea-8837-06067698adae" />

## Thank you!!
