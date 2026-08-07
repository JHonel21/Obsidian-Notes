After running BED_MESH_CALIBRATE, check the range in Mainsail's heightmap or console output:

Acceptable: 0.2-0.3mm total variance Good: 0.1-0.2mm total variance

Exceptional: <0.1mm total variance

For Ender 5 S1 specifically:

- These printers typically have 0.15-0.25mm variance stock
- Under 0.2mm is good enough for great prints
- The mesh compensates automatically, so small variances don't matter much

What the numbers mean: If your mesh shows a range of 0.18mm (like low point -0.09mm, high point +0.09mm), that's totally normal and fine.

Diminishing returns:

- Getting screw tilt under 00:10 → worth it
- Chasing 00:00 on every screw → not worth the time
- Bed mesh under 0.2mm → good
- Obsessing over <0.05mm mesh → unnecessary for FDM

Reality check: With a properly leveled bed (good screw tilt) and mesh compensation, variance up to 0.3mm still produces perfect first layers. The mesh handles it.