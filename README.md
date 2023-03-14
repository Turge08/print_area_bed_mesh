# Print Area Bed Mesh

## Description

- This Klipper macro will create a bed mesh based on the size of your print.

## Credit

Complete credit goes to ChipCE (https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02) for the idea and sample code. I completely rewrote the macro to be more dynamic and for some added features.

## Features:
- **Reusable Bed Mesh** - bed mesh will be reused if print area is smaller or equal to the previous one with ability to override
- **Easy To Add** - Nothing to update in the script since the existing bltouch or probe offsets, existing bed_mesh min/max, probe_count values are used from printer.cfg
- **Seamless Bed Mesh Calibration** - BED_MESH_CALIBRATE replaced with new script
- **Separate axis probe counts** - if print area X or y is 50% than available area, probe_count for that axis is changed from the default to 3
- **Supports Bed Mesh Relative Reference Index** - if "relative_reference_index" is set in bed_mesh, it will be reconfigured to use the middle point on the bed
- **Klicky Probe Compatible** - Works with Klicky Probe on Voron

## Disclaimer

This is currently in development. While it works on my 2 printers, it's impossible to account for everyone's various config. When first implementing this, be prepared to click the "Emergency Stop" button if it tries to probe off the bed.

## Change Log

- Combined klicky probe version with regular script
- Added 20mm buffer to bed mesh
- Fixed issue with bed mesh being skipped when current bed mesh is cleared
- Fixed issue with offset expanding bed mesh area out of bounds

## Setup

### 1. Download and install the macro
SSH into the Pi and run the following commands:<pre>cd ~
git clone https://github.com/Turge08/print_area_bed_mesh.git
~/print_area_bed_mesh/install.sh</pre>

### 2. Include macro in your printer.cfg 
Through Fluidd/Mainsail, edit printer.cfg file and add the following line at the top of your printer.cfg: <pre>[include print_area_bed_mesh.cfg]</pre>

### 3. Update Moonraker for easy updating
From Fluidd/Mainsail, edit moonraker.conf (in the same folder as your printer.cfg file) and add:<pre>[update_manager print_area_bed_mesh]
type: git_repo
path: ~/print_area_bed_mesh
origin: https://github.com/Turge08/print_area_bed_mesh.git
is_system_service: False</pre>

NOTE: You must perform step #1 at least once or Moonraker will generate an error.

### 4. Option 1 - Modify "start_print" macro in your printer.cfg
- Where you normally perform a BED_MESH_CALIBRATE in your start_print macro, replace it (or add it if you didn't previously have it) with the following line:<pre>
BED_MESH_CALIBRATE PRINT_MIN={params.PRINT_MIN} PRINT_MAX={params.PRINT_MAX}
</pre>

- If you want to force a new mesh every time, use the following syntax:<pre>
BED_MESH_CALIBRATE PRINT_MIN={params.PRINT_MIN} PRINT_MAX={params.PRINT_MAX} FORCE_NEW_MESH=True
</pre>

- Modify your printer's start g-code in your slicer to include the PRINT_MIN and PRINT_MAX parameters:

Examples:


- PrusaSlicer/SuperSlicer:
<pre>print_start EXTRUDER={first_layer_temperature[initial_extruder] + extruder_temperature_offset[initial_extruder]} BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature] PRINT_MIN={first_layer_print_min[0]},{first_layer_print_min[1]} PRINT_MAX={first_layer_print_max[0]},{first_layer_print_max[1]}</pre>

- Cura (add this to your start gcode at the end of the start_print command:)
<pre>PRINT_MIN=%MINX%,%MINY% PRINT_MAX=%MAXX%,%MAXY%</pre>

*(Cura slicer plugin) To make the macro to work in Cura slicer, you need to install the <a href="https://gist.github.com/frankbags/c85d37d9faff7bce67b6d18ec4e716ff">post process plugin by frankbags</a> - In cura menu Help -> Show configuration folder. - Copy the python script from the above link in to scripts folder. - Restart Cura - In cura menu Extensions -> Post processing and select Mesh Print Size

### 5. Option 2 - Modify your printer's start g-code in your slicer:

Examples:

- PrusaSlicer/SuperSlicer:<pre>BED_MESH_CALIBRATE PRINT_MIN={first_layer_print_min[0]},{first_layer_print_min[1]} PRINT_MAX={first_layer_print_max[0]},{first_layer_print_max[1]}</pre>

- Cura<pre>BED_MESH_CALIBRATE PRINT_MIN=%MINX%,%MINY% PRINT_MAX=%MAXX%,%MAXY%</pre>

- IdeaMaker<pre>BED_MESH_CALIBRATE PRINT_MIN={print_pos_min_x},{print_pos_min_y} PRINT_MAX={print_pos_max_x},{print_pos_max_y}</pre>

- BambuStudio/OrcaSlicer<pre>BED_MESH_CALIBRATE PRINT_MIN={first_layer_print_min[0]},{first_layer_print_min[1]} PRINT_MAX={first_layer_print_max[0]},{first_layer_print_max[1]}</pre>
