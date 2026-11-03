# Phase A: independent reconstruction

Evidence: only `input-readings.md` in this directory. No existing schematic, other project file, git data, parent history or agent output was accessed. This is a documentary reconstruction of owner measurements, not physical testing. Component polarity labels are the owner's labels.

## Preferred complete netlist

```text
Connector/contact    Wire     Node
Tip                  Red      P (button signal)
Ring 1               White    G (reference candidate)
Ring 2               Green    S (selection/status candidate)
Sleeve               Black    V (supply candidate)

R1: V -- 992 ohm measured in circuit -- A2
LED2: anode A2 --|>|-- cathode S
R3: S -- 508 ohm measured in circuit -- A1
LED1: anode A1 --|>|-- cathode G
R2: V -- 99.6 ohm measured in circuit -- K
KEY1: K -- normally open contact -- P
C1: P -- 98.7 nF measured in circuit -- G

V ---- R1 ---- LED2 >| ---- S ---- R3 ---- LED1 >| ---- G
|
+----- R2 ---- K ---- KEY1 (NO) ---- P
                                    |
                                   C1
                                    |
                                    G
```

In the drawing each `>|` denotes anode on the left, cathode on the right. C1 polarity is unidentified; the capacitor symbol implies no established polarization.

Component pad mapping:

- R1 B = V; R1 A = A2 = LED2 anode (latter link inferred through resistance/diode paths, not a direct zero-ohm measurement).
- R2 B = V; R2 A = K = both left KEY1 legs.
- R3 B = S; R3 A = A1 = LED1 anode (latter link inferred).
- C1 A = G; C1 B = P.
- KEY1 upper-left and lower-left form K; upper-right and lower-right form P. The tested left/right legs establish this orientation under the owner's LED-face view. Opposite sides join when pressed. There is no separately reported J1 cavity numbering; wire/contact mapping must not be turned into an invented PCB pin order.
- LED1 cathode = G; LED2 cathode = S, supported by 0.7/0.9 ohm readings before the possible disconnection and by later diode paths. Direct diode tests light each LED at reported 2.56 V; explicit confirmation of probe polarity on each direct test is incomplete, but cross-node diode tests support the stated orientation.

## Connectivity evidence and attempts to falsify it

Direct cable/pad paths measure roughly 0.5–1 ohm with a 0.2 ohm probe baseline. These support continuity, not intentional series resistors. KEY1 same-side readings of 0.1 ohm, below the stated baseline, show the limits of subtracting a single baseline mechanically.

R2 A to tip is 0.7 ohm pressed and OL released; R2 B to tip is 100.5 ohm pressed and OL released. R2 B to sleeve is 0.7–1 ohm; R2 A to sleeve is 100.4 ohm in either state. Together these strongly locate R2 before the left switch side, with the right side on P. The approximately 101 ohm sleeve-to-tip pressed measurement agrees. This is a closure toward V through R2, not a measured closure toward G.

R1 B to sleeve is 0.5–1 ohm in either state; R1 A to sleeve is corrected to 995 ohm pressed. R1 B to tip is 102 ohm pressed. R1 A to tip is 1,075 then 1,057 ohm pressed, versus a calculated 1,091.6 ohm nominal measured-resistor sum. The discrepancies (about 1.5% and 3.2%, before contact corrections) remain unexplained; this is not precision proof of a two-resistor series path. Meter/contact variability, in-circuit paths or an omitted path are alternatives. No reliable exact released R1 A-to-sleeve value was supplied.

LED2 anode to sleeve is 992 ohm released, and to tip about 1.1 kohm pressed/OL released. R1 A to S in diode mode gives 2.57 V and lights LED2. LED2 cathode-to-S is near zero. These favor R1 feeding LED2, although R1 A-to-LED2 anode has not been directly checked.

LED1 anode to S is about 500 ohm, matching R3; R3 A to G in diode mode gives 2.56 V and lights LED1. LED1 cathode-to-G is near zero. LED1 anode-to-sleeve OL is compatible with intervening semiconductors. R3 A-to-LED1 anode has not been directly checked.

R1 A-to-G OL in both diode directions does not disprove the two LEDs in series: two approximately 2.56 V forward drops can exceed the meter's available diode-test voltage. Conversely these values are measured under meter excitation, not the LEDs' forward drops at operating current. Red-to-R1 A diode mode, 0.762 V pressed/OL released, has an all-resistor path through KEY1, R2 and R1; it is not evidence of another diode.

