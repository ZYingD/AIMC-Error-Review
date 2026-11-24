# Array-level non-idealities

Array-level errors emerge when individually characterized memory cells are connected through shared word lines, bit lines, access devices, drivers, and sensing nodes. The resulting computation depends not only on the programmed conductance matrix, but also on physical position, array dimensions, input activity, conductance distribution, and settling time.

This note separates array effects from device variation and downstream circuit errors, and provides a reproducible way to evaluate their computational impact.

## Error taxonomy

| Array effect | Physical origin | Computational consequence | Important dependency |
| --- | --- | --- | --- |
| Word-line and bit-line IR drop | Distributed interconnect resistance carrying row and column currents | Position- and data-dependent reduction or distortion of cell voltages | Array size, wire resistance, active rows, conductance map |
| Driver and sensing-path resistance | Finite source resistance at the input and nonzero impedance at the output | Global or column-dependent gain loss that interacts with array current | Driver strength, termination, total current |
| Parasitic capacitance | Interconnect, access-device, and sensing-node capacitance | Incomplete settling, history dependence, and latency–accuracy trade-off | Pulse width, array dimensions, previous state |
| Sneak and leakage paths | Unselected conductive paths in passive or imperfectly isolated arrays | Bias-dependent leakage and undesired current summation | Array topology, selector behavior, bias scheme |
| Coupling and shared-node disturbance | Capacitive or resistive interaction among neighboring lines and shared supplies | Activity-dependent interference between rows or columns | Switching pattern, routing, timing |
| Spatial gradients and mismatch | Systematic variation in wires, contacts, and local loading | Near–far asymmetry and structured column or tile error | Physical location and layout |
| Current crowding and dynamic-range compression | Large simultaneous column current and limited delivery or sensing range | Nonlinear output compression, saturation, or redistribution | Row parallelism, input density, conductance states |

Not every implementation contains every effect. For example, a well-controlled 1T1R array can suppress passive sneak paths, but the access transistors and shared metal still contribute voltage drop, finite output resistance, and capacitance.

## From the ideal matrix equation to a distributed array

For an ideal linear array with row-voltage vector `x` and conductance matrix `G`:

```text
y_ideal = G x
```

Using the convention that `G[j,i]` connects input row `i` to output column `j`, the ideal column current is:

```text
y_ideal[j] = Σ_i G[j,i] x[i]
```

In a physical array, the voltage across cell `(j,i)` is generally not equal to the externally applied row voltage. A local cell current is better written as:

```text
I_cell[j,i] = G[j,i] · (V_WL[j,i] - V_BL[j,i])
```

The internal word-line and bit-line voltages must be obtained from the distributed network through Kirchhoff's current law. A compact system-level representation is:

```text
y_array = f_array(
    G, x,
    R_WL, R_BL,
    R_driver, R_sense,
    C_parasitic,
    topology, timing
)
```

Therefore, the array error is:

```text
e_array = y_array - Gx
```

Unlike a fixed additive perturbation, `e_array` can depend jointly on the input vector, programmed weights, physical placement, and time allowed for settling. Replacing it with independent zero-mean noise can miss systematic near–far error and workload-dependent distortion.

## Why error depends on data and position

Three interactions are especially important.

1. **Input activity changes line current.** More active rows or larger input amplitudes can increase accumulated current and the voltage drop along shared interconnects.
2. **The conductance map changes the electrical network.** Mapping many high-conductance cells onto the same region or column can create a larger local drop than a sparse or low-conductance map.
3. **Physical position changes the path impedance.** Two equal weights can produce different currents when one is placed near a driver or sensing node and the other is farther away.

Consequently, evaluating only random inputs, only average conductance, or only one cell location is not sufficient.

## Illustrative numerical example

Consider two cells contributing to one column. Let:

```text
G1 = 100 µS
G2 =  50 µS
Vin = 0.20 V
```

With ideal interconnects:

```text
I_ideal = Vin · (G1 + G2)
        = 0.20 · 150 µS
        = 30.0 µA
```

Suppose a distributed-array solution gives effective cell voltages of `0.19 V` for the near cell and `0.17 V` for the farther cell:

```text
I_array = 0.19 · 100 µS + 0.17 · 50 µS
        = 19.0 µA + 8.5 µA
        = 27.5 µA
```

The output error is:

```text
e_array = 27.5 µA - 30.0 µA = -2.5 µA
relative error = -2.5 / 30.0 = -8.33%
```

This is an illustrative calculation, not measured data or a claimed array design. The effective voltages are assumed to demonstrate the mechanism; in an actual evaluation they must come from extracted-layout simulation, circuit simulation, measurement, or a validated compact model.

The example also shows why a single global scale factor may be inadequate: the near cell loses `5%` of its applied voltage, while the farther cell loses `15%`.

## Static and transient evaluation

### Static analysis

For DC or sufficiently long pulses, solve the resistive network and compare ideal and non-ideal column outputs. Include:

- distributed word-line and bit-line resistance;
- driver and sensing-path impedance;
- access-device or selector behavior when relevant;
- selected and unselected bias conditions;
- actual conductance and input distributions.

### Transient analysis

For pulsed, charge-domain, or time-domain computation, include parasitic capacitance and the complete operation sequence. Report:

- input rise and fall time;
- evaluation and integration window;
- precharge or reset condition;
- residual charge and previous-cycle dependence;
- sampling instant;
- settling error versus latency.

