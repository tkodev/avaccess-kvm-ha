# Prototype design and validation plan

The passive schematic is reconstructed and matches the independent blind review. Planned initial static voltage tests are complete. See [actual readings](readings.md), [schematic](schematic.md), and [independent safety review](verification/round-1/comparison-and-safety-review.md). Both resistor-to-LED junctions originally inferred in the review are now directly confirmed at 0.1 Ω each; see the [post-review evidence](verification/round-1/post-review-measurements.md). No ESP32 interface, power-state behavior or firmware has been tested.

Always pair wire colours with contacts: red/tip, white/ring 1, green/ring 2, black/sleeve. White/ring 1 is the differential reference used for measurements; its compatibility with an ESP/USB ground bond is unverified.

## 1. Resolve only essential remaining remote evidence

Completed direct continuity checks with the remote unplugged: R1 A-to-LED2 + = 0.1 Ω; R3 A-to-LED1 + = 0.1 Ω. No further tests are needed to resolve those links. A photo/trace audit can add evidence if desired. Record J1's actual cavity order and viewing orientation before making a harness.

Do not characterize current limits by shorting, overloading or arbitrary resistor sweeps on the sole KVM. More static meter readings alone cannot prove transient or power-off safety.

## 2. Choose the prototype architecture

Use [controller solutions](solutions.md) as the source for the owner's preferred architecture and alternatives. See [interface protection](interface-protection.md) for powered-off, startup, loading and transient checks. These are interface design requirements, not additional measured properties of the KVM.

The owner's preferred controller replaces the original remote with a new board or XIAO plus minimal interface parts. It has no pass-through, original remote PCB, indicator LEDs or manual buttons. Prioritize a default-off button driver and protected electrical status sensing. Bare GPIO remains conditional; revision A supplies the proposed interface for evaluation.

The [revision A schematic](controller-schematic.md) now specifies a proposed circuit and BOM for this evaluation: PhotoMOS closure and MOSFET sensing, with the XIAO's internal status pull-up and a series GPIO resistor. Treat its power-state calculations as design evidence, not physical test results.

Remote-PCB reuse with isolated KEY1 closure remains an alternative prototype that preserves known loading. Separate ESP power and optical LED sensing can avoid a galvanic control/status connection, but this is not the preferred final form.

Across KEY1 on a retained remote PCB, existing R2 remains in circuit; do not double its resistance. For the preferred new board, preserve R2's ~100 Ω function exactly once in a red/tip-to-black/sleeve closure. Treat the measured passive network as the comparison baseline. Omission of R1/R3, LEDs or C1 requires separate validation; retaining C1 does not require retaining the original board.

Select actual contact/driver parts from datasheets, checking on-resistance, off leakage, transient current, voltage, isolation, default state and failure modes. Steady button current is about 2.18 mA; capacitor charging may require a larger transient capability. Firmware high impedance alone does not establish electrical power-off isolation.

The direct-GPIO-HIGH/high-side-driver option is conditional and unvalidated. It needs verified thresholds, reference compatibility, power-off protection and bounded pin voltages/currents before use. A GPIO-LOW pulse does not emulate the observed active-high button. Do not power the ESP32 from black/sleeve or connect its 3V3 rail to black/sleeve.

## 3. Validate disconnected from the KVM

Build and inspect the complete harness/interface using a current-limited bench source and a simulated remote/button-input load. Confirm each plug contact, connector cavity and component connection. Check for shorts, unintended supply injection, off-state leakage and operation with the original passive network.

Exercise controller power on/off, simulated KVM on/off, either startup order, reset, bootloader/flashing, brownout and loss of network. Verify a hardware released default and no unintended closure. Check pulse duration and a stuck-output scenario; use hardware limiting if a frozen output must not hold the button indefinitely. A meter cannot rule out short glitches; observe waveforms with appropriate instruments where needed.

An electrically connected feedback receiver needs validation at status LOW/HIGH, transitions and receiver power off. The unloaded/loaded measurements do not establish spare source/sink current. An optocoupler input also loads a signal; isolation alone does not make arbitrary loading acceptable.

## 4. Controlled first KVM connection

Only after disconnected checks pass, inspect the assembly again, insulate joints, provide strain relief and connect with the relevant power sources removed. Avoid live TRRS insertion/removal and never use partial insertion for probing.

Test one bounded press using the validated interface within previously observed working durations. Confirm one selection change and release. Check HIGH/LOW status feedback and manual KVM front-panel changes; no LEDs or buttons are required on the new controller. If evaluating the remote-PCB fallback, initially retain its original load and use the isolated contact. Map PC names later. Do not search destructive limits on the KVM.

If further waveform checks are essential, use suitable high-impedance differential/isolated equipment. Do not attach an earthed scope ground to an unverified node. Keep MM700 leads in COM/VΩ for voltage tests, use ordinary DC volts rather than Lo Z, and never use resistance/diode/capacitance modes on a powered circuit or place an ammeter across a signal.

## 5. ESPHome after interface validation

Revision A proposes D1/GPIO1 for the press command and D2/GPIO2 for inverted status. Confirm the board revision and startup behavior. Enable D2's internal pull-up before interpreting it; feedback is unknown before configuration. No firmware is implemented. One press command and one binary status may suffice; PC names remain deferred. Without separate evidence of KVM power, a HIGH GPIO reading does not distinguish the original LED2 state from a disconnected/powered-off KVM.

Use a momentary Toggle input command, reject overlap and wait for settled feedback. A roughly 200 ms pulse is only an initial prototype trial, not a measured B10 timing requirement. For explicit selection, read stable feedback, press once if needed, then confirm with a timeout. Treat unsettled state as unknown and avoid automatic toggle retries. Selection feedback does not establish PC/video readiness.

Use encrypted ESPHome API and authenticated OTA; keep credentials out of git. Verify firmware, reset/flash and power sequencing on the disconnected setup before using the KVM.

## Completion criteria

- Reviewed component ratings and assembled wiring match the intended interface.
- Controller faults/power sequencing do not inject voltage or cause unintended/sustained presses in the tested cases.
- Electrical sensing, if used, meets loading, voltage and power-off limits.
- One command produces one confirmed selection change; manual changes update feedback.
- Original remote functionality is retained when restored.

A documentary review and a passing bench test provide evidence for the tested configuration, not a zero-risk guarantee.
