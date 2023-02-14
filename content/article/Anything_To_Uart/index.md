+++
title =  "Anything To Uart"
date = "2022-07-05"
weight = 10
categories = [
]
tags = [
  "article", 
]
images = [
  "images/index.jpg"
]

+++
主控采用ESP32，成本仅需50元即可实现任意USB设备(游戏手柄,键盘,鼠标)转串口输出,可方便后续拓展其他功能,同时也可利用ESP32实现信息的无线传输。
<!--more-->
> [GitHub仓库链接(软硬件)](https://github.com/Prowinter/Anything-To-Uart)
> 
> [USB_Host_Shield_2.0](https://github.com/felis/USB_Host_Shield_2.0)
> 
![Center](/images/index.jpg?width=500px&height=300px#center)

## 系统框图

> 由于MAX3421E单芯片价格过高,可选择低廉的USB Host Shield模块拆解。注意与卖家确定模块芯片是否为对应型号。

![Center](/images/block_diagram.png#center)


## MAX3421E介绍

> [MAX3421E DataSheet](https://datasheets.maximintegrated.com/en/ds/MAX3421E.pdf)

The MAX3421E USB peripheral/host controller contains the digital logic and analog circuitry necessary to implement a full-speed USB peripheral or a full-/low- speed host compliant to **USB specification rev 2.0.** A built-in transceiver features ±15kV ESD protection and programmable USB connect and disconnect. An inter- nal serial interface engine (SIE) handles low-level USB protocol details such as error checking and bus retries. **The MAX3421E operates using a register set accessed by an SPI interface that operates up to 26MHz.** Any SPI master (microprocessor, ASIC, DSP, etc.) can add USB peripheral or host functionality using the simple 3- or 4- wire SPI interface.

## 注意事项

1、硬件ESP32模块WI-FI朝向考虑实际需求并不需要过多的使用WI-FI功能故放置朝向并不符合正规要求，若影响使用，请自行修改！

2、USB_Host_Shield_Library只提供了部分手柄有线和蓝牙适配器驱动，若想适配全部蓝牙设备只需要购买一个免驱的拓展蓝牙接收机即可。蓝牙手柄自带的蓝牙接收机协议没有解析完全。

