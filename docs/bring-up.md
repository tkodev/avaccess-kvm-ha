# Prototype design and validation plan

The passive schematic is reconstructed and matches the independent blind review. Planned initial static voltage tests are complete. See [actual readings](readings.md), [schematic](schematic.md), and [independent safety review](verification/round-1/comparison-and-safety-review.md). Both resistor-to-LED junctions originally inferred in the review are now directly confirmed at 0.1 Ω each; see the [post-review evidence](verification/round-1/post-review-measurements.md). No ESP32 interface, power-state behavior or firmware has been tested.

Always pair wire colours with contacts: red/tip, white/ring 1, green/ring 2, black/sleeve. White/ring 1 is the differential reference used for measurements; its compatibility with an ESP/USB ground bond is unverified.

## 1. Resolve only essential remaining remote evidence

Completed direct continuity checks with the remote unplugged: R1 A-to-LED2 + = 0.1 Ω; R3 A-to-LED1 + = 0.1 Ω. No further tests are needed to resolve those links. A photo/trace audit can add evidence if desired. Record J1's actual cavity order and viewing orientation before making a harness.

Do not characterize current limits by shorting, overloading or arbitrary resistor sweeps on the sole KVM. More static meter readings alone cannot prove transient or power-off safety.

## 2. Choose the prototype architecture

The owner's final controller replaces the original external remote; no pass-through is required. Recommended first prototype: reuse the remote PCB inside the controller to preserve R1/R2/R3/C1 and LED loading, and add an isolated normally-open contact across KEY1. Separate ESP power and optical LED sensing can avoid a galvanic control/status connection. This is a recommendation, not a selected part or validated assembly.

Across KEY1, existing R2 remains in circuit; do not double its resistance. If building a complete replacement, reproduce the measured passive network initially and implement red/tip-to-black/sleeve closure with R2's ~100 Ω function exactly once. Validate any omitted LED/capacitor load separately.

Select actual contact/driver parts from datasheets, checking on-resistance, off leakage, transient current, voltage, isolation, default state and failure modes. Steady button current is about 2.18 mA; capacitor charging may require a larger transient capability. Firmware high impedance alone does not establish electrical power-off isolation.

The direct-GPIO-HIGH/high-side-driver option is conditional and unvalidated. It needs verified thresholds, reference compatibility, power-off protection and bounded pin voltages/currents before use. A GPIO-LOW pulse does not emulate the observed active-high button. Do not power the ESP32 from black/sleeve or connect its 3V3 rail to black/sleeve.

## 3. Validate disconnected from the KVM

Build and inspect the complete harness/interface using a current-limited bench source and a simulated remote/button-input load. Confirm each plug contact, connector cavity and component connection. Check for shorts, unintended supply injection, off-state leakage and operation with the original passive network.

Exercise controller power on/off, simulated KVM on/off, either startup order, reset, bootloader/flashing, brownout and loss of network. Verify a hardware released default and no unintended closure. Check pulse duration and a stuck-output scenario; use hardware limiting if a frozen output must not hold the button indefinitely. A meter cannot rule out short glitches; observe waveforms with appropriate instruments where needed.

An electrically connected feedback receiver needs validation at status LOW/HIGH, transitions and receiver power off. The unloaded/loaded measurements do not establish spare source/sink current. An optocoupler input also loads a signal; isolation alone does not make arbitrary loading acceptable.

## 4. Controlled first KVM connection

Only after disconnected checks pass, inspect the assembly again, insulate joints, provide strain relief and connect with the relevant power sources removed. Avoid live TRRS insertion/removal and never use partial insertion for probing.

Initially retain the original load and test one bounded isolated press within previously observed working durations. Confirm one selection change and release. Observe LED1/HIGH and LED2/LOW feedback and manual front-panel changes. Map PC names later. Do not search destructive limits on the KVM.

If further waveform checks are essential, use suitable high-impedance differential/isolated equipment. Do not attach an earthed scope ground to an unverified node. Keep MM700 leads in COM/VΩ for voltage tests, use ordinary DC volts rather than Lo Z, and never use resistance/diode/capacitance modes on a powered circuit or place an ammeter across a signal.

## 5. ESPHome after interface validation

No final GPIO assignment or firmware is selected. Candidate XIAO pins require board-revision/datasheet confirmation and verified startup behavior. One press command and one binary status may suffice; PC names remain deferred.

Use a momentary Toggle input command, reject overlap and wait for settled feedback. A roughly 200 ms pulse is only an initial prototype trial, not a measured B10 timing requirement. For explicit selection, read stable feedback, press once if needed, then confirm with a timeout. Treat unsettled state as unknown and avoid automatic toggle retries. Selection feedback does not establish PC/video readiness.

Use encrypted ESPHome API and authenticated OTA; keep credentials out of git. Verify firmware, reset/flash and power sequencing on the disconnected setup before using the KVM.

## Completion criteria

- Reviewed component ratings and assembled wiring match the intended interface.
- Controller faults/power sequencing do not inject voltage or cause unintended/sustained presses in the tested cases.
- Electrical sensing, if used, meets loading, voltage and power-off limits.
- One command produces one confirmed selection change; manual changes update feedback.
- Original remote functionality is retained when restored.

A documentary review and a passing bench test provide evidence for the tested configuration, not a zero-risk guarantee.
