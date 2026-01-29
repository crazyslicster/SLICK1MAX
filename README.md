# SLICK1MAX
Creality K1 Max with CFS and HelperScript Configurations

## Intro
Since my CFS upgrade, I'm had nothing but issues with the Creality Firmware starting with V2.3.5.27, then V2.3.5.33 and now V2.3.5.34.

From having screen timeout issues to quality issues, slow prints, bad profiles and z offset issues.  
I also missed using Orca, Fluidd and Klipper with HelperScript so I put some big efforts into creating proper klipper configs and Ocra Slicer Profiles.  

Although a work in progress still, I've now got it tuned great.

Before doing any of this, if you're not already on firmware V2.3.5.34, please do so and reset your config

## HelperScript Installation
Follow the instructions here: https://guilouz.github.io/Creality-Helper-Script-Wiki/

## Things you'll need to adjust
- Your Z offset
  - On my model (2003), lots of users report offset issues.  Mine is set in klipper to +0.5
  - I suggest doing a test print of a flat single layer, watching and adjusting manually as it prints.

## Config Files
.\Klipper

After you've installed the HelperScript, you can copy my config files from this repo to /usr/data/printer_data/config

### Things it does:
- Some modifications to the Creality versions of the T0, T1, T2 and T3 commands so that head it properly set prior to printing
- Dummied a couple of commands like CX_NOZZLE_WIPE so it no longer runs
- I have a custom HEAT_SOAK gcode to detect larger prints and pause to left the bed soak some heat. (work in progress)
- Fix for SET_HOTEND_FAN error - just a dummy gcode\
- For now it uses KAMP but you'll want to deactive the adaptive purge if you use my Orca Profiles
  - I may remove KAMP altogether and add the custom gcode I'm testing to do a full mesh every few prints

## Orca Profiles
.\OrcaProfiles

For now, you will really need to use these profiles as they go hand and hand with the garbage that Creality does under the hood.
You'll see some of the layer change codes I had to do because creality does some strange stuff that we need to undue and have things run in a specific order.
To understand what I'm talking about, here is the specific GCODE I added to the layer change:

*** Be careful with the Machine Start gcode as I've added a plate scraping command instead of the slow wipe creailty does. You may not want this!

```
;LAYER CHANGE
;Layer: [layer_num]
{if layer_num == 0}
;LAYER 0 Prime
M104 S[nozzle_temperature_initial_layer]
M140 S[bed_temperature_initial_layer_single]
M109 S[nozzle_temperature_initial_layer]
M190 S[bed_temperature_initial_layer_single]

G4 P5000

G1 X0 Y0 F12000
G1 Z0.2 F600
G1 X0 Y65 F1200 E18   ; purge line

G1 E-0.6 F2400        ; small retract to break the filament
G1 Z0.6 F600          ; lift a bit so any ooze is off the plate
{endif}
```

## Issues
- So far the only issue I have is between the purge and print starting where there is a bit of excess filament.  May have a solution already but needs to be tested
