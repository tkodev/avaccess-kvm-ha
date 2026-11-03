# Reconstructed remote schematic

Reconstructed from owner measurements, not a manufacturer schematic. See [both schematic views](schematic.html), [original connected layout](schematic-connected.html), [simple branch view](schematic-simple.html), [bench history](bench-notes.md) and [measurement data](measurements.csv). One powered unloaded reading is recorded: black/sleeve to white/ring 1 = +3.334 V. No loaded-remote or ESP32 interface has been validated.

## Circuit

- Black/sleeve -> R1 (~1 kΩ) -> LED2 anode; LED2 cathode -> green/ring 2.
- Green/ring 2 -> R3 (~510 Ω) -> LED1 anode; LED1 cathode -> white/ring 1.
- Red/tip -> KEY1 -> R2 (~100 Ω) -> black/sleeve.
- Red/tip -> C1 (~100 nF) -> white/ring 1.

Green/ring 2 is an externally accessible midpoint between the LED branches. Do not infer that both LEDs always carry the same current, or that this circuit identifies ground or supply.

## Component terminals and evidence

- R1 B is directly on black/sleeve. R1 A is on the LED2 anode-side node, supported by LED2 + to black/sleeve = 992 Ω and R1 A to green/ring 2 lighting LED2 at 2.57 V in diode mode.
- R3 B is directly on green/ring 2. R3 A is on the LED1 anode-side node, supported by LED1 + to green/ring 2 ≈ 500 Ω and R3 A to white/ring 1 lighting LED1 at 2.56 V.
- LED2 cathode is directly on green/ring 2 (0.9 Ω); LED1 cathode directly on white/ring 1 (0.7 Ω).
- R2 B is directly on black/sleeve. R2 A reaches KEY1's left-side net. Red/tip to black/sleeve measures approximately 101 Ω when pressed.
- C1 A, nearest the PCB edge, is directly on white/ring 1. C1 B is directly on red/tip.
- KEY1, viewed from the LED face with LEDs at the bottom: right pair is red/tip; left pair reaches black/sleeve through R2. Within each side, upper-to-lower = 0.1 Ω released. Across sides the switch closes only when pressed.
- Direct component readings: R1 = 992 Ω; R2 = 99.6 Ω; R3 = 508 Ω; C1 = 98.7 nF. These are in-circuit values, with approximate nominal values used in the drawing.

The resistor-to-LED links are supported by multiple consistent measurements; direct opposite-face continuity was impractical and is not claimed. LED2 + to red/tip ≈ 1.1 kΩ pressed and OL released provides an additional R1-plus-R2 consistency check.

## Excluded readings

The owner identified possible J1 disconnection starting with the first LED1 + / LED2 + to black/sleeve request. That block, including the later LED1 cathode-to-white/ring 1 OL recheck, is excluded from schematic evidence. Fresh post-J1-check readings and the earlier valid measurements above support this reconstruction. CSV notes flag the suspect block; the bench history retains the correction.

## Still unknown

- KVM-side ground, supply and drive method, including whether signals are multiplexed.
- Operating voltages, sink/source currents and behavior in different power states.
- Which selected computer corresponds to each physical LED.
- C1's precise function; its red/tip-to-white/ring 1 connection alone does not prove debounce.
- Final ESP32 interface, conditioning, power arrangement and firmware.

Offline functional tracing is complete for the listed components. Review this circuit before planning any powered voltage measurements. Keep the ESP32 disconnected until electrical compatibility is established.

## Likely operating interpretation, not yet voltage-verified

A simple explanation for the traced network is black/sleeve = positive supply, white/ring 1 = ground, green/ring 2 = one push-pull selection-status output, and red/tip = an active-high button input. Under that hypothesis, green/ring 2 high lights LED1 toward white/ring 1 while LED2 is off; green/ring 2 low lights LED2 from black/sleeve while LED1 is off. This uses one status GPIO for two complementary indicators, plus one button input. It does not require two independently driven status outputs or a controller in the remote.

The button would raise red/tip toward black/sleeve through 100 Ω, with C1 from red/tip to white/ring 1 providing possible input filtering. A KVM-side pull-down would be expected but has not been measured. In this interpretation a GPIO pull-to-ground would not emulate the press. All four functional assignments remain hypotheses pending powered measurements; no voltage or ESP32 compatibility is inferred.

## Button emulation implications

The owner's GPIO-LOW technique on another device is appropriate only if a button signal is pulled toward ground. Here the traced press joins red/tip to black/sleeve through 100 Ω. Under the proposed positive-supply role for black/sleeve, this is active-high and a LOW pulse would do the opposite.

