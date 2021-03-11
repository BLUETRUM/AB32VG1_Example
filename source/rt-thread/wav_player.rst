===============================
从内部 Flash 读取 WAV 音频播放
===============================

这个文档是基于 1.0.1 的 BSP 的，使用新的版本可能存在问题，下面列出一些已知问题的解决方法的链接，后期会更新文档

+ https://club.rt-thread.org/ask/article/2595.html

实验准备
========

实验前需要下载

+ `rt-thread studio 安装包 <https://www.rt-thread.org/page/studio.html>`_ 
+ `Downloader(下载软件) <https://gitee.com/bluetrum/Downloader/blob/main/Downloader_v1.9.7.zip>`_ 
+ `配套的 USB 转串口驱动 <https://gitee.com/bluetrum/Downloader/blob/main/CP210x_Windows_Drivers.rar>`_

rt-thread studio 安装
-----------------------

首先需要确保已经安装 rt-thread studio 

在工具栏找到 SDK 管理器，点击后在弹出窗口，**Board_Support_Packages** -> **Bluetrum_AB32VG1-ab-prougen** ,勾选，安装资源包，至此可以在rt-thread studio基于AB32VG1做开发了

.. image:: images/studio_0.png
   :align: center

还需要在 SDK 管理器中安装 riscv 的工具链，否则无法编译

Downloader 安装
----------------

我们是使用 Downloader 进行程序的固件下载的，编译出来的固件后缀为.dcf,该文件位于工程Debug目录下。 Downloader 软件需要安装自己的 USB 转串口驱动，如果驱动不匹配，会报下面的这个错误，这时需要安装配套的 USB 转串口驱动 。

.. image:: images/download_0.png
   :align: center

安装完驱动后，切换到我们的驱动，具体操作如下图

.. image:: images/download_error_fix.gif
   :align: center

如何希望能够编译后自动下载，需要在 **Downloader** 中的下载的下拉窗中选择 **自动**

.. image:: images/download_1.png
   :align: center

实验操作
=========

studio 新建工程
----------------

打开studio，如下图所示，新建工程。

.. image:: images/studio_1.png
   :align: center

选择 `基于开发板`，然后选择 `AB32VG1-AB-PROUGEN`

.. image:: images/studio_2.png
   :align: center

编译
-----

单击编译按键，编译工程，如下图所示。

.. image:: images/studio_3.png
   :align: center

软件包安装
-----------

本次实验实现音乐播放功能，单击按键进行音乐切换。需要安装的软件包有 wavplayer/optparse/multibutton 三个软件包。其中 optparse 在 wavplayer 勾选后，自动选择。

进入软件包选择界面.

.. image:: images/wav_player/1.png
   :align: center

.. image:: images/wav_player/2.png
   :align: center

也可以通过`更多配置`查看所有软件包来选择个软件包：

wavplayer 软件包安装
---------------------

.. image:: images/wav_player/3.png
   :align: center

multibutton 软件包安装
----------------------

.. image:: images/wav_player/4.png
   :align: center


保存,下载软件包到工程
----------------------
软件包选择完成后，点击 保存 按钮，将配置保存并应用到工程中。保存的时候会弹出进度提示框，提示保存进度，会自动下载到 package 目录下。


.. image:: images/studio_5.png
   :align: center

demo编写
---------

安装完 wavplayer/optparse/multibutton 三个软件包之后，就完成此次试验所需要的依赖的软件包。接下来开始编写demo。

首先需要下载 romfs.c（本文件包含了两个音频文件用于demo播放） 替换 applications 下原有的 romfs.c 

:download:`romfs.c <others/romfs.c>`

然后在 applications 下新建 event_async.c 文件，复制以下代码

