链接文件介绍
============

在 AB32VG1 的 SDK 中，可以看到一个 link.lds 文件，这个文件就是链接文件

在文件开始的地方，可以看到几个定义好的大小

.. code-block:: c

    __data_ram_size = 8k;
    __stack_ram_size = 4k;
    __comm_ram_size = 42k;
    __heap_ram_size = 70k;

这几个大小可以根据自己的需要进行调整，但是需要确保这几个 size 加起来的总和是一定的。一般比较常见的是 data 段的空间不够，导致的在编译的时候报错，一般可以通过减小 comm 段或 heap 的大小给到 data 段解决
