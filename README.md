# AIMC-Error-Review

A cross-layer resource on non-idealities, error propagation, and mitigation in analog in-memory computing.

## About this repository

This repository provides a structured and continuously updated resource for understanding non-idealities in analog in-memory computing (AIMC). The focus is on how errors originate at different hardware abstraction levels, how they transform and propagate through the computation flow, and how cross-layer mitigation can improve robustness.

The repository is intended to grow over time with concise technical notes, updated references, original explanatory figures, and links to related work.

## Related review paper

The scope of this repository is closely aligned with the following review:

**From device non-idealities to computation accuracy: A cross-layer review of error mechanisms and mitigation in analog in-memory computing**

Zeying Ding, Yantong Di, Haoran Du, Haoran Chen, Yaoru Hou, Bo Liu, and Hao Cai

*Journal of Semiconductors*, 2026

DOI: `10.1088/1674-4926/26060006`

Official article page: [Journal of Semiconductors](https://www.jos.ac.cn/en/article/doi/10.1088/1674-4926/26060006)

## Cross-layer AIMC error framework

AIMC non-idealities should not be viewed as isolated effects. Errors originate at different hardware abstraction levels, are transformed by subsequent computation stages, and eventually affect the accuracy of the final output.

```text
Device
  ↓
Array
  ↓
Circuit
  ↓
Data Conversion
  ↓
System
  ↓
Computation Accuracy
```

The framework organizes AIMC error sources into five hardware levels:

- **Device level**  
  Device-to-device variation, temporal fluctuation, conductance drift, retention effects, and nonlinear or asymmetric device behavior can perturb the programmed weight state.

- **Array level**  
  Interconnect resistance, IR drop, sneak paths, parasitic effects, and array-level coupling distort the effective input and accumulated current or voltage during vector-matrix multiplication.

- **Circuit level**  
  Peripheral circuits introduce additional non-idealities through sensing offset, noise, finite gain, limited swing, mismatch, and other analog circuit imperfections.

- **Data-conversion level**  
  DAC and ADC interfaces introduce quantization, saturation, finite resolution, nonlinear transfer characteristics, and conversion noise.

- **System level**  
  Mapping, tiling, accumulation, scheduling, and finite-precision digital processing determine how lower-level errors are combined, propagated, or amplified during system execution.

These physical non-idealities can be mapped into a common computational view, including:

- weight perturbations
- multiplicative distortions
- nonlinear transfer effects
- additive errors
- quantization and accumulation errors

The key point is that an error generated at one level does not necessarily remain local. Its form and impact may change as it passes through sensing, conversion, accumulation, and system-level processing. Therefore, robustness should be evaluated from a cross-layer perspective rather than by optimizing each layer independently.

## Contents

- Paper information
- Cross-layer AIMC error framework
- Core error categories
- Planned technical notes and updates
- Citation and copyright information
