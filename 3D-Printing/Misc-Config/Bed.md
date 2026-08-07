- Manual Screw Leveling:  
    BED_SCREWS_ADJUST

- Printer will move to each corner.
- Adjust knobs so paper slightly resists between nozzle and bed.

- Z-Offset Setup:

- Use PROBE_CALIBRATE if you have a probe (e.g., CR Touch).
- Manually jog the nozzle and save the offset:  
      
    SAVE_CONFIG

- Bed Mesh Leveling (for warped beds):  
      
    BED_MESH_CALIBRATE  
    SAVE_CONFIG

- Run after screw leveling and Z-offset are set.
- Use mesh in slicer start G-code or enable in printer.cfg.