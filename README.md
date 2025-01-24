# Analog synthesizer to Open Sound Control (OSC) module

## Project description

Analog synth is coming back into hype among music artists. It has a strong culture of vintage, DIY, and customisation.

On the other hand, pro systems use digical control systems, such as DMX for lighting, or OpenSoundControl (OSC) for sound.

The idea for this project is to have a hardware to interface analog synth to advanced sound control systems.

This project is also designed to leverage the possibilities of open-source show control software [Chataigne](https://benjamin.kuperberg.fr/chataigne/).

There are a lot of solution to control an analog modular synthesizer from a computer, mainly by MIDI interfaces.
But not so much options to do the opposite: control others FROM analog modulations.
The features and purpose are very close to existing [Befaco VCMC](https://www.befaco.org/vcmc-2/), but with TCP-IP for OSC instead of serial for MIDI - so we need to change the microcontroller.

Some inspiration for hardware can be found at open hardware synths from Mutable Instruments company.
In its [repository](https://github.com/pichenettes/eurorack) we can find the exact reference design,
typically for the [Yarns V3](https://pichenettes.github.io/mutable-instruments-documentation/modules/yarns/) MIDI digital interface.

## Glossary

### Musical Instrument Digital Interface (MIDI)
Communication protocol dedicated to music, used for communication between musical instruments and multimedia devices.
Usually it is implemented as a serial connection, with a 5-pins DIN connector.

### OpenSoundControl (OSC)
Open, message-based protocol for communication between multimedia devices. Intended to be a next-generation replacement for MIDI.
[OSC specification 1.0 (Stanford University)](https://opensoundcontrol.stanford.edu/spec-1_0.html)
It is usually implemented on TCP-IP stack, most usually as UDP datagrams for real time applications.
It can be easily transmit thru Ethernet or Wifi at higher bit rate, and a much higher resolution of 32 bits float, than MIDI wich was based on 7 bits integer.

### CV - Control Voltages
The analog signals generated by the synthetisers are called CV.

### Gate and triggers
Other types of signals generated can be:
* Gate: a "digital" 0 and 1 signal, but with the synthesiser reference analog voltage,
* Trigger: a short-pulse "digital" 0 and 1 signal, to trigger a particular event.

### Chataigne
Chataigne is a user-friendly open-source show control software, allowing to connect several multimedia devices, hardware or software between them.
We develop belongside this hardware project asynth2osc a custom module for Chataigne, so this piece of software can be use easily to control anything from a real world analog modular,
from sound to light, videos and activators of any kind.

[Chataigne source code on Github](https://github.com/benkuper/Chataigne)
[Artist-friendly Modular Machine for Art and Technology - Chataigne Official website](http://benjamin.kuperberg.fr/chataigne)

### Analog synthetizer
Synthetizer that uses analog circuits and signals to generate sound electronically.
[See the wikipedia page](https://en.wikipedia.org/wiki/Analog_synthesizer)

### Analog modular synthetizer
Modular synthesizers are composed of separate hardware modules for different functions.
The modules can be connected together by the user to create a patch.
The outputs from the modules may include audio signals, analog control voltages, or digital signals for logic or timing conditions.
[See the wikipedia page](https://en.wikipedia.org/wiki/Modular_synthesizer)

### VCV Rack
Open-source software that emulate analog modular synthetizers. Most of hardware modules are present.
Especially, Tramasoft edited a module that is a kind of emulation of our asynth2osc module : cvOSCcv.
This virtual module can be use to use real world analog hardware to control the software.
[VCV Rack cvOSCcv module page](https://library.vcvrack.com/trowaSoft/cvOSCcv)

### Eurorack
Standardized mechanical format for synthetizer, in a modular 3U rack.
It usually fits in a "case" to ease musicians to build their own synthetizer, and have a unique Power Supply Unit and power busses for all the modules.

The dimensions are normalised :
* 3U = 128.5 mm height (U is professionnal standard 19" wide rack unit)
* 1HP = 5.08 mm, width is multiple of 1HP
* Depth should be <2.5mm or <4mm to be shallow

Exemple: Yarns is 12 HP and 3U, so front plate is 128.5x60.96mm. Its bottom PCB is tinier: 59x106,8mm, and has 3 screw holes to fit it to the front plate.

It uses 3.5mm jack for cabling, +/- 12V power supply on a 2x5p header connector, on the right lower corner of the board (facing the bottom).
Red side = -12V. Print a | on the board to indicate side. Don’t forget the protection circuit.

<!--
        +---------+
        |         |
 +12V   |  o   o  |  +12V
        |         |
 GND    |  o   o  |   GND
        |         |
 GND    |  o   o  |   GND
        |         |
 GND    |  o   o  |   GND
        |         |              |
 -12V   |  o   o  |  -12V        |
        |         |              |
        +---------+              |
                                 |
            ----                 |
                                 |
---------------------------------+
-->
![Kroki generated Ditaa](https://kroki.io/ditaa/svg/eNpTUIAAbV0Y0OaCCinUKMBZXArahkZhELF8BTAGskBi2FW7-7kooKsGCdJbtQIKAGrWxeINqBhMCRGmYAYaLiU4AaoSkBkElOAwRZcQ0AYApyBP7w==)

## Benchmark
### Befaco VCMC specs (close features, MIDI only)
* Teensy 32 controller
    * NXP MK20DX256VLH7
    * 2x 16-bit ADC
    * 1x 12-bit DAC

* 8 CV muxed on 1 ADC

### Yarns V3 specs (close MCU, MIDI only)
* ST STM32F103CBT6 controller
    * 2x 12-bit ADC (16 channels)
    * 2x DAC
* External DAC DAC8564
    * 1x 16-bit DAC (4 channels)

## First architecture (RPI for Wifi, Bluetooth and cabled Ethernet support)
The first prototype includes:
* 8 analog inputs
* Rotary encoder and screen
* 4 digital inputs and 4 buttons for 8 interrupt inputs
* 1 analog audio output
* Pretty front panel with Eurorack format
* 5V power supply input

The BOM is:
* Raspberry Pi 3
* Pi-Plates DAQC2
* Adafruit I2C rotary encoder module, with click and LED #4991
* Adafruit quad-alphanumeric segmented display with I2C #1912
* 4 mini-arcade buttons with retro-lighting

### Pi-Plates DAQC2
This RPI hat is the GPIO interface, with a very good analog front end:
* Specs [here](https://pi-plates.com/daqc2r1/)

* SoC: Microchip PIC16F1517-I/PT
* 16x 10-bit ADC
* Analog input via TI SN74LV4051A 8:1 analog multiplexer
* Open Collector outputs via TI DS2003CM high-current Darlington driver and NXP 74HC 8-bit parallel-in/serial out shift register.

![proto1 front panel assembled](https://github.com/madees/asynth2osc/blob/main/proto1greenut2.jpg)
![proto1 beta tests](https://github.com/madees/asynth2osc/blob/main/proto1greenut.bmp)
![proto1 beta tests](https://github.com/madees/asynth2osc/blob/main/proto1greenut3.bmp)

## Second architecture
This aims to integrate LEDs, buttons, and DACs on the same board to improve manufacturing and ralaibilty.
The Raspberry Pi is dropped and replaced by a custom board with WiFi/TCP-IP SoC and all the analog stuff.

It is possible to use a PCB board as front panel too, as a lot of synthesizer modules do.

For the software part, the board would only output the OSC.
I would consider implementing [TinyOSC](https://github.com/mhroth/tinyosc), a lightweight OSC implementation, preferably on Zephyr RTOS.

### Requirements
#### Ethernet
* Full TCP-IP stack implemented, with OSC other UDP protocol.
* DHCP, Link Local and Static IP settings.
* Wired RJ45, optionnal Wifi.

#### DAC
* This has to be the most crucial part for latency. Quick interface is preferable, like SPI.
* 8 channels input (audio stereo 3.5mm mini-jack).
* Minimum 16 bits, preferably 24.
* Sample rate: ideally at least 100Hz, application can be quite slow (LFO are around 2 Hz). Oversamplig is a must for smoothing and lower transmission rate.
* MCP3008 is an option.

#### Other IO
* MIDI input (also using stereo mini-jack).
Even if a lot of MIDI interface exist, and possibilties to send them other Ethernet with rtp-MIDI interfaces and protocol, forwarding some MIDI events to OSC would be a good feature.
With options to set the kind of datas (Programm Changes, Note ON, Control Changes etc) to be tranmitted.

MIDI in uses an UART port, see the [Arduino tutorial](https://www.instructables.com/Send-and-Receive-MIDI-with-Arduino/), or Yarns hardware.
Midi-jack pinout:

<!--
          | |
          +-+
NC 3  o           o  1  NC

Tip 5   o       o  4 Ring

            o
            2 Sleeve


          | |
          +-+
NC 3  o           o  1  NC

OUT- 5  o       o  4 OUT+

            o
            2 GND/Shield
-->
![Kroki generated Ditaa](https://kroki.io/ditaa/svg/eNpTUICBGoUaLjhHQVtXm8vPWcFYQSEfIQhiGyoo-DlzcYVkFiiYKiBkgbSJQlBmXjoXkiFgYRS-kUJwTmpqWSoXsjLSbPYPDdEFWY1iM1BQm5DN7n4u-sEZmak5KQAYSyrj)

UART baud rate is 31250 kbps.


#### Power supply
Eurorack standard includes a +12/-12V power supply, but the currents are quite low, and sensitive to pollution. So we should rather use a 5V additional external power supply as an option.

#### Mechanical
It has to fit in Eurorack format. The smaller, the better,but no hard requirement for width.
Some work has to be done to fit the connectors, leds, buttons and mechanical parts nicely.

#### CV inputs

VC analog inputs can wiggle between several voltage ranges:
* -5V / 5V
* -12V / 12V
* 0 / 5V
* 0 / 12V

We need an input stage able to fit those input the 0 / 3.3V ADC microcontroller input.

Attenuators are specific modules used to reshape a specific CV.

The most used input is copied from Mutable Instruments and remaps a -5V / 5V input in a 0 / 3.3V output, but needs a -10V bias to handle those negatives.
In Befaco VCMC, an optional voltage gizmo converts 5V power supply to -12V / +12V for standalone behaviour.
We could also use an inverting circuit but it will... invert: -5V mapped to 3.3V and 5V mapped to 0V.

Voltages are clamped to 0-3.3V using Shottky diodes, but it should maybe signal saturation to the user somehow.

Better scaling:
* Several input scalers muxed?
* A fancy IC with digital control?
* Single scaler controlled by DAC output (hard)?
* 2-steps converter: rescaler (-12/12 to -5/5), scaler (-5/5 to 0/3).


#### Diagram
<!--
graph TD
  A[ ESP32 ] ==>| RMII | B[ Ethernet PHY ]
  A <==> | SPI | C[ NOR Flash ]
  A <==> | I2C | D[ GPIO expander ]
  T> MIDI in] ==> D
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
Mermaid](https://kroki.io/mermaid/svg/eNpVUFFvgjAYfN-vuB8wTSbzbTYBKloHWgFdFuJD5wi6uWIEsy3xx-9rYbi9FHp33_W7K07quEPKbwA3wziRzgAbjEbsgjgSAhd4BNe7_KTzGnL6jI2R4oEkRCbSSPwM80WM4KCq3X9eDHw6eYaJFAvkX0elX_OT1XA0EstcMGYIx7yyVMoQCS6w13YVmO0CBu9c16Wumv2uk4adZHC53zHNWi4RUyK0OpQFPlSx33aKFiQTEgkGpz_Em9q-_7pPCfbs34whnt0P21zNNHdNrKjzLs-15SPLz692f9AFg3yKTEgLPtK1_KQuGhl6_R5Z3g3WdIYZuG_ikC5sGWfttJG6cldunN5ilroTusgMQUqdmRlpH1xScfnLucAq8Sy8bK2G9o02zsgY9YOQPrHJU-daq39hmzITcttXx4P6xuYHdX2POA==)


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
  T> 3.5 jack ] ==> U[ MIDI In ]
  U ==> | GPIO | A
  A <==> | USB | G> Debug USB ]
-->
![Kroki generated Mermaid](https://kroki.io/mermaid/svg/eNplkNtugkAYhO_7FPMA1aRS7-omi6u4VJRw0DQbL2hLwNaAEUzTxIfvvwsipjd7-mZmJ392So45IvEAcIUw8qwRdphM2AWBJyUusBVmdZ6eirSGv3jDTkvxQhKCoa8lU4XVOsD8kFT5PXd8uaZNMCxnojJsxmCf67osquajm4oTXSjwIjmUGfZFx7mYtlgyWMMxvpKP76t9Qc-2ObkMgfs8bhs0VsG1dd6llufa8Lnhq1vcncvferSumTlQcwNf6Vr-pKdWjsFwQKKn0YbWpYKYUkutW7bE2lht624cMQ-iR7gRd_QnDOFWIE-TTwrtO8c60un1acYcMoh9dTwkv0Yd9csbYazgSSEhCyOI_4_31iS0NaHE9P2cmevuD96Pg7I=)

# Power consumptions estimates
## 3V3 bus
* STM32H7 absolute maximum current through VDD/VSS: 620mA
* STM32H7 running at 100deg, VOS1, all peripheral enabled: around 500mA
* ETH PHY, 10BASE-T traffic: 130mA
* OLED display logic: 300uA

* 3V3 LDO: 800 mA
* Fuse: 1.25A, trip 2.5A
* Filter ferrite rating: 3A
* Schottky rating (SS24): 2A

## 10V
* OLED display lighting: 25mA
* Step-up rating: 2.1A
* Step-up inductor: 2.4A
* Schottky rating (SS24): 2A

## 12V
* Schottky rating (1N5819W): 1A
* LDO efficiency (vout/vin): 12-5V -> 42%, 5-3V -> 66%
* 800mA @ 3V3 => 800mA @ 12V (not crazy, but linear)

## 5V
* USB spec: 500 mA in USB 2.0
* USB spec: 900 mA in USB 3.0
* USB 2.0: 500mA on 3V3 after LDO
* USB 3.0: 900mA on 3V3 after LDO
Might be useful to have USB 3.0 supply.

# Notes about JLCPCB files
## Generate files

Some modification are required from Kicad output files (BOM csv and placement files) to be imported to JLCPCB for assembly.

* Change field names in BOM and placement CSV headers
* Fix component rotation

There is a way to automate both with a Python script called [jlc-kicad-tools](https://github.com/matthewlai/JLCKicadTools/tree/master).

This script uses a rotation look-up-table called cpl\_rotations\_db.csv. Update this file with the one in this repository.

    cp [PRJ]/kicad/tools/cpl_rotations_db.csv [INSTALLFOLDER]/JLCKicadTools/jlc_kicad_tools

Then install your version from the local folder:

    pip3 install [INSTALLFOLDER]/JLCKicadTools

You will need 2 files in the project folder:

* Netlist file: project.xml
* Placement file: project-all-pos.csv

In order to get the project.xml file, in Eeschema, go to "Legacy BOM", create a custom XML exporter with the following command:
```
/bin/echo "%I"
```

Don’t forget to create a "JLCPCB" column with the JLCPCB references, for advanced components. For standard ones, keep their value and package and use the "--include-all-component-groups", otherwise the script will erase them.

Exemple use:
```
jlc-kicad-tools myproject -o ./assembly --include-all-component-groups
```

## Pricing

Here is an approximate price breakdown for manufacturing at JLCPCB.
Some components, such as though-hole or big components, will be ordered separately on LCSC and soldered by hand.
The "component" line includes both PCBa and by-hand BOMs.
Here, we manufacture 5 boards.

| Item              | Price |
|-------------------|-------|
| Components        | 151€  |
| PCB manufacturing | 28€   |
| PCB assembly      | 105€  |
| Shipping          | 35€   |
| Refunds           | 18€   |
| Total (x5 boards) | 300€  |

This is a total of 60€ / board.

# Bringup V0.1 known issues
* PHY: Error in LDO routing: VDDCR pin 6 is an internally regulated LDO 1.2V output, not to be connected to 3V3 supply.
Add 1 μF and 470 pF decoupling capacitors to ground.
* Rotary quadrature encoder should use timer inputs on same timer peripheral, only if hardware driver
is used. Otherwise, use gpio\_driver, because anyway, the hardware driver does not send signal to input subsystem.
    * TIM3 CH3 PC8 = ROT B
    * TIM4 CH3 PD14 = ROT A
    * 90: TIM3 CH1 PB4 (remove nRST jumper and solder to ROT A)
    * 91: TIM3 CH2 is FREE, can also be exchanged with 90 to ease routing
* Cannot setup different interrupts for GPIO having the same numbers but on different ports.
Issue with A8 rotative encoder pushbutton.
See [this issue](https://github.com/zephyrproject-rtos/zephyr/issues/45234).
* Missing pin 1 dot on diode D2 for manufacturing
* Dot around ethernet PHY: crystal dot too close
* Put poka-yoke or marking to mount USB connector on right side
* Put poka-yoke or marking for buttons
* Reduce jack connectors holes to mount more precisely
* Add testpoints for MDIO?
* Maybe disconnect PHY reset from main reset
Reset is pulled low by STLink probe, it has 1V2 value when probe is connected even if not plugged in.
