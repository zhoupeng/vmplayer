# 文件整体结构 #

| **节区(Sections)** | **说明** |
|:-----------------|:-------|
| header           | 文件头    |
| domain内存页信息      | domain全部可用内存页信息 |
| shared\_info page | shared\_info页内容 |
| 内存内容             | Domain全部内存页内容 |
| M2P表信息           | 保存M2P表的大小、起始地址和块大小 |
| M2P表内容           | 指定domain的M2P表的全部内容 |

## 文件头 ##

定义见vmplayer/tools/replay/record.h
```
struct replay_file_header {
	unsigned magic;
	u32 dom;
	u32 flags;
	u32 shared_info_mfn;                            
	unsigned assist;                                    
	memory_t tot_pages;
	memory_t shared_info_frame;
	vcpu_guest_context_t ctxt;
};
```

| **字段** | **说明** |
|:-------|:-------|
| magic  | 0x11224433 |
| dom    | 被记录domain的ID号 |
| flags  | dominfo.flags(dom)状态信息 |
| shared\_info\_mfn | 没使用    |
| assist | 没使用    |
| tot\_pages | dominfo.tot\_pages(domain拥有的页数) |
| shared\_info\_frame | starting MFN of shared\_info struct |
| ctxt   | 0号虚拟cpu的上下文信息 |

## Domain内存页信息 ##

Domain全部可用内存页信息，共header.tot\_pages项，每项是pfn\_info结构。

pfn\_info结构定义在：vmplayer/xen/include/public/replay.h
```
struct pfn_info{
	uint64_t pfn;
	uint32_t type_and_count;
};
```

| **字段** | **说明** |
|:-------|:-------|
| pfn    | 内存页号   |
| type\_and\_count | 0-25:引用计数 |
|        | 26-31:类型信息 |

## 内存内容 ##

Domain的全部内存内容，共header.tot\_pages页

## shared\_info page ##

占一页空间

## M2P表信息 ##

M2P表信息存储在m2p\_info结构体中,定义在vmplayer/tools/replay/record.h

```
struct m2p_info {
	uint32_t mfn0;
	uint32_t size;
	uint32_t chunks;
}; 
```

| **字段** | **说明** |
|:-------|:-------|
| mfn0   | M2P表的起始物理页号 |
| size   | M2P表的大小 |
| chunks | M2P表块的大小，目前是2M |

## M2P表内容 ##

读取以m2p\_info.mfn0页起始，共m2p\_info.size大小的内存。