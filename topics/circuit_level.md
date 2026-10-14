# Circuit-level non-idealities

Peripheral circuits translate array signals into measurable values. Drivers, integrators, sense amplifiers, sample-and-hold circuits, and references may introduce offset, mismatch, thermal noise, limited bandwidth, finite gain, and saturation.

## Computational impact

A useful illustrative model is z = a·s + b + n, where s is an ideal analog signal, a is gain distortion, b is offset, and n is noise. Practical circuits may also exhibit nonlinear and input-dependent behavior.

## Evaluation questions

- Which errors are static and calibratable, and which vary over time?
- What happens at low signal swing and near decision thresholds?
- How do PVT variation and input activity affect the readout?
- Which circuit errors are amplified by subsequent conversion or accumulation?

See [Data conversion](data_conversion.md) and [Mitigation](mitigation.md).
