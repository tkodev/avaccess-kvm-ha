# AV Access KVM to Home Assistant

Connect an AV Access iDock B10 to Home Assistant using a Seeed Studio XIAO ESP32-C6 and ESPHome.

Status: passive schematic reconstruction and initial static live characterization are complete. A blind independent review matches the netlist. Both resistor-to-LED links that remained inferred at review time are now directly confirmed at 0.1 Ω each. The button is active-high, with red/tip at 0 V released and approximately 2.9 V held relative to white/ring 1. Green/ring 2 reports LED1/HIGH (~3 V) and LED2/LOW (~0 V). State-labelled supply and button results are compiled in [actual readings](docs/readings.md). No ESP32 interface or firmware has been tested.

## Recommended approach

Build a standalone ESP32 controller that plugs into the KVM's **CTL** port in place of the EXB01 remote. The owner will connect either the original remote or this controller, so no pass-through is needed. The headset jack is a separate port.

The reconstructed circuit suggests an active-high button: red/tip is raised toward black/sleeve through R2 (~100 Ω). White/ring 1 may be ground, black/sleeve may be positive supply, and green/ring 2 may be one complementary LED-status signal. Powered measurements now confirm red/tip rises from 0 V to 2.86 V during a press relative to white/ring 1. Remaining wire-role and loading details need verification. The earlier GPIO-LOW method would not emulate this measured press polarity. Choose the output interface only after measuring voltage, current requirements and power-state behavior; preserve the 100 Ω press path.

The XIAO ESP32-C6 is suitable for a Wi-Fi ESPHome bridge. Start with separate USB power. Do not power it from an unidentified CTL conductor or connect unmeasured CTL signals to GPIO.

## Findings

- Similar ESPHome KVM projects exist; no verified B10/EXB01 integration or contact map was found in the searches recorded here.
- The supplied photos show two LEDs, one tactile switch, three resistor designators and one capacitor designator. In-circuit values and functional connections are now recorded in the reconstructed schematic.
- The LED branches map as black/sleeve -> R1 -> LED2 -> green/ring 2 -> R3 -> LED1 -> white/ring 1, with green/ring 2 as an external midpoint. This does not identify KVM ground or drive polarity.
- Owner-reported mapping: red/tip, white/ring 1, green/ring 2, black/sleeve. No wire is electrically verified as ground; black/sleeve is a positive-supply candidate.
- Owner measured approximately 101 Ω between red/tip and black/sleeve with the button pressed. KEY1 connects red/tip through R2 (~100 Ω) to black/sleeve. Ground remains unverified.
- C1 measures approximately 100 nF and is directly connected between red/tip (end B) and white/ring 1 (end A), based on 0.5 Ω continuity measurements.
- AV Access documents panel and wired-remote switching for the B10. Its Ethernet connection provides networking to the computers; the reviewed documentation does not describe a control API.

## Project files

- [Research and sources](docs/research.md)
- [Bench findings and current handoff](docs/bench-notes.md)
- [Actual readings: live and earlier unpowered tests](docs/readings.md)
- [Independent reconstruction](docs/verification/round-1/independent-schematic.md) and [comparison/safety review](docs/verification/round-1/comparison-and-safety-review.md)
- [Reconstructed schematic](docs/schematic.md) and [both schematic views](docs/schematic.html)
- [Offline tracing checklist](docs/offline-tracing.md)
- [Measurement and build plan](docs/bring-up.md)
- [Measurement worksheet](docs/measurements.csv)
- [Original remote photos](docs/photos/README.md)

Next step: select the interface architecture and validate a disconnected prototype. The two remaining resistor-to-LED continuity checks are complete. The independent review recommends preserving the remote loads and using an isolated normally-open KEY1 closure initially. PC labels will be mapped later.
