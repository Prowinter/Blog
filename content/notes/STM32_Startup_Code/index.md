+++
title =  "STM32启动分析"
date = "2022-11-07"
weight = 2
categories = [
]
tags = [
  "STM32"
]
+++

为什么程序启动会进入main()函数中？堆栈在什么时候，什么位置进行初始化？本节将利用ARM提供的官方文档分析理解上述问题。(ARM Compiler v4.1)

<!--more-->

> 正常情况下中断应处于Handler模式，为什么Reset_Handler却处于Thread线程模式？`Execution restarts as privileged execution in Thread mode，异常重新作为特权级线程模式执行。`[Exception types](https://developer.arm.com/documentation/dui0552/a/the-cortex-m3-processor/exception-model/exception-types)

- [DAI0241B_startup_code_for_arm_compiler](https://developer.arm.com/documentation/dai0241/latest)

## 启动文件简介
由启动文件内介绍可知，系统上电复位后第一个执行的程序，主要完成以下工作(不同编译环境下可能存在部分差异):
1. Set the initial SP `(SP->0x0000 0000)`
2. Set the initial PC == Reset_Handler `(PC->0x0000 0004)`
3. Set the vector table entries with the exceptions ISR address
4. Configure the clock system   
5. Branches to main in the C library (which eventually calls main()).
   
### 映射配置表
| BOOT0 | BOOT1 | 映射到的存储器 | 0x0000 0000 | 0x0000 0004
|:-:|:-:|:-:|:-:|:-:
| x | 0 | 内部FLASH | 0x0800 0000 | 0x0800 0004
| 1 | 1 | 内部SRAM  | 0x2000 0000 | 0x2000 0004
| 0 | 1 | 系统存储器 | 0x1FFF B000 | 0x1FFF B004

```armasm  {title="keil/startup_stm32f103xb.s"}
; Reset handler
Reset_Handler    PROC
                 EXPORT  Reset_Handler             [WEAK]
     IMPORT  __main
     IMPORT  SystemInit
                 LDR     R0, =SystemInit ;KEIL内部封装
                 BLX     R0
                 LDR     R0, =__main
                 BX      R0
                 ENDP
```

## 有哪些方法进行堆栈初始化?
* Calling __user_setup_stackheap. This also obtains the bounds of the memory used by the heap (heap top and heap base).
* Loading the SP with the value of the symbol __initial_sp.
* Using the top of the ARM_LIB_STACK or ARM_LIB_STACKHEAP region specified in the linker scatter file.


## 何时进行堆栈初始化？
常规来说0x0000 0000指向的中断向量表__initial_sp，故在Reset_Handler之前可以进行堆栈初始化，也可在Reset_Handler内使用自定义函数__user_setup_stackheap进行堆栈初始化。


## __main()?
为什么启动代码中调用的是__main()，而不是直接调用main()？__main()是C库的入口点，__main()可以拆分为下:

  - [__scatterload](#__scatterload)
  - [__rt_entry](#__rt_entry)
    - [_platform_pre_stackheap_init](#_platform_pre_stackheap_init)
    - [__user_setup_stackheap](#__user_setup_stackheap)
    - [_platform_post_stackheap_init](#_platform_post_stackheap_init)
    - [__rt_lib_init](#__rt_lib_init)
    - [_platform_post_lib_init](#_platform_post_lib_init)
    - [main()](#main)
    - [exit()](#exit)

### __scatterload

* Initializes the Zero Initialized (ZI) regions to zero
* Copies or decompresses the non-root code and data region from their load-time locations to the execute-time regions.

### __rt_entry
初始化堆栈，调用C库子系统。

### _platform_pre_stackheap_init
标准C库不提供该函数，若需要可定义，该函数在堆栈初始化前执行。

### __user_setup_stackheap
该函数允许你设置堆栈和返回堆栈地址。

### _platform_post_stackheap_init
标准C库不提供该函数，若需要可定义，该函数在堆栈初始化后执行。

### __rt_lib_init
初始化各类C库子系统，包含以下函数功能：

  -  _fp_init
  -  _init_alloc
  -  _rand_init
  -  _get_lc_collate
  -  _get_lc_ctype
  -  _get_lc_monetary
  -  _get_lc_numeric
  -  _get_lc_time
  -  _atexit_init
  -  _signal_init
  -  _fp_trap_init
  -  _clock_init
  -  _getenv_init
  -  _initio
  -  _ARM_get_argv
  -  _alloca_initialize
  -  _ARM_exceptions_init
  -  __cpp_initialize__aeabi_

### _platform_post_lib_init
标准C库不提供该函数，若需要可定义，该函数在__rt_lib_init后，main()函数前执行。

### main()
进入主main函数，寄存器R0，R1作为参数传入main函数中。

### exit()
如果main函数返回，返回值传入exit()函数，最后程序退出。

## 附录
以下共提供两种Keil和ARM GCC的启动代码，Keil及IAR部分代码封装在IDE内部，详细调用请使用调试工具分析汇编代码，为获得更好的阅读效果,请复制至专业代码工具中分析。
###  KEIL
```armasm {title="keil/startup_stm32f103xb.s"}
aaa
```
### ARM_GCC
```armasm {title="arm_gcc/startup_stm32f103xb.s"}
bbb
```