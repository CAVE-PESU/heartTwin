# heartTwin: ECG Simulation & Cardiac Digital-Twin Prototype

[![Project Stage](https://img.shields.io/badge/stage-prototype-orange.svg)](#)
[![Primary Language](https://img.shields.io/badge/language-MATLAB_90%25-blue.svg)](#)
[![Secondary Language](https://img.shields.io/badge/language-Python_10%25-yellow.svg)](#)

**heartTwin** is a cardiac digital-twin prototype designed for synthetic ECG generation and parameter recovery. The repository provides a parametric framework capable of controlling full ECG wave morphology—including P, Q, QRS, S, and T components—driven by mathematical formulations and customizable physiological parameters.

---

## 📸 Overview & Architecture

The primary system relies on a modular MATLAB architecture that takes target heart rate and morphology parameters, builds independent constituent waves using a harmonic Fourier series, and synthesizes a complete ECG waveform. 

### Generation & Closed-Loop Analysis Pipeline

```
Patient / Target ECG Parameters (CSV)
            │
            ▼
┌───────────────────────────────────────────────────┐
│              MATLAB Wave Generator                │
│  ┌───────┐ ┌───────┐ ┌─────────┐ ┌───────┐ ┌───┐ │
│  │ P-Wave│ │ Q-Wave│ │ QRS-Wave│ │ S-Wave│ │T-W│ │
│  └───┬───┘ └───┬───┘ └────┬────┘ └───┬───┘ └─┬─┘ │
└──────┼─────────┼──────────┼──────────┼───────┼────┘
       └─────────┴────┬─────┴──────────┴───────┘
                      ▼
            Waveform Summation
                      │
                      ▼
           Synthetic ECG Waveform
                      │
                      ▼
       [ Closed-Loop Parameter Recovery ]
     (R-peak detection & Feature Extraction)
```

---

## 🗂️ Project Structure

All primary operational code is located inside the `final/` directory:

```text
CAVE-PESU/heartTwin/
└── final/
    ├── complete.m              # Primary workflow for end-to-end ECG generation
    ├── generated_ecg.m         # Core ECG simulation workflow
    ├── analyze_ecg.m           # Feature extraction module (peak detection & interval math)
    │
    ├── p_wav.m                 # P-wave generator (100-harmonic series)
    ├── q_wav.m                 # Q-wave generator (100-harmonic series)
    ├── qrs_wav.m               # QRS complex generator (100-harmonic series)
    ├── s_wav.m                 # S-wave generator (100-harmonic series)
    ├── t_wav.m                 # T-wave generator (100-harmonic series)
    │
    ├── genecg.py               # Differential equation-based electrical circuit experiment
    ├── ecg_parameters.csv      # Parameter configuration matrix
    ├── ecg_data.json           # Auxiliary ECG metadata
    └── generated_ecg_signal.mat# Serialized generated signal output
```

---

## 📄 Key Features

1. **Parametric Waveform Synthesis:** Modify ECG characteristics (`heartRate`, wave amplitudes, durations, PR/ST intervals) without hardcoding or redrawing signal structures.
2. **Harmonic Mathematical Modeling:** Waveforms are constructed via a 100-harmonic Fourier series approach, giving precise continuous control over wave parameters.
3. **Closed-Loop Parameter Extraction (`analyze_ecg.m`):** Runs peak-detection heuristics on synthesized `.mat` signals to recover parameters (PR, ST, RR intervals, BPM) and validate generator accuracy.
4. **Parallel Dynamical Experiment (`genecg.py`):** An experimental Python module modeling cardiac output as an RLC differential electrical circuit solved using `scipy.integrate.odeint`.

---

## 🚀 Getting Started

### Prerequisites

* **MATLAB** (R2020a or newer recommended)
* **Python 3.8+** (Optional, for electrical circuit modeling)
  * `numpy`
  * `scipy`
  * `matplotlib`

### MATLAB Workflow (Main Engine)

1. Open MATLAB and set your working directory to `final/`.
2. Inspect or modify morphological parameters inside `ecg_parameters.csv`. Supported fields:
   * **Heart Rate:** `heartRate`
   * **Amplitudes:** `p_amp`, `q_amp`, `qrs_amp`, `s_amp`, `t_amp`, `u_amp`
   * **Durations & Intervals:** `p_dur`, `p_pr`, `q_dur`, `qrs_dur`, `s_dur`, `t_dur`, `t_st`, `u_dur`
3. Run the primary generation script:
   ```matlab
   complete
   ```
4. Analyze and extract parameters from the generated signal:
   ```matlab
   analyze_ecg
   ```

### Python Workflow (Circuit Experiment)

Run the differential equation model to simulate signal propagation across modeled resistance (R), capacitance (C), and inductance (L):

```bash
cd final
python genecg.py
```

---

## 🔬 Mathematical & Technical Framework

### Waveform Generation
The MATLAB framework models individual ECG components W(t) using harmonic summation across 100 harmonics (n = 1 ... 100):

W(t) = sum_{n=1}^{100} [ a_n * cos(2 * pi * n * t / T) + b_n * sin(2 * pi * n * t / T) ]

Where period T is calculated directly from the configured heart rate:

T = 60 / heartRate

### Electrical Circuit Model (`genecg.py`)
The Python implementation models the myocardium as a passive network with node voltages V1 and V2, outputting the differential signal:

V_ECG(t) = V1(t) - V2(t)

---

## ⚠️ Known Limitations & Technical Gaps

While **heartTwin** offers a configurable baseline, several technical gaps remain prior to achieving full digital-twin status:

* **Electrophysiological Disconnect:** The MATLAB engine uses purely mathematical/harmonic signal-generation functions. It does not currently model cellular ion channels or cardiac membrane depolarisation/repolarisation dynamics.
* **Interval Coupling:** Heart rate modifications adjust the overall period (RR interval) linearly, but physiological coupling rules (e.g., rate-dependent QT scaling like Bazett's formula) are not yet integrated.
* **Analysis Heuristics:** `analyze_ecg.m` utilizes fixed peak thresholds (`MinPeakHeight = 0.5`, `MinPeakDistance = 50`). These parameters are susceptible to failure under conditions of severe tachycardia, arrhythmia, inversion, or added signal noise.
* **Parallel Pipelines:** The MATLAB Fourier synthesis model and the Python differential-equation model currently exist as isolated experiments without a unified interface.

---

## 🛣️ Roadmap

- [ ] **Unified Interface:** Bridge the Python RLC circuit model with the MATLAB morphology synthesizer.
- [ ] **Dynamic Interval Scaling:** Integrate physiological dependencies into the Fourier generation engine (e.g., dynamic QT/RR ratios).
- [ ] **Robust Parameter Estimation:** Replace fixed search windows in `analyze_ecg.m` with adaptive or machine-learning-based signal processing algorithms.
- [ ] **Patient Pipeline:** Build an automated pipeline mapping raw patient single-lead ECGs directly to estimated parameter files for personalized twin instantiation.

---

## 📜 License

This project is developed under the **CAVE-PESU** organization. See the repository license file for usage details.
