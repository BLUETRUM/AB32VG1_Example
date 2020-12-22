介绍
=========

AB32VG1 开发板是以中科蓝讯 (Bluetrum) 公司推出的基于 RISC-V 架构的高配置芯片 AB32VG1 为核心所组成的。

+ CPU: AB32VG1 (LQFP48 封装，主频 120M ，片上集成 RAM 192K, flash 4Mbit，ADC，PWM，USB，UART，IIC 等资源)
+ 搭载蓝牙模块
+ 搭载FM模块
+ 一路 TF Card 接口
+ 一路 USB 接口
+ 一路 IIC 接口
+ 一路音频接口 (美标 CTIA)
+ 六路 ADC 输入引脚端子引出
+ 六路 PWM 输出引脚端子引出
+ 一个全彩 LED 灯模块，一个电源指示灯，三个烧录指示灯
+ 一个 IRDA (红外接收端口)
+ 一个 Reset 按键，三个功能按键 (通用版为两个功能按键)
+ 板子规格尺寸：6cm * 9cm
+ I/O口通过 2.54MM 标准间距引出，同时兼容 Arduino Uno 扩展接口，方便二次开发

**开发板目前情况**

目前适配了 rt-thread RTOS ，提供了 pin、uart 和 audio 的实现， `BSP <https://github.com/RT-Thread/rt-thread/tree/master/bsp/bluetrum/ab32vg1-ab-prougen>`_ 目前已经提交到 github

下面是提供的示例

rt-thread
------------

+ :ref:`从内部 Flash 读取 WAV 音频播放` 


