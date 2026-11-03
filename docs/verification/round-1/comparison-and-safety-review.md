# Independent comparison and electrical safety review

## Verdict and review boundary

The proposed passive-remote netlist **matches** the independently reconstructed netlist for every listed component, LED orientation, contact and switch leg. No topology mismatch was found. This is agreement between interpretations of the same owner-reported measurements, not independent physical verification or proof that the topology is unique.

**Matching the remote schematic is not enough to validate any ESP32 wiring. No direct connection is approved by this review.** Static meter evidence supports active-high actuation and binary status in the observed states, but does not establish interface limits across transients, reset or power sequencing. No interface has been built, attached or physically tested.

Phase A used only `input-readings.md` as project evidence. Its artifact was written, then locked in a separate tool call before reading `docs/schematic.md` and `docs/bring-up.md`. No other project files or embedded project links were read. External primary MCU/board documentation was consulted only in Phase B. Phase A SHA-256:

`c1b5e9075c05d9b42553d89d2c5d7300125b64a6a22d025e54afedf31bd4b586`

## Component-by-component comparison

Node names below are from the locked reconstruction: V = sleeve/black; G = ring 1/white; S = ring 2/green; P = tip/red. These names do not establish earth ground or supply ratings.

- **R1:** B on V; A feeding LED2 anode; measured 992 ohm, nominal about 1 kohm. Exact match. R1 A-to-anode remains inferred from several resistance/diode paths, not a direct zero-ohm check.
- **LED2:** anode after R1, cathode on S. Exact match. Cathode-to-S 0.9 ohm and R1 A-to-S diode excitation lighting LED2 support orientation. Its anode-to-V 992 ohm and anode-to-P approximately 1.1 kohm only pressed support pairing.
- **R3:** B on S; A feeding LED1 anode; measured 508 ohm, nominal about 510 ohm. Exact match. R3 A-to-anode is inferred, as above.
- **LED1:** anode after R3, cathode on G. Exact match. Cathode-to-G 0.7 ohm and R3 A-to-G diode excitation lighting LED1 support orientation. Anode-to-S about 500 ohm supports pairing.
- **R2:** B on V, A on left KEY1 net K; measured 99.6 ohm, nominal about 100 ohm. Exact match. Tip-to-V approximately 101 ohm pressed agrees.
- **KEY1:** normally open K-to-P; upper/lower left legs join K, upper/lower right legs join P, viewed from the LED face with LEDs below. Exact match. Same-side 0.1 ohm readings establish paired legs; R2 A-to-P changes from OL released to 0.7 ohm pressed.
- **C1:** A, nearest edge, on G; B on P; measured 98.7 nF, nominal about 100 nF. Exact match. No capacitor polarity or voltage rating was established.
- **Cable/connector:** tip red P, ring 1 white G, ring 2 green S, sleeve black V. Exact match. Neither reconstruction establishes J1 cavity numbering or its viewing orientation; a future connector drawing must not invent those from TRRS order.

S is externally driven and joins the LED branches. Their currents need not be equal. Supply-to-G diode OL does not refute two series LEDs whose combined forward voltage may exceed meter compliance. Direct LED tests reported 2.56 V and separate lighting; polarity was requested but not separately confirmed for both. Cross-node tests make the stated polarity persuasive, not manufacturer-certified.

## Falsification, exclusions and numerical consistency

The minimum topology explains the observations without extra parts. Hidden parallel paths, contact errors and semiconductor excitation can still imitate parts of it. In-circuit resistance/capacitance readings are not isolated component values, and OL is not universal proof of no path. No-beep across all plug pairs cannot negate a measured 100 ohm pressed path: the beep threshold was not supplied. The partial R3-to-V/G answers cannot be expanded into verified checks of both ends.

Withdrawn unlabelled C1 readings are excluded in favor of the corrected 97.5 nF C1 A-to-tip and switched C1 A-to-sleeve readings. The erroneous R1 A-to-V 0.995 ohm report is corrected to 995 ohm **pressed only**; no corrected released value was supplied. Initial zero released diode mode is superseded by settled OL. The possible J1-disconnection block, including LED1 cathode-to-G OL, is excluded rather than used to overturn the earlier continuity. Fresh reseated measurements support the preferred topology.

The 2.1 mV S readings in both LED states remain a real historical contradiction. Poor pad contact is suspected but unconfirmed. Later 33 mV LED2-on and 3 V LED1-on support binary status; the latter's exact held/released condition was not restated. The owner reports persistence while LED1 stays lit. A settled repeat would resolve this more cleanly than deleting the inconvenient readings. The ambiguous 3.3 mV P statement and unreadable transient cannot establish held behavior; later explicitly held/released four-state readings resolve that issue.

Calculated checks, distinct from actual readings:

