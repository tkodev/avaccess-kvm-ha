# Agent instructions

## Project direction

Integrate the AV Access iDock B10 CTL port with Home Assistant using a Seeed XIAO ESP32-C6 and ESPHome.

The owner prefers a new minimal board or the XIAO with necessary interface components. It must handle control and electrical selection sensing without a pass-through, original remote PCB, indicator LEDs or manual buttons. Minimize total components while meeting electrical requirements. Remote-PCB reuse and optical sensing are alternatives, not the preferred final design. Keep detailed options and decisions in [solutions](docs/solutions.md).

## Read the owning documents

- [README](README.md): project overview and current status.
- [Solutions](docs/solutions.md): preferred design and alternatives.
- [Schematic](docs/schematic.md): reconstructed remote circuit and evidence limits.
- [Controller schematic](docs/controller-schematic.md): proposed interface, BOM, pin assignments and unverified conditions. Keep it separate from the measured remote schematic.
- [Readings](docs/readings.md) and [worksheet](docs/measurements.csv): actual measurements, corrections and calculated values.
- [Interface protection](docs/interface-protection.md) and [build plan](docs/bring-up.md): remaining design checks and staged validation.
- [Bench notes](docs/bench-notes.md): chronological history, including superseded hypotheses. Use its current handoff and the owning documents for present conclusions.

Keep project facts in these files rather than persistent agent memory. Update the owning document when evidence or decisions change; avoid duplicating the full measurement history here.

## Electrical work

- Always pair wire colours with plug contacts: **red/tip, white/ring 1, green/ring 2, black/sleeve**. Never assume black/sleeve is ground. Do not invent J1 cavity numbering from TRRS order.
- Distinguish measured facts, calculations, inferences, proposed circuits and validated hardware. Matching the remote schematic does not validate an ESP32 interface.
- The observed button is active-high. GPIO-LOW button emulation from another project is not applicable without a different interface. Preserve the approximately 100 Ω press-path function when reproducing the contact closure; do not duplicate R2 across KEY1 on a retained remote PCB.
- Do not treat normal 3.3 V levels as proof of GPIO compatibility. Check both power-off directions, startup/reset, current/transients, status loading and ground compatibility. Do not tie ESP32 3V3 to black/sleeve or assume the CTL port can power the XIAO.
- Treat removal of the original LED branches or C1 as a design change requiring validation. Their presence in the measured remote does not make them mandatory final components.
- The owner has one expensive KVM. Validate the proposed interface disconnected from it first. Never propose shorting or overloading it to discover limits. Follow the build plan for powered measurements and first connection.
- Check technical claims and selected parts against primary manufacturer documentation. Record exact parts, ratings, assumptions and unresolved limits before presenting wiring as ready to build. Do not promise zero risk or claim hardware tests the agent did not perform.

## Evidence and changes

Preserve raw readings, units, probe polarity, connection/load state, button state and selected LED state when available. Mark missing context as unknown. Retain corrections and excluded readings with their reason. Map computer names only when the owner provides that mapping.

Preserve the frozen round-1 review inputs, reports and checkpoint hashes under `docs/verification/round-1/`. Put later evidence in a separate addendum, then update current documents. Do not rewrite the blind review to include later knowledge.

Use concise prose. Load the owner's git, writing and figma skills before implementing code when available. Keep credentials and machine-local state out of git. Preserve unrelated working changes. Commit, push or create a PR only when requested; use Conventional Commits. Return the PR URL whenever work changes a PR.

For documentation edits, check local links and `git diff --check`. For hardware or firmware changes, use checks appropriate to the actual design and report what was tested and what remains unverified.