C1 B-to-tip and C1 A-to-white are near zero. The 97.5 nF C1 A-to-tip reading agrees with C1's 98.7 nF in-circuit value. C1 A-to-sleeve reading 97.5 nF only pressed fits access through R2 and KEY1. The 0.15 nF released and fluctuating 0.4 nF switched cable readings are meter/network observations, not additional identified capacitors. The unspecified zero capacitance while pressing cannot define a net.

No-beep across plug pairs is compatible with a 100 ohm pressed path if the meter's beep threshold is lower. No-beep is not six OL measurements. OL resistance through LED paths is also not proof of absent connectivity. The R3-to-white/sleeve OL answers did not identify which resistor end was measured, so they cannot establish both ends individually.

Alternative topology: hidden parallel paths or components could produce the same finite meter observations. The packet does not provide a PCB image, a complete pad-to-pad matrix, isolated component values or complete internal KVM circuitry. The preferred circuit uses every identifiable component without requiring extra parts, but should not be claimed uniquely proven. In particular the two inferred resistor-to-anode junctions deserve direct disconnected continuity confirmation if exact topology is required.

## Roles inferred from powered readings

G is the measured reference, not a proven earth, chassis or USB ground. V is approximately 3.3 V relative to G and supplies the remote loads, but its regulator/source type and current capability are unknown. S changes with selection: cable-only 31.1 mV versus 3.34 V, loaded LED2-on latest 33 mV versus LED1-on 3 V. Thus low S enables V/R1/LED2/S and high S enables S/R3/LED1/G. Computer identities are not established. The two branches load S in opposite directions. A push-pull driver, switched resistive source/sink or other circuit could cause these observations; unloaded voltage alone cannot distinguish them.

P is about zero released, including 3 mV with cable only. With the original switch held it is 2.895 V toward LED2 or 2.861 V toward LED1. This strongly suggests an active-high button input with a KVM-side return/pull-down, but neither that return resistance nor the recognition thresholds are established. C1 likely filters/debounces P; internal KVM timing is unknown. Very quick and 0.5–1 second presses can work; no minimum pulse, edge, repeat or debounce rule is measured.

Latest labelled V readings are 3.339 V released LED1, 3.117 V held toward LED2, 3.282 V released LED2 and 3.080 V held toward LED1. Pressed P readings can be paired by named direction, with the caveat that separate meter sessions are not simultaneous: (3.117 - 2.895)/99.6 = 2.23 mA and (3.080 - 2.861)/99.6 = 2.20 mA inferred R2 currents. The earlier directly measured 217 mV across R2 implies 2.18 mA. Earlier V=3.1 and P=2.86 imply 2.41 mA, an approximate non-simultaneous comparison. These are original button-path currents, not available controller current.

V falls about 0.20–0.22 V during held operation in the labelled readings. The same-selection released/held pairs differ by 0.259 V (LED1) and 0.165 V (LED2), because a held press changes selection. No single differential source resistance can be extracted: LED loads, sampling time and selection differ. These observations establish sag, not a rated supply, transient limits or spare source capability. P remaining high while held and returning low released resolves the earlier ambiguous near-zero/transient P reports. LED2-on low S enables LED2; LED1-on high S enables LED1, consistent with the preferred topology.

## Exclusions and unresolved evidence

Withdrawn unlabelled C1 readings are superseded by labelled C1 A readings. The erroneous 0.995 ohm R1 A-to-sleeve report is superseded only for the corrected pressed reading (995 ohm). The initial zero released diode report is superseded by settled OL. The possible-J1-disconnection block is excluded, including its LED1 cathode OL recheck; later reseated readings take precedence.

The two 2.1 mV status readings, including LED1 on, conflict with the preferred status behavior and later 3 V observation. Poor contact is suspected, not proven. Retain this as an unresolved measurement discrepancy; it cannot be used to claim invariant status. Earlier LED2-on 0.8 mV and 2.1 mV versus latest 33 mV are all low-level observations with varying conditions/contact quality, not a calibrated logic-low specification. The latest 3 V LED1-on reading lacks explicit held/released confirmation. The 3.3 mV P statement did not establish a physically held state; later explicit four-state measurements supersede that interpretation.

## Safety boundary

This circuit reconstruction does not validate an ESP32 interface. Static readings do not establish absolute voltage limits, negative/transient excursions, source/sink current limits, startup behavior, power sequencing, shared ground safety, or acceptable press timing. No controller was attached. Preserve the original remote and its LED/passive loading unless a changed load is separately validated. Prefer a normally-open isolated contact across the original KEY1 pads for actuation; establish its behavior on a disconnected prototype first. No unqualified safe-to-connect finding is possible from this packet.
