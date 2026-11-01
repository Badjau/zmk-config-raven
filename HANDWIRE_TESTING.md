# Left handwire testing notes

Updated: 2026-11-01. Branch: `left-handwire-test`.

## Scope

Test a left 6-column by 4-row ortholinear handwire, eventually paired with the existing Raven right split. Preserve the right split's pin configuration and existing keymap. Do not change shared matrix settings to fix left-only wiring.

The left overlay overrides the shared transform and scan settings. `raven.keymap` selects a blank test keymap under `CONFIG_SHIELD_RAVEN_LEFT`; the original keymap remains in the other branch of that conditional.

## Confirmed reference switch

The physical top-right key, intended as **T**, previously sent **Tab**. Its intended soldered connections (the correct pin labels in wiring.png) are:

- Switch pin 1 connects directly to P0.31.
- Switch pin 2 has the diode; its column wire connects to P1.13.
- The latest hardware test works only with these plugs swapped under col2row. Therefore the intended plugs require the opposite scan direction: drive P1.13 and sense P0.31.

The previous anode/cathode interpretation of wiring.png was incorrect. The config now uses row2col with P1.13 as firmware row 0 (output) and P0.31 as firmware column 0 (input). These firmware labels do not establish physical row/column orientation.

With the GPIO arrays below, this is electrical `RC(0,0)`. Its previous Tab output came from the original transform/keymap placing `RC(0,0)` first. That output did not establish its physical location.

## Current GPIO assignments

`031` means P0.31; `111` means P1.11, **not P0.11**.

| Electrical row index | GPIO |
| --- | --- |
| 0 | P1.13 |
| 1 | P0.29 |
| 2 | P0.02 |
| 3 | P1.15 |

| Electrical column index | GPIO |
| --- | --- |
| 0 | P0.31 |
| 1 | P1.11 |
| 2 | P0.10 |
| 3 | P0.08 |
| 4 | P1.06 |
| 5 | P1.04 |

The left scanner uses `row2col`: rows are active-high outputs, columns are active-high inputs with pull-downs. This matches the confirmed electrical path above. Do not infer diode direction solely from which wire is called a physical row or column.

UART0 is disabled on the left because P0.08 is a matrix column. Soft-off wakeup uses electrical row 1 (P0.29) as output and column 4 (P1.06) as input.

## Physical transform and blank keymap

Three separate mappings are involved:

1. GPIO array indices determine the electrical matrix coordinate.
2. The matrix transform orders those coordinates into physical key positions.
3. Keymap bindings assign actions to those ordered positions.

The current left transform assumes rows run top to bottom and electrical columns run **right to left when viewed from the keycap side**:

```text
RC(0,5) RC(0,4) RC(0,3) RC(0,2) RC(0,1) RC(0,0)
RC(1,5) RC(1,4) RC(1,3) RC(1,2) RC(1,1) RC(1,0)
RC(2,5) RC(2,4) RC(2,3) RC(2,2) RC(2,1) RC(2,0)
RC(3,5) RC(3,4) RC(3,3) RC(3,2) RC(3,1) RC(3,0)
```

The test keymap, in physical order, is:

```text
none none none none none T
none none none none none none
none none none none none none
none none none none none none
```

Only the top-right reference switch is assigned an action. Other switches intentionally produce no output (`&none`).

**Still to verify:** the full column reversal and remaining row order are inferred from the reference switch and the earlier pin list. They have not been established by testing every position. The earlier claim that the column GPIO list ran left to right conflicts with the confirmed top-right switch. Keep the distinction between confirmed wiring and inferred physical order.

Both assigning T directly to the first electrical position and transforming that position to the top-right T binding produce the same output for this single switch. That switch alone cannot validate the entire transform. Before filling the keymap, temporarily assign distinct keys to other positions and verify them from the keycap side.

## Relevant files

- `boards/shields/raven/raven_left.overlay`: left pins, scan polarity, physical transform, wakeup pins.
- `boards/shields/raven/raven.keymap`: conditional left test map and preserved original map.
- `boards/shields/raven/raven_right.overlay`: existing right split; preserve it.
- `boards/shields/raven/raven.dtsi`: shared defaults; left overrides them.
- `wiring.png`: soldered reference switch and diode orientation; use the labels marked correct for intended GPIO connections.

## Build and flash

```powershell
git branch --show-current
git add boards/shields/raven/raven_left.overlay boards/shields/raven/raven.keymap HANDWIRE_TESTING.md
git commit -m "Document and configure left handwire test layout"
git push -u origin left-handwire-test
```

The push triggers the repository's GitHub Actions firmware workflow. Download the successful run's firmware artifact, extract it, and flash only the `raven_left` UF2 to the left controller in bootloader mode. Keep the existing right controller firmware.

Local firmware builds have not been run in this session because `west` is unavailable. Whitespace/diff checks passed for the latest code edits; hardware validation of the latest transform remains outstanding.
