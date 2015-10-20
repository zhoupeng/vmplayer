


# vmplayer 介绍 #

> vmplayer是中科院软件所基础软件国家工程研究中心的操作系统研究室目前正在进行的一个虚拟机全系统执行过程记录和重放项目。它参考了Revirt项目，重新设计了软件架构，支持记录与重放的分离，并在Xen 3.3.0以上版本中实现。


# Revirt 介绍 #

> Revirt 是由美国密歇根大学的Peter Chen教授带领的团队发起的项目。它是基于虚拟机技术实现的重放系统。

> 它的基本思想是记录一个虚拟机系统在某个时刻的状态，并且记录自这个时刻开始在这个虚拟机系统中发生的所有不确定性事件。将这个状态和所有不确定性事件写入日志文件。以后就可以根据这个日志文件对这个虚拟机进行全系统重放。

> Revirt系统最先在UMLinux虚拟机上实现，并基于此做了许多实验。2005年到2006年间他们又在Xen上实现了Revirt系统(简称Xen-Revirt)，Xen-Revirt实现了在多处理器系统上进行确定性重放(系统简称SMP-Revirt)。

> 遗憾的是Xen-Revirt自2006年后没有进一步更新与完善，Xen-Revirt是基于Xen2.0到Xen3.0开发版进行开发，而现在Xen已经发生了很大变化，并且Xen-Revirt 缺少架构概述和使用文档。



# 应用前景 #

  * 全系统确定性重放

  * 操作系统和应用程序调试

  * 主动审计

  * 基于事件迁移

  * 容错与可信恢复

  * 计算机取证

  * 入侵检测

# 国内相关工作 #

  * 华中科技大学 “计算系统虚拟化基础理论与方法研究”973项目

# 相关工作比较 #

# 相关论文 #

  * Revirt: Enabling Intrusion Analysis through Virtual-Machine Logging and Replay, Proceedings of the 2002 Symposium on Operating Systems Design and Implementation

  * Execution Replay for Multiprocessor Virtual Machines, 2008 ACM

  * XenLR: Xen-based Logging for Deterministic Replay, 2008 Japan-China Joint Workshop on Frontier of Computer Science and Technology

  * Live Migration of Virtual Machine Based on Full System Trace and Replay, 2009 High-Performance Distributed Computing

  * 更多相关论文 http://www.eecs.umich.edu/~pmchen/papers/

# 相关链接 #

  * Revirt 主页 http://www.eecs.umich.edu/virtual/

# 关于我们 #

> 操作系统研究室是基础软件国家工程研究中心下属的第一个研究室，研究室目前有职工3人，博士生3人，硕士生6人，研究室负责人是武延军副研究员。研究室目前主要的研究方向是操作系统与虚拟化技术，目前已经成功实现了对Ether、Revirt、Remus等著名虚拟化研究项目在Xen高版本（>3.3.0）的移植和改进，正在开发HVM全系统重放、面向虚拟平台的轻量级操作系统等研究工作。
> 同时，研究室在操作系统安全与可信计算方面也有大量实践积累，通过与中兴集团国民技术半导体公司合作，成功实现了国内首个基于TCM芯片的信任链系统。
