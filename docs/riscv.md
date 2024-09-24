# RISC-V 指令集概述

RISC-V指令集的一个特点是**模块化**，其核心是一个名为 RV32I 的基础 ISA，可运行完整的软件栈。
RV32I 已冻结，永不改变，这为编译器开发者、操作系统开发者和汇编语言程序员提供了稳定的指令目标。
模块化特性源于可选的标准扩展，硬件可根据应用程序的需求决定是否包含它们。
这种模块化特性能设计出面积小、能耗低的 RISC-V 处理器，这对于嵌入式应用至关重要。
RISC-V 编译器得知当前硬件包含哪些扩展后，便可为该硬件生成最优代码。

一般约定将扩展对应的字母加到指令集名称之后，以指示包含哪些扩展。
例如，RV32IMFDA 在必选基础指令集（RV32I）上添加了乘法（RV32M），单精度浮点（RV32F）, 双精度浮点（RV32D）和原子指令（RV32A）扩展。

!!! note "通用标量指令"

	RV32MFDA 以及 Zicsr，Zifencei 是 RISC-V 的标准扩展，它们与 RV32I 统称为 RV32G（G 代表 general），即 RV32G = RV32IMAFDZicsr_Zifencei。

	如果你注意过 ``RISC-V gcc`` 中的 ``-march`` 选项，你可能会发现它形如 ``rv32imac_zba_zbb_zbs_zbc_zicsr_zifencei``。
	这是因为，除了标准扩展之外，还有其他可选扩展，例如 ``zb*`` 指的是 “B” 标准扩展（Bit-manipulation）。

RISC-V 支持变长指令，要求指令长度为 16 位的整数倍，与 16 位地址对齐。
对于 16 位的压缩指令集，其最低两位不能为 11；对于 32 位指令集，其最低两位固定为 11，第 [4:2] 位不能为 111。

硬件可以根据低位的若干位快速判断出指令的位宽，便于硬件电路的实现。

<figure>
  <img src="../figs/riscv_instr_length.png" width=90%>
  <figcaption>RISC-V Instruction Length Encoding</figcaption>
</figure>




----

RISC-V Privileged Instruction Set Architecture


RISC-V Instruction Set
^^^^^^^^^^^^^^^^^

.. note::



Privilege
^^^^^^^^^^^^^^^^^


`RISC-V 官方文档 <https://riscv.org/technical/specifications/>`__ 分为两大部分：非特权指令集（Unprivileged ISA）和特权指令集（Privileged ISA）。

Unprivileged ISA
#################

这部分定义了所有执行模式下都可以使用的基本指令集。
它包括整数、浮点、原子操作等基本指令，以及寄存器和基本的执行环境。
这些指令集构成了RISC-V程序的核心，是所有RISC-V兼容设备必须支持的基础。

Privileged ISA
#################

处理器的代码可以运行在不同等级的模式下，一般而言软件代码会默认运行在用户模式（*user mode*, U mode）下，该模式具有最低的权限。
除此以外，还有监管模式（*supervisor mode*, S mode）和机器模式（*machine mode*, M mode）。
操作系统一般运行在 S 模式下，而 M 模式则具有最高的特权，最重要的特性是拦截和处理异常（不寻常的运行时事件）。
在 M 模式下运行的代码能完全访问内存、I/O 和底层系统功能，这对启动和配置系统是必不可少的。
因此，M 模式是唯一一个所有标准 RISC-V 处理器都必须实现的特权模式。
应用程序（在U-mode下运行）通常无法直接执行特权指令，而是通过系统调用（Syscall）机制请求操作系统（在S-mode或M-mode下运行）提供服务，如访问硬件或管理资源。

.. note::

    实际上简单的 RISC-V 微控制器仅支持 M 模式。
	我们流片的 CPU 支持 U、S、M 模式，可以启动 Linux。


Control Status Register
^^^^^^^^^^^^^^^^^

体系结构的课程中一定会学习到寄存器堆（Register File），这些寄存器也被称为 GPR（General Purpose Register）。
实际上还有另一个“寄存器堆”——控制状态寄存器（CSR），它们被用来实现特权架构所带来的新特性，例如 :code:`mcause` 用于记录异常和中断的原因。
除了处理特权架构，还有一些 CSR 用于标识处理器特性或测量性能，例如 :code:`mcycle` 用于记录运行周期数。
CSR 记录了 CPU 当前的状态信息，因此对于仿真或者流片后验证都十分重要。