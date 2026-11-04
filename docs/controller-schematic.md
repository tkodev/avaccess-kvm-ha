# Preferred controller schematic, revision A

**Proposed prototype, not validated KVM wiring.** This implements the [preferred standalone solution](solutions.md): XIAO control and electrical sensing, without the original remote PCB, pass-through, indicator LEDs or manual button. The [measured remote schematic](schematic.md) remains a separate reference.

View the [drawing](controller-schematic.html) or [standalone SVG](controller-schematic.svg). This is a component-level schematic and netlist, not a PCB layout or fabrication package.

## Circuit

```text
BUTTON CONTACT PATH

black/sleeve ── R101 100 Ω ── U101 pin 4
                              normally-open PhotoMOS
red/tip ──────────────────── U101 pin 3
   │
 C101 100 nF
   │
white/ring 1 ───────────────────────── COMMON

BUTTON COMMAND

XIAO D1 / GPIO1 ──┬── R102 150 Ω ── U101 pin 1, anode
                 │
             R103 10 kΩ             U101 pin 2, cathode ── COMMON
                 │
               COMMON

STATUS RECEIVER

green/ring 2 ──┬────────────────── Q101 pin 1, gate
              │
          R104 100 kΩ               Q101 pin 2, source ── COMMON
              │
            COMMON                 Q101 pin 3, drain ── R105 1 kΩ ── XIAO D2 / GPIO2
                                                                   │
                                                     internal pull-up enabled
                                                                   │
                                                               XIAO 3V3

COMMON = white/ring 1 joined to XIAO GND, subject to ground verification.
Power the XIAO from separate USB. Its 3V3 rail never joins black/sleeve.
```

U101 = Panasonic **AQY212EH**, DIP4. Q101 = AOS **AO3400A**, SOT-23. Use these exact parts for the draft checks; similar names do not guarantee identical ratings or pinouts. Designators R101–R105 and C101 belong to the new board, not the original remote.

## Parts

| Reference | Part/value | Purpose |
| --- | --- | --- |
| U101 | AQY212EH, normally-open PhotoMOS | Reproduce the button contact using the KVM's own supply |
| Q101 | AO3400A, N-channel MOSFET | Sense status at its gate; switch an ESP-powered GPIO node |
| R101 | 100 Ω, 1%, ≥0.25 W | Reproduce original R2's press-path function |
| R102 | 150 Ω, 1%, ≥0.25 W | Set PhotoMOS input current |
| R103 | 10 kΩ, 1%, ≥0.125 W | Pull button command LOW when the GPIO is high impedance |
| R104 | 100 kΩ, 1%, ≥0.125 W | Discharge the MOSFET gate when status is disconnected/floating |
| R105 | 1 kΩ, 1%, ≥0.125 W | Limit current if the status GPIO is accidentally configured as an output |
| C101 | 100 nF ceramic, X7R, ±10%, ≥16 V | Retain original C1's button-input filtering function |

**Eight supporting components**, excluding the XIAO, TRRS plug/cable, mounting and USB supply. The status pull-up uses the XIAO's internal GPIO pull-up, enabled in firmware; it is not an added component. No separate IC supply or decoupling capacitor is required by U101 or Q101. The PhotoMOS has an internal light emitter; it is not a visible status indicator or optical sensor aimed at the old remote.

## Pin connections

| Connection | Destination |
| --- | --- |
| Black/sleeve | R101, then U101 pin 4 |
| Red/tip | U101 pin 3 and C101 |
| Green/ring 2 | Q101 gate, pin 1, and R104 |
| White/ring 1 | COMMON: XIAO GND, U101 pin 2, Q101 source pin 2, lower ends of R103/R104/C101 |
| XIAO D1 / GPIO1 | R102 and upper end of R103 |
| R102 other end | U101 input anode, pin 1 |
| XIAO D2 / GPIO2 | R105, then Q101 drain, pin 3; internal pull-up enabled |
| XIAO 3V3 | Internal GPIO pull-up supply; no external connection to CTL supply |

U101 contact pins 3/4 are bidirectional. Q101 source/drain are not interchangeable here: its body diode must point from COMMON toward the GPIO drain node. Verify package orientation against the manufacturer drawings before assembly. TRRS contact names do not assign physical solder-terminal or original J1 cavity numbers.

D1/GPIO1 and D2/GPIO2 are proposed assignments from the [Seeed pin map](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/). Confirm the owner's board revision. Check the complete application and reset/boot behavior before use; do not enable a peripheral that drives the command pin unexpectedly. No firmware is supplied yet.

## Operation

Configure D2 as an input with its internal pull-up enabled before interpreting feedback. During reset or before that configuration, feedback may float and must be treated as unknown.

D1 HIGH drives the PhotoMOS input and closes its output, connecting black/sleeve through R101 to red/tip. D1 LOW or high impedance releases it after the PhotoMOS turn-off delay. C101 charges and discharges as in the original button circuit. A GPIO stuck HIGH will hold the contact closed: this draft has no hardware pulse limiter.

Q101 inverts status. With the XIAO powered and the KVM providing the observed levels:

| KVM status | Q101 | D2 reading |
| --- | --- | --- |
| Green/ring 2 HIGH, original LED1 state | On | LOW |
| Green/ring 2 LOW, original LED2 state | Off | HIGH |

Computer labels are still deferred. A powered-off/disconnected KVM may also produce D2 HIGH; this circuit does not independently detect KVM power. Do not report a confirmed computer selection from that reading alone when KVM power is unknown.

