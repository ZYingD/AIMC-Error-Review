# Cross-layer error mitigation

Mitigation is most effective when the correction mechanism matches the error source, its variability, and the cost of correction.

## Candidate approaches

| Level | Illustrative approaches | Trade-offs |
| --- | --- | --- |
| Device | Program-and-verify, state selection, device-aware modeling | Write cost, endurance, characterization effort |
| Array | Smaller tiles, compensation, mapping strategies | Peripheral overhead, routing, utilization |
| Circuit | Offset cancellation, reference tracking, calibration | Area, latency, energy, calibration lifetime |
| Conversion | Range optimization, adaptive precision, converter calibration | Converter complexity, accuracy-energy trade-off |
| System | Variation-aware mapping, quantization-aware or hardware-aware training | Training cost, portability, retraining needs |

## Reporting checklist

Compare end-to-end task accuracy, energy, latency, area, and robustness under matched conditions. Distinguish one-time calibration from recurring calibration, and quantify the cost of additional measurements or control circuitry.

Cross-layer mitigation does not imply every design needs every technique. The goal is to locate the dominant error pathways and intervene where the system-level benefit justifies the cost.

See [Error propagation](error_propagation.md).
