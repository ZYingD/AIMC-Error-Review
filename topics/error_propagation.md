# Cross-layer error propagation

An error introduced in an AIMC device, array, or peripheral circuit can change form as it passes through later stages. Evaluating isolated component error is therefore not sufficient to predict application-level accuracy.

## A compact mathematical view

For a linear layer with ideal output y = Wx, perturbations in weights and inputs produce

```text
(W + ΔW)(x + Δx) - Wx
  = ΔW x + W Δx + ΔW Δx
```

The cross term may be negligible for small perturbations but should not be silently discarded. Circuit transfer nonlinearities, ADC quantization, and clipping introduce further effects that cannot always be represented as independent additive noise.

For a multilayer network, a local perturbation can be attenuated or amplified by later transformations. Activation functions, normalization, quantization, and data-dependent operating points influence that propagation.

## Suggested evaluation workflow

1. Establish an ideal numerical baseline.
2. Characterize device, array, circuit, and conversion errors under stated conditions.
3. Inject one error source at a time to identify sensitivity.
4. Evaluate coupled errors using realistic correlations and operating points.
5. Report intermediate-layer deviations alongside task-level metrics.

## Important distinction

The five-level framework includes a **system level**: mapping, tiling, scheduling, digital accumulation, and algorithmic choices determine how physical errors affect end-to-end accuracy.

See [Mitigation](mitigation.md) and the [repository overview](../README.md).
