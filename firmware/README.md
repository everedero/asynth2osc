# Asynth2Osc firmware

* Zephyr RTOS based firmware
* STM32H743VITx

## Connect ST-Link
You will need an ST-Link probe to flash the firmware to the board.
Two different connectors allow to flash the board:

* J4, a standard 2.54mm header, to be soldered on bottom side
* J3 TagConnect, a pogo pin solution, no soldering needed but you will have to make a custom cable.

### Pinout
#### DUT connectors
##### J4 JTAG connector
This pinout follows the ST-Link connector pinout, with an additional row for JTAG JTDI.
The second column in only GND, it was added for connector mechanical stability and easy GND access.
<!--
             +-------+
      3V3    | 1 | 2 | GND
             |   |   |
SWCLK JTCK   | 3 | 4 | GND
             |   |   |
      GND    | 5 | 6 | GND
             |   |   |
SWDIO JTMS   | 7 | 8 | GND
             |   |   |
      NRST   | 9 | 10| GND
             |   |   |
SWO   JTDO   | 11| 12| GND
             |   |   |
      JTDI   | 13| 14| GND
             |   |   |
             +-------+
-->
![Kroki generated Ditaa](https://kroki.io/ditaa/svg/eNpTUEAC2roQoM0F4RuHGYOoGgVDIDYCYnc_Fy5kDUAhKOYKDnf28VbwCnH2BgsZA7EJfh0QPlABRNgUiM0I2eHi6Q-0wzcYLGQOxBbE2OEXFBwCFrIE-cWAgB3-QNorxMUf4nNDIDYiwg6gDk-IDmMgNiFCB3qgAwBQM0uR)

##### J3 TagConnect TC2030
TBD: Not sure about this pinout. The J3 pinout follows the manufacturers's recommendation for JTAG.
<!--
1 3V3
2 JTMS
3 NRST
4 JTCK
5 GND
6 JTDO


          O

  1   o      o   2

  3   o      o   4

  5   o      o   6

   O            O

         o
        +-------+
        | 1 | 2 |
        |   |   +-+
        | 3 | 4   |
        |   |   +-+
        | 5 | 6 |
        |   |   |
        +-------+

1 VTREF
2 SWDIO
3 nRST
4 SWCLK
5 GND
-->

<!--
          O

   9  o      o  2

  10  o      o  1

   7  o      o  4

   8  o      o  3

   5  o      o  6


   O            O

         o
        +--------+
        | 1 | 2  |
        |   |    |
        | 3 | 4  |
        |   |    +-+
        | 5 | 6    |
        |   |    +-+
        | 7 | 8  |
        |   |    |
        | 9 | 10 |
        |   |    |
        +--------+
-->

#### ST-Link connectors
##### Nucleo ST-Link/V2 pinout CN2
If you are using the ST-Link embedded on a Nucleo board as your debugger, do not forget to remove the 2 jumpers CN2, as shown on the overlay.

* Jumpers equipped: flash the Nucleo
* Jumpers removed: flash an external board connected on CN2

You can even separate the ST-Link part from the Nucleo part with the breakout pads!
<!--
        o
     +---+
     | 1 | VDD    3V3
     |   |
     | 2 | SWCLK  JTCK
     |   |
     | 3 | GND
     |   |
     | 4 | SWDIO  JTMS
     |   |
     | 5 | NRST   NRST
     |   |
     | 6 | SWO    JTDO
     |   |
     +---+
-->
![Kroki generated Ditaa](https://kroki.io/ditaa/svg/eNpTUICAfC4wpa2rq6sNYdYoGAJxmIsLiGMcZgwTBWIY0wiIg8OdfbwVFLxCnL2xqDAGYnc_FywyJmC9Lp7-IL2-wVhUmAKxX1BwCJADorCoMAOb4Q_ieIW4-GOogPgGAGM1LFQ=)

##### Official ST-LinK/V2
Oh yeah, you can actually by the probe too. Be careful when buying those, there are quite a fair amount of ST-Link counterfeits and not all of them are fully functional.
<!--
                   o
                  +-------+
             VAPP | 1 | 2 | VAPP
                  |   |   |
             TRST | 3 | 4 | GND
                  |   |   |
              TDI | 5 | 6 | GND
                  |   |   |
      SWDIO   TMS | 7 | 8 | GND
                  |   |   |
      SWCLK   TCK | 9 | 10| GND
                  +   |   |
               NC   11| 12| GND
                  +   |   |
      SWO     TDO | 13| 14| GND
                  |   |   |
             NRST | 15| 16| GND
                  |   |   |
               NC | 17| 18| GND
                  |   |   |
              VDD | 19| 20| GND
                  +-------+
-->
![Kroki generated Ditaa](https://kroki.io/ditaa/svg/eNqVklsKwyAQRf-7ivmXQs07n8WBEtJqqJIsoxvI4nuHtIWQCiocwTscdAaJDut1OmbqvC21r83XaaKVNCiAHP_I65d9LTx9QF6CCtwsp7sUeEBegybZ9QsPTtyHR96CLsM191FcMyLvpelLzFWRN5M12LSGW6S6fnGffp3cWYIqc1Z2m7OuQZM7Z3kzvBZ0ue7MLG6PzxGf1e9jvQFbdnNG)

#### Pinout recap

| JTAG Signal | SWIO Signal | Board J4 | Board TagConnect J3  | Nucleo ST-Link CN2 | ST-Link 2x10 |
|-------------|-------------|----------|----------------------|--------------------|--------------|
| JTCK        | SWCLK       | 3        | 4                    | 2                  | 9            |
| JTMS        | SWDIO       | 7        | 2                    | 4                  | 7            |
| JTDI        |             | 13       | NC                   | NC                 | 5            |
| JTDO        | SWO         | 11       | 6                    | 6                  | 13           |
| nRST        | nRST        | 9        | 3                    | 5                  | 15           |
| VDD         | VDD         | 1        | 1                    | 1                  | 19           |
| GND         | GND         | 5, 2-14  | 5                    | 3                  | 2-20         |
