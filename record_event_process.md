record 事件通知机制目前主要有3种：
1）在系统调度上下文转换中，通过update\_dom\_time()触发软中断，通过相应的软中断相应函数传输并处理事件
2）通过监听xen事件通道/dev/xen/evtchn,调用相应的hypercall，通过底层的prepare\_event()传递并记录事件,这种机制主要适用于间接触发的事件，即是由DomU通过frontend driver调用backend driver触发的事件，具体分为两类，a)domU与dom0或其他dom的通信 b)domU事件的处理必须通过driver domain的协助
3）domU运行过程中直接调用hypervisor，在某些条件成立的情况下触发的事件