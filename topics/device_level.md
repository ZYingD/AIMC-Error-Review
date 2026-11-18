# Device-level non-idealities

Device-level errors originate in the physical memory element and its programmed state. In analog in-memory computing (AIMC), the stored conductance is not a perfectly fixed numerical weight: it depends on fabrication, programming history, elapsed time, temperature, read conditions, and the physical state of the device.

The purpose of this note is to separate these effects before they are propagated into array, circuit, conversion, and system-level errors.

## Error taxonomy

| Error source | Typical behavior | Computational consequence | Important reporting axis |
| --- | --- | --- | --- |
| Device-to-device variation | Persistent differences among nominally identical cells | Spatially structured weight error | Cell, row, column, tile, chip |
| Cycle-to-cycle programming variation | A cell reaches different states across repeated writes | Programming uncertainty and limited effective precision | Target state, pulse sequence, number of retries |
| Temporal read noise | Conductance fluctuates during or between reads | Sample-dependent output noise | Bandwidth, sampling interval, read voltage |
| Conductance drift and retention loss | The programmed state changes with elapsed time | Time-dependent weight displacement | Time after programming, temperature, state |
| State-dependent nonlinearity | Read current is not proportional to applied voltage or stored state | Input-dependent multiplication error | Conductance level, voltage range, polarity |
| Asymmetric or nonlinear updates | Positive and negative updates have different size or probability | Biased online learning and accumulated update error | Direction, pulse count, starting state |
| Endurance degradation and hard faults | Repeated operation changes the device or creates stuck states | Persistent outliers, unavailable weights, remapping demand | Write count, fault criterion, recovery method |

These categories can overlap. For example, a high-conductance state may exhibit both stronger drift and a different noise distribution. Treating every effect as independent, zero-mean Gaussian noise can therefore hide the dominant failure mechanism.

## Computational representation

For an intended conductance matrix `G`, a first-order model is:

```text
G_actual = G + ΔG
y_actual = (G + ΔG)x
Δy = ΔG x
```

This model is useful for sensitivity analysis, but a single fixed `ΔG` is often insufficient. A more informative decomposition is:

```text
G_actual(t, T, v) =
    G
  + ΔG_spatial
  + ΔG_program
  + ΔG_drift(t, T)
  + n_read(t, v)
```

where `t` is elapsed time, `T` is temperature, and `v` is the applied read condition. The terms need not be independent or additive in a real device.

For state- or voltage-dependent behavior, it is safer to use a transfer function:

```text
i = f_device(G, v, t, T, history)
```

rather than assuming `i = Gv`. This distinction matters because a nonlinear device error can become input dependent and cannot always be represented as a weight perturbation alone.

## Characterization plan

A useful device characterization separates the following axes.

### Spatial variation

Measure many cells and preserve their physical location. Report whether the variation is random or contains row, column, edge, or tile-level structure. Spatial correlation can interact with weight mapping and array position.

### Programming variation

Repeat programming on the same cells and distinguish device-to-device spread from cycle-to-cycle spread. Report target levels, programming algorithm, verify tolerance, number of attempts, and any discarded or failed cells.

### Time dependence

Measure immediately after programming and at stated later times. If accelerated temperature testing or a fitted drift law is used, report the model and extrapolation range rather than presenting the prediction as direct long-term measurement.

### Operating conditions

Sweep read voltage and relevant temperature range. If supply or temperature changes also affect the peripheral circuit, keep the device-only and combined measurements distinguishable.

### Distribution shape

Report tails and outliers in addition to mean and standard deviation. Multilevel AIMC accuracy may be limited by state overlap, skewed distributions, or a small fraction of severe faults.

## How device errors propagate

A device error rarely remains a local conductance error.

- **Through the array:** changed cell currents alter IR drop and the operating point of neighboring devices, so array distortion can depend on the programmed conductance pattern.
- **Through sensing:** a conductance shift changes the signal margin presented to the sense or integration circuit; near a threshold, a small shift may cause a large decision error.
- **Through conversion:** device-induced changes in the analog output distribution can increase clipping or make a fixed ADC range inefficient.
- **Through mapping and accumulation:** systematic spatial errors may repeat across tiles or bit slices instead of averaging out.
- **Through learning:** asymmetric updates can introduce a persistent training bias even when the average single-pulse error appears small.

This is why device statistics should be propagated through the actual computation path rather than judged only by conductance precision.

## Mitigation options and trade-offs

| Mitigation | Primary target | Main trade-off |
| --- | --- | --- |
| Program-and-verify | Programming spread and state overlap | Write latency, energy, endurance |
| Differential or redundant encoding | Common-mode variation and failed states | Cell count and readout overhead |
| State-aware conductance targets | State-dependent variation, nonlinearity, or drift | Characterization and mapping complexity |
| Periodic or event-driven recalibration | Drift and operating-condition changes | Interruption, monitor, and storage cost |
| Fault-aware remapping | Stuck or severely degraded cells | Spare capacity and metadata |
| Hardware-aware training | Residual statistical and coupled errors | Model dependence, retraining cost, model mismatch risk |

Mitigation should match the error timescale. A one-time trim may correct static device-to-device variation but cannot track rapid temporal noise; continuous calibration may be wasteful for slow drift.

## Evaluation checklist

- Are device-to-device and cycle-to-cycle variations measured separately?
- Are conductance distributions reported for every relevant state rather than only their average?
- Are read voltage, temperature, timing, and device history stated?
- Are temporal noise, drift, and retention evaluated on appropriate timescales?
- Are correlations across state, location, time, and operating condition preserved?
- Are tails, stuck states, and failed programming events included?
- Is the device model validated outside the samples used to fit it?
- Are device-only results distinguished from array and peripheral-circuit effects?
- Are mitigation benefits reported together with write, endurance, area, energy, and calibration overhead?
- Is end-to-end accuracy evaluated with the same device model and conditions used in characterization?

## Further reading

1. Z. Ding *et al.*, “From device non-idealities to computation accuracy: A cross-layer review of error mechanisms and mitigation in analog in-memory computing,” *Journal of Semiconductors*, 2026. [Official article page](https://www.jos.ac.cn/en/article/doi/10.1088/1674-4926/26060006), DOI: [10.1088/1674-4926/26060006](https://doi.org/10.1088/1674-4926/26060006).
2. G. Pedretti and D. Ielmini, “In-memory computing with resistive memory circuits: Status and outlook,” *Electronics*, 2021. DOI: [10.3390/electronics10091063](https://doi.org/10.3390/electronics10091063).
3. K. Higuchi *et al.*, “Computation-in-memory simulation platform to investigate inference accuracy degradation by device non-ideality interactions in deep neural network applications,” *Japanese Journal of Applied Physics*, 2022. DOI: [10.35848/1347-4065/ac44d0](https://doi.org/10.35848/1347-4065/ac44d0).

See [Array-level non-idealities](array_level.md), [Error propagation](error_propagation.md), and [Mitigation](mitigation.md).
