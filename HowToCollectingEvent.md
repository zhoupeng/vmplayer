

# Introduction #

采集不同的事件是通过在适当位置安装钩子实现的，下面以RDTSC为例子分析下事件记录采集过程。


# Details #

> ## 基本思路 ##


  1. 把RDTSC设置成特权指令，这样RDTSC只能在ring0执行
  1. 当RDTSC在guest os执行时就会触发通用保护异常(GPF)
  1. 异常被xen hypervisor 捕获
  1. GPF 异常处理函数被调用(Xen中就是do\_general\_protection)
  1. 在Xen hypervisor中对RDTSC进行模拟执行，将执行结果返回给guest os
  1. guest os 继续执行RDTSC指令的下一条指令

以上整个过程对于guest os透明。

通过对RDTSC分析，我们需要在第5步添加钩子点收集RDTSC模拟执行结果，在RDTSC指令模拟执行的结果返回前生成RDTSC事件记录，并将事件记录插入replay queue，事件记录通过Record架构最终被写入日志文件(这个过程可以参看 http://code.google.com/p/vmplayer/wiki/IntroducetoRecordArchitecture )。

> ## 注意的问题 ##

  * RDTSC模拟执行后它的执行性能会显著下降，而RDTSC指令在系统中的使用很频繁

  * 如果Dom0启动前把RDTSC设置成特权指令，则系统启动失败

> 为解决上面问题我采取的方法是只有被监控的guest os要被调入时才设置RDTSC为特权指令，当被监控的guest os被调出时取消设置，对于Dom0和idle domain从不设置。

> ## 详细分析 ##

  * 设置RDTSC特权指令

> 在context\_switch函数中通过指令set\_in\_cr4(X86\_CR4\_TSD)把RDTSC设为特权指令，具体请看提交的patch代码结合上面的基本思路描述可能会更明白。

  * 捕获RDTSC的代码流程(请看xen3.3.0被patch后的代码)

> 被监控guest os中执行RDTSC

> --> 触发GPF异常

> -->do\_general\_protection  // arch/x86/traps.c
> > -->emulate\_privileged\_op// arch/x86/traps.c，在这里调用钩子函数do\_tsc\_fixup
> > > -->do\_tsc\_fixup(v, regs)// arch/x86/x86\_32/seg\_fixup.c
> > > > -->report\_monitored\_rdtsc//它接下调用子函数收集RDTSC执行结果，准备一条记录；这些子函数是Record框架通用代码不再赘述


> -->report\_monitored\_rdtsc\_finish //接下来调用子函数把数据插入replay queue以及发送virq事件；（ 同上 ）

> 上面粗线条的分析了下流程，详细情况可以参照这个线索看下具体代码。