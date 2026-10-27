# On-Chip Spiking Neural Networks for PPG-Based Blood Pressure Estimation

A compact spiking neural network (SNN) that estimates systolic and diastolic blood
pressure from photoplethysmography (PPG) features, designed to run on **DYNAP-SE1**
mixed-signal neuromorphic hardware.

Bachelor thesis, Department of Computer Science, ETH Zurich — carried out at the
Institute of Neuroinformatics (INI), UZH/ETH. Supervised by Prof. Dr. Elisa Donati.

## Motivation

Cuffless, continuous blood-pressure monitoring from PPG is attractive because PPG
sensors are already built into most consumer wearables. Most published models, however,
are large neural networks that exceed the memory and energy budgets of the small
processors inside those devices. This work asks how small and hardware-friendly such a
model can be made while remaining accurate, and whether it can be deployed on analog
neuromorphic hardware.

## Approach

- **Feature selection.** A multi-stage pipeline reduces the 218 features of a public
  PPG benchmark to **17**, using impurity-based importance ranking, correlation
  clustering, and monotonicity filtering.
- **Network.** 32 hidden and 10 output leaky integrate-and-fire (LIF) neurons with
  learned per-neuron decay factors and sparse connectivity — roughly **780 parameters**.
- **Decoding.** Instead of the 200-neuron population vote used by the baseline, the
  output spike train is passed through a fixed exponential kernel (τ = 20 steps) that
  reconstructs a continuous signal resembling the arterial blood-pressure waveform;
  systolic and diastolic values are read directly from it.
- **On-chip deployment.** The trained network is mapped onto a DYNAP-SE1 processor,
  which required weight quantisation to six discrete levels, clustering of decay
  constants across physical cores, and manual analog bias tuning.

## Results

On the BCG dataset (5-fold cross-validation), reported as mean absolute error:

| Metric                | This work | Notes                                   |
| --------------------- | --------- | --------------------------------------- |
| Systolic (SBP) MAE    | 12.51 mmHg | Matches the baseline SNN (~12.97 mmHg) |
| Diastolic (DBP) MAE   | 7.87 mmHg  |                                         |
| Parameters            | ~780       | ~6× fewer than the baseline SNN         |

The main contribution is architectural: the network reaches baseline-level systolic
accuracy with roughly six times fewer parameters and a decoding scheme compatible with
neuromorphic hardware. On-chip accuracy does not yet match the software simulation; the
deployment confirms the architecture is structurally compatible with the DYNAP-SE1 and
identifies the remaining engineering challenges (weight resolution, per-core parameter
sharing, analog variability).

## Repository structure

> Adjust to match your actual files.

```
.
├── src/            # feature selection, SNN model, training, evaluation
├── deployment/     # DYNAP-SE1 mapping and on-chip experiments
├── data/           # not included — see "Data" below
└── thesis.pdf      # full written thesis
```

## Getting started

```bash
# clone
git clone https://github.com/Mzlm51/BachelorThesis.git
cd BachelorThesis

# environment
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

> Add the exact commands to reproduce feature selection, training, and evaluation here.

## Data

This project uses a public PPG blood-pressure benchmark. The raw dataset is **not**
redistributed in this repository; download it from the original source and place it
under `data/` (see the thesis for the exact benchmark and preprocessing steps).

## Hardware

Deployment targets the **DYNAP-SE1** (Dynamic Neuromorphic Asynchronous Processor,
Scalable Edition 1), a mixed-signal neuromorphic processor developed at INI, UZH/ETH.
Reproducing the on-chip results requires access to this hardware.

## Acknowledgements

Supervised by Prof. Dr. Elisa Donati at the Institute of Neuroinformatics, UZH/ETH.

## License

No license is set yet. Without one, the code is "all rights reserved" by default — add a
license file if you want others to be able to reuse it, and confirm you have permission
to publish any institute-owned code or data before doing so.
