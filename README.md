# Butterworth Lowpass Filter Design

---

## 📘 Introduction

A **Butterworth filter** is a type of signal processing filter designed to have a frequency response that is as flat as mathematically possible in the passband. Unlike filters that exhibit ripples or irregularities in the passband or stopband, the Butterworth filter provides a smooth and monotonic response, making it ideal for applications that require minimal signal distortion.

This project focuses on the design and analysis of a 7th-order Butterworth low-pass filter based on specific performance requirements, including passband and stopband frequencies, maximum ripple, and minimum attenuation.

---

## 📐 Project Specifications

The specifications provided for this project are summarized in the following table:

| Filter Type | \($f_p$\) | \($f_s$\) | \($A_{\text{max}}$\)  | \($A_{\text{min}}$\) | \($K$\) |
|-------------|-----------|-----------|-----------------------|-----------------------|---------|
| Butterworth | 40 kHz    | 61 kHz    | 2 dB                  | 20 dB                 | 5       |

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

### 🧮 Pole Frequencies and Quality Factors

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

