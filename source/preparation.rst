.. _Experiment preparation:

实验准备
========================

实验前需要下载

+ `rt-thread studio 安装包 <https://www.rt-thread.org/page/studio.html>`_ 
+ `Downloader(下载软件) <https://gitee.com/bluetrum/AB32VG1_DOC/blob/master/tools/Downloader_v2.2.0.zip>`_ 
+ `配套的 USB 转串口驱动(V1 版本专用) <https://gitee.com/bluetrum/AB32VG1_DOC/blob/master/tools/CP210x_Windows_Drivers.rar>`_

rt-thread studio 安装
--------------------------

首先需要确保已经安装 rt-thread studio 

在工具栏找到 SDK 管理器，点击后在弹出窗口，**Board_Support_Packages** -> **Bluetrum_AB32VG1-ab-prougen** ,勾选，安装资源包，至此可以在rt-thread studio基于AB32VG1做开发了

.. image:: rt-thread/images/studio_0.png
   :align: center

还需要在 SDK 管理器中安装 riscv 的工具链，否则无法编译

.. image:: rt-thread/images/studio_6.png
   :align: center

Downloader 安装
--------------------

我们是使用 Downloader 进行程序的固件下载的，编译出来的固件后缀为 .dcf,该文件位于工程Debug目录下。

目前的开发板都是 V2 版本了，V2 版本的开发板使用的串口芯片是 CH340，可以直接使用系统自动安装的驱动。

V1 版本的开发板使用 Downloader 软件，需要安装特定版本的 USB 转串口驱动，如果驱动不匹配，会报下面的这个错误，这时需要安装配套的 USB 转串口驱动 。

.. image:: rt-thread/images/download_0.png
   :align: center

V1 版本的开发板使用的串口芯片是 CP2102 ，需要切换到我们的驱动，具体操作如下图

.. image:: rt-thread/images/download_error_fix.gif
   :align: center

如何希望能够编译后自动下载，需要在 **Downloader** 中的下载的下拉窗中选择 **自动**

.. image:: rt-thread/images/download_1.png
   :align: center

RT-Thread Studio IDE 使用的基础介绍
====================================

studio 新建工程
----------------

打开studio，如下图所示，新建工程。

.. image:: rt-thread/images/studio_1.png
   :align: center

选择 `基于开发板`，然后选择 `AB32VG1-AB-PROUGEN`

.. image:: rt-thread/images/studio_2.png
   :align: center

编译
-----

单击编译按键，编译工程，如下图所示。

.. image:: rt-thread/images/studio_3.png
   :align: center

实验操作
==========

+ :ref:`从内部Flash读取WAV音频播放`   
