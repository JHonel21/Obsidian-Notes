---
title: Ender 5 S1 Klipper Tuning Guide
tags: [3d-printing, klipper, prusaslicer, calibration, ender-5-s1]
created: 2026-11-21
---

# Ender 5 S1 Klipper Tuning Guide

> [!info] Setup this guide is written for
> - Creality Ender 5 S1 running Klipper, Mainsail, Moonraker, KAMP, CR Touch
> - Hardened steel 0.4 mm nozzle, acrylic enclosure **printing with the lid off**
> - PrusaSlicer with one print preset and one filament preset per filament
> - Pressure advance tuned with the **Ellis pattern method**
> - Current filament: Polymaker PolyLite PLA Pro (blue). Previous calibration was on Hobby Lobby rainbow PLA

## Contents

- [[#0. Ground rules]]
- [[#1. Order of operations]]
- [[#Part A. Machine calibration]]
- [[#Part B. Filament calibration]]
- [[#Part C. Per-print routine]]
- [[#Part D. When to redo what]]
- [[#Part E. Troubleshooting]]
- [[#Part F. Reference]]
- [[#Part G. Filament log template]]

---

## 0. Ground rules

1. **Change one thing at a time** and write the result down. Two changes at once means you can't tell which one helped.
2. **Calibrate in the exact configuration you print in.** Same lid state (off), same side and front panels, same build sheet, same part fan behavior. PID, mesh and shaper results all depend on this.
3. **Never hand-edit the `SAVE_CONFIG` block** at the bottom of printer.cfg. Never uncomment the old `pid_kp` lines in `[extruder]` or `[heater_bed]`. `SAVE_CONFIG` owns those values.
4. **Back up printer.cfg before every session.** Download it from Mainsail or commit it to Git. When pasting a new printer.cfg, keep the live `SAVE_CONFIG` block from the printer, not the one in the file.
5. **Clean before you calibrate.** Wipe the nozzle, clean the bed with IPA, and dry the filament if it has been sitting out (55 C for 6 hours).
6. **Bad filament ruins every test.** If a test looks wrong in a random way (pops, bubbles, rough surface), dry the spool before blaming settings.

---

## 1. Order of operations

Print this checklist for a full recalibration. Part A is machine-level and only needs repeating when hardware changes. Part B is repeated per filament.

### Part A (machine)

- [ ] A1. Mechanical baseline
- [ ] A2. Extruder rotation_distance
- [ ] A3. PID tuning (extruder, then bed)
- [ ] A4. Probe Z offset
- [ ] A5. Bed screws and bed mesh
- [ ] A6. Axis twist compensation
- [ ] A7. Input shaper and max accel

### Part B (per filament)

- [ ] B0. Dry, load, create presets with baseline values
- [ ] B1. Temperature tower
- [ ] B2. First layer check
- [ ] B3. Max volumetric speed
- [ ] B4. Extrusion multiplier
- [ ] B5. Pressure advance (Ellis pattern)
- [ ] B6. Retraction
- [ ] B7. Cooling, overhangs, bridging
- [ ] B8. Dimensional accuracy
- [ ] B9. Record results and lock the preset

> [!tip] If Ellis' guide orders something differently
> Follow Ellis where it differs. Two rules always hold: temperature goes first, and retraction goes last (retraction results depend on temperature, flow and pressure advance being right).

---

# Part A. Machine calibration

## A1. Mechanical baseline

Do this before any software tuning. Software cannot fix a loose machine.

- [ ] Eccentric nuts on the X gantry and bed: no wobble, but wheels still turn freely
- [ ] Belts: tight and equal, no fraying
- [ ] Frame square and all frame screws snug (the Ender 5 S1 frame can loosen over time)
- [ ] Z lead screw clean and lightly lubricated, Z rods straight
- [ ] Nozzle tightened **hot** (at 215 C) so it seals against the heatbreak
- [ ] Extruder gear clean of filament dust, tension arm set so filament doesn't slip or get crushed
- [ ] CR Touch pin moves freely and the mount is tight
- [ ] Build sheet clean and flat

## A2. Extruder rotation_distance

Current value in printer.cfg: `rotation_distance: 7.564500`. Recheck if you change the extruder, gear or nozzle setup.

1. Heat the nozzle to 215 C.
2. Mark the filament 70 mm from the extruder body entrance.
3. Run in the Mainsail console:
   ```
   G91
   G1 E50 F60
   G90
   ```
4. Measure from the mark to the extruder body. Actual extruded = 70 minus the new distance.
5. Calculate:
   ```
   new_rotation_distance = old_rotation_distance x (actual_extruded / 50)
   ```
6. Edit `rotation_distance` in `[extruder]`, restart, repeat once to confirm.

> [!note]
> This sets the baseline. The per-filament extrusion multiplier in step B4 handles filament-to-filament differences, so don't chase small variations here.

## A3. PID tuning

Do the extruder and bed separately, one after the other. Don't heat both at once.

> [!warning] Tune in your real print configuration
> Lid **off**, side and front panels exactly as you print, build sheet installed, printer in its normal spot with no new drafts. PID values tuned in a different thermal environment will not hold. If you ever start printing with the lid on, redo this section with the lid on.

### Prep

- [ ] Printer cold and sitting at room temperature
- [ ] Nozzle clean, filament loaded or not (doesn't matter)
- [ ] Mainsail temperature graph visible so you can watch it

### Extruder PID (target 215 C)

215 C is the PolyLite PLA Pro working temperature. One tune at 215 holds fine for roughly 200 to 235 C. Only retune at a different target if you start printing something very different (PETG at 240 for example).

1. Turn the part cooling fan on at a typical print speed, because that's the disturbance the controller fights during a print:
   ```
   M106 S128
   ```
   Use `S255` instead if your typical average fan is higher.
2. Run:
   ```
   PID_CALIBRATE HEATER=extruder TARGET=215
   ```
3. Wait about 3 to 6 minutes for it to finish. The console prints `pid_Kp`, `pid_Ki`, `pid_Kd`.
4. Save and restart:
   ```
   M107
   SAVE_CONFIG
   ```

### Bed PID (target 55 C)

55 C is the running bed temperature. The first layer at 60 C is a short transition and doesn't need its own tune.

1. Nozzle cold, part fan off, build sheet installed.
2. Run:
   ```
   PID_CALIBRATE HEATER=heater_bed TARGET=55
   ```
3. Wait about 5 to 10 minutes. Save:
   ```
   SAVE_CONFIG
   ```

### Verify

1. Heat the bed to 55 C and the nozzle to 215 C. Watch the graph.
2. **Good:** overshoot of about 1 to 2 C or less, then a flat line within about plus or minus 0.5 C.
3. **Bad:** long oscillation or a sustained drift. Rerun the tune, and check for drafts, a loose heater or thermistor, or a cooling fan blowing on the heater block.
4. Leave the old `#pid_kp` lines commented out. Do not uncomment them.

## A4. Probe Z offset

The saved value is under `[bltouch]` in the `SAVE_CONFIG` block (currently `z_offset = 1.764`). Redo after any nozzle, hotend or probe change.

1. Heat the bed to 55 C and the nozzle to about 150 C. Nozzle clean.
2. Home all axes:
   ```
   G28
   ```
3. Start the paper test:
   ```
   PROBE_CALIBRATE
   ```
4. Move the nozzle down with `TESTZ Z=-0.1`, then `TESTZ Z=-0.025` and so on until the paper drags slightly. Use `TESTZ Z=+0.025` to back off.
5. Accept and save:
   ```
   ACCEPT
   SAVE_CONFIG
   ```

Fine tuning happens in [[#B2. First layer check]] with live baby steps.

## A5. Bed screws and bed mesh

### Screws

Use the screws tilt macro to get the bed mechanically level so the mesh has less to compensate.

1. Heat the bed to 55 C and home.
2. Run `G32` (calls `SCREWS_TILT_CALCULATE`).
3. Adjust each screw by the reported turn (format `hh:mm`, minutes of a full turn, CW means tighten).
4. Repeat until every screw reads within a few minutes of the base screw.

### Mesh

The new `PRINT_START` macro soaks the bed, homes, and runs `BED_MESH_CALIBRATE` before every print. You no longer run a mesh manually.

- With **Label objects** set to Firmware-specific in PrusaSlicer, KAMP meshes only the print area.
- With labels off, KAMP falls back to a full 9x9 mesh. That works but is slow with 4 samples per point.
- Your old saved mesh (before this change) had a range of about 0.2 mm with a left to right trend. A trend like that is what [[#A6. Axis twist compensation]] can help with.

Sanity check after the first mesh with the new macro: run `BED_MESH_OUTPUT` and look at the range. A range above roughly 0.3 to 0.4 mm means check the screws and the frame, not just the mesh.

## A6. Axis twist compensation

Your printer.cfg has the `[axis_twist_compensation]` section but there are no saved values in `SAVE_CONFIG`, so it has not been calibrated. The Ender 5 S1 is known for a slight X axis twist.

1. Heat the bed to 55 C, nozzle about 150 C and clean.
2. Home, then run:
   ```
   AXIS_TWIST_COMPENSATION_CALIBRATE
   ```
3. Do the paper test at each point it moves to (same procedure as A4), using `TESTZ` and `ACCEPT`.
4. `SAVE_CONFIG`.
5. Rerun the mesh and compare the range to before.

## A7. Input shaper and max accel

The saved shaper values are MZV at 63.2 Hz (X) and 47.4 Hz (Y). Redo this if the enclosure panels went on after the last test, if you changed belt tension, if you moved the printer to a different surface, or if you changed toolhead weight.

> [!warning] Do it in the print configuration
> Panels on or off exactly as you print. Resonances shift with the enclosure.

1. Follow Klipper's "Measuring Resonances" docs for ADXL345 wiring and the Pi SPI setup.
2. In printer.cfg, uncomment the `[mcu rpi]`, `[adxl345]` and `[resonance_tester]` blocks, then `RESTART`.
3. Verify the accelerometer:
   ```
   ACCELEROMETER_QUERY
   MEASURE_AXES_NOISE
   ```
4. Run the calibration:
   ```
   SHAPER_CALIBRATE
   ```
5. Read the recommended shaper per axis and its **recommended max accel**. Prefer shapers that give low vibration with low smoothing. MZV is often the best compromise.
6. `SAVE_CONFIG`.
7. Set `max_accel` in `[printer]` at or below the lower of the two recommended values. Your current comment says the previous results were 6400 and 6600, and `max_accel` is 6500.
8. Re-comment the ADXL blocks, remove the accelerometer from the toolhead, and `RESTART`.

---

# Part B. Filament calibration

Everything in Part B is done with the real print preset, so the results transfer to real prints. Do not test with a draft profile.

## B0. Prep and baseline profile

1. **Dry** the spool if it has been out. 55 C for 6 hours.
2. **Load** the filament and purge until the color is clean.
3. In PrusaSlicer, **clone** the filament preset and name it for the real filament, for example `PolyLite PLA Pro Blue`. Do not reuse a silk preset for a non-silk spool.
4. Clone the print preset to match.
5. Enter the baseline values below. They are starting points that Part B refines.

### Baseline: filament preset (PolyLite PLA Pro)

| Setting | Start value | Notes |
|---|---|---|
| Nozzle temp | 215 C, first layer 220 C | Label range 190 to 220 C. Hardened steel runs a bit cooler than brass at the tip, so sit in the upper half. Refined in B1 |
| Bed temp | 55 C, first layer 60 C | Label range 30 to 60 C |
| Min / max fan | 100 / 100 | Back off to 70 to 80 only if layer adhesion is weak |
| Bridge fan | 100 | |
| Disable fan first layers | 2 | Full fan by layer 4 |
| Max volumetric speed | 12 mm3/s | Refined in B3 |
| Extrusion multiplier | 0.98 | Refined in B4 |
| Retraction length | 1.0 mm | Set explicitly in the **filament** preset |
| Retraction speed | 30 mm/s | Direct drive spec for this filament |
| Retraction lift | 0.1 mm (optional) | |
| Slowdown below layer time | 8 to 10 s | Min print speed 10 to 15 mm/s |
| Filament start G-code | `SET_PRESSURE_ADVANCE ADVANCE=0.0XX` | Value from B5 |

> [!warning] Set the retraction fields in the filament preset
> Your printer preset has `retract_length = 2`. If the filament preset leaves retraction fields blank (nil), the printer's 2 mm applies. Fill the filament values in so they override it.

### Baseline: print preset

| Setting                      | Value                                                                                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Layer height / first layer   | 0.2 mm / 0.25 mm                                                                                                                           |
| Extrusion widths (mm, not %) | default 0.45, first layer 0.5, perimeters 0.45, external 0.45, infill 0.45, solid 0.45, top 0.4, support 0.4                               |
| Walls                        | 3 (4 for functional parts)                                                                                                                 |
| Top / bottom layers          | 5 / 4                                                                                                                                      |
| Infill                       | 25 to 30 percent, cubic or gyroid                                                                                                          |
| Speeds (mm/s)                | first layer 25, perimeters 60, external 40, small perimeters 30, infill 60 (70 max), solid 30, top 30, bridges 60, gap fill 20, travel 250 |
| Accelerations (mm/s2)        | first layer 800, default 3000, external 2000, perimeters 3500, infill 5500, solid 3500, top solid 2500, bridge 2000, travel 6000           |
| Seam                         | Aligned or rear (matte filament hides seams well)                                                                                          |
| Thick bridges                | On                                                                                                                                         |
| Label objects                | **Firmware-specific** (required for KAMP adaptive mesh and exclude object)                                                                 |
| Arc fitting                  | Optional. Test on a small print first. `[gcode_arcs]` is already in printer.cfg                                                            |

> [!note] Why accels are listed
> If every acceleration field is 0, PrusaSlicer emits nothing and Klipper runs everything at `max_accel` (6500), including outer walls and the first layer. Always set them.

### Baseline: printer preset

Start G-code (one line):

```
PRINT_START BED=[first_layer_bed_temperature] EXTRUDER=[first_layer_temperature] SOAK=180
```

End G-code:

```
PRINT_END
```

> [!warning] Turn off automatic temperature commands
> PrusaSlicer only skips its own heating commands if it finds literal `M104`, `M109`, `M140` or `M190` lines in the start G-code. A line like `PRINT_START ...` doesn't count, so PrusaSlicer adds `M190` and `M104` (first layer temp) **before** your macro and `M109` after it. That heats the nozzle to full temp before the soak and mesh, which causes ooze.
> In PrusaSlicer, switch to Expert mode and go to **Printer Settings > Custom G-code**, then uncheck **Emit temperature commands automatically**. The macro already handles all heating. `PRINT_START` also starts with `M104 S0` as a backstop in case a slicer or test generator still preheats.

### Pre-flight checks

- [ ] Both presets are actually selected (the active presets in an exported bundle can be stale)
- [ ] Extrusion widths are in mm and not percent
- [ ] Label objects is Firmware-specific
- [ ] **Emit temperature commands automatically** is unchecked (Printer Settings > Custom G-code, Expert mode)
- [ ] Filament start G-code contains a `SET_PRESSURE_ADVANCE` line (placeholder value for now)

## B1. Temperature tower

Goal: find the temperature with the best balance of layer adhesion, bridging, stringing and surface.

> [!note] PrusaSlicer has no built-in calibration menu
> That feature exists in OrcaSlicer and SuperSlicer, not PrusaSlicer. For PrusaSlicer you use a test model and the layer slider to insert temperature changes.

1. Download a temperature tower STL with equal-height blocks (search Printables for "temperature tower" and pick one with 5 to 7 blocks, each with an overhang and a bridge, or use the one Ellis' guide points to).
2. Slice with the real print preset and filament preset. For this test only, set the filament nozzle temp and first layer temp to **225** (the hottest block).
3. In the layer preview, find the first layer of the second block with the vertical slider. Right-click the **+** icon on the slider and choose **Add custom G-code**. Enter `M104 S220`. Repeat at the start of each following block with `M104 S215`, `M104 S210`, down to `M104 S195`.
4. Slice and send the G-code, then set the filament temps back to your baseline so the test values don't stick. Print the tower.
5. Evaluate each block:
   - **Layer adhesion:** try to snap or twist each block. Weak blocks mean too cold.
   - **Bridging and overhangs:** better when cooler.
   - **Stringing and blobs:** worse when hotter.
   - **Surface:** shine and consistency.
6. Pick the lowest temperature that still gives strong layer adhesion. Set that as the nozzle temp and set the first layer temp 5 C higher.
7. If you end up above 220 C, note it in the log. That's outside the label window but fine for this filament if the prints are clean.

## B2. First layer check

1. Slice a single flat layer: a 150 x 150 mm square at the first layer height with solid infill only, or use the first layer pattern from Ellis' guide.
2. Print it and watch the first layer live. Use baby steps in Mainsail or:
   ```
   SET_GCODE_OFFSET Z_ADJUST=-0.02 MOVE=1
   ```
   (use plus to raise the nozzle, minus to lower it)
3. **Target:** lines fused into a smooth sheet, no gaps between lines, no ridges, no rough or transparent patches.
   - Gaps or ropey lines: too high, lower in 0.01 to 0.02 steps
   - Ridges, rough surface, elephant foot: too low, raise
4. When it looks right, apply and save the offset:
   ```
   Z_OFFSET_APPLY_PROBE
   SAVE_CONFIG
   ```
5. Print a second time to confirm, especially at the corners and the center.

> [!tip] If the first layer is great in the middle and bad at the edges
> That's a mesh issue, not offset. Check the mesh range, the bed screws and A6 (axis twist).

## B3. Max volumetric speed

Goal: set a safe ceiling so the slicer never asks for more flow than the hotend can melt.

PrusaSlicer has no built-in max flow test, so use an extrusion-in-air test from the console. Flow in mm3/s equals `F / 60 x 2.405`, where 2.405 mm2 is the cross-section of 1.75 mm filament.

1. Heat the nozzle to your chosen temp (for example 215 C), home, and lift the nozzle clear of the bed:
   ```
   G28
   G1 Z50 F600
   M83
   ```
2. Extrude 50 mm at each of these feedrates, one at a time, and watch and listen:

   | Command | Flow |
   |---|---|
   | `G1 E50 F240` | 9.6 mm3/s |
   | `G1 E50 F300` | 12.0 mm3/s |
   | `G1 E50 F360` | 14.4 mm3/s |
   | `G1 E50 F420` | 16.8 mm3/s |
   | `G1 E50 F480` | 19.2 mm3/s |

   If Klipper complains the extrude-only move is too long, use `E40`.
3. **Pass:** a continuous strand with no clicking, grinding, slowing or visible skipping. **Fail:** clicking, a thin or stuttering strand, or the extruder gear slipping.
4. Take the highest flow that passed. Extrusion in air overstates what you get in a real print, because a real print has back pressure from the bed and the previous layer. Set `filament_max_volumetric_speed` about **20 to 25 percent below** the air limit.
5. Expect a final cap around 10 to 14 mm3/s for this filament. If the air limit is under about 12 mm3/s, raise the nozzle temp 5 C and retest, or check for a partial clog or heat creep.
6. Restore absolute extrusion afterward with `M82` (the next `PRINT_START` does this anyway).
7. Optional confirmation print: a vase-mode cylinder at your capped flow. Speed in mm/s equals flow / (0.45 x 0.2), so 12 mm3/s is about 133 mm/s. The walls should look consistent from bottom to top with no thin or gappy sections.

> [!note]
> Your speeds at 0.2 mm layers and 0.45 mm widths only demand about 5 to 8 mm3/s, so this cap mostly protects you if you raise speeds, thicken layers or widen lines later.

## B4. Extrusion multiplier

Use the Ellis single-wall method since you already follow his guide, then confirm with a top surface check.

### Ellis single-wall cube

1. Slice a cube with **vase mode** or a single perimeter, no top, no bottom, 0.2 mm layers, extrusion width 0.45. Real temps and speeds.
2. Print it.
3. Measure the wall thickness with calipers at several points on each of the four walls (avoid the seam and corners). Average them.
4. Calculate:
   ```
   new_EM = old_EM x (target_width / measured_width)
   ```
   Target width is the width you set (0.45).
5. Enter the new EM in the filament preset and reprint to confirm. Expect a value between 0.95 and 1.00.

### Top surface check

1. Print a flat slab about 40 x 40 x 3 mm with 100 percent infill and 5 top layers, using your real print preset.
2. Look at the top surface. **Gaps or visible line separation** mean under-extrusion. **Ridges, blobs or a rough, raised look** mean over-extrusion.
3. If it disagrees with the caliper result by a visible amount, adjust EM by 0.01 and reprint the slab. Also check for wet filament or filament diameter that varies along the spool.

## B5. Pressure advance (Ellis pattern method)

Pressure advance (PA) depends on temperature, line width, layer height, speed and acceleration. Run it **after** B1 to B4 are settled and with the same settings you'll print with.

1. Open Ellis' pressure advance pattern generator.
2. Set the inputs to match the real profile:
   - Nozzle 0.4, bed 220 x 220, G-code flavor Klipper
   - Nozzle temp and bed temp from B1 (for example 215 and 55)
   - Layer height 0.2, line width 0.45, fan as in the filament preset
   - Slow speed around 20 mm/s, fast speed around your fastest wall speed (60 mm/s)
   - Acceleration equal to the accel you use on perimeters
   - PA start 0, end 0.1, step 0.005 (direct drive range)
   - Follow the tool's own tooltips if they differ from these starting points
   - **Start and end G-code:** include them. If the tool has custom start and end G-code fields, put `PRINT_START BED=55 EXTRUDER=215 SOAK=180` in the start field and `PRINT_END` in the end field, using literal numbers because the tool doesn't know PrusaSlicer placeholders. Without them the pattern prints with no mesh, no purge and no parking.
   - After generating, open the G-code and read the first 20 lines. Make sure the tool didn't add its own heat or home commands with different temps before your start block.
3. Generate, slice and print. The G-code sets PA itself for each pattern line.
4. Read the pattern. The best value gives **sharp, even corners with no bulge and no gap**, and the same line width in the slow and fast sections.
   - Corners **bulge** or the line gets thick after the corner: PA too low
   - **Gaps** at corners or thin lines at speed: PA too high
5. Run a finer pass around your pick if you want (for example plus or minus 0.01 in steps of 0.002).
6. Put the final value in the **filament start G-code**:
   ```
   SET_PRESSURE_ADVANCE ADVANCE=0.0XX
   ```
7. Leave `pressure_advance: 0.065` in `[extruder]` as the fallback default. Klipper falls back to it on every restart, so every filament sets its own value at print start.
8. Check the active value any time with:
   ```
   SET_PRESSURE_ADVANCE
   ```

## B6. Retraction

Goal: the **smallest** retraction that removes stringing. More than needed causes clogs and blobs.

1. Use a stringing test (two thin towers some distance apart works well) and follow Ellis' guide for the Klipper method. Without a built-in tool in PrusaSlicer, the simple version is to print the same test three times with retraction length set to values that bracket your current one, for example 0.6, 1.0 and 1.4 mm (or 1.2, 1.6 and 2.0 mm if 1.0 mm already strings), changing only that between prints. Speed stays at 30 mm/s. Ellis' guide also covers a faster tower method, which needs Klipper firmware retraction enabled in printer.cfg and in the PrusaSlicer printer preset. Follow his steps for that if you want it, and remove both settings afterward.
2. Find the lowest retraction that is clean and set that length in the filament preset. Refine in steps of 0.2 mm if needed.
3. If stringing is still present at about 1.5 mm:
   - Dry the filament first
   - Lower the nozzle temp 5 C and retest
   - Check travel speed and that `retract_before_travel` isn't suppressing short retractions
4. Keep speed at 30 mm/s. Add a 0.1 mm lift only if you see nozzle drag on travel.

## B7. Cooling, overhangs, bridging

1. Print an overhang and bridge test (or use the overhang sections of the temp tower).
2. With 100 percent fan, check 45, 60 and 70 degree overhangs and a 40 to 60 mm bridge.
3. If layer adhesion is poor on tall thin walls or small parts, drop min fan to 70 to 80 percent.
4. For small parts, check that **slowdown below layer time** gives the layer time to cool without turning the part into a blob.
5. Bridges that sag: lower the bridge speed in 10 mm/s steps or bridge flow if available. Bridges that look rough: raise it.

## B8. Dimensional accuracy

1. Print a 20 mm calibration cube and a part with holes or slots you care about.
2. Measure X, Y and Z with calipers.
3. If all XY dimensions are consistently off by the same amount, adjust `xy_size_compensation` in the print preset (negative for oversize). Do **not** use the extrusion multiplier to fix dimensions.
4. If Z is off, check the Z `rotation_distance` and first layer height before blaming slicer settings.

## B9. Record results and lock the preset

1. Fill in the log in [[#Part G. Filament log template]].
2. Save the PrusaSlicer presets and export the bundle for backup.
3. Note the spool brand, color and date so a future color of the same brand can start from these numbers.

---

# Part C. Per-print routine

### Before

- [ ] Correct filament **and** print presets selected
- [ ] Filament start G-code has the right PA for this spool
- [ ] Spool dry and feeding freely
- [ ] Nozzle wiped, bed cleaned with IPA
- [ ] Label objects on, `PRINT_START` and `PRINT_END` lines in the printer preset
- [ ] Lid off and panels in your normal state

### During

- [ ] Watch the **first layer** and adjust Z with baby steps if needed. If you change it, apply and save with `Z_OFFSET_APPLY_PROBE` and `SAVE_CONFIG` after the print
- [ ] Glance at the temperature graph in the first few minutes

### After

- [ ] Inspect the first layer side, a corner and the top surface
- [ ] Note any issue in the filament log
- [ ] Don't pull the part off a hot bed

---

# Part D. When to redo what

| Event | Redo |
|---|---|
| New filament brand or type | All of Part B |
| Same brand and type, different color | B1 quick check, B4, B5. Colors can behave differently |
| New spool, same color | B2 first layer check and a short PA sanity print |
| New nozzle (size or material) | A2 not needed. A3 extruder PID, A4, then all of Part B |
| Hotend, heater or thermistor change | A3, A4, B1, B3, B5 |
| Enclosure panels added, removed or relocated | A3 (bed and extruder PID), A7, mesh check |
| Belts retensioned or toolhead weight changed | A7 |
| Printer moved or leveled again | A5, A4 check |
| Klipper or Moonraker major update | Quick test print, check macros still behave |
| Changing line width or layer height significantly | B3, B4, B5 |
| Changing max accel | B5 (PA) and check for ringing |
| Seasonal ambient temperature shift | Check A3 verify step and first layer |

---

# Part E. Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| First layer gaps or ropey lines | Nozzle too high, mesh off | Lower Z offset (B2), remesh, check screws |
| First layer too squished or rough | Nozzle too low | Raise Z offset |
| Good first layer in the center, bad at edges | Bed mesh or twist | Check mesh range, A5, A6 |
| Stringing | Retraction too low, temp too high, wet filament | B6, lower temp 5 C, dry |
| Blobs and zits at seams | PA wrong or retraction too aggressive | B5, B6, adjust seam position |
| Bulging corners | PA too low | Increase PA |
| Gaps or thin lines after corners | PA too high | Decrease PA |
| Ringing or ghosting | Accel too high, shaper stale | A7, lower perimeter accel |
| Under-extrusion at speed | Over max flow, temp too low, partial clog | B3, raise temp 5 C, check nozzle |
| Extruder clicking | Over flow limit, heat creep, clog | Lower max volumetric speed, check heatbreak fan |
| Heat creep (clicking mid-print on long prints) | Hotend heat soaking | Set heatbreak `fan_speed` from 0.8 to 1.0, check airflow |
| Delamination or weak layers | Too cold, too much fan | Raise temp, drop min fan to 70 to 80 percent |
| Rough or pitted surface, popping | Wet filament | Dry 55 C for 6 hours |
| Temperature unstable | Old PID or draft | A3 in the real configuration |
| Warping at corners | Bed too cool, drafts | Bed to 60 C, clean bed, avoid drafts, brim |
| Dimensions off uniformly | Slicer compensation | B8 |

---

# Part F. Reference

## Command cheat sheet

| Task | Command |
|---|---|
| Home | `G28` |
| Bed screws | `G32` or `SCREWS_TILT_CALCULATE` |
| Bed mesh | `BED_MESH_CALIBRATE` (KAMP adaptive) |
| Mesh output | `BED_MESH_OUTPUT` |
| Probe Z offset | `PROBE_CALIBRATE`, `TESTZ Z=-0.025`, `ACCEPT`, `SAVE_CONFIG` |
| Live Z offset | `SET_GCODE_OFFSET Z_ADJUST=-0.02 MOVE=1` |
| Save live Z offset to config | `Z_OFFSET_APPLY_PROBE` then `SAVE_CONFIG` |
| Extruder PID | `PID_CALIBRATE HEATER=extruder TARGET=215` |
| Bed PID | `PID_CALIBRATE HEATER=heater_bed TARGET=55` |
| Axis twist | `AXIS_TWIST_COMPENSATION_CALIBRATE` |
| Accelerometer check | `ACCELEROMETER_QUERY`, `MEASURE_AXES_NOISE` |
| Input shaper | `SHAPER_CALIBRATE` |
| Set PA | `SET_PRESSURE_ADVANCE ADVANCE=0.0XX` |
| Check PA | `SET_PRESSURE_ADVANCE` |
| Part fan 50 percent / off | `M106 S128` / `M107` |

## Macro parameters

`PRINT_START` accepts:

| Parameter | Default | Meaning |
|---|---|---|
| `BED` | 60 | Bed temp |
| `EXTRUDER` | 220 | Nozzle temp |
| `SOAK` | 180 | Bed soak seconds with the nozzle cold, before homing and meshing |

Sequence: part fan off, cancel any slicer preheat, bed to temp, soak, home, nozzle preheat to 150 C, bed mesh, move to purge start, nozzle to full temp, purge lines, lift. `PRINT_END` retracts, lifts Z (capped at the Z maximum), parks, turns heaters off, fan off, steppers off.

## Filament start G-code (per filament preset)

```
SET_PRESSURE_ADVANCE ADVANCE=0.0XX
```

## Where things live

- **Klipper config:** printer.cfg (the `SAVE_CONFIG` block at the bottom is owned by Klipper)
- **PrusaSlicer presets:** one filament preset and one print preset per filament
- **KAMP:** `KAMP/Adaptive_Meshing.cfg` and `KAMP/KAMP_Settings.cfg`
- **Backups:** download printer.cfg from Mainsail before sessions and export the PrusaSlicer bundle after

## Filament notes

### Polymaker PolyLite PLA Pro (old formula, label values)

- Nozzle 190 to 220 C, bed 30 to 60 C, speed 30 to 70 mm/s, fan on
- Direct drive retraction 1 mm at 30 mm/s
- Dry at 55 C for 6 hours only if it has absorbed moisture
- Polymaker has said PolyLite PLA Pro is being replaced by a newer Polymaker PLA Pro with different settings (about 210 to 230 C and a much higher speed rating). Do **not** reuse this profile for the new formula. Run Part B again

---

# Part G. Filament log template

Copy this block for each filament and fill it in as you go.

```
Filament: ______________________   Brand/type: ______________
Color: ____________   Spool date: ___________   Dried? Y / N

B1 Temperature     nozzle: ____  first layer: ____  bed: ____ / ____
B2 First layer     Z offset (probe z_offset): ________
B3 Max vol speed   result: ____ mm3/s   set to: ____ mm3/s
B4 Extrusion mult  old: ____  new: ____  method: Ellis wall test
B5 Pressure adv    value: ______   pattern settings: ________________
B6 Retraction      length: ____ mm   speed: ____ mm/s
B7 Fan             min: ____   max: ____   bridge: ____
B8 Dimensions      cube X: ____ Y: ____ Z: ____   xy comp: ____

Print preset used: ______________   Filament preset: ______________
Notes: ______________________________________________________________
```

## PolyLite PLA Pro Blue (fill in)

| Item | Value |
|---|---|
| Nozzle / first layer | |
| Bed / first layer | |
| Fan min / max | |
| Max volumetric speed | |
| Extrusion multiplier | |
| Pressure advance | |
| Retraction length / speed | |
| Spool date | |
| Notes | |
