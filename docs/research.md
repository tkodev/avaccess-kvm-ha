# Research

Researched 2026-11-03. Search results are evidence of similar projects, not proof that no B10 implementation exists.

## Manufacturer documentation

- [iDock B10 product page](https://www.avaccess.com/products/idock-b10/): lists the EXB01 remote.
- [B10 user manual](https://www.avaccess.com/wp-content/uploads/2025/12/iDock-B10-User-Manual.pdf), printed pages 4–6: distinguishes the headset jack from CTL; describes input indicators and remote switching. No electrical CTL pinout is provided.
- [AV Access iDock FAQ](https://www.avaccess.com/blogs/guides/idock-kvm-docking-station-faqs/): lists panel and wired-remote switching for B10. Hotkeys and IR are listed for other models, so do not assume they apply here.
- [EXB01 announcement](https://community.avaccess.com/t/a-switch-button-extension-solution-for-idock-exb01/404): describes the CTL-connected accessory. Original compatibility discussion predates B10; use the B10 manual for B10 compatibility.

No supported HTTP, serial, USB control API, or Home Assistant integration was found for this model. This does not establish that an undocumented interface cannot exist. The LAN port alone is not evidence of network control.

## Existing projects

### Close match: 3.5 mm button emulation

[Vladimir Timofeenko: Zero to ESP32 remote control](https://vtimofeenko.com/posts/zero-to-esp32-remote-control/) uses ESPHome and a PC817 optocoupler to emulate a KVM button through a 3.5 mm connector. It demonstrates the basic approach, but does not establish B10 compatibility or its LED wiring. Do not copy its tip/sleeve assignment or component values as a B10 schematic.

### Button control with state feedback

[Home Assistant Community: Controlling a KVM Switch](https://community.home-assistant.io/t/controlling-a-kvm-switch/461455/10) reports successful ESP32 control of a different KVM using a mini-USB-shaped remote connector. Continuity testing identified separate press and status signals. This supports measuring the remote rather than assuming the connector follows a standard protocol. Its wiring is not applicable to the EXB01 without verification.

Searches included `AV Access iDock B10 ESPHome home assistant remote switch pinout`, `AV Access KVM wired remote 3.5mm ESP32 home assistant github`, quoted `EXB01 pinout`, `AV Access ESPHome`, and `idock-b10 esphome`.

## Evidence from supplied photos

- Front: `KEY1`, `LED1`, `LED2`, and LED polarity markings are visible.
- Rear: four cable conductors appear to enter a small connector; `R1`, `R2`, `R3`, and `C1` are visible. The owner identifies the PCB as `KEY01-B60 V2`, dated `2024.09.26`. This corrects the initial photo interpretation of the marking.
- No controller IC is visible on either photographed side. A passive circuit is plausible.
- Two resistors may limit LED current; another resistor and the capacitor may serve the button circuit. Their roles and values cannot be established from these photos.
- Later owner measurements map red/tip, white/ring 1, green/ring 2 and black/sleeve. Pressed red/tip-to-black/sleeve resistance is approximately 101 Ω. Subsequent offline tracing and live measurements established the functional circuit, component values and normal powered behavior. Interface limits remain unverified. See the current [schematic](schematic.md) and chronological [bench notes](bench-notes.md).

## Interface options

Current decision: prioritize a new minimal board or XIAO with protected control and electrical sensing, without the remote PCB, pass-through, LEDs or manual buttons. See [controller solutions](solutions.md) for the preferred design and alternatives. The following list includes earlier approaches.

1. **Standalone CTL controller:** selected by the owner. Replaces the EXB01 while connected; no simultaneous original remote and no pass-through. Measure the original circuit first, then reproduce its press behavior and sense selection state.
2. **Tap the remote PCB internally:** easiest way to access the actual switch contacts. Solder an isolated closure across the two distinct switch nets. This avoids recreating any cable-side resistor network, but modifies the remote and needs strain relief.
3. **Modify the KVM internally:** unnecessary unless external access proves unsuitable. The remote already exposes the required human control and indicators.
4. **Mechanical button pusher:** avoids electrical reverse engineering, but adds bulk and does not itself report the active input.

The owner has successfully used GPIO-low button emulation on another device. Powered measurements here support a different operating model: black/sleeve supplies approximately 3.3 V relative to white/ring 1, green/ring 2 reports selection state, and red/tip is an active-high button input. Compatibility of white/ring 1 with ESP32/USB ground remains unverified.

A LOW pulse would not reproduce the measured press. A GPIO HIGH through 100 Ω with high-impedance release remains an unvalidated candidate requiring voltage, current and power-state analysis. An isolated KEY1 closure with the original PCB preserves R2 and the other loads, but is now a prototype/fallback option rather than the owner's preferred final form. See [interface protection](interface-protection.md) and the [current build plan](bring-up.md); the early open-drain-sink proposal is superseded.

For feedback, green/ring 2 reports LED1/HIGH (~3 V) and LED2/LOW (~0 V) relative to white/ring 1 with the remote attached. One conditioned input may report both selections, but its loading and power-off behavior need validation. Optical LED observation is another candidate. Removing the original remote loads is a separate change requiring validation.

## XIAO and ESPHome references

### Available connector parts

The owner also has [uxcell PJ-320D PCB-mount 3.5 mm sockets, 24-pack](https://www.amazon.ca/dp/B0G1CND36F). The listing specifies four pins, but its installation text only names tip/ring/sleeve. Verify each socket exposes four independent plug contacts using an unplugged TRRS cable and a continuity meter; four solder terminals alone do not prove four independent contacts.

For the selected standalone controller, a socket is optional: use it with a verified straight-through four-conductor TRRS male-to-male cable, or wire a TRRS male pigtail directly to the circuit. The controller plugs into the KVM instead of the original remote. Map each solder terminal experimentally. These bare sockets provide mechanical connection only, with no voltage protection or isolation. Check the surface-mount footprint before choosing perfboard or a breakout PCB.

- [Seeed hardware and pin map](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/): D0 = GPIO0, D1 = GPIO1, D2 = GPIO2. GPIO3 and GPIO14 serve antenna switching; D3 is GPIO21, not GPIO3.
- [PlatformIO board definition](https://docs.platformio.org/en/latest/boards/espressif32/seeed_xiao_esp32c6.html): board ID `seeed_xiao_esp32c6`.
- [ESPHome ESP32 platform](https://esphome.io/components/esp32/): use ESP-IDF for the C6 and verify the installed ESPHome release supports the board.
- [GPIO switch](https://esphome.io/components/switch/gpio/), [template button](https://esphome.io/components/button/template/), [scripts](https://esphome.io/components/script/): building blocks for a timed, non-overlapping button press.

Use Wi-Fi and the native ESPHome API initially. C6 radio support does not make a normal ESPHome configuration a Zigbee device.

GPIO behavior reference: [Espressif ESP32-C6 GPIO documentation](https://docs.espressif.com/projects/esp-idf/en/stable/esp32c6/api-reference/peripherals/gpio.html).

## Board-marking search

The owner supplied the marking `KEY01-B60 V2 / 2024.09.26`. Searches on 2026-11-03 for the exact marking, schematic/KVM variants, `KEY01` with the date, and AV Access EXB01 pinout did not locate a relevant public schematic or electrical pinout. This is a search result, not proof that none exists. The manufacturer EXB01 announcement documents the accessory, not its electrical connections. Continue from the measured contact mapping in the bench notes.
