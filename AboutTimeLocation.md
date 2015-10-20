# performance counter



> # 概念 #

  * 时间点(time location)： 它是在程序运行过程中，指令流的某个具体位置。它是一个动态的概念。

  * 具体而言，在Revirt中，时间点描述了一个事件发生的时间，在重放阶段需要在相同的时间点重放这个事件。

  * 在x86系统上时间点可以用3个属性唯一表示：EIP(指令指针，指令计数器)、BC(分支计数器，又可以称作性能计数器PC-performance counter)、ECX(ECX 寄存器)。

  * 需要特别说明的是为什么需要ECX，这是因为存在一些串操作指令，它们结合rep前缀可以重复执行直到ECX变成0，并且在重复执行期间EIP和BC没有变化，只有结合ECX才能在指令流中区分串操作指令的每一次执行。

  * 理解时需要注意的问题，在这个记录重放系统中，被监控的对象是一个完整的guest os，所以我们只需考虑这个guest os的指令流对时间点的贡献，而不需要考虑hypervisor以及其它guest os(如Dom0)对时间点的影响;另外时间点中的分支字段是自启动RECORD以来在被监控的guest os上分支计数器累加的值，即下一个事件记录的分支字段累加了上一个事件记录的分之字段的值。

> # 如何获取时间点 #

> ## EIP和 ECX ##

> EIP和 ECX的获取很简单，可以从guest os 的用户寄存器组直接读取。

> ## 如何获取分支计数器值 ##

> ### 关于分支计数器 ###

  * performance monitor是用于度量分析程序性能的硬件支持，分支计数(performance counter)是其中一个功能。目前Intel和AMD都提供了相应支持。

  * performance monitor在Intel不同处理器系列上不兼容，这里我只关心P4系列和core2系列，因为原始revirt是基于p4系列，我们的机器是core2系列。关于P4系列可以在intel 2007年手册上找到详细说明，关于core2系列在2007年手册上没有需要查看intel 2009年手册。

> ### 初始化设置 ###

  * 包括在intel P4系列和core2系列上需要对硬件进行的基本设置，其中core2部分在当前机器上使用，p4部分在原revirt上使用。
  * 在P4系列上主要涉及通过wrmsr指令对MSR\_TBPU\_ESCR0、MSR\_MS\_CCCR0和MSR\_MS\_COUNTER0寄存器访问。
  * 对于core2系列上主要涉及对IA32\_PERFEVTSEL0、IA32\_PMC0、IA32\_PERF\_GLOBAL\_CTRL和IA32\_PERF\_GLOBAL\_STATUS的访问。

> # 详细分析 #

> 请看文档 “时间点.pdf” http://code.google.com/p/vmplayer/downloads/detail?name=%E6%97%B6%E9%97%B4%E7%82%B9.pdf