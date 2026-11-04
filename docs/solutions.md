# Controller solutions

## Owner's preferred solution: standalone minimal board

Build a new controller around the XIAO ESP32-C6, either with a small interface PCB or components wired directly to the XIAO. It replaces the remote at the CTL port. No pass-through, original remote PCB, indicator LEDs or manual buttons are wanted. The XIAO handles control, selection sensing and Home Assistant communication. Minimize supporting components while meeting the electrical requirements.

This makes sense as the final product direction. The measurements support a momentary active-high control input and one binary status signal, so manual controls and indicator LEDs are not inherently needed for the user interface. Whether the KVM works correctly without their electrical loads remains to be validated. A XIAO-only connection with no interface components is not yet established as safe.

The [revision A controller schematic](controller-schematic.md) now proposes an eight-component interface: PhotoMOS button closure, MOSFET status sensing, five resistors and one capacitor. It uses the XIAO's internal status pull-up. Parts are specified for this draft, but neither the interface nor operation without the old LED loads is validated.

### Preferred implementation to evaluate

- **Control:** a default-off switch/driver reproduces the measured red/tip-to-black/sleeve press path through approximately 100 Ω. Prefer switching the KVM's own black/sleeve supply rather than driving the input from the ESP32 supply. This is a design candidate, not wiring ready to build.
- **Sensing:** a high-impedance, suitably protected receiver reads green/ring 2 relative to white/ring 1. Observed levels are approximately 3 V for LED1/HIGH and 0 V for LED2/LOW; the names describe the original remote's states, not LEDs required on the new board. PC labels remain deferred.
- **Power:** separate USB power for the XIAO initially. Do not tie its 3V3 rail to black/sleeve. A shared white/ring 1-to-ESP32 ground requires verification; otherwise evaluate an isolated interface.
- **Parts:** select the fewest parts that provide control, sensing, default release and power-off protection. Include required bias and decoupling components in the count. Revision A has a proposed BOM; it is not a validated final assembly.

Remove R1/R3 and their LEDs only after validating operation without those branches. Retain R2's approximately 100 Ω press-path function initially. C1's approximately 100 nF function may be retained on the new board without retaining the remote PCB; omitting it is a separate change requiring validation. The six original passive/LED components are a baseline circuit, not a mandatory final BOM.

The XIAO provides GPIO and Wi-Fi for this role. Its presence does not establish that the KVM signals can connect directly to its pins. [Seeed board reference](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/), [ESP32-C6 electrical specifications](https://documentation.espressif.com/esp32-c6_datasheet_en.html)

## Other options

### Bare GPIO control and sensing

Smallest apparent component count, but conditional. Earlier GPIO-HIGH control with high-impedance release is unvalidated; GPIO-LOW does not reproduce the measured press. Both the button output and status input need voltage/current, startup and powered-off checks. This is an optimization candidate within the preferred standalone design, not an approved shortcut.

### New board with isolated control and sensing

Also meets the owner's preferred form: no remote PCB, pass-through, LEDs or manual buttons. An isolated normally-open contact can reproduce the press path, with an electrically isolated status receiver. The latter must be selected for the unknown status loading limits; an optocoupler input is not automatically a harmless load. This may use more parts but avoids a deliberate signal-ground bond. Exact circuitry is pending.

### Reuse the remote PCB with isolated button closure

Earlier suggested prototype/fallback. Retains the known passive and LED loads, then adds a PhotoMOS across KEY1. Optical LED sensing is possible. This reduces changes to the original electrical load but retains hardware the owner prefers to remove. Approximately three added button-interface parts does not include the retained board, sensing, wiring or power supply. It is not the preferred final solution.

## Decision and next work

Prioritize the standalone board with protected control and direct electrical status sensing. Keep bare GPIO as a possibility only if the checks support it. Keep isolation and remote-PCB reuse as alternatives, not prerequisites for the preferred design.

Use the [revision A schematic and BOM](controller-schematic.md) for the first concrete design evaluation, accounting for [interface protection](interface-protection.md). Explicit powered-off protection is a component feature to verify, not something implied by normal 3.3 V operation. [TI powered-off protection reference](https://www.ti.com/document-viewer/lit/html/SCDA015)

Validate the proposed interface on a disconnected simulated load, then validate the effect of omitted remote loads through the [staged build plan](bring-up.md). No new hardware measurements or firmware implementation are recorded by this decision.
