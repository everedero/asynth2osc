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

## Benchmark
### Befaco VCMC specs
* Teensy 32 controller
    * NXP MK20DX256VLH7
    * 2x 16-bit ADC
    * 1x 12-bit DAC

### Yarns V3 specs
* ST STM32F103CBT6 controller
    * 2x 12-bit ADC (16 channels)
    * 2x DAC
* External DAC DAC8564
    * 1x 16-bit DAC (4 channels)

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
* Pi-Plates DAQCR1 (?)
* Adafruit I2C rotary encoder module, with click and LED #4991
* Adafruit quad-alphanumeric segmented display with I2C #1912
* 4 mini-arcade buttons with retro-lighting

### Pi-Plates DAQC2

* Specs [here](https://pi-plates.com/daqc2r1/)

### Pi-Plates DAQCplate

* SoC: Microchip PIC16F1517-I/PT
* 16x 10-bit ADC
* Analog input via TI SN74LV4051A 8:1 analog multiplexer
* Open Collector outputs via TI DS2003CM high-current Darlington driver and NXP 74HC 8-bit parallel-in/serial out shift register.

## Second architecture
This aims to integrate LEDs, buttons, and DACs on a board.
The Raspberry Pi is dropped and replaced by a custom board with WiFi/TCP-IP SoC and all the analog stuff.
It is possible to use a PCB board as front panel too.

For the software part, the board would only output the OSC, or ideally have an embedded, web-interface version of Chataigne.
I would consider implementing [TinyOSC](https://github.com/mhroth/tinyosc), a lightweight OSC implementation, preferably on Zephyr RTOS.

### Requirements
#### DAC
* 8 channels input
* Minimum 16 bits, preferably 24
* Sample rate: ideally 50Hz, it can be quite slow (LFO are around 2 Hz)
* MCP3008 ?

#### Power supply
Eurorack standard includes a +12/-12V power supply, but the currents are quite low, and sensitive to pollution. So we should rather use a 5V additional external power supply.

#### Mechanical
It has to fit in Eurorack format. The smaller, the better,but no hard requirement for width.
Some work has to be done to fit the connectors, leds, buttons and mechanical parts nicely.

#### Diagram
<!--
graph TD
  A[ ESP32 ] ==>| RMII | B[ Ethernet PHY ]
  A <==> | SPI | C[ NOR Flash ]
  A <==> | I2C | D[ GPIO expander ]
  D ==> | GPIO | E> LEDs ]
  F> Buttons ] ==> | GPIO | D
  G[ ADC ] ==> | SPI | A
  H[ Analog magic ] ==> | Analog | G
  I> 3.5 jacks ] ==> H
  B ==> J> RJ45 ]
  A ==> | DAC | M[ Analog out ]
  M ==> N> 3.5 jack ]
  M ==> O> PWM LED ]
  K> Power jack ] -.- | 12V | L[ DCDC ]
  L -.- | 3V3 | A
  A <==> | UART, JTAG | P[ FTDI ]
  P ==> Q> Debug USB ]
  Q -.- | 5V | L
  A === | U.FL | R[ Antenna ]
  A ==> | SPI | S> Display ]
-->

![Kroki generated
Mermaid](https://kroki.io/mermaid/svg/eNpVkNFugjAYhe99ivMA02Qy72YToKA40AroshAvOkfQzYERzLbEh9_fFjHeFHrO95_2tDjJ4w4p7wF2Bi8R1hAbjMfsgjgKAlzgkNzs8lOZNxDTN2wUimdCyEyEQtwM80UM_yDr3b0fDF1aeYaJCBbIf4-y_MhPmuEwiHYu8BhCj9fa8hmcc9NUZW3ucqPUPScZbO52jrmCTcaUjFIeqgLfsthvO6IVKYSggMEajPApt1_X9CnJjv6bMcSzp1HbwUxzW1WIuuzq3NAcAZEG5rc8PWbUBYN4jVQjLb7Qtvqh4gZDf9CnzMfhmtYwA3dVH-LC1rHWVtupe8mVHacPmKX2hDYig5_yQM8IfeCSgefv5wKrxNHyso0a6TPaPmMVNPBD-sSqUJOXpbxra14zobR9fTzIP1WV7N4_oG6LJw==)


#### STM32 option
* Only performance range (STM32F7 and STM32H7) have RMII
* STM32H7 has 16-bits ADC, it's perfect

<!--
graph TD
  A[ STM32 ] ==>| RMII | B[ Ethernet PHY ]
  A <==> | SPI | C[ NOR Flash ]
  A <==> | GPIO | D> LEDs ]
  E> Buttons ] ==> | GPIO | A
  H[ Analog in ] ==> | ADC | A
  I> 3.5 jacks ] ==> H
  B ==> J> RJ45 ]
  A ==> | DAC | F[ Analog out ]
  F ==> N> 3.5 jack ]
  A ==> | PWM | O> PWM LED ]
  K> Power jack ] -.- | 12V | L[ DCDC ]
  L -.- | 3V3 | A
  A <==> | UART, JTAG | P> SWD header ]
  L -.- | 5V | G
  A ==> | SPI | S> Display ]
  A <==> | USB | G> Debug USB ]
-->
![Kroki generated Mermaid](https://kroki.io/mermaid/svg/eNpVkMtugzAQRff9ivsBTaSGZtdYMphn80BAElWIBW1RSIsg4qGqUj6-Y0NJ2IzHPveOr-ZUp5cckXgAeIww2mgLJFit2BXBxnVxhR7DbPOsLrMWvvOGRErxQhKCoS8lRoztLoBVpE0-5bbv7ugQDGtTNIqZDHrXtlXZ9B_dVJyoE4OXaVGdcC5HzoUxYJdBmy_xlX58_9sdetZV5zEE3vNySNBbBZdWa5xada3iluLb27iJyz9uqO6Yaii5gq90rX6yepBjNp-R6GlxoLqOIQxKKXXrgWgHbUg9rmPPg-gRXsRt-QlDeBTIs_STht47l3KkfZenX3PIIM7NpUh_p0veh7rUE83eu5O6Jn96b3dM)