.. code-block:: c
    :linenos:

    #include <rtthread.h> 
    #include <rtdevice.h>
    #include "board.h"
    #include <multi_button.h>
    #include "wavplayer.h"

    #define BUTTON_PIN_0 rt_pin_get("PF.0")
    #define BUTTON_PIN_1 rt_pin_get("PF.1")

    #define NUM_OF_SONGS    (2u)

    static struct button btn_0;
    static struct button btn_1;

    static uint32_t cnt_0 = 0;
    static uint32_t cnt_1 = 0;

    static char *table[2] =
    {
        "wav_1.wav",
        "wav_2.wav",
    };

    void saia_channels_set(uint8_t channels);
    void saia_volume_set(rt_uint8_t volume);
    uint8_t saia_volume_get(void);

    static uint8_t button_read_pin_0(void) 
    {
        return rt_pin_read(BUTTON_PIN_0);
    }

    static uint8_t button_read_pin_1(void) 
    {
        return rt_pin_read(BUTTON_PIN_1);
    }

    static void button_0_callback(void *btn)
    {
        uint32_t btn_event_val;

        btn_event_val = get_button_event((struct button *)btn);

        switch(btn_event_val)
        {
        case SINGLE_CLICK:
            if (cnt_0 == 1) {
                saia_volume_set(30);
            }else if (cnt_0 == 2) {
                saia_volume_set(50);
            }else {
                saia_volume_set(100);
                cnt_0 = 0;
            }
            cnt_0++;
            rt_kprintf("vol=%d\n", saia_volume_get());
            rt_kprintf("button 0 single click\n");
        break; 

        case DOUBLE_CLICK:
            if (cnt_0 == 1) {
                saia_channels_set(1);
            }else {
                saia_channels_set(2);
                cnt_0 = 0;
            }
            cnt_0++;
            rt_kprintf("button 0 double click\n");
        break; 

        case LONG_RRESS_START:
            rt_kprintf("button 0 long press start\n");
        break; 

        case LONG_PRESS_HOLD:
            rt_kprintf("button 0 long press hold\n");
        break; 
        }
    }

    static void button_1_callback(void *btn)
    {
        uint32_t btn_event_val;
        
        btn_event_val = get_button_event((struct button *)btn);
        
        switch(btn_event_val)
        {
        case SINGLE_CLICK:
            wavplayer_play(table[(cnt_1++) % NUM_OF_SONGS]);
            rt_kprintf("button 1 single click\n");
        break; 

        case DOUBLE_CLICK:
            rt_kprintf("button 1 double click\n");
        break; 

        case LONG_RRESS_START:
            rt_kprintf("button 1 long press start\n");
        break; 

        case LONG_PRESS_HOLD:
            rt_kprintf("button 1 long press hold\n");
        break; 
        }
    }

    static void btn_thread_entry(void* p)
    {
        while(1)
        {
            /* 5ms */
            rt_thread_delay(RT_TICK_PER_SECOND/200);
            button_ticks(); 
        }
    }

    static int multi_button_test(void)
    {
        rt_thread_t thread = RT_NULL;

        /* Create background ticks thread */
        thread = rt_thread_create("btn", btn_thread_entry, RT_NULL, 1024, 10, 10);
        if(thread == RT_NULL)
        {
            return RT_ERROR; 
        }
        rt_thread_startup(thread);

        /* low level drive */
        rt_pin_mode  (BUTTON_PIN_0, PIN_MODE_INPUT_PULLUP); 
        button_init  (&btn_0, button_read_pin_0, PIN_LOW);
        button_attach(&btn_0, SINGLE_CLICK,     button_0_callback);
        button_attach(&btn_0, DOUBLE_CLICK,     button_0_callback);
        button_attach(&btn_0, LONG_RRESS_START, button_0_callback);
        button_attach(&btn_0, LONG_PRESS_HOLD,  button_0_callback);
        button_start (&btn_0);

        rt_pin_mode  (BUTTON_PIN_1, PIN_MODE_INPUT_PULLUP); 
        button_init  (&btn_1, button_read_pin_1, PIN_LOW);
        button_attach(&btn_1, SINGLE_CLICK,     button_1_callback);
        button_attach(&btn_1, DOUBLE_CLICK,     button_1_callback);
        button_attach(&btn_1, LONG_RRESS_START, button_1_callback);
        button_attach(&btn_1, LONG_PRESS_HOLD,  button_1_callback);
        button_start (&btn_1);

        return RT_EOK; 
    }
    INIT_APP_EXPORT(multi_button_test); 

程序下载
---------

demo编写完成后，单击编译按钮开始编译，编译成功后下载编译后生成的 `.dcf` 固件到芯片；

双击打开Downloader v1.9.7。

.. image:: images/wav_player/5.png
   :align: center

下载成功后会在串口界面打印"Hello World"， 并会有led灯闪烁

.. image:: images/wav_player/6.png
   :align: center

此时按下按键S2，会播放第一首音乐，再次按下，播放下一首音乐，依次循环。

.. image:: images/wav_player/7.png
   :align: center

