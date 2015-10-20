软中断事件记录机制：
/**Set to indicate that a domtime should happen in softirq time**/

1) 设定软中断处理函数
> open\_softirq(DELAYED\_MONITOR\_EVENTS\_SOFTIRQ, check\_delayed\_events\_softirq);
2) xen调度
> schedule(void)  //schedule.c
3) 每次调度过程，会调用 update\_dom\_time()，更新idle domain时间
> v->replay->replay\_flags |= REPLAYFLAG\_DELAY\_DOMTIME; 设定replay\_flags,bit 16
4) if(test\_bit(_VCPUF\_monitored, &next->vcpu\_flags)
> > && (next->replay->replay\_flags & (REPLAYFLAG\_DELAY\_SPEND|REPLAYFLAG\_DELAY\_DOMTIME)))

> =>raise\_softirq(DELAYED\_MONITOR\_EVENTS\_SOFTIRQ);
5) 软中断处理函数根据中断号触发软中断处理函数，根据条件不同可能触发两类事件处理 a)RECORD\_EVT\_DOMTIME b)RECORD\_EVT\_EVTCHN\_SET\_PENDING
> > check\_delayed\_events\_softirq(void) 进行条件判断并进行相应更新处理
6)_update\_dom\_time(v); RECORD\_EVT\_DOMTIME事件处理

> if(v->domain->shared\_info->tsc\_timestamp != full\_tsc\_irq)
> > 进行对称多处理相应操作，并记录RECORD\_EVT\_DOMTIME事件，此处需要严格的锁机制
7) 获取事件的相应字段，并写入vcpu事件存储队列

关于事件队列：每个VCPU分配1000/nvcpu个页面的事件存储页面，domU设定vcpus=4,则4个vcpu事件队列都会初始化，否则只会初始化vcpu 0事件队列