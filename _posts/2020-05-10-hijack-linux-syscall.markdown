---
layout: post
title:  "使用 Loadable Kernel Module 劫持 Linux 系统调用"
date:   2020-5-3 13:00:00 +0800
categories: pytorch pylint vscode flake8
---

# 使用 Loadable Kernel Module 劫持 Linux 系统调用

## 参考

- [The Linux Kernel Module Programming Guide](http://tldp.org/LDP/lkmpg/2.6/html/index.html)
- [System Programming: Hijack Linux System Calls: Part III. System Call Table](https://syprog.blogspot.com/2011/10/hijack-linux-system-calls-part-iii.html)


## 系统环境

- OS: Deepin 15.11
- GCC: 6.3.0
- Kernel: 4.15.0-30deepin-generic

## 建立 Loadable Kernel Module

一个 Loadable Kernel Module 至少需要两个函数组成：`init_module` 和 `cleanup_module`。但通常可以通过 `linux/init.h`（Linux Kernel 5.x 中疑似被删除）中的两个宏 `module_init` 和 `module_exit` 来分别指定入口函数和出口函数。

可以通过 `MODULE_LICENCE` 宏来指定该模组所使用的证书，如 GPL。

可以通过 `MODULE_AUTHOR` 宏来指定该模组的作者。

可以通过 `MODULE_DESCRIPTION` 宏来指定该模组的描述。

几个宏的顺序无所谓。（根据有限的观察猜的）

```C
/* FILENAME: fucklinux.c */
#include <linux/init.h>
#include <linux/kernel.h>
#include <linux/module.h>

static int __init
fuck_init(void) {
    printk(KERN_INFO "Fuck Linux.\n");
}

static void __exit
fuck_cleanup(void) {
    printk(KERN_INFO "Fuck Linux again.\n");
}

module_init(fuck_init);
module_exit(fuck_cleanup);

MODULE_LICENCE("GPL");
MODULE_AUTHOR("fuckfuck");
MODULE_DESCRIPTION("a module fucks linux");
```

编译时可以使用以下简单的 Makefile

```Makefile
obj-m: fucklinux.o

UNAME=$(shell uname -r)

all:
    make -C /lib/modules/$(UNAME)/build M=$(PWD) modules

.PHONY: clean
clean:
    make -C /lib/modules/$(UNAME)/build M=$(PWD) clean
```


编译会获得一大堆文件，其中我们会经常用到的是 `fucklinux.ko`.

查看 module 信息
```sh
$ modinfo fucklinux.ko
```

安装 module
```sh
# insmod fucklinux.ko
```

卸载 module
```sh
# rmmod fucklinux
```

`printk` 的输出显然不会输出到 `stdout`，而是输出到了日志文件中。查看日志
```sh
$ dmesg
```

配合 tail 食用更香。

## 获得系统调用表（`sys_call_table`）

系统调用表就是一个装着函数指针的数组，也许是为了兼容 32 位软件，在 deepin 中似乎有两个系统调用表：`sys_call_table` 和 `ia32_sys_call_table`。

在上古版本中，内核中是有一个叫做 `sys_call_table` 的变量的，通过这个变量就可以得到系统调用表，然而时代变了，Linux 变现代了，这个变量也消失了。

现在的 Linux 中想要读取 `sys_call_table` 必须去特定的内存页读取。而每个机器每个系统这个地址都不一样。甚至可能每次重启都不一样，谁知道呢。

不过在启动的 Linux 中可以通过 `/proc/kallsyms` 读取这个变量的存放位置：
```sh
# grep sys_call_table /proc/kallsyms
```

未完待续。