- R1 + R2 = 1,091.6 ohm. R1 A-to-P pressed measured 1,075 and 1,057 ohm, lower by 16.6 and 34.6 ohm (1.5% and 3.2%). Contact/meter/in-circuit effects are possible, but a precision series-sum match cannot be claimed. LED2 anode-to-P about 1.1 kohm is only a coarse confirmation.
- Direct R2 drop: 0.217 V / 99.6 ohm = **2.18 mA**, an inferred steady pressed current, not an ammeter measurement.
- Separately measured same-direction V and P yield (3.117 - 2.895)/99.6 = **2.23 mA**, and (3.080 - 2.861)/99.6 = **2.20 mA**. They are not simultaneous samples. Earlier approximate 3.1 - 2.86 gives 2.41 mA and is less precise.
- If P had only a resistive return, P/current would suggest about **1.3 kohm**. This is an equivalent operating-point inference, not a measured pull-down or proof of linear behavior.
- R2 × C1 = **9.83 microseconds**, only the local RC contribution. A discharged C1 initially connected through 99.6 ohm to about 3.3 V could draw roughly **33 mA** briefly under an ideal-source model. Supply impedance and internal circuitry can change this. Steady 2.2 mA alone is insufficient for selecting a contact/driver. Neither RC estimate establishes KVM debounce or press recognition timing.
- V is 3.339/3.282 V released and 3.117/3.080 V held in the labelled set. Before/after held transitions fall about 0.20–0.22 V. Comparing the same LED state instead gives 0.259/0.165 V, but selection and acquisition differ. An inferred single supply resistance would be misleading. Sag is established; source capability is not.
- The 2.56 V meter-excited LED readings must not be used as fixed operating drops to calculate actual LED currents. No operating resistor drops for R1/R3 were supplied.

## Documentation objections

The appended results in `schematic.md` are broadly consistent with the packet, including active-high pressed P, sag and corrected status mapping. However, its opening claim of only one powered unloaded reading, earlier pending loaded/current statements and later completed results conflict. `bring-up.md` likewise says loaded supply/current and voltage-verified roles remain untested after announcing characterization complete. These are stale narrative statements, not contrary measurements. A reader should use the labelled latest readings with their limitations.

“Offline functional tracing is complete” is too strong if read as unique component-level proof: two resistor-to-anode joins remain inferred. Push-pull S is plausible, not established; a resistive or other source/sink arrangement could produce the readings. White is a usable differential measurement reference, not a verified safe bond to USB/earth. Active-high P is well supported for the observed powered states; its required thresholds, internal return, maximum current and off-state behavior are not established.

The bring-up plan has useful constraints: separate controller power, no powered resistance tests, no ammeter shunt, no partial TRRS insertion, default release, unknown-state handling and no blind retry. Its direct-GPIO possibility is conditional, not an implementation approval. Replacing the remote is a larger validation problem than merely adding an isolated closure across its existing KEY1. Removing LED branches and C1 changes loads and filtering; the cable-only measurements cover only a few static points, not equivalent operation.

## Severity-ranked risks and required controls

**High, possible hardware damage: supply/ground mistakes and connector bridges.** Sleeve/black is positive relative to white, so conventional audio-color or sleeve-ground assumptions could short it. The tested return is not proven earth/USB ground. A USB-powered XIAO can bond systems through its host. TRRS insertion can bridge supply, status and button contacts transiently; R2 protects only its own branch, not arbitrary contact shorts. Verify every completed harness contact on a disconnected prototype, insulate exposed joints, provide strain relief, and mate/unmate only with relevant sources removed. Do not experiment with a partially inserted live plug.

**High, possible damage: back-feeding, rail contention and unknown signal limits.** A nominal 3.3 V reading does not authorize a direct GPIO connection. An externally powered HIGH can feed an unpowered KVM; S can feed an unpowered ESP input; a GPIO LOW can fight the original pressed switch. Firmware high impedance does not guarantee power-off isolation. A series 100 ohm resistor is neither level translation nor a complete protection scheme. Use a floating normally-open contact for actuation, or a documented isolated switch whose leakage, on resistance, transient current and off-state behavior are validated. Do not tie XIAO 3V3 to V or power the controller from V. Independent power does not eliminate signal or USB ground paths.

**High, possible malfunction/damage: insufficient source or sink capability.** S drives opposing LED loads; V visibly sags under the original press. Neither can be treated as a spare supply. No current rating, current limit or spare capacity was measured. Avoid load-to-failure, short-circuit or arbitrary resistor sweep tests on the sole KVM. Preserve the original network first; use optical observation of its LEDs where practical for feedback. If electrical feedback is needed, use a specified low-load, power-off-safe receiver and validate loading before connection. An optocoupler input also consumes current and cannot be assumed harmless.

