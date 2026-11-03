# Offline tracing checklist

Status: functional tracing complete for all listed components. See the consolidated [reconstructed schematic](schematic.md) for current results. Resistor-to-LED pairings were initially cross-checked through diode-path illumination, then directly confirmed after independent review: R1 A-to-LED2 + = 0.1 Ω; R3 A-to-LED1 + = 0.1 Ω. The checklist below records the workflow, not outstanding requests.

Communication preference: always pair each wire colour with its plug contact: **red/tip**, **white/ring 1**, **green/ring 2**, **black/sleeve**. Use these pairs in measurement requests and explanations.

Owner decision: trace all accessible passive connections before powered testing. Keep the remote unplugged from the KVM and ESP32 throughout this checklist. Leave J1 connected to the remote PCB for wire-to-component measurements.

## Known network

- Red/tip -> button-controlled connection -> R2 A -> R2 approximately 100 Ω -> R2 B -> black/sleeve.
- Red/tip -> C1 B -> C1 approximately 100 nF -> C1 A -> white/ring 1.
- R1 = 992 Ω; R3 = 508 Ω, measured in circuit.
- Probe-to-probe baseline = 0.2 Ω. Direct connections have measured approximately 0.5–1 Ω.
- LED branches are now mapped: black/sleeve -> R1 -> LED2 -> green/ring 2, then green/ring 2 -> R3 -> LED1 -> white/ring 1. Ground/supply and green/ring 2 operating behavior are not voltage-verified.

## 1. Trace R1 and R3

Use Ω mode, button released. For each resistor, label the end nearest the PCB edge A and the opposite end B; record orientation before measuring. These new labels apply only to R1/R3; existing C1/R2 labels retain their previous definitions.

For each end, measure to all four J1 wire terminals: red/tip, white/ring 1, green/ring 2, black/sleeve. Record actual resistance or OL. Near-zero indicates a direct connection; a finite resistor-sized value may include other components. Allow transient capacitor readings to settle. Do not interpret OL as proof no semiconductor or capacitor path exists.

Report R1 first, then R3. Do not fill unmeasured entries from predictions.

## 2. Trace LED terminals

Identify the two soldered ends of each LED and record the PCB's visible + marking without assuming the diode orientation has been electrically verified. In Ω mode, trace each end to the four J1 wires and to R1/R3 ends not yet assigned to a wire. Look for near-zero direct connections. Record nonzero results as observations, not direct wires.

Use the resulting terminal continuity to pair each LED with a resistor if supported. Determine whether the LED branches share a common net and which wire carries each branch. Physical LED1/LED2 naming does not establish which PC each indicates.

## 3. Confirm LED polarity

With no external power, select diode mode. Probe directly across each LED in both directions and record the voltage or OL, probe colours and any faint illumination. Red on anode and black on cathode normally gives forward conduction, but the meter may not provide sufficient voltage for every LED. OL in both directions is inconclusive. In-circuit paths may affect the result. Cross-check against the silkscreen and traced terminals; do not desolder just to force a reading.

## 4. Map KEY1 legs

Label the four switch legs by physical position relative to a fixed board orientation. Measure all six leg pairs released and pressed. Distinguish permanently joined duplicate terminals from the pair of switch nets that joins only when pressed. Trace those nets to red/tip and R2 A to confirm the inferred button path directly at KEY1.

## 5. Complete the net list

Reconcile every terminal of J1, R1/R2/R3, C1, LED1/LED2 and KEY1 against the recorded measurements. Confirm C1's two connections and check for any additional branches or shared nodes. List remaining ambiguity rather than assigning ground from colour or sleeve position. Produce a complete passive schematic before proposing powered tests.

Offline tests establish connectivity and component behavior, but may not establish KVM-side ground, drive voltage or safe GPIO compatibility. Keep those explicitly unresolved until measured later.
