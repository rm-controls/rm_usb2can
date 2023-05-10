# rm_usb2can

![Version](https://img.shields.io/badge/Version-2.0.1-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

[English](https://github.com/rm-controls/rm_usb2can/blob/main/README.md)/中文

***

### 简介

&nbsp;&nbsp;&nbsp;&nbsp;本项目的开发目的是为了给[英特尔® NUC](https://www.intel.cn/content/www/cn/zh/products/details/nuc.html)及其它x86架构的平台提供一个CAN (Controller Area Network)的外设接口。由于NUC及大部分x86不具有SPI等简单外设接口，故无法使用MCP2515等SPI (Serial Peripheral Interface) 转CAN芯片。为此，本项目基于github的开源方案[candleLight](https://github.com/candle-usb/candleLight_fw/tree/master)开发，采用STM32F072CBT6作为主控芯片，实现USB (Universal Serial Bus) 转CAN的功能。STM32F072CBT6具有能够同时工作的USB全速外设和CAN外设，封装为QFP48 (Quad Flat Package)，是较为优秀的解决方案。下图为Intel NUC与USB2CAN连接实物图。

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/nuc_with_usb2can.jpg)

***

### 开发工具

+ EDA工具： KiCAD 6.0.9
+ 编译工具： gcc-arm-none-eabi 10.3.1 20210824 (release)
+ 烧录工具： STM32CubeProgrammer v2.13.0

***

### 目录结构

+ manufacture：生产USB2CAN的生产制造文件。
+ program：Github开源方案[candleLight](https://github.com/candle-usb/candleLight_fw/tree/master)修改后的源码。
+ circuit：用KiCAD设计的电路原理图及PCB源文件。
+ image：README中的图片。

***

### 电路设计

#### 原理图设计要点

&nbsp;&nbsp;&nbsp;&nbsp;USB HUB电路设计：由于整车需要至少2路CAN总线来保证电机回传数据包的完整性，所以我们采用了SL2.1s作为USB HUB芯片实现USB一拖四的方案。另外，我们还通过使用施密特触发器来实现上电时序，保证USB设备枚举的顺序在每次上电后都是一致的。

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/usb_hub.png)

&nbsp;&nbsp;&nbsp;&nbsp;USB转CAN电路设计：用STM32F072CBT6实现USB转CAN功能。CAN电平转换芯片采用TJA1050芯片，该芯片使用5V供电，由于该芯片具有更高的共模耐压，极大地减小了CAN收发器损坏的概率。图中的R6、R7电阻用途为更改STM32的Boot模式，从而使STM32能够通过更改R6、R7的短接模式而在DFU (Device Firmware Upgrade) 烧录模式与Flash模式下切换，方便烧录固件。

![stm32_can](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_can.png)

#### PCB布局布线要点

1. 所有USB总线需要设置成差分对，在布线时严格遵循差分信号布线规则。
2. 所有CAN总线需要设置成差分对，在布线时严格遵循差分信号布线规则。
3. 晶振和谐振电容底部最好挖空不铺铜。
4. 电源线线宽应尽量大于0.3mm以保证电源轨道不塌陷。

***

### 电路制作

&nbsp;&nbsp;&nbsp;&nbsp;我们在[manufacture文件夹](https://github.com/rm-controls/rm_usb2can/tree/main/manufacture)中提供了可直接用于制造的Gerber文件用于生产，该版本可实现一路USB转2路CAN+2路UART，其电路原理图见[circuit文件夹](https://github.com/rm-controls/rm_usb2can/tree/main/circuit)。改电路采用两层PCB进行设计，面积约为58*35mm。你可以直接将打包好的Gerber文件提交个生产厂家（如：[嘉立创](https://www.jlc.com/#)）进行生产。

&nbsp;&nbsp;&nbsp;&nbsp;你还可以在[manufacture文件夹](https://github.com/rm-controls/rm_usb2can/tree/main/manufacture)中找到该板对应的BOM (Bill Of Material) 表，并且依据这张表格将元器件焊接到已经制作好的PCB上。焊接完成后，请仔细检查是否有引脚之间有残余的焊锡，那将会导致电路板发生短路，同时你还要检查引脚是否有虚焊的现象发生。

&nbsp;&nbsp;&nbsp;&nbsp;焊接完成后，用万用表检查5V电源接口与GND之间是否短路、3.3V电源输出与GND之间是否短路。当确保上述情况没有出现时，再将板子连接电脑的USB口，此时应该可以看到板上的电源指示灯亮起，并且电脑能够识别到USB HUB芯片。

**注意：请勿同时焊接用于切换STM32下载模式的上/下拉电阻，那样有可能使电源与GND之间出现短路！**

***

### 固件烧录

&nbsp;&nbsp;&nbsp;&nbsp;你可以在[Release](https://github.com/rm-controls/rm_usb2can/releases)中下载已经编译好的固件，或者是跟随[candleLight文档](https://github.com/candle-usb/candleLight_fw/tree/master#building)自行编译固件。最终你会得到.bin文件，我们使用[STM32CubeProgrammer](https://www.st.com/zh/development-tools/stm32cubeprog.html)来将固件通过USB烧录到STM32中。下面展示了下载固件的步骤：

1. 将STM32上BOOT0引脚的下拉电阻取下，焊接上上拉电阻。
2. 将板子连接到电脑的USB口中，打开STM32CubeProgrammer。
3. 在STM32CubeProgrammer中点击下图所示的三个按钮，搜寻可用的STM32设备。

![stm32_programer_1](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_1.png)

4. 连接成功后点击“Read”按钮以读取固件，选择准备好的固件。

![stm32_programer_2](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_2.png)

5. 点击“Download”以下载固件。下载成功后，可以看到成功的标识。

![stm32_programer_3](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_programer_3.png)

6. 将STM32上BOOT0引脚的上拉电阻取下，焊接上下拉电阻。此时再重新上电，固件将在STM32上开始运行。

***

### 在Ubuntu上测试功能

1. 安装依赖软件包：

```bash
$ sudo apt-get update && sudo apt-get -y upgrade
$ sudo apt-get install -y can-utils net-tools
```

2. 查看是否检测到CAN设备：

```bash
$ ifconfig -a
# 如果在列表中发现含有can名称的设备就说明该设备可以被识别到了
```

3. 用**直连线**将两个CAN口连接起来，便于进行回环测试。
4. 初始化CAN0和CAN1设备，比特率为1Mbits：

```bash
$ sudo ip link set can0 up type can bitrate 1000000
$ sudo ip link set can1 up type can bitrate 1000000
```

5. 打开一个新终端用于监测can0接收到的信息：

```bash
$ candump can0
```

6. 在一个终端中使用can1发送信息：

```bash
$ cansend can1 200#5A5A5A5A5A5A5A5A
```

若能在监测can0的终端中看到can1发来的信息如下，说明通信成功，模块工作正常。

```bash
can0 200 [8] 5A 5A 5A 5A 5A 5A 5A 5A
```

***

### 许可证

源代码根据[MIT条款许可证](https://github.com/rm-controls/rm_usb2can/blob/main/LICENSE)发布。

**组织：DynamicX <br>
维护人：朱彦臻, 2208213223@qq.com**

该产品已经在Ubuntu 18.04、20.04和22.04下进行了测试。这是一个研究代码，希望它经常更改，并且不承认任何特定用途的适用性。