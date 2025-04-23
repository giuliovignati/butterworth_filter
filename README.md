 🧈 **Butterworth Low-Pass Filter Design**
---

## 📘 Introduction

A **Butterworth filter** is a type of signal processing filter designed to have a frequency response that is as flat as mathematically possible in the passband. Unlike filters that exhibit ripples or irregularities in the passband or stopband, the Butterworth filter provides a smooth and monotonic response, making it ideal for applications that require minimal signal distortion.

This project focuses on the design and analysis of a 7th-order Butterworth low-pass filter based on specific performance requirements, including passband and stopband frequencies, maximum ripple, and minimum attenuation.

---

## 📐 Project Specifications

The specifications provided for this project are summarized in the following table:

| Filter Type |   $f_p$   |   $f_s$   |   $A_{\text{max}}$    |   $A_{\text{min}}$    |  $K$  |
|-------------|-----------|-----------|-----------------------|-----------------------|-------|
| Butterworth | 40 kHz    | 61 kHz    | 2 dB                  | 20 dB                 | 5     |

---

## 🔍 Parameter Calculation and Design Decisions

### 🎛️ Butterworth Filter Poles

The magnitude of the transfer function of a Butterworth filter $T(i\omega)$ is given by:

$|T(i\omega)| = \frac{1}{\sqrt{1 + \varepsilon^2 \left(\frac{\omega}{\omega_p}\right)^{2N}}}$

Where:

- $N$ is the filter order  
- $\varepsilon = \sqrt{10^{A_{\text{max}}/10} - 1} = 0.765$

To ensure a minimum attenuation of $A_{\text{min}}$ in the stopband, the minimum order $N$ required is calculated as:

$A(\omega_s) = 10 \log\left(1 + \varepsilon^2 \left(\frac{\omega_s}{\omega_p}\right)^{2N}\right)$

Solving gives:

$N_{\text{min}} = 7$

Poles lie on a semicircle in the complex plane:

$R = \omega_p \cdot \left(\frac{1}{\varepsilon}\right)^{1/7} = 261.129 \cdot 10^3 \, \text{rad/s}$

**Complex Poles of the Filter:**

| Index       | Pole Expression (in terms of $R$)         |
|-------------|-------------------------------------------|
|  $p_{0,6}$  | $R \cdot (-0.22252 \pm i \cdot 0.97493)$  |
|  $p_{1,5}$  | $R \cdot (-0.62349 \pm i \cdot 0.078183)$ |
|  $p_{2,4}$  | $R \cdot (-0.90097 \pm i \cdot 0.43388)$  |
|    $p_3$    | $-R $                                     |

---

### 🎚️ Pole Frequencies and Quality Factors

Given $p_k = R \cdot (\sigma_k + i\omega_k)$, we derive:

- Resonant frequency:  
  $\omega_0 = R$

- Quality factor:  
  $Q = -\frac{1}{2\sigma}$

**Quality Factors:**

| Poles     | Quality Factor $Q$ |
|-----------|--------------------|
| $p_{0,6}$ | $2.2469$           |
| $p_{1,5}$ | $0.8019$           |
| $p_{2,4}$ | $0.5549$           |

---

## 🔧 Filter Design

### 🧠 Design Choices

The project design involved balancing trade-offs among performance, component count, and sensitivity. The implemented solution includes three smart Sallen-Key stages, a single integrator stage, and an inverting buffer.

An alternative solution aimed at minimizing component usage would have included three Sallen-Key filters, one RC filter, and a non-inverting amplifier stage. This approach would have saved one operational amplifier. However, it was ultimately discarded in favor of a fully active solution, which offers greater versatility and improved performance.

Given the relatively non-stringent specifications, a general-purpose operational amplifier was selected, specifically the *TL072* (and *TL071*).

### 🧩 Component Value Calculation

Following the model of the smart Sallen-Key filter (schematic shown in the following figure), the resistor and capacitor values for each stage are determined according to the following equations:

$\omega_0 = \frac{1}{R \sqrt{C_1 C_2}} \quad$ ; $\quad Q = \frac{1}{2} \sqrt{\frac{C_1}{C_2}}$

