# Array-level non-idealities

Analog crossbar arrays exhibit effects that cannot be inferred from isolated memory cells. Wire resistance and parasitic capacitance, voltage drops, coupling, and topology-dependent current paths can alter the effective voltages and accumulated signals.

## Computational impact

In an ideal linear crossbar, y = Gx. In a practical array, effective device voltages may depend on input pattern, conductance distribution, array geometry, and line resistance. Therefore, a fixed independent weight-error model may be insufficient.

## Evaluation questions

- How do error magnitude and spatial distribution change with array dimensions?
- Are input patterns and programmed conductance distributions varied?
- Are near-end and far-end locations compared?
- Does partitioning into smaller tiles improve accuracy enough to justify overhead?

See [Circuit-level errors](circuit_level.md) and [Error propagation](error_propagation.md).
