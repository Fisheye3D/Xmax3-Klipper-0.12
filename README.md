Changes to X-max 3, Screen replacement with BTT HDMI5 running of rasp pi zero 2 w, Dual side fans, Exhaust temp control, Led control, Cart probe, BTT SFS V2, Config cleanup, and improvements.  Use at your own risk, I am not liable for any damage you may incur by using my config or macros. 


### Orca slicer start/end gcode provided or printer_start macros may be used, PLA/ABS macros in the macro folder. 


# Orca slicer Start gcode: 
```
G28 ; home all axes
M104 S160 ; set extruder temperature to 160C
M190 S[first_layer_bed_temperature] ; set bed temperature and wait for it to reach target
M109 S160 ; wait for extruder temperature to reach 160C
BED_MESH_CLEAR ; clear the previous bed mesh
BED_MESH_CALIBRATE mesh_min={adaptive_bed_mesh_min[0]},{adaptive_bed_mesh_min[1]} mesh_max={adaptive_bed_mesh_max[0]},{adaptive_bed_mesh_max[1]} ALGORITHM=[bed_mesh_algo] PROBE_COUNT={bed_mesh_probe_count[0]},{bed_mesh_probe_count[1]} ADAPTIVE=1 ADAPTIVE_MARGIN=0
G0 X0 Y0 Z50 F6000 ; move the nozzle to just above the bed
M109 S[first_layer_temperature] ; wait for extruder temperature to reach target
SET_TEMPERATURE_FAN_TARGET temperature_fan="chamber" target=35 ; set chamber exhaust fan target temperature to 35C
M83 ; set extruder to relative mode
G0 Z5 F1200 ; lower the nozzle to just above the bed
G0 X{first_layer_print_min[0]} Y{max(0, first_layer_print_min[1] - 2)} F12000 ; move the nozzle to the start of the print
G0 Z0.2 F600 ; lower the nozzle to the start height
G1 E3 F1800 ; prime the nozzle
G0 Z0.3 F600 ; raise the nozzle slightly to avoid scratching the bed
G1 X{min(first_layer_print_min[0] + 30,print_bed_max[0])} E6 F600 ; draw a purge line

SFS_on
```

# Orca slicer End gcode:
```
SFS_off

M104 S0 ; turn off extruder
M140 S0 ; turn off bed
M141 S0 ; turn off chamber heater
G1 E-3 F1800 ; retract the filament a bit
G91 ; set to relative positioning
G0 Z5 F600 ; move Z up a bit
G90 ; set to absolute positioning
G0 X0 Y0 F12000 ; move X/Y to min endstops, so the head is out of the way
{if max_layer_z < max_print_height - 30}G1 Z{max_layer_z + 30} F600{else}G1 Z{max_print_height} F600{endif} ; move Z to a safe height
M84 ; disable motors
M107 ; turn off fan

```

