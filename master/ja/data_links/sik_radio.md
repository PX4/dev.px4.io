# SiK Radio

[SiK radio](https://github.com/LorenzMeier/SiK) is a collection of firmware and tools for telemetry radios.

Information about *using* SiK Radio can be found it the *PX4 User Guide*: [Telemetry > SiK Radio](http://docs.px4.io/en/telemetry/sik_radio.html)

The ("developer") information below explains how to build SiK firmware from source and configure it using AT commands.

## Build Instructions

You will need to install the required 8051 compiler, as this is not included in the default PX4 Build toolchain.

### Mac OS

Install the toolchain:

```sh
brew install sdcc
```

Build the image for the standard SiK Radio / 3DR Radio:

```sh
git clone https://github.com/LorenzMeier/SiK.git
cd SiK/Firmware
make install
```

Upload it to the radio \(**change the serial port name**\):

    tools/uploader.py --port /dev/tty.usbserial-CHANGETHIS dst/radio~hm_trp.ihx
    

## Configuration Instructions

The radio supports AT commands for configuration.

```sh
screen /dev/tty.usbserial-CHANGETHIS 57600 8N1
```

Then start command mode:

> **Note** DO NOT TYPE ANYTHING ONE SECOND BEFORE AND AFTER

    +++
    

List the current settings:

    ATI5
    

Then set the net ID, write settings and reboot radio:

    ATS3=55
    AT&W
    ATZ
    

> **Note** You might have to power-cycle the radio to connect it to the 2nd radio.