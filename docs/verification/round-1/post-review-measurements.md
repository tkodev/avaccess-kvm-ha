# Post-review measurements

This note was added after the independent review; it does not modify the locked Phase A reconstruction, input packet, review report or exact reviewed-reference snapshot.

## Resolved findings

The review identified two resistor-to-LED junctions supported by indirect readings but lacking direct continuity checks. The owner subsequently measured, with the remote unplugged from the KVM:

- R3 A ↔ LED1 +: **0.1 Ω**.
- R1 A ↔ LED2 +: **0.1 Ω**.

Both now have direct near-zero-resistance evidence, confirming the proposed netlist links. These new measurements were supplied after the blind review and were not independently repeated by the reviewer or parent agent.

## Remaining boundary

The passive circuit has stronger physical evidence. No ESP32 interface has been built or tested. Supply capacity, source/sink limits, transient behavior, grounding/power-off paths and startup/fault behavior remain outside the existing validation. The review's isolated normally-open closure and disconnected-prototype recommendations still apply.

See [current schematic](../../schematic.md), [actual readings](../../readings.md), and the original [comparison/safety review](comparison-and-safety-review.md).
