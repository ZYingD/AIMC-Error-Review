# Device-level non-idealities

Device-level errors originate in the physical memory element and its programmed state. Typical examples include device-to-device conductance variation, cycle-to-cycle programming uncertainty, temporal noise, drift, retention degradation, and nonlinear state-dependent responses.

## Computational impact

For an intended conductance matrix G, a simple first-order representation is G_actual = G + ΔG. The resulting dot-product perturbation is Δy = ΔG x, assuming the applied input x and readout path are ideal. This model does not capture all state-dependent or time-varying behavior.

## Evaluation questions

- Is variation spatial, temporal, or both?
- Does error depend on conductance state, read voltage, or temperature?
- Are distributions measured across devices and programming cycles?
- How does device error interact with downstream array and sensing errors?

See [Error propagation](error_propagation.md) and [Mitigation](mitigation.md).
