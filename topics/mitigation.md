# Cross-layer error mitigation

Mitigation is most effective when the correction mechanism matches the error source, its variability, observability, and correction cost. A technique should not be selected only because it improves one isolated hardware metric. Its value depends on whether it interrupts a dominant error pathway before that error is transformed or amplified by later stages.

## Mitigation objective

A practical cross-layer design can be viewed as a constrained optimization problem:

```text
minimize    task loss under non-ideal hardware
subject to  energy, latency, area, endurance, and calibration constraints
```

The objective is not to eliminate every physical deviation. It is to reduce the deviations that materially affect the final computation while avoiding correction overhead that is larger than the recovered benefit.

## Characterize the error before choosing a correction

Four questions help determine where mitigation should be placed.

| Error property | Diagnostic question | Likely implication |
| --- | --- | --- |
| Deterministic or stochastic | Does the error repeat under the same input and operating condition? | Repeatable bias can often be calibrated; random noise may require margin, averaging, coding, or robust training |
| Static or time varying | Does it change after fabrication, with temperature, or with device age? | Static errors may use one-time characterization; dynamic errors need tracking or periodic recalibration |
| Local or global | Is it associated with one cell, row, column, tile, converter, or the whole chip? | Local errors favor fine-grained mapping or calibration; global errors may be corrected with shared references or digital scaling |
| Observable or latent | Can an on-chip signal or test mode estimate the error directly? | Observable errors support feedback correction; latent errors may require proxy measurements or end-to-end adaptation |

A fifth question is whether the error is monotonic with a measurable condition such as temperature, supply voltage, array position, input activity, or conductance state. A stable relationship can make low-cost prediction or table-based correction possible.

## Cross-layer intervention matrix

| Intervention level | Representative techniques | Best suited to | Main cost or limitation |
| --- | --- | --- | --- |
| Device and programming | Program-and-verify, state selection, differential encoding, device-aware conductance targets | Programming spread, stuck states, retention and drift | Write time, endurance, characterization effort |
| Array and mapping | Smaller tiles, parasitic-aware mapping, row-activity control, remapping, redundancy | IR drop, line resistance, spatial gradients, defective cells | Utilization loss, routing and metadata overhead |
| Circuit | Offset cancellation, reference tracking, gain correction, adaptive bias, background calibration | Sensing offset, gain error, PVT-dependent margins | Area, latency, energy, calibration stability |
| Data conversion | Range adaptation, nonlinear thresholds, adaptive resolution, converter calibration | Quantization loss, clipping, distribution mismatch | Converter complexity and control overhead |
| System and algorithm | Quantization-aware training, hardware-aware training, error-aware mapping, digital rescaling | Residual coupled errors and application sensitivity | Retraining cost, portability and model dependence |

The table does not imply a one-to-one mapping. For example, IR drop originates in the array but may be mitigated by limiting active rows, changing weight placement, digitally rescaling partial sums, or training with an array-aware model.

## Match correction timescale to error timescale

Calibration overhead is often underestimated because only the correction circuit is reported. The update schedule also matters.

| Calibration class | Typical trigger | What should be reported |
| --- | --- | --- |
| One-time | Manufacturing test or initial deployment | Test time, stored calibration bits, nonvolatile storage overhead |
| Startup | Every power-on or reset | Startup latency and energy |
| Periodic | Fixed time interval or operation count | Update period, duty cycle, interruption cost |
| Event driven | Temperature, voltage, error flag, or confidence threshold | Monitor overhead, trigger threshold, false alarm rate |
| Continuous | Background tracking during operation | Steady-state energy, interference with normal computation, convergence behavior |

A slowly varying drift does not necessarily justify continuous calibration. Conversely, a one-time trim cannot guarantee robustness when the dominant error follows supply or temperature changes during operation.

## Example: selecting a mitigation stack

Consider an AIMC tile with three observed effects:

1. cell programming errors are mostly static after program-and-verify;
2. column gain varies with temperature;
3. the ADC clips only for a small fraction of high-activity inputs.

A reasonable mitigation stack could be:

- use program-and-verify only for cells outside an acceptable conductance window;
- track column or shared-reference gain when temperature crosses a threshold;
- adapt the ADC range or split high-activity operations rather than increasing converter resolution for every input;
- expose the remaining error distribution to hardware-aware training.

This example is illustrative. The important point is that each technique targets a different error timescale and location. Applying all corrections continuously would add unnecessary write, calibration, and conversion overhead.

## Avoid double counting mitigation benefits

When several techniques are combined, the total accuracy recovery is generally not the sum of their isolated improvements. Two mechanisms may correct the same bias, or one mechanism may change the operating distribution seen by another.

A useful ablation sequence is:

```text
Ideal baseline
→ Non-ideal hardware without mitigation
→ Add mitigation A
→ Add mitigation B
→ Add A and B together
→ Include all correction overheads
```

Report both isolated and combined results. If `A + B` provides less improvement than expected from their separate results, the overlap should be explained rather than hidden.

## Evaluation and reporting checklist

### Accuracy and robustness

- Use the same dataset, preprocessing, precision, and mapping across comparisons.
- Report intermediate-layer error together with the final task metric.
- Include correlated and coupled errors, not only one source at a time.
- Evaluate across relevant process, voltage, temperature, conductance, and input-activity conditions.
- Distinguish average behavior from tails, clipping events, and worst-case failures.

### Hardware overhead

- Include calibration measurement, control logic, storage, references, and test modes.
- Report whether energy and latency are paid once, periodically, per inference, or per operation.
- Quantify endurance cost when correction introduces additional writes.
- State whether extra ADC resolution, redundant cells, or spare rows are included in area and energy.

### Training and portability

- State whether training uses measured hardware data, a fitted model, or assumed noise.
- Separate retraining benefit from circuit-level mitigation benefit.
- Test model mismatch when the training-time error model differs from the evaluated hardware.
- Report whether recalibration or retraining is required for a new chip, tile, model, or operating range.

## A practical selection rule

Prefer the lowest-cost intervention that can observe or predict the dominant error before it is amplified. Move correction upward toward the system or algorithm only when lower-level correction is too expensive, insufficiently observable, or unable to handle coupled residual errors.

Cross-layer mitigation does not mean that every design needs a correction at every layer. It means that the design decision is made using end-to-end evidence rather than an isolated component metric.

## Further reading

1. Z. Ding *et al.*, “From device non-idealities to computation accuracy: A cross-layer review of error mechanisms and mitigation in analog in-memory computing,” *Journal of Semiconductors*, 2026. [Official article page](https://www.jos.ac.cn/en/article/doi/10.1088/1674-4926/26060006), DOI: [10.1088/1674-4926/26060006](https://doi.org/10.1088/1674-4926/26060006).
2. P. Mannocci *et al.*, “Achieving high precision in analog in-memory computing systems,” *npj Unconventional Computing*, 2026. DOI: [10.1038/s44335-025-00044-2](https://doi.org/10.1038/s44335-025-00044-2).
3. M. J. Rasch *et al.*, “Hardware-aware training for large-scale and diverse deep learning inference workloads using in-memory computing-based accelerators,” *Nature Communications*, 2023. DOI: [10.1038/s41467-023-40770-4](https://doi.org/10.1038/s41467-023-40770-4).
4. S. K. Roy *et al.*, “Compute SNDR-boosted 22-nm MRAM-based in-memory computing macro using statistical error compensation,” *IEEE Journal of Solid-State Circuits*, 2025. DOI: [10.1109/JSSC.2024.3442013](https://doi.org/10.1109/JSSC.2024.3442013).
5. P. Deaville, B. N. Zhang, and N. Verma, “A fully row/column-parallel in-memory computing macro in foundry MRAM with differential readout for noise rejection,” *IEEE Journal of Solid-State Circuits*, 2024. DOI: [10.1109/JSSC.2024.3386462](https://doi.org/10.1109/JSSC.2024.3386462).

See [Error propagation](error_propagation.md) and the [repository overview](../README.md).
