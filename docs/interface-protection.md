# ESP32 interface protection

The remote circuit and normal powered behavior are supported by the [recorded readings](readings.md) and [schematic](schematic.md). The remaining work is to design and validate the ESP32 interface. No interface hardware or firmware has been tested, and no new physical measurements are recorded here.

The owner's preferred direction is a new minimal board or XIAO with control and electrical sensing, without the remote PCB, pass-through, LEDs or manual buttons. See [controller solutions](solutions.md). The protection checks apply to that design too; remote-PCB reuse below is an alternative.

The [revision A controller schematic](controller-schematic.md) proposes an eight-component PhotoMOS/MOSFET interface. Its button path avoids using ESP power to drive the KVM input, and its status gate avoids a direct CTL-to-GPIO connection. It still joins white/ring 1 to ESP GND, uses the internal status pull-up and needs bench validation; it is not a fully isolated system.

## What remains to check

| Condition | Question to resolve before direct GPIO wiring |
| --- | --- |
| Both devices powered | Do logic thresholds, output levels, pin currents and status loading stay within the selected parts' specifications? |
| KVM on, ESP32 off | Can green/ring 2 or another connected conductor inject current into the unpowered ESP32? |
| ESP32 on, KVM off | Can the button driver on red/tip, or a status pull-up, inject current into the unpowered KVM? |
| Startup, reset, flashing or brownout | Does the hardware keep the button released before firmware runs? What happens if an output remains asserted? |
| Button transitions | Can the driver handle C1 charging as well as the calculated 2.18 mA steady held current? |
| Shared ground | Is a white/ring 1-to-ESP32 ground bond compatible with both devices' USB, supply and earth connections? |

Back-powering means a signal connection supplies current into a device whose normal supply is off. TI describes this mechanism and components with explicit powered-off protection, often specified as **Ioff**. Any selected buffer or switch needs suitable ratings for its actual connected pins and power states. Firmware high impedance alone does not establish power-off protection. [TI explanation](https://www.ti.com/document-viewer/lit/html/SSZTAP0)

The ESP32-C6 datasheet is the reference for pin limits, logic thresholds and startup configuration. The measured status levels are compatible with nominal 3.3 V logic, but that comparison alone does not establish power-off tolerance, transient limits or a safe shared ground. [Espressif datasheet](https://documentation.espressif.com/esp32-c6_datasheet_en.html)

The approximately 33 mA ideal initial C1 charging estimate in the schematic is calculated, not measured. It is neither a guaranteed peak nor a device rating. The KVM's internal input thresholds, protection structures and available status current remain unknown.

## Remote-PCB reuse candidate

Reuse the remote PCB and add a normally-open PhotoMOS contact across KEY1. This retains R1/R2/R3/C1 and both LED loads. A candidate implementation adds a PhotoMOS, an input-current resistor and a default-off pull-down: approximately three new components, plus wiring and separate ESP32 power. Exact values and startup behavior still require verification.

The Panasonic **AQY212EH** is a candidate to evaluate, not a selected or validated part. Check input drive, on-resistance, off leakage, switching current and timing against its datasheet and the assembled circuit. The pull-down must keep the PhotoMOS input off in the tested boot/reset states. [Manufacturer specifications](https://industry.panasonic.com/global/en/products/control/relay/photomos/number/aqy212eh)

Across KEY1, the original R2 remains in the press path. Do not add another 100 Ω for this connection. A complete replacement using red/tip-to-black/sleeve closure must reproduce R2's function exactly once.

Optical observation of an existing LED can add status feedback without an electrical status connection. A light-dependent resistor and resistor are one candidate, requiring shielding and calibration. This is not a final sensor selection. Any electrically connected receiver, including an optocoupler input, must have its loading and power-off behavior checked.

A fully isolated arrangement avoids a deliberate ESP32-to-KVM signal or ground bond. Other power/USB connections must also be considered. Isolation does not replace checking the contact ratings, assembly and default state.

## Validation before connecting the KVM

Select the actual circuit and check its datasheets first. Build a disconnected prototype using a current-limited source and simulated KVM load. Exercise both startup orders, either side powered off, reset/flashing, brownout and a stuck output. Check leakage, unintended supply injection and button release. A multimeter cannot exclude brief glitches; use suitable waveform measurements where required.

Do not find current limits by shorting or overloading the owner's KVM. More normal static voltage readings alone will not settle these interface questions. See the [build plan](bring-up.md) for staged validation and first connection.
