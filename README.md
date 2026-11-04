# Bad, bad version of the analog synthesizer to Open Sound Control (OSC) module

## Project description

See branch main for the real project description.

On this branch, we modify the working design, to make a test project for automatic review tools.

Do not use it for reference. Do not copy it. Absolutely never send it to production.
This is a design full of mistakes for the sake of finding mistakes.

## Error list

### LAN8742A PHY
* VDDCR is 1.2V LDO output, should not be connected to 3V3.
* nINTSEL/LED2 should be pulled down, to 0. Or we should have a 50 MHz clock instead of 25 MHz.
  * INT/REF\_CLK\_OUT in this schematics look like it is used to generate the 50MHz clock for RMII,
    not as interrupt.
  * LED R80 on the connector should be pulled down to match the inversion.
* RX\_S\_N and RX\_S\_P on U11 (USB ESD diodes) are inverted.
* RBIAS R58 is 51k, it should be 12.1k.

### Interface
* SW7 and SW8 LED has no serial resistor, directly connected to 3V3. Should have a min 68 ohms.
* R82 should be around 1Mohms, not 10k, for the OLED display, as the comment says.

### Supply
* The fuse is short-circuited, net name VBUS should be modified.
* No protection against plugging the Eurorack connector the wrong way round: -12V instead of +12V
  would damage the board. A diode would be great.
* LDO U1 (LM1117M) LCSC reference is for a 5V LDO, it should be a 3V3.

### STM32H7 SoC
* Cannot setup different interrupts for GPIO (EXTI register) having the same numbers but on different ports.
  A comment specifically says the buttons should be interrupts.
    * See [this issue](https://github.com/zephyrproject-rtos/zephyr/issues/45234).
    * Issue with A8 rotative encoder pushbutton VS C8 rotary input B.
    * Issue with D14 rotary input A and E14 trig input.
* Capacitor C23 and C24 should be much lower than 100nF, 4.3pF for instance.
* STM32 NRST pin is not connected, not to a button, not to any debugger pin.
* STM32 Value, RefNum and LCSC number are for the 144 pin version (STM32H723\_Z\_GT instead
  of STM32H723\_V\_GT)
