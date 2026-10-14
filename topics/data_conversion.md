# Data-conversion non-idealities

DACs and ADCs bridge digital representations and analog array computation. Their limitations include quantization, clipping, integral and differential nonlinearity, reference variation, offset, and conversion noise.

## Computational impact

For an ideal uniform quantizer, quantization error is bounded by approximately half an LSB when the signal lies inside the supported range and rounding is used. The familiar uniform-noise approximation requires additional assumptions and should not be applied blindly to correlated or saturated signals.

## Evaluation questions

- Are resolution, full-scale range, and clipping reported together?
- Does the converter resolution match the actual signal distribution?
- Are converter energy and area included in system comparisons?
- How does conversion interact with bit slicing and partial-sum accumulation?

See [Error propagation](error_propagation.md) and [Mitigation](mitigation.md).