A static IR-drop model cannot establish transient accuracy when the output is sampled before the array settles.

## Recommended evaluation workflow

1. **Define the ideal reference.** State the matrix orientation, input encoding, weight representation, and ideal accumulation rule.
2. **Extract or disclose physical parameters.** Use layout-extracted interconnect and device parasitics when available; otherwise label assumed resistance and capacitance values explicitly.
3. **Validate the array model.** Compare the compact model with transistor-level, SPICE, or measured results on representative sizes and operating points.
4. **Sweep array dimensions.** Report error versus row and column count rather than testing only the target size.
5. **Sweep workload conditions.** Vary input density, row parallelism, amplitude, sign pattern, and programmed conductance distribution.
6. **Preserve physical placement.** Compare near, middle, and far locations and include spatial error maps when useful.
7. **Evaluate coupled errors.** Combine array effects with device variation and realistic peripheral loading after isolated sensitivity tests.
8. **Propagate to later stages.** Check whether the distorted output changes ADC clipping, code selection, partial-sum accumulation, or final task accuracy.
9. **Account for mitigation overhead.** Include additional drivers, local converters, routing, calibration storage, latency, and energy.

## Useful metrics

No single metric describes every array error. Useful complementary measures include:

- per-column absolute and relative error;
- normalized root-mean-square error across output vectors;
- maximum or percentile error, not only mean error;
- near–far error ratio and spatial heatmaps;
- effective gain and nonlinearity versus row activity;
- settling error at the actual sampling time;
- ADC code-change or clipping rate after array distortion;
- task-level accuracy under the same mapping and preprocessing;
- energy, latency, area, and utilization after mitigation.

Relative error should be interpreted carefully near an ideal output of zero. Report absolute or normalized error with a stated denominator in cancellation-dominated cases.

## Mitigation options and trade-offs

| Mitigation | Primary benefit | Main cost or limitation |
| --- | --- | --- |
| Smaller subarrays or partitioning | Shorter lines and lower accumulated current | Additional routing, drivers, converters, accumulation, and unused capacity |
| Wider or lower-resistance interconnect | Reduced line drop | Area, capacitance, routing congestion |
| Row-activity control | Limits peak current and dynamic-range compression | Additional cycles and lower throughput |
| Conductance- and position-aware mapping | Avoids vulnerable locations or high-current clusters | Mapping complexity, metadata, possible utilization loss |
| Differential organization | Rejects some common-mode disturbances | Extra cells, current, sensing, and matching requirements |
| Local reference or in-situ calibration | Tracks spatially structured gain or offset | Reference area, calibration time, storage, residual model error |
| Digital or algorithmic compensation | Corrects predictable residual distortion | Model mismatch, retraining, portability, digital overhead |

Partitioning is not automatically beneficial. The accuracy improvement must be compared with the extra peripheral circuits and inter-tile accumulation, which can introduce new circuit- and conversion-level errors.

## Reporting checklist

- Are array dimensions, cell topology, wire parameters, and physical orientation stated?
- Are source, access-device, and sensing-path resistances included or explicitly excluded?
- Are static resistance and transient capacitance evaluated on the relevant timescale?
- Are input sparsity, row parallelism, amplitude, and conductance distribution swept?
- Are near, middle, and far locations compared?
- Are systematic spatial errors distinguished from random device variation?
- Is the compact model validated against a more detailed simulation or measurement?
- Are ideal and non-ideal results computed using identical inputs, weights, mapping, and precision?
- Are output error, downstream conversion effects, and task accuracy reported separately?
- Are partitioning, calibration, routing, and accumulation overheads included?

## Further reading

1. Z. Ding *et al.*, “From device non-idealities to computation accuracy: A cross-layer review of error mechanisms and mitigation in analog in-memory computing,” *Journal of Semiconductors*, 2026. [Official article page](https://www.jos.ac.cn/en/article/doi/10.1088/1674-4926/26060006), DOI: [10.1088/1674-4926/26060006](https://doi.org/10.1088/1674-4926/26060006).
2. G. Pedretti and D. Ielmini, “In-memory computing with resistive memory circuits: Status and outlook,” *Electronics*, 2021. DOI: [10.3390/electronics10091063](https://doi.org/10.3390/electronics10091063).
3. M. H. Amin, M. E. Elbtity, and R. Zand, “Interconnect parasitics and partitioning in fully-analog in-memory computing architectures,” *IEEE International Symposium on Circuits and Systems*, 2022. DOI: [10.1109/ISCAS48785.2022.9937884](https://doi.org/10.1109/ISCAS48785.2022.9937884).
4. M. H. Amin, M. E. Elbtity, and R. Zand, “Xbar-partitioning: A practical way for parasitics and noise tolerance in analog IMC circuits,” *IEEE Journal on Emerging and Selected Topics in Circuits and Systems*, 2022. DOI: [10.1109/JETCAS.2022.3222966](https://doi.org/10.1109/JETCAS.2022.3222966).
5. M. J. Rasch *et al.*, “Hardware-aware training for large-scale and diverse deep learning inference workloads using in-memory computing-based accelerators,” *Nature Communications*, 2023. DOI: [10.1038/s41467-023-40770-4](https://doi.org/10.1038/s41467-023-40770-4).

See [Device-level non-idealities](device_level.md), [Circuit-level non-idealities](circuit_level.md), [Error propagation](error_propagation.md), and [Mitigation](mitigation.md).
