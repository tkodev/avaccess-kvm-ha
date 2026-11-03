# Reconstructed remote schematic

This is the best-supported reconstruction of the owner measurements, not a manufacturer schematic. A fresh subagent reconstructed the same netlist from a readings-only packet before seeing this document. See its [locked reconstruction](verification/round-1/independent-schematic.md), [checkpoint](verification/round-1/phase-a-checkpoint.json) and [comparison/safety review](verification/round-1/comparison-and-safety-review.md).

The remote schematic is supported by the recorded functional tests and direct continuity measurements. The two resistor-to-LED junctions originally inferred during review were subsequently measured directly at 0.1 Ω each; see the [post-review evidence](verification/round-1/post-review-measurements.md). This strengthens the physical netlist but does not uniquely exclude hidden parallel paths or validate an ESP32 connection.

## Circuit

- Black/sleeve -> R1 (~1 kΩ) -> LED2 anode; LED2 cathode -> green/ring 2.
- Green/ring 2 -> R3 (~510 Ω) -> LED1 anode; LED1 cathode -> white/ring 1.
- Red/tip -> KEY1 -> R2 (~100 Ω) -> black/sleeve.
- Red/tip -> C1 (~100 nF) -> white/ring 1.

Green/ring 2 is an external midpoint. The LED branches need not carry equal current. View [both layouts](schematic.html), the [connected layout](schematic-connected.html), the [simple branches](schematic-simple.html), or the intermediate [grouped layout](schematic-grouped.html).

## Terminal mapping and evidence

- R1 B is directly on black/sleeve. R1 A joins LED2 +, directly confirmed at 0.1 Ω after review. Earlier evidence: LED2 + to black/sleeve = 992 Ω, and R1 A to green/ring 2 lights LED2 at 2.57 V.
- R3 B is directly on green/ring 2. R3 A joins LED1 +, directly confirmed at 0.1 Ω after review. Earlier evidence: LED1 + to green/ring 2 ≈ 500 Ω, and R3 A to white/ring 1 lights LED1 at 2.56 V.
- LED2 cathode connects directly to green/ring 2 (0.9 Ω); LED1 cathode directly to white/ring 1 (0.7 Ω).
- R2 B connects directly to black/sleeve. R2 A reaches KEY1's left-side net; red/tip to black/sleeve measures approximately 101 Ω pressed.
- C1 A, nearest the PCB edge, connects directly to white/ring 1. C1 B connects directly to red/tip.
- KEY1, viewed from the LED face with LEDs below: right-side pair is red/tip; left-side pair reaches black/sleeve through R2. Each same-side upper/lower pair measures 0.1 Ω released. Pressing joins the two sides.
- In-circuit component readings: R1 992 Ω; R2 99.6 Ω; R3 508 Ω; C1 98.7 nF. Nominal values in the diagram are approximate. Component voltage ratings and tolerances are not established.
- J1 cavity numbering and connector viewing orientation were not separately recorded. TRRS order must not be used to invent J1 pin numbering.

## Observed powered behavior

All voltage levels below use white/ring 1 as the differential reference. The [full reading list](readings.md) separates unloaded/loaded tests and retains repeated/corrected observations.

- Black/sleeve: 3.334 V unloaded. With the remote attached: 3.339 V released with LED1 on, 3.117 V held toward LED2, 3.282 V released with LED2 on, 3.080 V held toward LED1.
- Green/ring 2: 31.1 mV and 3.34 V unloaded across a selection change. Latest loaded readings: 33 mV with LED2 on and 3 V with LED1 on; the owner confirms the high level persists while LED1 is lit.
- Red/tip: 3 mV unloaded at rest; latest loaded readings are 0 V released in both LED states, 2.895 V held toward LED2 and 2.861 V held toward LED1. Explicit hold/release tests confirm a sustained active-high press.
- Direct R2 pressed drop: 217 mV. Across measured 99.6 Ω this gives approximately 2.18 mA steady held current, calculated rather than directly measured.
- Quick taps and roughly 0.5–1 s presses can switch inputs. Minimum pulse, debounce, cooldown, repeat-on-hold and activation edge were not measured.

These observations support black/sleeve as the remote supply, white/ring 1 as its reference, green/ring 2 as binary selection feedback and red/tip as an active-high input. White/ring 1 is not verified as a safe bond to USB/earth. Push-pull status drive is plausible, but its circuit type, source/sink ratings and transient envelope are unknown. PC names are intentionally deferred; use LED1/HIGH and LED2/LOW.

## Corrections and limits

The possible J1-disconnection block is excluded from topology evidence. The earlier 2.1 mV status readings are retained as suspect contact observations, with no confirmed cause. Unit corrections and withdrawn capacitor observations remain in [readings.md](readings.md) and [bench history](bench-notes.md).

R1 A-to-red/tip pressed readings of 1.075/1.057 kΩ do not precisely equal R1 + R2 = 1.0916 kΩ. The independent reviewer retained this discrepancy. In-circuit readings and OL results do not uniquely prove all paths.

The 2.18 mA steady current does not bound C1 charging current. An ideal 3.3 V source through 99.6 Ω into discharged C1 would initially give about 33 mA; real source impedance changes that. This is a design estimate, not a measured transient or component rating. C1's local R2×C1 contribution is about 9.8 µs; it does not establish KVM debounce timing.

## Interface safety boundary

No direct GPIO interface is approved or implemented. Matching the passive remote schematic does not establish input thresholds, power-off tolerance, supply capacity, status loading limits or startup behavior.

The recommended first prototype preserves the remote PCB and its passive/LED loads, using an independently powered, normally-open isolated contact across KEY1. Original R2 then remains in circuit: do not add another 100 Ω merely for a KEY1-parallel closure. A standalone red/tip-to-black/sleeve replacement needs R2's function exactly once.

Optical observation of the original LEDs can avoid an electrical status connection. An electrically connected receiver needs explicit loading and power-off protection analysis. Removing the original LED branches/C1 is a separate change requiring validation.

A direct GPIO HIGH or high-side driver was an earlier conditional proposal, not validated wiring. The owner's GPIO-LOW technique does not emulate this active-high press. Review the [prototype plan](bring-up.md) and independent safety findings before selecting parts or making any controller connection.
