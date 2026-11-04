# PCB and enclosure work

Status: requirements and reference geometry gathered. A routed PCB, manufacturing package and printable enclosure have **not** been created yet. The [revision A circuit](../docs/controller-schematic.md) is still an untested prototype design.

## Confirmed requirements

- Mount the XIAO ESP32-C6 on removable pin headers.
- Use a documented 3.5 mm, four-contact TRRS jack rather than the owner's generic PJ-320D sockets.
- Face the jack opening and the XIAO USB-C port toward the same enclosure wall.
- Provide PCB retention clips in the printed base, accessible ports and a snap-fit lid.
- Preserve the [preferred standalone design](../docs/solutions.md): no remote PCB, added indicator LEDs or manual button.

## References gathered

- [Seeed XIAO ESP32-C6 resources](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/#resources): official board files and footprint downloads.
- [Seeed XIAO footprint archive](https://files.seeedstudio.com/wiki/XIAO-KiCad-Library/New_XIAO_Series_Footprints.zip): includes `XIAO-ESP32-C6-DIP.kicad_mod`. Its header pitch is 2.54 mm and the row spacing is 15.24 mm. Recheck pin orientation and the owner's board revision before layout.
- [Same Sky SJ-43514 product](https://www2.sameskydevices.com/product/interconnect/connectors/audio-connectors/jacks/sj-43514) and [manufacturer drawing](https://jp.sameskydevices.com/product/resource/sj-4351x.pdf): candidate documented right-angle, through-hole, four-contact jack. It is not the SMT variant. The drawing maps pin 1 to black/sleeve, pin 2 to red/tip, pin 3 to white/ring 1 and pin 4 to green/ring 2. This is a candidate, not an implemented footprint.

Downloaded reference archives are cached locally under `hardware/vendor/` and ignored by git. Fetch from the source links when needed; these downloads are not project PCB or enclosure deliverables.

## Next work

Create an editable schematic and routed PCB, with checked component footprints, antenna clearance, mechanical retention areas and test access. Select the actual male/female header pair before fixing the XIAO mounting height and USB-C opening.

Use the PCB and assembled component geometry to create the base and snap-fit lid. Account for plug-body clearance, print orientation, material and printer tolerances; verify fit with a test print. Export editable CAD and printable meshes after geometry checks.

Fusion Electronics supports PCB design and synchronized 3D PCB geometry suitable for enclosure modeling. The Fusion MCP's available capabilities still need verification before choosing an automation workflow. Do not install additional software without the owner's instruction.

The [electrical build plan](../docs/bring-up.md) still applies before connecting an assembled controller to the KVM.