**Medium to high, unintended actuation:** reset, bootloader, flashing, brownout, watchdog, OTA and lost network can change pin states or leave a request asserted. Require a hardware released default with the controller absent or unpowered, no boot-driven closure and a bounded pulse. Prefer hardware pulse limiting if a stuck output is unacceptable; firmware timeout alone cannot release a frozen output. Exercise all these cases on a simulated remote load first. Observed successful quick and 0.5–1 second presses do not identify minimum pulse, edge, repeat-on-hold or cooldown. A 200 ms prototype pulse is a hypothesis, not a measured requirement.

**Medium, wrong state or unstable operation:** removing LEDs/C1, enabling GPIO pulls or using a receiver with excessive leakage can shift S/P. Low/high LED correlation does not prove push-pull behavior, video readiness or PC numbering. Report unsettled feedback as unknown, serialize requests, press once, wait with a timeout and avoid automatic toggle retries.

### MCU and board limits from primary sources

[Espressif ESP32-C6 datasheet v1.5](https://documentation.espressif.com/esp32-c6_datasheet_en.html), sections 2.3, 3 and 5: DC input HIGH minimum is 0.75×VDD, LOW maximum 0.25×VDD; the listed input range extends from -0.3 V to VDD+0.3 V. At VDD=3.3 V, thresholds are 2.475/0.825 V. Observed S levels satisfy these static thresholds only. The 3.6 V absolute maximum in the power-pin table is not a blanket GPIO or power-off tolerance. Typical 40 mA source/28 mA sink entries are characterized conditions, not guaranteed safe design currents. Strapping GPIOs are 4, 5, 8, 9 and 15; USB uses 12/13 and UART0 16/17. These restrictions do not guarantee glitch-free startup on other pins.

[Seeed XIAO ESP32C6 guide](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/) maps D0/D1/D2 to GPIO0/1/2 and labels 3V3 as a power output. These candidate pins avoid the listed strapping pins, but are not approved wiring. Confirm the actual board revision and circuit before selecting pins. The controller's application settings cannot by themselves prove behavior before firmware runs. No cited source here grants powered-off input tolerance or validates KVM compatibility.

## Minimum staged evidence and constraints

1. **Disconnected prototype first:** build and continuity-check the entire harness/interface against a remote replica, using current-limited bench sources and a simulated P return. Verify normal closure path, C1 charging, leakage on release and isolation between ESP power/ground and KVM-side contacts. Include all pins and housing shorts. For semiconductor switches, select actual parts from datasheets and check voltage, pulse/current, on-resistance, leakage, isolation and failure behavior with margins. No final part ratings can be derived from this packet alone.
2. **Disconnected startup/fault matrix:** record output/contact behavior and receiver currents with controller on/off, simulated KVM on/off, either starting first, resets, bootloader flashing, brownout and network loss. Test status at low/high and transitions. Verify no unintended closure, sustained pulse, rail injection or false “known” state. Observe waveforms where necessary; a meter cannot rule out short glitches. Do this before risking the real KVM.
3. **Lowest-risk first integration:** retain the original remote, R1/R2/R3/C1 and LEDs, and close across KEY1 using an independently powered, normally-open isolated contact. Optical LED sensing can preserve galvanic separation and avoid new electrical status loading. If remote replacement is mandatory, reproduce its passive network initially; any omitted branch needs separate justification. Do not add another 100 ohm in series when closing across KEY1, since original R2 is already present. A standalone tip-to-sleeve replacement must include R2's function exactly once.
4. **Only essential KVM confirmation:** after disconnected tests pass, confirm settled status with verified probe contact and clearly recorded released/held conditions. With the original load retained, use a high-impedance meter and, only if necessary, a suitable differential/isolated instrument to check transition/startup voltage envelope. Do not attach an earthed scope ground to an unverified node. Power down for rewiring. Optional direct checks of the two inferred resistor/anode joins must be on the unplugged remote only, without forcing access or desoldering.
5. **Controlled functional validation:** try a single bounded isolated closure within previously observed successful press durations, verify one change and release, then confirm manual front-panel feedback. Map PC identities last. Do not deliberately search destructive current/voltage limits or repeatedly stress the KVM. Exact thresholds and source capability need not be discovered if the design preserves the original electrical load and adds no galvanic controller connection. Electrical sensing or direct GPIO instead requires verified voltage envelope, receiver loading, grounding and power-off isolation before attachment; the existing packet does not provide these.

Residual uncertainty includes internal KVM topology/protection, unseen transients, exact input thresholds, source/sink ratings, component tolerances/ratings and long-term behavior. Even a passing staged prototype is evidence only for its tested configuration. This documentary review supports the passive schematic and a conservative validation path, not an unqualified “safe to connect” conclusion.
