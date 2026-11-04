# Bench findings and handoff

Recorded 2026-11-03 from the owner's measurements and conversation. No hardware was tested by the agent.

For a measurement-only compilation, see [actual readings](readings.md), split into live and earlier unpowered tests, with corrections and excluded observations labelled.

## Current handoff

Owner preference: a new minimal board or XIAO with control and electrical status sensing, without pass-through, original remote PCB, LEDs or manual buttons. Minimize supporting components subject to electrical validation. See [controller solutions](solutions.md); earlier remote-PCB/optical-sensing proposals remain alternatives.

The [revision A controller schematic](controller-schematic.md) now records a proposed eight-component implementation and pin assignments. It has not been built or measured. The shared reference, internal status pull-up and removal of original LED loads remain validation items. No new owner readings are added by this design work.

Offline tracing and planned initial live characterization are complete. Use the consolidated [actual readings](readings.md), [schematic](schematic.md), and [build plan](bring-up.md). Entries below are chronological and include superseded hypotheses and excluded readings.

Current measured behavior: black/sleeve supplies approximately 3.3 V relative to white/ring 1, with a roughly 0.2 V drop during a held press. Green/ring 2 is LOW with LED2 and approximately 3 V with LED1, persisting while LED1 is lit. Red/tip is 0 V released and 2.895 V held toward LED2 / 2.861 V held toward LED1. Pressing connects red/tip through R2 (~100 Ω) to black/sleeve; direct R2 voltage measurement gives an estimated 2.18 mA press current.

The owner's GPIO-LOW method on another device does not reproduce this active-high input. Final button driver, conditioned status input, power arrangement and firmware are not implemented. Evaluate input thresholds, removed-remote loading, default release and power sequencing during prototype design/validation. Keep PC labels as LED1/HIGH and LED2/LOW for now. The [interface protection note](interface-protection.md) records the remaining checks and minimal-parts candidate; it adds no physical readings.

## Goal and decisions

Integrate the AV Access iDock B10 with Home Assistant using the available Seeed Studio XIAO ESP32-C6 and ESPHome. Build a standalone controller that replaces the EXB01 remote at the CTL port. Only one controller is connected at a time; no pass-through is needed.

The owner has previously emulated another device's button by pulling its contacts low with ESP GPIOs. Evaluate a simpler shared-ground/open-drain interface here, subject to measurements. No GPIO connection to this KVM has been validated. Use actual ESP GND for a verified KVM ground; a GPIO output is not a floating contact closure.

Available equipment:

