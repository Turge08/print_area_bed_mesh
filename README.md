# Print Area Bed Mesh

## Description

- This macro will create a bed_mesh based on the size of your print.

## Credit

Complete credit goes to ChipCE (https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02) for the idea and sample code. I completely rewrote the macro to be more dynamic and for some added features.

## Features:
	- bed mesh will be reused if print area is smaller or equal to the previous one with ability to override
	- offsets from bltouch or probe are used
	- existing bed_mesh min/max, probe_count values are used from printer.cfg
	- method renamed to bed_mesh_calibrate and is overwriting built-in bed_mesh_calibrate
	- individual probe count (x and y) is used - if print area X or y is 50% than available area, probe_count for that axis is changed from the default to 3
	- if "relative_reference_index" is set in bed_mesh, it will be reconfigured to use the middle point on the bed
	- Works with Klicky Probe on Voron

## Disclaimer

It's impossible to account for everyone's various config. When first implementing this, be prepared to click the :Emergency Stop" button if it tries to probe off the bed.

## Setup

- Modify your print_start macro to include the 2 parameters (PRINT_MIN and PRINT_MAX) 
<pre>
[gcode_macro print_start]
variable_parameter_PRINT_MIN : 0,0
variable_parameter_PRINT_MAX : 0,0
gcode:
</pre>

- Where you normally perform a bed_mesh in your start_print macro, replace it (or add it if you didn't previously have it) with the following line:
<pre>
BED_MESH_CALIBRATE PRINT_MIN={params.PRINT_MIN} PRINT_MAX={params.PRINT_MAX}
</pre>

- Modify your printer start g-code to include the PRINT_MIN and PRINT_MAX parameters:

Examples:


PrusaSlicer/SuperSlicer:
<pre>print_start EXTRUDER={first_layer_temperature[initial_extruder] + extruder_temperature_offset[initial_extruder]} BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature] PRINT_MIN={first_layer_print_min[0]},{first_layer_print_min[1]} PRINT_MAX={first_layer_print_max[0]},{first_layer_print_max[0]}</pre>

Cura (add this to your start_print)
<pre>PRINT_MIN=%MINX%,%MINY% PRINT_MAX=%MAXX%,%MAXY%</pre>

*(Cura slicer plugin) To make the macro to work in Cura slicer, you need to install the post process plugin by frankbags - In cura menu Help -> Show configuration folder. - Copy the python script from the above link in to plugins folder. - Restart Cura - In cura menu Extensions -> Post processing and select Mesh Print Size
