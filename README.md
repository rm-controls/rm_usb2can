# rm_usb2can

![Version](https://img.shields.io/badge/Version-2.0.1-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

English/[中文](https://github.com/rm-controls/rm_usb2can/blob/main/README_CN.md)

***

### Brief Introduction

&nbsp;&nbsp;&nbsp;&nbsp;The purpose of this project is to provide [Intel® NUC](https://www.intel.com/content/www/us/en/products/details/nuc.html) and other x86 device with a CAN (Controller Area Network) peripheral interface. Since NUC and most x86 device does not have simple peripheral interfaces such as SPI (Serial Peripheral Interface), it is impossible to use SPI to can chips such as MCP2515. Therefore, the project is developed based on the open source solution [candleLight](https://github.com/candle-usb/candleLight_fw/tree/master) on GitHub, and STM32F072CBT6 is used as the main control chip to realize the function of USB (Universal Serial Bus) to can. STM32F072CBT6 has USB full speed peripherals and can peripherals that can work at the same time. It is packaged as QFP48 (Quad Flat Package) and is an excellent solution.The following figure shows the physical connection between Intel NUC and usb2can.

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/nuc_with_usb2can.jpg)

***

### Develop Tools

+ EDA Tool: KiCAD 6.0.9
+ Compiler: gcc-arm-none-eabi 10.3.1 20210824 (release)
+ Programming Tool: STM32CubeProgrammer v2.13.0

***

### Directory Structure

+ manufacture: USB2CAN production manufacturing file.
+ program: Github open source program [candleLight](https://github.com/candle-usb/candleLight_fw/tree/master) modified source code.
+ circuit: Circuit schematic and PCB source files designed with KiCAD.
+ image: Images of README.

***

### Circuit Design

#### Key Points Of Schematic Design

&nbsp;&nbsp;&nbsp;&nbsp;USB HUB Circuit Design: since the whole vehicle needs at least two can buses to ensure the integrity of the data packet returned by the motor, we use SL2.1s as the USB HUB chip to realize the scheme of USB one driving four. In addition, we also use Schmidt trigger to realize the power on sequence to ensure that the enumeration sequence of USB devices is consistent after each power on.

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/usb_hub.png)

&nbsp;&nbsp;&nbsp;&nbsp;USB To CAN Circuit Design: use STM32F072CBT6 to realize the function of USB to can. The CAN level conversion chip uses the TJA1050 chip, which uses a 5V power supply and greatly reduces the probability of CAN transceiver damage due to the chip's higher common mode withstand voltage. The purpose of R6 and R7 resistors in the figure is to change the boot mode of STM32, so that STM32 can switch between DFU (Device Firmware Upgrade) burning mode and flash mode by changing the short circuit mode of R6 and R7, so as to facilitate firmware burning.

![stm32_can](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_can.png)

#### Key Points Of PCB Layout And Wiring

1. All USB buses need to be set as differential pairs, and the wiring rules of differential signals shall be strictly followed during wiring.
2. All CAN buses need to be set as differential pairs, and the wiring rules of differential signals shall be strictly followed during wiring.
3. The bottom of crystal oscillator and resonant capacitor should be hollowed out without copper.
4. The power line width shall be greater than 0.3mm as far as possible to ensure that the power track does not collapse.

***

### Circuit Making

&nbsp;&nbsp;&nbsp;&nbsp;We provide Gerber files that can be used directly for manufacturing in the [manufacturer folder](https://github.com/rm-controls/rm_usb2can/tree/main/manufacture). This version can realize one way USB to 2 way CAN + 2 way UART, and its circuit schematic is shown in the [circuit folder](https://github.com/rm-controls/rm_usb2can/tree/main/circuit). You can submit the packaged Gerber file directly to a manufacturer (e.g., [PCB Way](https://www.pcbway.com/)) for production.

&nbsp;&nbsp;&nbsp;&nbsp;You can also find the corresponding BOM (Bill Of Material) table for the board in the [manufacturer folder](https://github.com/rm-controls/rm_usb2can/tree/main/manufacture) and solder the components to the already made PCB according to this table. After the soldering is complete, please carefully check whether there is residual solder between the pins, that will lead to a short circuit on the board, and you also have to check whether the pins have the phenomenon of false solder.

&nbsp;&nbsp;&nbsp;&nbsp;After welding, use a multimeter to check whether there is a short circuit between 5V power interface and GND, and whether there is a short circuit between 3.3V power output and GND. When the above situation does not occur, connect the board to the USB port of the computer. At this time, you should see that the power indicator on the board is on, and the computer can recognize the USB HUB chip.

**Note: do not weld the up / down resistance used to switch STM32 download mode at the same time, as it may cause a short circuit between the power supply and GND!**

***

### Firmware Programming

&nbsp;&nbsp;&nbsp;&nbsp;You can download the compiled firmware in [Release](https://github.com/rm-controls/rm_usb2can/releases) page, or follow the [candlelight documentation](https://github.com/candle-usb/candleLight_fw/tree/master#building) to compile firmware by yourself. Finally, you will get the .bin file. We use [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html) to burn the firmware into STM32 through USB. The steps to download firmware are shown below:

1. Remove the pull-down resistance of BOOT0 pin of STM32 and weld the pull-up resistance.
2. Connect the board to the USB port of the computer and open STM32CubeProgrammer.
3. Click the three buttons shown in the following figure in STM32CubeProgrammer to search for available STM32 devices.

![stm32_programer_1](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_1.png)

4. After successful connection, click the "Read" button to read the firmware and select the prepared firmware.

![stm32_programer_2](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_2.png)

5. Click "Download" to download the firmware. After the download is successful, you can see the successful information.

![stm32_programer_3](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_3.png)

6. Remove the pull-up resistance of BOOT0 pin on STM32 and weld the pull-down resistance. Power on again at this time, and the firmware will start running on STM32.

***

### Test Features On Ubuntu

1. Install dependent packages:

```bash
$ sudo apt-get update && sudo apt-get -y upgrade
$ sudo apt-get install -y can-utils net-tools
```

2. Check whether can device is detected:

```bash
$ ifconfig -a
# If a device with can name is found in the list, it means that the device can be recognized
```

3. Connect the two can ports with a **straight line** to facilitate loop back test.
4. Initialize CAN0 and CAN1 devices with a bit rate of 1Mbits:

```bash
$ sudo ip link set can0 up type can bitrate 1000000
$ sudo ip link set can1 up type can bitrate 1000000
```

5. Open a new terminal to monitor the information received by can0:

```bash
$ candump can0
```

6. Sending information using can1 in a terminal:

```bash
$ cansend can1 200#5A5A5A5A5A5A5A5A
```

If you can see the following information from can1 in the terminal monitoring can0, it indicates that the communication is successful and the module works normally.

```bash
can0 200 [8] 5A 5A 5A 5A 5A 5A 5A 5A
```

***

### License

The source code is released under a [MIT License](https://github.com/rm-controls/rm_usb2can/blob/main/LICENSE).

**Affiliation：DynamicX<br>
Maintainer：YanzhenZhu, 2208213223@qq.com**

The product has been tested under Ubuntu 18.04, 20.04 and 22.04. This is research code, expect that it changes often and any fitness for a particular purpose is disclaimed.