- Seeed Studio XIAO ESP32-C6.
- Klein Tools MM700 multimeter.
- uxcell PJ-320D 3.5 mm four-pin sockets, [Amazon ASIN B0G1CND36F](https://www.amazon.ca/dp/B0G1CND36F). Socket terminal mapping still needs verification.
- Original remote, opened again with access to its inner four-wire connector.

## Confirmed by owner

Board marking: **KEY01-B60 V2 / 2024.09.26**, as read by the owner. This supersedes the initial photo-based identification.

Plug contacts, from the free end toward the cable:

- Tip = red wire.
- Ring 1 = white wire.
- Ring 2 = green wire.
- Sleeve = black wire.

These are cable/contact mappings only. Black/sleeve is not yet verified as circuit ground; white and green are not yet assigned to LED channels.

With the remote unplugged, the owner measured approximately **101 Ω between tip and sleeve while holding the button pressed**. Initially no contact pair gave a continuity beep, even when pressed. The MM700 beeps below 50 Ω, so a 101 Ω path explains why it need not beep.

The exact released tip-to-sleeve resistance has not been reported. No powered voltage readings or other pairwise resistance values have been reported.

Individual resistor readings, measured in circuit with the MM700:

- R1: 0.992 kΩ = 992 Ω.
- R2: 99.6 Ω.
- R3: 0.508 kΩ = 508 Ω.

These are owner-reported in-circuit readings, not isolated component measurements. R1 is consistent with nominal 1 kΩ and R2 with nominal 100 Ω. R3 is approximately 510 Ω, but its nominal value is not confirmed. The owner also obtained continuity between nearby points on each of the six resistor solder joints, indicating probe contact was possible.

## Working interpretation

R2 is a likely candidate for a nominal 100 Ω resistor in the button path because its 99.6 Ω reading closely matches the approximately 101 Ω pressed red-to-black reading. Trace the connections to confirm. R1 and R3 may limit LED current, but that has not been established. Reproduce the measured press behavior in the replacement; do not assume a zero-ohm GPIO short is equivalent.

The PCB photos show KEY1, LED1, LED2, R1/R2/R3 and C1. Component values and connections have not been fully traced. The exact electrical CTL pinout remains incomplete.

## Resume here

The owner can now access the remote's inner connector, avoiding the inaccessible plug when connected.

1. Keep ESP32 disconnected. With the remote unplugged from the KVM, measure red-to-black resistance released and record the value and units, including OL if shown.
2. Keep black meter lead in COM and red lead in VΩ. Select ordinary V and use SEL to select DC. Do not use Lo Z.
3. Connect the original remote to the powered KVM. Probe the accessible metal terminals: black probe to black wire, red probe to red wire. Avoid bridging adjacent terminals.
4. Record voltage with the button released and held pressed, including the sign and which computer was selected. If practical, repeat starting from each selected input.
5. Switch back to resistance or continuity only after unplugging the remote again.

These voltage readings are red relative to black; they do not independently establish ground. Afterward identify the reference/ground, characterize white and green in both selected-input states, and trace the button resistor path.

If button voltage, sink current and power sequencing permit direct GPIO, use open-drain low for press and high impedance for release, with the required series resistance. Otherwise choose an external transistor or isolated closure. Firmware and final wiring remain pending these measurements.

Meter reference: [Klein MM700 manual](https://data.kleintools.com/sites/all/product_assets/documents/instructions/klein/MM700_1390112ART_WEB.pdf), printed pages 11–13, for DC voltage, continuity threshold and resistance modes.

## Component locations and fabrication markings

Owner-reported board inventory:

- Connector side: C1, R1, R2, R3, J1 (four-wire connector).
- LED side: LED1, LED2, KEY1 (button).
- Additional markings: UG logo, DS5, UL Recognized Component symbol (described by the owner as RU with the R backwards), `94V-0`, E464601, 0225. The owner corrected the earlier `Rg4v-0` transcription.

The owner confirms the stylized UL Recognized Component symbol followed by `94V-0`, a PCB flammability marking. This is not a voltage rating or certification of the complete KVM. UL file E464601 identifies Jiangxi Union Gain Electronics Technology Co., Ltd., a PCB fabricator, rather than a unique KVM circuit. DS5 appears to be a board construction/type designation. The UG logo likely belongs to the same fabricator. The meaning of 0225 is unverified; it may be a production/date/lot code and must not be treated as a confirmed date.

Sources: [UL file E464601](https://iq.ul.com/ul/cert.aspx?ULID=104617002); [safety report listing DS5 under the same PCB manufacturer/file](https://files.asbis.com/AQARA/BG/WIRELESS%20REMOTE%20SWITCH%20H1%20DOUBLE%20ROCKER/CE/Wireless%20Remote%20Switch%20H1%28Double%20Rocker%29%20-%20WRS-R02-%C2%A0CE%20Safety%20Report.pdf), page 47. These markings do not provide a schematic or change the measured wire mapping.

## C1 measurement

The owner reports C1 = **98.7 nF**, consistent with nominal **100 nF (0.1 µF)**. This was reported during the in-circuit measurement workflow; removal or lifting a terminal was not reported. Other connected components may affect the reading.

C1 may filter noise or contribute to button debounce, but its role remains unconfirmed until its connections are traced. Its capacitance alone does not establish the circuit function or debounce timing.

## Plug-level capacitance observations

With J1 reconnected to the remote PCB and the remote disconnected from the KVM, the owner reports approximately 0.15 nF stable between sleeve and each other plug contact. Pressing the button gives a zero reading; the exact pairs for that pressed observation were not individually specified. Repeated button presses during sleeve-to-tip measurement produce approximately 0.4 nF and fluctuating readings.

These are whole-circuit meter observations, not valid isolated C1 measurements. Switching the button during a capacitance measurement changes the network and disrupts the meter's charging cycle. A 0.15 nF reading may include lead/fixture capacitance; an open-probe baseline has not been measured. These observations neither locate C1 nor invalidate the earlier 98.7 nF reading directly across its terminals. Resume with resistance/continuity tracing of C1 terminals to J1 contacts rather than assigning a circuit from these capacitance readings.

## Clarified C1-to-red measurement

The owner explicitly withdrew the preceding two conversational observations about red-to-C1 ends and sleeve-to-C1 behavior. Do not use those withdrawn observations or the hypotheses derived from them as evidence.

Replacement observation: **C1 end A (outermost, nearest the PCB edge) to red = 97.5 nF, both with the button pressed and released.** This is an in-circuit capacitance reading, not proof of a direct electrical connection. C1 end B is the opposite end. Red-to-B resistance and sleeve-to-C1 connections remain unconfirmed.

## Clarified C1 end A to black measurement

Owner reports **C1 end A (nearest PCB edge) to black/sleeve = 97.5 nF when pressed, 0.15 nF when released**. Together with end A to red = 97.5 nF in both states, this is consistent with the button connecting black into the red-side path through the approximately 100 ohm pressed resistance. This interpretation remains provisional: capacitance measurements through the assembled network do not establish direct connections. Next confirm red-to-C1-end-B resistance with the remote unplugged and button released.

## Confirmed C1 end B connection

Owner reports **C1 end B to red/tip = 0.5 ohm, both pressed and released**. This near-zero resistance establishes a direct connection at multimeter resolution; lead/contact resistance can account for part or all of the reading. C1 therefore has its B terminal on the red/tip net. End A's net and circuit ground remain unidentified. No powered voltage measurements have been reported.

Next trace end A to white/ring 1 and green/ring 2 in resistance mode with the remote unplugged and button released. Do not infer ground from wire colour.

## Confirmed C1 end A connection

Owner reports **C1 end A to white/ring 1 = 0.5 ohm** and **C1 end A to green/ring 2 = OL**, each with the button pressed and released.

Together with B-to-red = 0.5 ohm, this maps **C1 directly between white/ring 1 (end A) and red/tip (end B)**. The measured C1 value is approximately 100 nF. The OL reading to green is an observation in resistance mode, not proof that every possible electrical path is absent. C1's function and circuit ground remain unconfirmed. These findings supersede earlier notes describing either capacitor terminal as unmapped.

Follow-up: owner remeasured red/tip to C1 end B at **1 ohm** (button state not specified). This remains consistent with the earlier 0.5 ohm reading and the same direct connection; probe/contact resistance has not been subtracted.

Meter baseline: owner reports **probe tip to probe tip = 0.2 ohm**. The 0.5–1 ohm connection readings remain consistent with direct PCB connections plus probe/contact resistance; exact trace resistance is not established.

Owner reports **red-to-white = OL** and **red-to-green = OL** in resistance mode. Button state was not explicitly restated; these followed a request to measure released. OL does not rule out capacitor or LED paths. C1 remains mapped between red and white by direct terminal-to-wire measurements.

## R2 end A tracing

Owner-defined R2 end A (physical orientation not specified):

- A to red/tip: 0.7 ohm pressed, OL released.
- A to black/sleeve: 100.4 ohm pressed and released.

These readings support the provisional path red -> KEY1 -> R2 end A -> R2 -> black. Confirm the far end of R2 directly connects to black before treating the complete path as traced. Predicted readings for end B are near-zero to black in both states and approximately 100 ohm to red when pressed, OL released; these are predictions, not measurements. Ground remains unidentified.

## R2 end B to red

The owner clarified the probe pair: **R2 end B to red/tip = 100.5 ohm pressed, OL released**. This was not an end-B-to-black measurement. It matches the predicted button-plus-R2 path. End B to black remains unmeasured/reported and is the next direct-connection check.

## Completed button-path mapping

Owner reports **black/sleeve to R2 end B = 0.7–1 ohm**, pressed and released. This confirms the direct connection at meter resolution.

The combined measurements establish the effective button path:

**red/tip -> button-controlled connection -> R2 end A -> R2 (approximately 100 ohm) -> R2 end B -> black/sleeve**.

The button-controlled connection reads 0.7 ohm pressed and OL released between red and R2 A. This maps the effective circuit behavior; individual KEY1 solder legs have not been mapped separately. Red-to-black pressed resistance is approximately 101 ohm. Ground, powered polarity/voltage and LED paths remain unknown. Do not infer that black is ground from its colour or this path.

## R1 tracing started

Owner reports **red/tip to R1 A = 1.075 kΩ when pressed only**. The exact released display was not supplied. R1 A follows the requested orientation, nearest the PCB edge, unless corrected by the owner. This is a resistive path, not direct continuity, and does not yet establish which intervening components produce it. Next measure black to R1 A and R1 B with the button released to test their connection to the black-side network.

Owner reports **R1 B to red/tip = 102 ohm when pressed**. Released reading not supplied. This is consistent with R1 B being on the black-side network, but a near-zero R1 B-to-black measurement is needed to establish the direct connection.

## R1-to-black observations requiring unit confirmation

Owner reports black to R1 A = **0.995 ohm** and black to R1 B = **0.5–1.0 ohm**, each pressed and released. Record these literally; do not silently convert A to kΩ. R1 B's near-zero reading supports its direct connection to black. If the A reading is also truly in ohms, both resistor ends appear on the same net, conflicting with the earlier approximately 992 ohm R1 reading and the different red-to-A/B pressed readings. A possible explanation is that A was displayed as 0.995 kΩ, but this remains a question for the owner. Resolve units/probe points before assigning R1 A's net or revising the schematic.

## R1 A units clarified

Owner now reports **R1 A to red = 1.057 kΩ pressed** and **R1 A to black = 0.995 kΩ pressed**. The latter resolves the earlier reported 0.995 ohm unit ambiguity for the pressed measurement. The corrected released value was not explicitly restated.

R1 B connects directly to black/sleeve (0.5–1.0 ohm). R1 A connects to black through approximately 1 kΩ, consistent with the measured R1. R1 A's other branch remains untraced. Red-to-A pressed readings have varied from 1.075 kΩ to 1.057 kΩ, and do not exactly equal the sum of the separately measured R1 and R2 values. Do not infer a precise series-only network from those values; additional paths or measurement variation remain possible.

Next measure R1 A to white and green with the remote unplugged, button released, in Ω mode. LED or diode paths can show OL in resistance mode.

## R1 A to remaining plug contacts

Owner reports **R1 A to white/ring 1 = OL** and **R1 A to green/ring 2 = OL**, both pressed and released. These are resistance-mode observations; they do not exclude LED paths. Next trace R1 A directly to the two solder terminals of LED1 and LED2, button released, with no external power.

## R3 tracing started

After being asked to measure black/sleeve to R3 A and R3 B, the owner replied OL pressed and released without specifying individual ends. Record the response as reported; coverage of both ends remains to be confirmed. The owner cannot conveniently probe components on opposite PCB faces, so continue same-side measurements at J1 and resistor terminals. R1-to-LED direct tracing is deferred; diode-mode tests through accessible terminals may help later, subject to meter test voltage.

R3 follow-up: after being asked to check white/ring 1 to R3 A and R3 B, the owner replied OL pressed/released, again without individual end labels. Treat this as a reported response to those checks; individual endpoint coverage remains unconfirmed. The earlier question about whether both black/sleeve-to-R3 ends were tested was not explicitly answered. Continue with one probe pair per request to eliminate ambiguity. Next requested pair: green/ring 2 to R3 A, button released, resistance mode, remote unplugged.

## Confirmed R3 connection to green/ring 2

Owner explicitly reports **green/ring 2 to R3 A = 0.5 kΩ** and **green/ring 2 to R3 B = 0.5 Ω**, stable pressed and released. This confirms R3 B directly connects to green/ring 2, with approximately 500 Ω through R3 to A. The earlier directly measured R3 value was 508 Ω. R3 A's downstream branch remains unmapped. Next same-side check: red/tip to R3 A in Ω mode, remote unplugged, button released.

## R3 to red/tip

Following the request to measure red/tip to R3 A with the button released, the owner reports **both R3 A and R3 B show OL**. Record these as released-state observations based on the requested setup; pressed state was not restated. No direct resistor-only path to red/tip was detected. Next try diode-mode measurements between R1 A and white/ring 1 in both probe polarities, with no external power, to investigate an LED path using same-side probe access.

## First diode-mode test

Owner clarified **R1 A to white/ring 1 = OL in both probe directions** in diode mode. No numeric forward voltage or LED illumination was reported. This does not rule out an LED path because the meter may lack sufficient test voltage. Continue mapping with that limitation explicit.

## Diode-mode red/tip to R1 A

Owner reports red meter probe on red/tip, black meter probe on R1 A: **0 unpressed**, **0.762 V pressed**. The released reading was reported simply as 0, without units or display precision; settling time was not stated. Preserve this literally, not as OL. Reverse polarity has not been reported.

The pressed value can arise from meter test current through the approximately 1 kΩ resistive button path and is not sufficient evidence of an LED forward drop. A persistent zero released reading would conflict with the earlier open/resistive observations; confirm whether it is transient, a literal 0.000 V, or OL before inferring a net or semiconductor connection. Ask owner to keep the same probe points, release the button, wait 5–10 seconds and read the display with the diode icon present.

## Released diode reading corrected

After waiting with red meter probe on red/tip and black meter probe on R1 A, the owner confirms **OL released**. This supersedes the earlier ambiguous zero released report. Pressed remains 0.762 V, compatible with the known resistive button path; no LED connection has been established by this test.

Next check LED1 directly in diode mode on the LED face, keeping both probes on that same face. This tests whether the meter can forward-bias the LED before interpreting further OL readings through the connector-side network. Start with red meter probe on the pad marked + and black meter probe on the other LED1 pad. Keep the remote unplugged.

## Direct LED diode tests

Owner confirms LED1 and LED2 each light **separately when probed directly**, each showing **2.56 V** in diode mode. This is not simultaneous illumination from probing one LED. The requested setup was red meter probe on the pad marked +, black on the opposite pad; the owner did not separately restate probe orientation. This confirms the MM700 can forward-bias each LED directly. OL through other paths remains subject to added series resistance and meter voltage limits.

To avoid opposite-face probing, lay the unplugged 3.5 mm plug beside the LED face and trace LED solder pads to the plug contacts in Ω mode. No need to probe a rear resistor and front LED simultaneously. First trace LED1 pad marked + to red/tip, white/ring 1, green/ring 2 and black/sleeve. The other LED pad and LED2 follow.

## LED1 pad-to-plug response

After a request to measure LED1's + pad to plug contacts in Ω mode, the owner reports **white/ring 1 = 2.56 V**, all other contacts OL. The voltage unit indicates a likely diode-mode measurement, not a resistance reading. Probe polarity was not specified; do not treat this as direct continuity or assign LED polarity from it alone. If red meter probe was on LED1 + and black on white/ring 1, it supports white/ring 1 as the LED1 cathode-side connection, pending near-zero resistance from the opposite LED1 pad to white/ring 1. Next explicitly select Ω and measure that pair.

## LED2 pad-to-plug diode response

Owner reports **green/ring 2 = 2.56 V with the red meter probe on LED2's + pad**. The other meter probe is understood from context to be on green/ring 2; its colour was not separately restated. This supports an LED conduction path to green/ring 2, not necessarily a direct cathode connection: R3 or another path may intervene. Explicit Ω-mode checks from each LED's opposite pad to the reported plug contact remain pending.

## Confirmed LED negative-pad connections

Owner reports **white/ring 1 to LED1 − pad = 0.7 Ω** and **green/ring 2 to LED2 − pad = 0.9 Ω** in response to the Ω-mode request. These establish direct terminal connections at meter resolution. Together with direct diode tests, the pads are treated as cathodes according to the owner/silkscreen labels; direct diode probe orientation was requested but not independently restated for both LEDs.

LED1 negative pad shares the white/ring 1 net with C1 A. LED2 negative pad shares the green/ring 2 net with R3 B. LED positive-pad connections remain untraced. Do not yet assume both R1 and R3 are series LED current-limiting resistors; all endpoints must be reconciled. Next check each LED + pad to black/sleeve in Ω mode with the button released and remote unplugged.

## Retracted test block: possible J1 disconnection

**Owner correction:** J1 may have disconnected starting with the request to measure LED1 + and LED2 + to black/sleeve. Treat that request's response and every subsequent measurement below, up to the restart note, as unreliable. Retain them as history only, not schematic evidence. The earlier LED negative-pad readings (LED1 − to white/ring 1 = 0.7 Ω; LED2 − to green/ring 2 = 0.9 Ω) precede this block and are retained. The later OL recheck does not overturn those earlier results because it occurred during the suspect block.

## LED positive pads to black/sleeve response

Asked to measure LED1 + and LED2 + to black/sleeve in Ω mode with the button released, the owner replied OL without specifying whether one or both pairs were tested. Keep the endpoint coverage unconfirmed rather than assigning both as verified OL. Next ask a single pair: LED1 + to red/tip, Ω mode, button released, remote unplugged.

Owner reports **LED1 + to red/tip = OL**, following the request for Ω mode, released button and remote unplugged. Next single pair: LED1 + to green/ring 2 in the same setup.

Owner reports **LED1 + to green/ring 2 = OL** in response to the unplugged/released Ω-mode request. Next confirm LED1 + to black/sleeve as a single pair, since the earlier OL response followed a two-pair request and did not distinguish LED1 from LED2.

## LED1 positive-pad to black/sleeve confirmed OL

Owner explicitly reports **LED1 + to black/sleeve = OL** for the single-pair Ω-mode request. LED1 + has now shown OL to red/tip, green/ring 2 and black/sleeve; no LED1 positive supply/resistor connection is established. Do not assign R1 to LED1 from its value or appearance. Before further tracing, recheck the known LED1 − to white/ring 1 near-zero connection to verify continued J1/cable contact and probe placement. The earlier directly measured result was 0.7 Ω.

## Connectivity discrepancy: pause LED mapping

Owner rechecked **LED1 − to white/ring 1** and now reports **OL**, contradicting the earlier 0.7 Ω result. Preserve both observations. Treat the LED-to-contact mapping as unresolved until this discrepancy is explained; do not conclude the circuit changed or the LED is faulty. Recent positive-pad OL readings may reflect connectivity, probe placement or measurement setup rather than true open paths.

With the remote unplugged, verify meter probe-to-probe baseline, J1 seating and plug-to-cable contact first. Next isolate cable access by measuring white/ring 1 to the exposed white-wire metal terminal at J1 on the cable side. This should be near zero if the reported wire map and cable contact remain valid. Avoid forcing probes into the connector. Re-establish the previous direct LED negative-pad connection before assigning LED nets or updating the visual schematic.

## Restart after possible J1 disconnection

With no external power, reseat J1 and keep it attached to the board. Restart in Ω mode with button released. First measure only **LED1 + to black/sleeve**, then **LED2 + to black/sleeve**. No fresh readings after reseating have yet been reported. All suspect-block OL observations are excluded from conclusions pending retest.

## Fresh retest: LED1 positive pad to black/sleeve

After the J1 reseating request, owner reports **LED1 + to black/sleeve = OL** for the single-pair retest in the requested Ω-mode, button-released, externally unpowered setup. This is a new observation, distinct from the excluded suspect block. No fresh LED2 + to black/sleeve reading has yet been reported. Next request that pair alone.

## Fresh retest: LED2 positive pad to black/sleeve

Owner reports **LED2 + to black/sleeve = 0.992 kΩ (992 Ω)** after the J1 reseating checkpoint, in the requested Ω-mode/button-released setup. This matches R1's directly measured 992 Ω and its end B's direct black/sleeve connection, strongly supporting R1 between black/sleeve and LED2 +. The direct R1 A-to-LED2 + connection has not been separately measured. Combined with the earlier LED2 − to green/ring 2 direct mapping, the supported candidate branch is black/sleeve -> R1 -> LED2 -> green/ring 2, with anode toward R1. Ground and powered polarity remain unassigned.

Next retest LED1 + to green/ring 2 in Ω mode, button released, J1 connected and remote unplugged. The old OL for that pair is in the excluded possible-disconnection block.

## Fresh retest: LED1 positive pad to green/ring 2

Owner reports **LED1 + to green/ring 2 = 0.5 kΩ (approximately 500 Ω)** after the J1 reseating checkpoint, in the requested Ω-mode/button-released setup. This matches R3's measured 508 Ω and its end B's direct green/ring 2 connection, supporting R3 between green/ring 2 and LED1 +. Direct R3 A-to-LED1 + continuity has not been separately measured.

Combined candidate LED network: **black/sleeve -> R1 (~1 kΩ) -> LED2 (anode to cathode) -> green/ring 2 -> R3 (~510 Ω) -> LED1 (anode to cathode) -> white/ring 1**. The green/ring 2 junction is externally connected through J1, so this is a network with an accessible midpoint, not proof that both LEDs always carry the same current. Resistor-to-LED pairings are strongly supported by matching resistances, while the two direct opposite-face links remain unmeasured. No KVM-side ground, supply or drive method has been identified.

Next single-pair offline check: LED2 + to red/tip in Ω mode with button released, then pressed. This can test consistency with the known button/R2 path. Keep J1 attached and the remote unplugged from the KVM.

## LED2 positive-pad to red/tip consistency check

Owner reports **LED2 + to red/tip = 1.1 kΩ pressed, OL released** after the J1 checkpoint. This matches the approximate R1 (~1 kΩ) plus R2 (~100 Ω) path through KEY1 and supports the reconstructed network. No powered tests have been performed.

Next map KEY1's physical legs while remaining unpowered. Define orientation as viewing the LED face with LED1/LED2 at the bottom; identify upper-left, upper-right, lower-left and lower-right legs. Start upper-left KEY1 leg to red/tip, released and pressed. This will distinguish duplicate permanently joined legs from the two switched nets without assuming internal switch orientation.

## KEY1 left-side leg tracing

In response to upper-left KEY1 leg to red/tip, the owner reports **left side = 0.7 Ω pressed, OL released**. Record as a tested left-side leg, not proof that both left legs were independently checked. Orientation requested: LED face, LEDs at bottom. This supports the tested leg being on the R2 A switch net, rather than the permanently red/tip-connected switch net. Next check that same tested leg to black/sleeve with the button released; approximately 100 Ω is predicted but not yet measured at this leg.

## KEY1 tested left leg to black/sleeve

Owner reports **same tested left-side KEY1 leg to black/sleeve = 100 Ω**, pressed and released. Together with that leg's 0.7 Ω to red/tip only when pressed, this confirms the leg is on the R2 side of the switch and reaches black/sleeve through approximately 100 Ω. The direct tested-leg-to-R2-A trace remains inferred from the combined measurements. Next check one right-side KEY1 leg to red/tip, pressed and released, in the same unpowered Ω-mode setup.

## KEY1 tested right leg to red/tip

Owner reports **tested right-side KEY1 leg to red/tip = 0.7 Ω**, pressed and released. The tested right leg is directly on red/tip; the tested left leg is on the R2 side and reaches black/sleeve through 100 Ω. This validates the effective KEY1/R2 button path at actual switch legs.

Remaining physical KEY1 check: with the button released, measure upper-left to lower-left, then upper-right to lower-right, using the established LED-face/LEDs-at-bottom orientation. Near-zero within each side is expected for duplicate terminals but is not yet reported. Do not infer unmeasured duplicate-leg connections as confirmed.

## KEY1 duplicate legs confirmed

Owner reports **upper-left to lower-left = 0.1 Ω** and **upper-right to lower-right = 0.1 Ω**, in response to the requested released-button test. Each side is therefore a pair of duplicate permanently connected terminals. Orientation: LED face, LEDs at bottom. The right-side pair is red/tip; the left-side pair reaches black/sleeve through R2 (~100 Ω), and KEY1 connects the two sides when pressed.

Next unpowered cross-check of inferred resistor/LED pairing uses only connector-side access: in diode mode, red meter probe on R1 A and black meter probe on green/ring 2, button released. A forward LED2 reading/illumination would support the R1 A-to-LED2-anode net without probing opposite faces. R3 A to white/ring 1 can subsequently test LED1. These remain planned checks until readings are reported.

## R1/LED2 pairing cross-check

With red meter probe on R1 A and black meter probe on green/ring 2, owner reports **LED2 lights at 2.57 V**. This independently supports R1 A at LED2's anode-side net, with LED2 cathode directly on green/ring 2 (previously 0.9 Ω). Combined with R1 B on black/sleeve, the R1/LED2 branch is well supported: black/sleeve -> R1 -> LED2 -> green/ring 2. No direct opposite-face R1 A-to-LED2 + resistance measurement was made.

Next symmetric check: diode mode, red meter probe on R3 A, black meter probe on white/ring 1, remote unplugged and button released; record the voltage and which LED illuminates.

## R3/LED1 pairing cross-check and offline tracing milestone

With red meter probe on R3 A and black meter probe on white/ring 1, owner reports **LED1 lights at 2.56 V**. This independently supports the R3 A-to-LED1-anode-side path, complementing the approximately 500 Ω LED1 + to green/ring 2 resistance and the direct LED1 cathode-to-white/ring 1 connection.

All listed remote components now have functional connections reconstructed from unpowered tests. The two resistor-to-LED internal nodes were established by multiple consistent resistance/diode-path observations rather than direct opposite-face continuity; no such direct test is claimed. KVM-side ground/supply, operating voltages, LED-to-PC numbering and safe GPIO interface remain unknown. No powered KVM tests have been performed.

## First powered cable-side voltage

Owner reports **+3.334 V** with **black meter probe on white/ring 1** and **red meter probe on black/sleeve**, at the cable-side J1 terminals. Setup follows the agreed unloaded test: cable plugged into the powered KVM, J1 disconnected from the remote PCB, ESP32 disconnected. Selected computer was not reported.

This establishes black/sleeve is 3.334 V above white/ring 1 under that test condition and supports the proposed supply/reference roles. It does not establish loaded behavior, permissible current, compatibility with an ESP ground connection, or behavior in other selection/power states. No powered loaded-remote or ESP32 tests have been performed.

Next cable-side voltage check: retain black meter probe on white/ring 1 and move red meter probe to green/ring 2. Record signed DC voltage, then use the KVM front-panel button to compare selection states; the disconnected remote PCB cannot operate its button or LEDs.

## First green/ring 2 powered reading

Owner reports **31.1 mV (0.0311 V)** at cable-side J1 with red meter probe on green/ring 2 and black meter probe on white/ring 1. Setup remains unloaded: remote PCB disconnected from J1, cable connected to powered KVM, ESP32 disconnected. Selected PC not reported. The near-zero reading is consistent with a LOW status output but requires comparison after a selection change.

Next: keep the same probes in DC-voltage mode, switch once using the KVM front-panel input-selection button, and report the new green/ring 2-to-white/ring 1 voltage. Do not use the KVM power button; the remote button is unavailable while J1 is disconnected.

## Green/ring 2 toggles with front-panel selection

After the requested KVM front-panel input-selection change, owner reports **3.34 V** with red meter probe on green/ring 2 and black meter probe on white/ring 1. The previous state was 31.1 mV. Both readings are cable-side/unloaded with J1 disconnected from the remote PCB and ESP32 disconnected.

This confirms green/ring 2 changes between near-zero and approximately 3.3 V with selected input in the tested unloaded conditions. Exact PC-to-level mapping and loaded LED behavior remain unreported. The reconstructed LED network predicts HIGH -> LED1 and LOW -> LED2; this has not yet been observed with the powered remote attached.

Next measure red/tip relative to white/ring 1 in DC-voltage mode, still cable-only/unloaded, to establish the resting button-input voltage. This will not measure a press because KEY1 is on the disconnected PCB.

## Unloaded button-input resting voltage

Owner reports **red/tip relative to white/ring 1 = 3 mV (0.003 V)** at cable-side J1, with the remote PCB and ESP32 disconnected. The KVM remains powered. This is consistent with an active-high input resting LOW; a pressed voltage has not yet been measured. The exact KVM selection state was not separately restated.

Next loaded-remote test can use C1's exposed solder ends instead of inaccessible seated J1 terminals: power off KVM, reseat J1, then power on with the original remote connected and ESP32 disconnected. In DC-voltage mode, black meter probe on C1 A (nearest PCB edge, white/ring 1 net), red meter probe on C1 B (red/tip net). Record released and held-pressed voltages, avoiding a probe bridge across pads. This reuses previously traced C1 terminal mappings and does not require a new connector breakout. No loaded result has yet been reported.

## Loaded button voltage confirms active-high press

With the original remote reconnected, black meter probe on C1 A (white/ring 1 net), red meter probe on C1 B (red/tip net), owner reports **0 V released** and **2.86 V held pressed**. This is the first loaded-remote voltage observation and confirms red/tip rises during a physical press relative to white/ring 1. A LOW pulse would not reproduce the measured press polarity.

Do not assume the loaded black/sleeve supply remains equal to the earlier unloaded 3.334 V or compute press current from those unlike conditions. Next measure black/sleeve relative to white/ring 1 with the remote attached, released and pressed, using accessible same-face points: red meter probe R2 B (black/sleeve), black meter probe C1 A (white/ring 1), DC-voltage mode. A subsequent voltage directly across R2 while pressed can establish resistor current from its measured 99.6 Ω value. ESP32 remains disconnected; interface compatibility is not yet established.

## Loaded supply changes during press

With black meter probe on C1 A (white/ring 1) and red meter probe on R2 B (black/sleeve), owner reports **3.3 V released** and **3.1 V pressed** with the original remote attached. This is approximately 0.2 V lower during the held press under the tested conditions. Do not assume a stiff 3.3 V rail or infer available current for powering the ESP32.

The earlier loaded button voltage was 2.86 V pressed. Subtracting readings taken at different times gives only an approximate indication, not a direct R2 voltage measurement. Next measure directly across R2 in DC-voltage mode: red meter probe on R2 B (black/sleeve), black meter probe on R2 A (button-side, connected to red/tip through KEY1 when pressed). Hold KEY1 pressed and record the signed voltage. R2 measured 99.6 Ω; its current can then be estimated as measured voltage / 99.6 Ω. ESP32 remains disconnected.

## Direct R2 pressed voltage and derived current

Owner reports **217 mV across R2 while KEY1 is held pressed**, following red meter probe on R2 B (black/sleeve) and black meter probe on R2 A (button-side). Original remote is attached to the powered KVM; ESP32 remains disconnected.

Using measured R2 = 99.6 Ω, estimated current through R2 is **0.217 V / 99.6 Ω = 0.00218 A, approximately 2.18 mA**. This is derived from a voltage measurement, not a direct ammeter reading. It characterizes the tested held-press condition; input thresholds, transients, GPIO power sequencing and worst-case behavior are not established.

Voltage-across-known-resistor measurement preserves the existing circuit. Current mode requires opening the branch and inserting the meter in series. Placing an ammeter across R2 would bypass the 100 Ω resistor and change the circuit; placing it across supply and reference could short them. No direct current-mode test is needed for this measurement.

## Loaded selection feedback: LED2 state

Owner reports **green/ring 2 relative to white/ring 1 = 0.8 mV (0.0008 V), LED2 lit**, with the original remote attached. Probe setup requested: red meter probe R3 B (green/ring 2), black meter probe C1 A (white/ring 1), DC-voltage mode, ESP32 disconnected. Selected computer identity was not supplied.

This confirms the loaded LOW status state corresponds to physical LED2. Next use the KVM input-selection button to change selection and record the same voltage, illuminated LED and PC identity. Loaded HIGH state remains pending. Earlier broad statements that live characterization was complete were premature: both loaded feedback states and button/supply behavior in the other selected state still need checking.

## Loaded status recheck: same near-zero reading for both LEDs

Owner reports **2.1 mV with LED2 on**, then after a button press **2.1 mV with LED1 on**, using the requested red meter probe on R3 B / green/ring 2 and black meter probe on C1 A / white/ring 1. Original remote is attached; ESP32 disconnected; DC-voltage mode requested. Whether the button was still held during the second measurement was not explicitly established.

Preserve both readings as actual observations. They do not validate a loaded HIGH status state and conflict with the expected LED1 state of the reconstructed network and the earlier unloaded 31.1 mV-to-3.34 V selection change. Do not infer the schematic is wrong, the KVM faulty, or the two readings equivalent to confirmed stable status states without checking setup and settling.

Next leave LED1 selected, fully release KEY1 and wait 2–3 seconds before rereading the same probe pair. If still near zero, verify probe contact and the R3 B-to-green/ring 2 / C1 A-to-white/ring 1 mappings unpowered before revising the interface model.

## Loaded status retest matches the expected pattern

Owner retested the same pair: black meter probe on C1 A / white/ring 1, red meter probe on R3 B / green/ring 2. Reports **33 mV (0.033 V) with LED2 on**, then after pressing the button **3 V with LED1 on**. These are the latest loaded status observations and match the reconstructed LED network and prior unloaded LOW/HIGH behavior.

The previous 2.1 mV readings in both LED states remain in history as inconsistent observations; no definite cause was established. The latest result followed a request to measure after fully releasing/settling, but the owner described a button press rather than explicitly restating held/released status. Retain that metadata limitation; do not invent a release confirmation or PC identity. No ESP32 interface has been connected.

## Stable status and button timing observations

Owner confirms **3 V corresponds to LED1 and persists while LED1 is lit**. Use LED1/HIGH and LED2/LOW labels; PC names will be mapped later at the owner's request. Do not require that naming before interface work.

Owner reports that both a very quick press and approximately **0.5–1 second held press** can trigger switching. The quick press duration, activation edge, minimum accepted pulse, release handling, retrigger interval and long-hold repeat behavior were not measured. Software debounce or throttling is possible but not established by these observations. C1 can contribute hardware filtering; no filter time constant has been established without the KVM input network.

For a later ESPHome prototype, use one bounded momentary pulse and reject overlapping commands, then verify one pulse produces one switch. An approximately 200 ms initial trial is a design starting point, not a measured timing requirement. Exact minimum pulse and cooldown can be checked later with the implemented interface if needed. No firmware or GPIO interface is implemented yet.

## Suspected cause of earlier 2.1 mV status readings

Owner suspects the earlier 2.1 mV readings in both LED states resulted from incomplete probe contact with the pads. This is a plausible measurement-contact explanation, not an independently confirmed cause. Flag those two readings as suspect and retain them in history. Use the later consistent loaded observations for the current status interpretation: LED2/LOW = 33 mV; LED1/HIGH = 3 V, stable while LED1 is lit. Do not revise the reconstructed circuit solely from the earlier suspect readings.

## Button-voltage state comparison: condition needs clarification

After being asked for C1 B / red/tip relative to C1 A / white/ring 1 across both LED selections and held presses, owner reports **stable 3.3 mV no matter the state**. Do not assign this response to all four requested conditions: it is unclear whether 'state' refers only to selected LED while KEY1 is released, or includes a physically held press. Near-zero released voltage in both selections is consistent with prior measurements; a near-zero held-press value would conflict with the earlier 2.86 V pressed reading. Clarify whether the 3.3 mV persists while KEY1 is physically held before interpreting or labelling the condition.

## Latest button observation: zero released, unreadable transient

For the requested C1 B / red/tip to C1 A / white/ring 1 measurement, owner now reports **0 V released**, with a brief jump to an unreadable higher value when the LED selection changes. No numeric peak or pulse duration is supplied, and it remains unclear whether KEY1 was physically held throughout the rise/fall or only tapped.

Do not infer edge-triggering, an internal one-shot, or a changed steady-state circuit from this display observation. A short manual press may be missed by the meter's display updates. Next explicitly hold KEY1 continuously for about 2 seconds and observe whether the reading stays raised while held or returns to zero before release. Compare only after establishing the actual button condition. The earlier held-press 2.86 V measurement remains recorded.

## Sustained button-voltage recheck

Owner explicitly confirms **red/tip relative to white/ring 1 = 2.86 V while held down and 0 V when released**, following the request to keep red meter probe on C1 B / red/tip and black meter probe on C1 A / white/ring 1. This repeats the earlier loaded button result and establishes that the voltage remains high during a held press rather than only producing an unreadable edge transient. Exact hold duration and selected-LED identities were not separately reported.

The earlier 3.3 mV 'regardless of state' and unreadable rise observations must not be used to claim a LOW held-button signal or an internal pulse generator. Held/released behavior is now clarified. Exact firmware debounce/throttle timing remains unmeasured.

Finish the already-planned supply comparison by recording black/sleeve relative to white/ring 1 at R2 B and C1 A, for LED1 released, a held press toward LED2, LED2 released and a held press toward LED1. Keep ESP32 disconnected and DC-voltage mode selected.

## Latest four-state set: owner considers most accurate; probe point needs confirmation

Following a request for red meter probe on R2 B / black/sleeve and black meter probe on C1 A / white/ring 1 (supply comparison), owner reports:

- LED1 selected, released: **0 V**.
- Held press switching toward LED2: **2.895 V**.
- LED2 selected, released: **0 V**.
- Held press switching toward LED1: **2.861 V**.

Owner considers this the most accurate set so far. Preserve the measurements and state labels literally. The pattern agrees with earlier red/tip button-input readings but conflicts with earlier black/sleeve supply readings of 3.3 V released / 3.1 V pressed. It is not yet known whether the red meter probe remained on C1 B / red/tip, was on R2 A / button side, was on requested R2 B / black/sleeve, or another setup difference occurred. Do not silently relabel these as button readings or replace supply measurements. Confirm exact physical probe point first. The black meter probe was requested at C1 A / white/ring 1; actual placement was not restated either.

## Latest four-state set assigned to button input

Owner explicitly confirms the actual probes for the latest four-state set: **red meter probe on C1 B / red/tip**, **black meter probe on C1 A / white/ring 1**. These are button-input voltages across C1, not supply voltages. The probe ambiguity is resolved and the earlier supply request was not the pair actually measured.

Accepted latest button set: LED1 released 0 V; held press toward LED2 2.895 V; LED2 released 0 V; held press toward LED1 2.861 V. The owner considers this the most accurate set so far. It confirms active-high behavior in both switching directions. The earlier supply observations (black/sleeve to white/ring 1: 3.3 V released, 3.1 V pressed) remain separate, with selected-LED state not recorded. State-labelled supply comparison is still pending.

## State-labelled loaded supply comparison complete

With red meter probe on R2 B / black/sleeve and black meter probe on C1 A / white/ring 1, owner reports:

- LED1 on, released: **3.339 V**.
- Held press switching toward LED2: **3.117 V**.
- LED2 on, released: **3.282 V**.
- Held press switching toward LED1: **3.080 V**.

These are loaded original-remote supply measurements with ESP32 disconnected. They are separate from the preceding confirmed C1 button-input measurements. Derived differences from each starting released state to held press are **222 mV** toward LED2 and **202 mV** toward LED1; these are calculations, not additional meter readings.

This completes the planned initial live characterization. No further original-remote multimeter tests are required before designing a prototype interface. PC naming remains intentionally deferred. Supply-current capacity for powering ESP32, logic thresholds under altered loading, startup/power-off/back-feed behavior and actual GPIO switching still require interface design and prototype validation. No ESP32 connection or firmware has been tested.

## Independent review completed

A fresh subagent derived the same component-level netlist from the readings-only packet before reading the existing schematic. The locked Phase A hash, input packet hash and unchanged comparison-reference hash were verified by the parent. The report supports the reconstructed netlist but does not validate an ESP32 connection or uniquely prove hidden topology. It flags two inferred resistor-to-LED links, unknown transient/power-off/loading limits and stale current-document statements.

The exact reviewed schematic is preserved at [reviewed-schematic.md](verification/round-1/reviewed-schematic.md). Current schematic/build-plan prose was then consolidated to remove stale pending-measurement claims and distinguish measured behavior from interface validation. The locked independent reconstruction and report were not edited. See the [review](verification/round-1/comparison-and-safety-review.md) for the staged isolated-interface recommendation and residual risks.

## Direct resistor-to-LED links confirmed after independent review

Owner reports **R3 A ↔ LED1 + = 0.1 Ω** and **R1 A ↔ LED2 + = 0.1 Ω** in response to the request for direct Ω-mode measurements with the remote unplugged. These close the two originally inferred connections in the reconstructed netlist. The readings are near zero; the earlier 0.2 Ω probe baseline is not a fixed value to subtract mechanically from every low-resistance reading.

Current schematic and prototype plan now mark both links directly confirmed. The original blind reconstruction, checkpoint, input packet, comparison report and reviewed-reference snapshot remain unchanged. A separate post-review evidence note records this resolution. No ESP32 interface, transient, power-off or supply-capacity validation is implied.