## Design checks

These are datasheet checks and calculations, not hardware test results. Scope: nominal 3.3 V XIAO operation and an initial indoor prototype around 0–50°C. Recheck temperature and tolerance limits for the final assembly.

The [Panasonic catalog](https://mediap.industry.panasonic.eu/assets/download-files/import/semi_eng_ge1a_aqy21_e_dip4.pdf), printed pages 2–3 and 6, specifies U101's pinout, 5–30 mA recommended input current, 1.5 V maximum forward drop, 2.5 Ω maximum contact resistance and 1 µA maximum off leakage under its stated test conditions. Its load rating comfortably exceeds the estimated tens-of-mA C101 charging pulse. Verify drive and switching in the prototype, including temperature dependence.

- At 3.3 V and a typical 1.25 V input drop, R102 gives about **13.7 mA**. R103 adds **0.33 mA** to GPIO load.
- Using a 2.64 V GPIO HIGH, 1.5 V input drop and R102 at +1%, input current is about **7.52 mA**. At an assumed 2.4 V output it is about **5.94 mA**. These arithmetic cases do not guarantee GPIO output over all supply/temperature conditions. Measure actual current and output voltage; configure appropriate output drive using the [ESP32-C6 specifications](https://documentation.espressif.com/esp32-c6_datasheet_en.html).
- A conservative 3.6 V / minimum R102 calculation, even assuming zero input drop, gives **24.3 mA** and **87.3 mW** in R102. This is a bounding calculation, not a normal operating point or a GPIO current rating.
- U101's specified 2.5 Ω on-resistance would add about **5.5 mV** at the previously calculated 2.18 mA held current. Actual current can change with the new load. R101 still supplies approximately the original 100 Ω function once.

The [AO3400A datasheet](https://www.aosmd.com/res/data_sheets/AO3400A.pdf), pages 1–2, specifies gate drive at 2.5 V, gate leakage and source/drain behavior. Its 2.5 V on-resistance specification gives more useful evidence than gate-threshold voltage alone.

- At the observed 3 V status HIGH, R104 draws about **30 µA**, plus gate leakage and transient gate charging. This is a new load requiring verification; it is not zero-load sensing.
- With the ESP32-C6's **typical 45 kΩ internal pull-up**, Q101 on gives approximately **72 µA** drain current and **72 mV** at D2 across R105. These are nominal calculations, not guaranteed GPIO pull-up limits.
- A 1 µA drain-leakage case drops about 45 mV across that typical pull-up; the MOSFET datasheet's 5 µA case at 55°C drops about 225 mV. Confirm GPIO HIGH/LOW margins with the actual board and temperature. Add an external 10 kΩ pull-up to XIAO 3V3 if the internal pull-up does not provide sufficient margin; that makes nine supporting components.
- If D2 is accidentally driven HIGH while Q101 is on, R105 limits the nominal contention current to approximately **3.3 mA**. It does not provide arbitrary overvoltage or fault immunity.

## Power states and unresolved limits

- **KVM on, XIAO off:** U101's input is off; its output remains open subject to leakage. Q101 may be on, but the internal GPIO pull-up has no powered supply. There is no intended DC path from its gate into the GPIO. Capacitive coupling still needs consideration.
- **XIAO on, KVM off:** U101 switches only the KVM contact path, not ESP power into red/tip. R104 pulls a floating gate LOW; the enabled internal pull-up raises D2 locally. Actual KVM power-off impedances remain unknown.
- **Reset/flashing:** R103 provides a released default for a high-impedance D1. It cannot override a GPIO actively driven HIGH. Bench-check reset, flashing, brownout and both startup orders.
- **Shared reference:** this is **not a galvanically isolated system**. White/ring 1 joins ESP GND even though U101 has an isolated input. Verify that bond with the actual USB/power arrangement before connecting the KVM. If unsuitable, revise the status interface for isolation.
- **Removed loads:** the original R1/R3/LED branches are omitted; C1's function is retained. Status levels, drive behavior and switching without those LED loads are unverified. A 33 mV LOW measured with the original remote attached is not a guaranteed new-board LOW.
- **Faults/transients:** no cable TVS, reverse-wiring protection, gate clamp, current limiter for arbitrary shorts or hardware pulse limiter is included. Device internal ESD ratings do not establish finished-assembly immunity. Add protection if the measured environment requires it; do not claim the eight-part count guarantees safety.

## Validation before KVM connection

Follow the [build plan](bring-up.md). First inspect pin orientation and wiring, then test disconnected from the KVM using current-limited supplies. A synthetic button load around 1.3 kΩ to COMMON can exercise the measured current scale; it does not identify a resistor inside the KVM.

1. Verify PhotoMOS input current, command voltage, released leakage, button waveform and actual bounded pulse duration. Exercise reset/flashing, brownout and stuck-HIGH behavior.
2. Drive the status input through simulated 0 V and approximately 3 V levels; enable the internal pull-up and check input loading, D2 levels and transitions with either supply off and both startup orders. Check MOSFET capacitive coupling when power changes.
3. Verify the proposed ground bond separately, and confirm harness contacts with all equipment unpowered. Keep the expensive KVM out of fault testing.
4. Only after those checks pass, stage a first KVM connection and validate the changed LED loading. Compare against [actual readings](readings.md). Record measured results before calling revision A validated.

No physical tests or new owner readings were performed to produce this schematic.
