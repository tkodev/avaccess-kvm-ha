# AV Access KVM to Home Assistant

Connect an AV Access iDock B10 to Home Assistant using a Seeed Studio XIAO ESP32-C6 and ESPHome.

Status: passive schematic reconstruction and initial static live characterization are complete. A blind independent review matches the netlist. Both resistor-to-LED links that remained inferred at review time are now directly confirmed at 0.1 Ω each. The button is active-high, with red/tip at 0 V released and approximately 2.9 V held relative to white/ring 1. Green/ring 2 reports LED1/HIGH (~3 V) and LED2/LOW (~0 V). State-labelled supply and button results are compiled in [actual readings](docs/readings.md). No ESP32 interface or firmware has been tested.

## Recommended approach

Build a standalone ESP32 controller that plugs into the KVM's **CTL** port in place of the EXB01 remote. The owner will connect either the original remote or this controller, so no pass-through is needed. The headset jack is a separate port.

Powered measurements support an active-high button: red/tip is raised toward black/sleeve through R2 (~100 Ω). White/ring 1 is the measurement reference, black/sleeve supplies approximately 3.3 V, and green/ring 2 reports selection state. Red/tip rises from 0 V to approximately 2.9 V during a held press. A safe ESP32 ground bond, loading limits and power-off behavior remain unverified. The earlier GPIO-LOW method would not emulate this measured press polarity.

The owner's preferred solution is a new minimal board around the XIAO, or the XIAO with the necessary interface components: no pass-through, remote PCB, indicator LEDs or manual buttons. The XIAO handles control and electrical status sensing. This is a sensible design direction; bare GPIO safety and operation without the original passive/LED loads remain unverified. See [controller solutions](docs/solutions.md) for options and [interface protection](docs/interface-protection.md) for the remaining checks. Reusing the remote PCB with an isolated KEY1 closure remains a prototype/fallback option.

The [revision A controller schematic](docs/controller-schematic.md) specifies an eight-component prototype using a PhotoMOS button switch and MOSFET status receiver. It retains the 100 Ω press-path and 100 nF filter functions, and uses the XIAO's internal status pull-up. Shared-ground compatibility, power sequencing and removal of the original LED loads still need bench validation.

The XIAO ESP32-C6 is suitable for a Wi-Fi ESPHome bridge. Start with separate USB power. Do not power it from an unidentified CTL conductor or connect unmeasured CTL signals to GPIO.

## Findings

- Similar ESPHome KVM projects exist; no verified B10/EXB01 integration or contact map was found in the searches recorded here.
- The supplied photos show two LEDs, one tactile switch, three resistor designators and one capacitor designator. In-circuit values and functional connections are now recorded in the reconstructed schematic.
- The LED branches map as black/sleeve -> R1 -> LED2 -> green/ring 2 -> R3 -> LED1 -> white/ring 1, with green/ring 2 as an external midpoint. Live readings support LED1/HIGH and LED2/LOW status; source/sink ratings remain unknown.
- Owner-reported mapping: red/tip, white/ring 1, green/ring 2, black/sleeve. White/ring 1 is the measured reference; compatibility with ESP32/USB ground remains unverified.
- Owner measured approximately 101 Ω between red/tip and black/sleeve with the button pressed. KEY1 connects red/tip through R2 (~100 Ω) to black/sleeve.
- C1 measures approximately 100 nF and is directly connected between red/tip (end B) and white/ring 1 (end A), based on 0.5 Ω continuity measurements.
- AV Access documents panel and wired-remote switching for the B10. Its Ethernet connection provides networking to the computers; the reviewed documentation does not describe a control API.

## Project files

- [Agent instructions](AGENTS.md)
- [Research and sources](docs/research.md)
- [Bench findings and current handoff](docs/bench-notes.md)
- [Actual readings: live and earlier unpowered tests](docs/readings.md)
- [Independent reconstruction](docs/verification/round-1/independent-schematic.md) and [comparison/safety review](docs/verification/round-1/comparison-and-safety-review.md)
- [Reconstructed schematic](docs/schematic.md) and [both schematic views](docs/schematic.html)
- [Offline tracing checklist](docs/offline-tracing.md)
- [Measurement and build plan](docs/bring-up.md)
- [Controller solutions and owner's preferred design](docs/solutions.md)
- [Proposed controller schematic and BOM](docs/controller-schematic.md) and [drawing](docs/controller-schematic.html)
- [PCB and snap-fit enclosure requirements and progress](hardware/README.md)
- [Interface protection and minimal-parts options](docs/interface-protection.md)
- [Measurement worksheet](docs/measurements.csv)
- [Original remote photos](docs/photos/README.md)

Next step: validate the proposed revision A interface on a disconnected prototype and verify the proposed common ground. The independent review's remote-PCB/isolated-closure recommendation remains a fallback. The two remaining resistor-to-LED continuity checks are complete. PC labels will be mapped later.