If voltage/current thresholds and power-state behavior permit, direct GPIO HIGH through 100 Ω with high-impedance release may emulate the press. Otherwise use a suitable high-side driver or an isolated closure reproducing red/tip-to-black/sleeve through 100 Ω. A GPIO is not a floating switch, and open-drain mode does not provide voltage translation. No such circuit is selected or validated yet. See the [build plan](bring-up.md) for the measurement sequence.

The KVM controller, not the passive remote, is expected to detect a press, toggle selection and drive green/ring 2 high or low. Its exact debounce and held-button behavior remain untested. Green/ring 2 feedback could supply one input for both selected states once its levels and polarity are verified.

Display layouts: [schematic.html](schematic.html) preserves both the original connected circuit and the latest simple four-branch view. Each also has its own file linked above. Repeated wire/contact labels in the simple view denote the same nets. The intermediate grouped-terminal layout is retained separately as [schematic-grouped.html](schematic-grouped.html).

Powered evidence: cable-side J1, disconnected from the remote PCB, measures black/sleeve = +3.334 V relative to white/ring 1. This supports the supply/reference interpretation, pending other states and loaded measurements. It does not establish supply-current capability or ESP32 compatibility.

Unloaded status test: green/ring 2 relative to white/ring 1 changed from 31.1 mV to 3.34 V after a KVM front-panel selection change. Binary selection feedback is supported under the tested conditions; PC numbering and powered loaded LED behavior remain unverified.

Unloaded button-input rest: red/tip to white/ring 1 = 3 mV with J1 disconnected from the PCB. This supports a LOW resting input but does not yet establish pressed voltage. Loaded measurement is planned across C1 A (white/ring 1 reference) and C1 B (red/tip).

## Loaded press polarity verified

Across C1, referenced to C1 A / white/ring 1, red/tip measures 0 V released and 2.86 V pressed with the original remote attached to the powered KVM. This confirms active-high press polarity, superseding earlier uncertainty about whether the press raises or lowers red/tip. Loaded supply voltage, press current and ESP32 interface limits remain to be checked. No GPIO wiring has been validated.

Loaded supply observation: black/sleeve relative to white/ring 1 = 3.3 V released and 3.1 V pressed, probed at R2 B and C1 A. This rail is not established as a suitable ESP32 power source. Direct R2 pressed voltage and current remain pending.

Held-press R2 measurement: 217 mV across measured 99.6 Ω yields approximately 2.18 mA through the button branch. This is an Ohm's-law estimate from the intact loaded circuit, not a direct ammeter reading or a complete GPIO compatibility test.

Loaded status LOW is now observed: green/ring 2 to white/ring 1 = 0.8 mV with LED2 lit. Selected PC identity and loaded HIGH state remain unreported.

Loaded status discrepancy: subsequent R3 B / green/ring 2 to C1 A / white/ring 1 readings were 2.1 mV with LED2 on and 2.1 mV after a button press with LED1 on. Whether KEY1 remained held in the second reading is unresolved. A settled released reading and probe verification are needed before claiming loaded binary status levels.

Latest loaded status retest: green/ring 2 relative to white/ring 1 = 33 mV with LED2 on and 3 V with LED1 on after pressing the button. This matches the reconstructed LOW/HIGH pattern. Previous 2.1 mV-in-both-states readings remain historical discrepancies of unknown cause. Fully released/settled conditions were requested but not explicitly restated.

Status persistence and labels: owner confirms 3 V stays while LED1 is lit. Use LED1/HIGH and LED2/LOW; PC labels are intentionally deferred. Both quick taps and approximately 0.5–1 s held presses can trigger switching, but no debounce, minimum pulse or cooldown has been measured.

Measurement quality update: the owner suspects incomplete probe-to-pad contact caused the earlier 2.1 mV status readings. Treat them as suspect historical observations; the consistent later 33 mV LED2/LOW and 3 V LED1/HIGH readings support the current status mapping. Cause has not been independently confirmed.

Held-button behavior rechecked: red/tip remains at 2.86 V relative to white/ring 1 while KEY1 is held and returns to 0 V released. The input is a sustained active-high level during a held press under the tested conditions.

Latest state-labelled button test, probes explicitly confirmed at C1 B / red/tip relative to C1 A / white/ring 1: released 0 V in both LED states; held press toward LED2 2.895 V; held press toward LED1 2.861 V. This set is the owner's preferred button measurement. Supply state comparison remains separate and pending.

## Final initial live supply comparison

Black/sleeve relative to white/ring 1, original remote attached: 3.339 V released with LED1 on, 3.117 V held toward LED2, 3.282 V released with LED2 on, 3.080 V held toward LED1. This completes the planned initial live characterization together with the state-labelled C1 button readings and loaded status observations. Final ESP32 interface and power-state validation remain pending; PC labels are deferred.