![Image](https://github.com/user-attachments/assets/efca4a23-9c13-4085-a93a-94759744b827)

Considering the values of $Q$ and $\omega_0$ obtained, and choosing discrete resistor and capacitor values that best approximate the theoretical values, the following values were obtained:

| Component | S-K 1 | S-K 2 | S-K 3 |
| --- | --- | --- | --- |
| $R [k\Omega]$ | $5.1$ | $7.5$ | $7.5$ |
| $C_1 [pF]$ | $820$ | $820$ | $2.2 \cdot 10^3$ |
| $C_2 [pF]$ | $680$ | $330$ | $100$ |
| $Q$ | $0.5491$ | $0.7882$ | $2.3452$ |
| $\omega_0 [krad/s]$ | $262.6$ | $256.3$ | $284.3$ |

For the integrator stage (based on the schematic) the following formulas are used:

$$
\omega_0 = \frac{1}{R_2 C}, \quad K = \frac{R_2}{R_1}
$$

The chosen values are: $R_1 = 8.1k\Omega$,  $R_2' = 39k\Omega$,  $R_2'' = 2k\Omega$, and  $C' = C'' = 47pF$. 

For the inverting buffer, the resistor values are $R = 10k\Omega$. Given the simple DC gain specification, all amplification is handled in the single-pole stage. Therefore, it was the only stage where care was taken to use resistor series to closely approximate the ideal gain value, while the capacitor parallel configuration was used primarily for sizing purposes.

Finally, the order of the stages, from input to output, is: integrator, Sallen-Key smart filter with increasing $Q$, and inverting buffer.

An additional note on filter sizing: Initially, different sizing was planned, especially for the first and third Sallen-Key stages. However, upon testing the real filter's performance and considering the numerous non-idealities of the circuit on a breadboard (e.g., parasitic capacitance), the actual values differed significantly from the expected ones (cutting off at around $4kHz$ instead of the target). As a result, an alternative sizing was chosen to better meet the design specifications, aiming to use larger capacitors to reduce parasitic effects. Further details on the alternative sizing approach are provided in section \ref{chapter:complementi_dimensionamento}.

## Circuit Sensitivity

The sensitivity values across all stages of the circuit are shown in the tables below. It can be observed that, due to the smart Sallen-Key configuration for all two-pole stages, the circuit exhibits excellent sensitivity values, making it highly robust to variations in component values.

### Sallen-Key Stage Sensitivity

| Component | Sensitivity Value |
| --- | --- |
| $S^Q_R$ | 0 |
| $S^Q_{C_1}$ | 0.5 |
| $S^Q_{C_2}$ | -0.5 |
| $S^{\omega_0}_{\{R,C_1,C_2\}}$ | -0.5 |

### Integrator Stage Sensitivity

| Component | Sensitivity Value |
| --- | --- |
| $S^K_{R_1}$ | -1 |
| $S^K_{R_2}$ | 1 |
| $S^{\omega_0}_{\{R_2,C\}}$ | -1 |
| $S^{\omega_0}_{R_1}$ | 0 |

### Inverting Buffer Sensitivity

| Component | Sensitivity Value |
| --- | --- |
| $S^K_{R_1}$ | -1 |
| $S^K_{R_2}$ | 1 |

--- 

## ⚙️ Spice Simulations
####Schematic used for Spice simulations
![Image](https://github.com/user-attachments/assets/acd42b64-5252-413d-986f-0e1b06ac0b8a)

### Fourier Domain Analysis

The previous schematic was used to perform the following Montecarlo simulation with $5\%$ tolerance for resistors and $15\%$ tolerance for capacitors, while the values presented in the table below refer to the simulation with exact nominal values. The last row in the table shows the actual frequency at which $A_{min} = 2dB$ is satisfied, which occurs with a $0.5kHz$ offset.

At this point, it is essential to observe that at $61kHz$, the attenuation is much smaller compared to the designed value ($\approx -6dB$). This discrepancy is due to the effect of additional poles in the circuit, probably caused by the non-idealities of the operational amplifiers. This effect can be directly observed in the simulation with nominal values by evaluating the phase Bode plot. In addition to the contribution from the 7 poles coinciding around $40kHz$, which would lead to a total phase shift of $630^\circ$, an additional phase shift is visible beyond $0.5MHz$, suggesting the presence of extra poles. It is worth noting that this behavior could have allowed us to achieve a solution that fully met the design specifications, for example, by simply shifting the cutoff frequency $\omega_0$ of the stages further ahead. However, we chose not to implement this solution in order to remain consistent with the theoretical models, while still obtaining valid results with this configuration.

#### Bode Diagram Simulation Values (Nominal Values)

| Frequency | $V_{out}/V_{in}$ [dB] | $V_{out}/V_{in}$ [V] |
| --- | --- | --- |
| $DC (1Hz)$ | $13.98$ | $5$ |
| $40kHz$ | $11.80$ | $3.89$ |
| $61kHz$ | $-8.67$ | $0.37$ |
| $39.5kHz$ | $11.98$ | $3.97$ |

#### Montecarlo simulation:
![Image](https://github.com/user-attachments/assets/7bf311f9-d3ee-4263-9795-9c027a02cdd1)
#### Montecarlo simulation with cutoff zoom:
![Image](https://github.com/user-attachments/assets/81c9d8ec-2548-443e-8844-f36b40bc4c78)
#### Nominal frequency analysis:
![Image](https://github.com/user-attachments/assets/23ab6132-78c1-4a24-be94-418501bd7aad)


### 🕒 Time Domain Analysis

The time domain simulations of the filter at the relevant frequencies are shown in the following graphs. The peak values presented are entirely consistent with those calculated previously.

#### 1Hz Simulation
![Image](https://github.com/user-attachments/assets/5b88f015-0ac9-4f17-b8f1-557143c21d9a)
#### 40Hz Simulation
![Image](https://github.com/user-attachments/assets/80e27a38-a653-4dc3-be60-1041e75d58cd)
#### 61Hz Simulation
![Image](https://github.com/user-attachments/assets/7fcb7b34-3256-4b62-9a33-a5cb8034fbd1)

### Notes on Non-Ideal Component Sizing

As previously mentioned, initial alternative sizing options were proposed using real measured values, which leveraged component imprecision to achieve more accurate $Q$ and $\omega_0$ values for each stage (as shown in the following schematic). This approach led to simulations that fully satisfied the design specifications (see following table).

However, this sizing strategy was ultimately discarded because the component values deviated significantly from their nominal counterparts. This made the solution impractical for large-scale implementation, as it relied on the non-ideal behavior of resistors and capacitors. Additionally, while simulations using nominal values were still somewhat acceptable, the use of very small capacitors had significant effects in the real circuit implementation, resulting in performance that deviated substantially from the design specifications.

#### Bode Diagram Simulation Values (Alternative Real Sizing)

| Frequency | $V_{out}/V_{in}$ [dB] | $V_{out}/V_{in}$ [V] |
| --- | --- | --- |
| $DC (1Hz)$ | $14.074$ | $5.05$ |
| $40kHz$ | $12.36$ | $4.15$ |
| $61kHz$ | $-10.12$ | $0.31$ |

#### Filter Schematic with alternative real values 
![Image](https://github.com/user-attachments/assets/305542c4-da05-43ee-9fc6-b7c61dc043a0)

## Physical Implementation of the Circuit and Experimental Measurements

The following oscilloscope screenshots display the input (Channel 1) and the output (Channel 2) of the filter. Compared to the ideal simulations previously shown in Table, non-idealities in the circuit and deviations from nominal component values led to a shift of approximately $0.7kHz$ in the actual cutoff frequency.

#### 100Hz Measurement
![Image](https://github.com/user-attachments/assets/ebcaa1a5-8c0a-46c8-af04-d3f2bb361e34)
#### 40kHz Measurement
![Image](https://github.com/user-attachments/assets/1a6c519a-cb4a-455d-a89c-563c863a6f78)
#### 61kHz Measurement
![Image](https://github.com/user-attachments/assets/63adef68-e5b3-4194-b639-ce28eee3d7c3)

The following table presents the current measurements for each operational amplifier and estimates of power consumption. It was observed that the current draw remains constant throughout the input frequency range.

#### Measured Voltages at Key Frequencies

|       | $V_{in}$ [V] | $V_{out}$ [V] |
|-------|--------------|---------------|
| $DC (1Hz)$ | $1.03$ | $5.2$ |
| $40kHz$    | $1.03$ | $3.58$ |
| $61kHz$    | $1.03$ | $0.314$ |
| $38.8kHz$  | $1.03$ | $3.96$ |

#### Current and Power Dissipation (Power Supply: $\pm12V$)

| Component       | Current [$mA$] | Power Dissipated [$mW$] |
|-----------------|----------------|--------------------------|
| $TL072^{'}$     | $3.972$         | $95.33$                  |
| $TL072^{''}$    | $1.769$         | $42.26$                  |
| $TL071$         | $1.319$         | $31.66$                  |




## ⚡ PCB Circuit Schematic

To ensure project completeness and replicability, the circuit schematic was transposed into KiCad, an open-source suite for electrical schematic and PCB design.

The schematic was structured using a block-division approach to enhance clarity. Input, output, and power blocks were specifically highlighted. For completeness, unconnected pins, power lines, and the selected mounting holes were marked and properly grounded.

Given the schematic's simplicity, we opted for a 2-layer board layout: the top layer is dedicated to signal lines, while the bottom layer features a ground plane and power traces for the op-amps. This setup enhances reference stability.

A significant design decision was the selection of **footprints** for each component — i.e., standardized physical packages. With no strict space constraints, we selected **TH (Through-Hole)** packages. Despite being bulkier than SMD (Surface Mount Device) equivalents, TH components are easier to source and solder manually.

- **Resistors**: 1/4W, metal film
- **Capacitors**: Ceramic, 50V tolerance

#### Selected Footprint Dimensions

| Component   | Length [mm] | Diameter [mm] | Pitch [mm] |
|-------------|--------------|----------------|-------------|
| Resistor    | 6.3          | 2.5            | 10.16       |
| Capacitor   | 4.7          | 2.5            | 5.0         |

To allow for easy substitution of op-amps, we used **DIP8 sockets** (Dual In-line Package, 8 pins, 7.62mm nominal width), enabling insertion/removal without soldering.

Peripheral connections are handled via **screw terminals (KF301)**. These meet project specs comfortably:

- **Pitch**: 5.08mm  
- **Max Current**: 13.5A  
- **Max Voltage**: 250V  
- **Wire Size Range**: 26AWG – 16AWG  

Before routing, trace widths were dimensioned using KiCad's **Track Width Calculator**, based on max current, trace length, and temperature rise:

- **Signal traces**: 0.35mm  
- **Power traces**: 0.4mm  

These values fully satisfy design constraints.

### ✅ Additional Design Steps

- **ERC (Electrical Rules Check)** and **DRC (Design Rules Check)** were performed in KiCad.
- Manufacturer constraints (e.g., JLC PCB) were input for validation.
- Front and back **Silkscreen labels** were added for reference values.
- KiCad's **3D Viewer** was used to preview the assembled board, verify component spacing, and inspect label positioning before production.

#### KiCad Schematic
![Image](https://github.com/user-attachments/assets/bad41c45-5c6c-4602-a341-584074e282cc)
#### KiCad PCB editor view
![Image](https://github.com/user-attachments/assets/95b3d5d3-e24b-4f0b-9008-c043c5453d89)
#### PCB view (front side) from 3d viewer
![Image](https://github.com/user-attachments/assets/0cc80b84-78e2-4372-899b-723c4edd710f)
#### PCB view (back side) from 3d viewer
![Image](https://github.com/user-attachments/assets/74ffd5ae-d191-46c9-bfcf-be1163841ce8)
#### PCB view with selected 3d models from 3d viewer
![Image](https://github.com/user-attachments/assets/cc4973f4-6345-475a-bf68-523a2493bea3)
