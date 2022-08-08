# Introduction

This is a fork of [RatOS-Configuration repo](https://github.com/Rat-OS/RatOS-configuration) that adds support for the [Voron Trident](https://vorondesign.com/voron_trident).

## Features

- Support for the Voron Trident (currently only 250)
- Adds two new variables:
  - Optional "motors off" at print end
  - Support for textured plates in the print start command to apply a configurable Z-offset

## Assumptions

- This repo can be used across multiple printers without any modifications needed
- Choice of Klicky as the z-probe
  - The included template might be compatible with other z-probes, but is untested (PRs welcome)
- RatOS itself is not used, only the configuration setup
  - RatOS might work, but is untested
- LDO 42STH48-2504AC A & B motors
  - BOM calls for LDO 42STH40-2004MAH (PRs welcome)
- Fysetc Spider v1.1 controller
  - Other MCUs haven't been tested (PRs welcome)

## Install

- Setup Raspberry Pi using the method of your choice
  - Following either the [Mainsail OS](https://docs.mainsail.xyz/setup/mainsail-os) or [FluiddPi](https://docs.fluidd.xyz/installation/fluiddpi) installation instructions is recommended
- Chose a unique name for your printer which will be referenced as `<name>` below
- SSH to your Raspberry Pi and change directory to klipper_config:
  - ```shell
    ssh pi@<hostname>.local
    cd ~/klipper_config
    ```
- Clone repo:
  - ```shell
    git clone https://github.com/AdamYellen/RatOS-Configuration config
    ```
- Copy the included template:
  - ```shell
    cp config/templates/voron-trident-klicky.template.cfg printer-<name>.cfg
    ```
- Modify the mcu setting at the bottom of `printer-<name>.cfg`
  - Hint: `ls /dev/serial/by-id/`
- Install [klipper_z_calibration](https://github.com/protoloft/klipper_z_calibration)
- Install [Klicky Probe](https://github.com/jlas1/Klicky-Probe/tree/main/Klipper_macros) macros in a specific sub-directory:
  - ```shell
     mkdir klicky
     cd klicky
     wget https://raw.githubusercontent.com/jlas1/Klicky-Probe/main/Klipper_macros/Klipper_macros.zip
     unzip Klipper_macros.zip
     cd ..
- Modify the klipper args to reference the new printer name and restart Klipper
  - There are two ways to edit this reference based on how Klipper was installed
    - Edit the KLIPPY_ARGS line in /etc/defaults/klipper:
    - ```shell
      KLIPPY_ARGS="/home/pi/klipper/klippy/klippy.py /home/pi/klipper_config/printer-<name>.cfg -l /tmp/klippy.log -a /tmp/klippy_uds"
      ```
    - Restart Klipper:
    - ```shell
      sudo /etc/init.d/klipper restart
      ```
  - **OR**
    - Edit the KLIPPER_CONFIG line in /etc/systemd/system/klipper.service:
    - ```shell
      Environment=KLIPPER_CONFIG=/home/pi/klipper_config/printer-<name>.cfg
      ```
    - Restart Klipper:
    - ```shell
      sudo systemctl restart klipper
      ```
- Use your interface of choice (Mainsail, Fluidd, nano, vi, etc.) to edit the copied template to suit your needs

## New Variables

- Add `TEXTUREDPLATE=true` to your slicer's PRINT_START command and adjust `variable_textured_plate_offset` in the config file
  - This is an additional Z-offset that will be applied after Z-probing
- Enable or disable the motors off variable (`variable_end_print_motors_off`) in the config file to control that behavior
