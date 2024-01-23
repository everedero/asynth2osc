# Analog synth to OpenOSC interface

## Project description

Analog synth is coming back into hype among music artists. It has a strong culture of vintage, DIY, and customisation.

On the other hand, pro systems use digical control systems, such as DMX for lighting, or OpenSoundControl (OSC) for sound.

The idea for this project is to have a hardware to interface analog synth to advanced sound control systems.

This project is also designed to leverage the possibilities of open-source software [Chataigne](https://benjamin.kuperberg.fr/chataigne/).

It is similar to [Befaco VCMC](https://www.befaco.org/vcmc-2/), but with TCP-IP for OSC instead of serial for MIDI - so we need to upgrade the microcontroller.

Some inspiration for hardware can be found at open hardware synths company Mutable Instruments. In its [repository](https://github.com/pichenettes/eurorack) we can find the exact reference design, typically for the [Yarns V3](https://pichenettes.github.io/mutable-instruments-documentation/modules/yarns/) MIDI digital interface.

## Glossary

### OpenSoundControl (OSC)
Open, message-based protocol for communication between multimedia devices. Intended to be a next-generation replacement for MIDI. It is usually implemented on TCP-IP stack.

### Musical Instrument Digital Interface (MIDI)
Communication protocol dedicated to music, used for communication between musical instruments and multimedia devices. Usually implemented as a serial connection, with a 5-pins DIN connector.

### Chataigne
Chataigne is a user-friendly open-source software allowing to connect several multimedia devices between them, for instance, triggering light color changes via DMX on an OpenOSC event sent by a mixer.

### Analog synthetizer
Synthetizer that uses analog circuits and signals to generate sound electronically.

[See the wikipedia page](https://en.wikipedia.org/wiki/Analog_synthesizer)

### VCV Rack
Open-source software able to emulate analog synthetizers.

### Eurorack
Standardized mechanical format for synthetizer, in a modular 3U rack.
It uses 3.5mm jack for cabling, +/- 12V power supply.

## First architecture
The first prototype includes:
* 8 analog inputs
* Rotary encoder and screen
* 4 digital inputs and 4 buttons for 8 interrupt inputs
* 1 analog audio output
* Pretty front panel with Eurorack format
* 5V power supply input

The BOM is:
* Raspberry Pi 3
* Pi-Plates DAQCR1
* Adafruit I2C rotary encoder module, with click and LED #4991
* Adafruit quad-alphanumeric segmented display with I2C #1912
* 4 mini-arcade buttons with retro-lighting

## Second architecture
This aims to integrate LEDs, buttons, and DACs in a Raspberry Pi hat format.
It is possible to use a PCB board as front panel too.

### Requirements
#### DAC
* 8 channels
* Minimum 16 bits, preferably 24
* Sample rate: ideally 50Hz, it can be quite slow (LFO are around 2 Hz)
* MCP3008 ?

#### Power supply
Eurorack standard includes a +12/-12V power supply, but the currents are quite low, and sensitive to pollution. So we should rather use a 5V additional external power supply.

#### Mechanical
It has to fit in Eurorack format. The smaller, the better,but no hard requirement for width.
Some work has to be done to fit the connectors and Raspberry Pi nicely.

## Third architecture
In a future, dream project, the Raspberry Pi is dropped and replaced by a custom board with WiFi/TCP-IP SoC and all the analog stuff.

In this project, we would either drop the embedded Chataigne and only output the OSC, or ideally have an embedded, web-interface version of Chataigne.

I would consider implementing [TinyOSC](https://github.com/mhroth/tinyosc), a lightweight OSC implementation, preferably on Zephyr RTOS.

Maybe some Chataigne config file can be exported and used by the embedded firmware, to suppress the need for a computer running Chataigne somewhere in the installation?
