# Enabling KAMP Purging

KAMP Purging draws a single short ~30mm long, ~2mm wide, and ~0.8mm tall line near the front left of the area that the model will occupy.

This serves a similar purpose to the stock Qidi purge patterns by clearing the nozzle of any oozing material just before starting the first print object.
The KAMP Purge line uses about the same amount of filament as the stock Qidi purge pattern.

The main complaint about the stock Qidi purge pattern is that it can be difficult to remove cleanly.
This can result in touching the build surface area excessinvely, possibly leaving oily residue from fingers.
This can then affect surface adhesion quality for future prints until the plate is cleaned/washed again.

The KAMP purge line, being thicker and shorter, makes it easy to flick off the build surface with a fingernail, resulting in less contact with the build plate.

## How to Enable

Doing the following will enable the `LINE_PURGE` macro in Klipper, which we will use later.

First we must edit the `KAMP_Setting.cfg` file, which lives in the same directory as `printer.cfg`

![Where to find KAMP_Settings file](./Finding_Kamp_Settings_Cfg_File.png)

Now we must uncomment the `[include ./KAMP/Line_Purge.cfg]` line 

If you plan on printing with TPU, I also recommend changing the `variable_flow_rate` field in that file to have a value of `4`.
The default of `12` here is way too high for TPU, and possibly some other filament types, and can cause oozing and extruder issues.

Once that line is uncommented, and `variable_flow_rate` is adjusted, click save and restart.

![Uncommenting KAMP's Line Purge Config](./Uncomment-Line-Purge.png)

## Editing the Printer's Start G-Code in the slicer

The stock Qidi purge pattern is created by a set of lines in the Printer's Start G-Code definition in the slicer.

The Qidi Purge Pattern G-Code will look like this:

```
G0 Z[initial_layer_print_height] F600
G1 E3 F1800
G1 X{(min(print_bed_max[0] - 12, first_layer_print_min[0] + 80))} E{85 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 Y{max((min(print_bed_max[1] - 3, first_layer_print_min[1] + 80) - 85), 0) + 2} E{2 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 X{max((min(print_bed_max[0] - 12, first_layer_print_min[0] + 80) - 85), 0)} E{85 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 Y{max((min(print_bed_max[1] - 3, first_layer_print_min[1] + 80) - 85), 0) + 85} E{83 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 X{max((min(print_bed_max[0] - 12, first_layer_print_min[0] + 80) - 85), 0) + 2} E{2 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 Y{max((min(print_bed_max[1] - 3, first_layer_print_min[1] + 80) - 85), 0) + 3} E{82 * 0.5 * initial_layer_print_height * nozzle_diameter[0]} F3000
G1 X{max((min(print_bed_max[0] - 12, first_layer_print_min[0] + 80) - 85), 0) + 3} Z0
G1 X{max((min(print_bed_max[0] - 12, first_layer_print_min[0] + 80) - 85), 0) + 6}
```

We want to remove those lines and put `LINE_PURGE` in their place, like so.

![Modifying Slicer Printer Start G-Code](./Adding_Line_Purge_Gcode.png)

Restart klipper.
Now you have an easy to remove purge line that takes up less of the build area.

_Note: At the start of a print during KAMP purging you might notice a comment in the console about turning on firmware retraction. Ignore this. It is not necessary and slicer based retraction is superior._

## Possible issues with using KAMP purging
Whilst the location of the KAMP purge line is adaptive based on printed objects, it does not consider brims or skirts.

On the Plus4 there are 2 KAMP_Settings.cfg, the one in the KAMP directory is not used, the one in the same directory as printer.cfg is the one being used. This can be a little confusing because the actual Line_Purge.cfg is in the KAMP\ directory. Keep that in mind if you decide to edit settings.

If using beacon or cartographer, the default 10mm is possibly too close. The purge line is barely cleared by the screws and it's possible for the beacon/carto to knock the the purge line into the path of the nozzle if there is a bit of curled up ooze attached to it. You also probably should be running Stew675's modified KAMP purge https://github.com/qidi-community/Plus4-Wiki/blob/main/content/bed-scanning-probes/Beacon3D/RevH-Normal/Line_Purge.cfg
