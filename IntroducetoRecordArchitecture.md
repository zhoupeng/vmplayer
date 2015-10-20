

# Introduction #

Record部分负责获取事件并生成日志文件，为Replay阶段准备数据。


# Details #

> ## 整体架构 ##
http://vmplayer.googlecode.com/files/RecordArchitecture.JPG

  * 绿色线条是事件记录生成与传输信道，这是通过设备文件和共享数据结构机制实现，事件体信息的采集通过钩子机制(这些钩子安装在hypervisor中对事件进行处理的函数里)，然后获取当前的时间点信息，接着把事件体和时间点信息合成事件记录，最后把事件记录投递到共享数据结构，共享数据结构在hypervisor看来就是被监控dom的重放队列(replay queue)；
  * 黑色线条是控制命令传输信道(通过设备文件和hypercall机制实现)；
  * 红色线条是同步事件传输信道(通过wait queue 和event channel机制实现)，具体而言就是当没有可读的事件记录时tool application会阻塞在wait queue上，接下来当重放队列获得足够多数据时hypervisor向Dom0发送virq，然后Dom0调用virq事件处理函数，事件处理函数唤醒阻塞在wait queue的tool application，tool application被唤醒后继续读取事件记录数据；
  * 这个事件记录与重放系统对DomU是透明的，这里用虚线表示。

> ## 共享数据结构 ##

http://vmplayer.googlecode.com/files/SharedDS.JPG

  * 共享数据结构内存由Dom0分配，被hypervisor共享，这些数据结构是通过循环队列进行管理。
  * 注：Buffers是Record\_event\_ring的一个字段，Buffers是unsigned long型数组，events是record\_event\_buffer的一个字段，是record\_event类型数组。这里为了清晰把这两个字段画在相应结构的外面。