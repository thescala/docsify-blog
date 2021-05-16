# jstat 命令查看 jvm 的 GC 情况

## 常用的 jstat 命令

### `jstat -gcutil pid`

统计 gc 信息统计。(总结垃圾回收统计)

```shell
S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
0.00   0.00  97.64  99.81  96.20  89.30   1447   39.674   398  534.968  574.641
```

> - S0：幸存 1 区当前使用比例
> - S1：幸存 2 区当前使用比例
> - E：伊甸园区使用比例
> - O：老年代使用比例
> - M：元数据区使用比例
> - CCS：压缩使用比例
> - YGC：年轻代垃圾回收次数
> - FGC：老年代垃圾回收次数
> - FGCT：老年代垃圾回收消耗时间
> - GCT：垃圾回收消耗总时间

### `jstat -gc pid`

(垃圾回收统计)可以显示 gc 的信息，查看 gc 的次数，及时间。其中最后五项，分别是 young gc 的次数，young gc 的时间，full gc 的次数，full gc 的时间，gc 的总时间。

```shell
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
17472.0 17472.0  0.0  0.0   139776.0 61784.7   349568.0   349565.3  505164.0 485801.3 65548.0 58558.5   1468   39.674  419   567.190  606.864
```

> - S0C：第一个幸存区的大小
> - S1C：第二个幸存区的大小
> - S0U：第一个幸存区的使用大小
> - S1U：第二个幸存区的使用大小
> - EC：伊甸园区的大小
> - EU：伊甸园区的使用大小
> - OC：老年代大小
> - OU：老年代使用大小
> - MC：方法区大小
> - MU：方法区使用大小
> - CCSC:压缩类空间大小
> - CCSU:压缩类空间使用大小
> - YGC：年轻代垃圾回收次数
> - YGCT：年轻代垃圾回收消耗时间
> - FGC：老年代垃圾回收次数
> - FGCT：老年代垃圾回收消耗时间
> - GCT：垃圾回收消耗总时间

### `jstat -gccapacity pid`

VM 内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN 显示的是最小 perm 的内存使用量，PGCMX 显示的是 perm 的内存最大使用量，PGC 是当前新生成的 perm 内存占用量，PC 是但前 perm 内存占用量。其他的可以根据这个类推， OC 是 old 内纯的占用量

```shell
NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
192.0  174720.0 174720.0 17472.0 17472.0 139776.0  64.0   349568.0   349568.0   349568.0      0.0    1488896.0   505164.0   0.0   1048576.0  65548.0   1469   420
```

> - NGCMN：新生代最小容量
> - NGCMX：新生代最大容量
> - NGC：当前新生代容量
> - S0C：第一个幸存区大小
> - S1C：第二个幸存区的大小
> - EC：伊甸园区的大小
> - OGCMN：老年代最小容量
> - OGCMX：老年代最大容量
> - OGC：当前老年代大小
> - OC:当前老年代大小
> - MCMN:最小元数据容量
> - MCMX：最大元数据容量
> - MC：当前元数据空间大小
> - CCSMN：最小压缩类空间大小
> - CCSMX：最大压缩类空间大小
> - CCSC：当前压缩类空间大小
> - YGC：年轻代 gc 次数
> - FGC：老年代 GC 次数

### `jstat -gcnew pid`

年轻代对象的信息。(新生代垃圾回收统计)

```shell
 S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
17472.0 17472.0  0.0   0.0  1   6  8736.0 139776.0 104064.1   1476   39.674
```

> - S0C：第一个幸存区大小
> - S1C：第二个幸存区的大小
> - S0U：第一个幸存区的使用大小
> - S1U：第二个幸存区的使用大小
> - TT:对象在新生代存活的次数
> - MTT:对象在新生代存活的最大次数
> - DSS:期望的幸存区大小
> - EC：伊甸园区的大小
> - EU：伊甸园区的使用大小
> - YGC：年轻代垃圾回收次数
> - YGCT：年轻代垃圾回收消耗时间

### `jstat -gcnewcapacity pid`

年轻代对象的信息及其占用量。(新生代内存统计)

```shell
NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC
 192.0   174720.0   174720.0  17472.0  17472.0  17472.0  17472.0   139776.0   139776.0  1626   578
```

> - NGCMN：新生代最小容量
> - NGCMX：新生代最大容量
> - NGC：当前新生代容量
> - S0CMX：最大幸存 1 区大小
> - S0C：当前幸存 1 区大小
> - S1CMX：最大幸存 2 区大小
> - S1C：当前幸存 2 区大小
> - ECMX：最大伊甸园区大小
> - EC：当前伊甸园区大小
> - YGC：年轻代垃圾回收次数
> - FGC：老年代回收次数

### `jstat -gcold pid`

old 代对象的信息(老年代垃圾回收统计)

```shell
  MC       MU      CCSC     CCSU       OC          OU       YGC    FGC    FGCT     GCT
517964.0 498405.0  66828.0  59670.2    349568.0    349567.6   1620   572  779.667  819.345

```

> - MC：方法区大小
> - MU：方法区使用大小
> - CCSC:压缩类空间大小
> - CCSU:压缩类空间使用大小
> - OC：老年代大小
> - OU：老年代使用大小
> - YGC：年轻代垃圾回收次数
> - FGC：老年代垃圾回收次数
> - FGCT：老年代垃圾回收消耗时间
> - GCT：垃圾回收消耗总时间

### `jstat -gcoldcapacity pid`

old 代对象的信息及其占用量。(老年代内存统计)

```shell
 OGCMN    OGCMX        OGC         OC       YGC   FGC    FGCT     GCT
 64.0    349568.0     349568.0   349568.0  1585   537   728.626  768.303
```

> - OGCMN：老年代最小容量
> - OGCMX：老年代最大容量
> - OGC：当前老年代大小
> - OC：老年代大小
> - YGC：年轻代垃圾回收次数
> - FGC：老年代垃圾回收次数
> - FGCT：老年代垃圾回收消耗时间
> - GCT：垃圾回收消耗总时间

### `jstat -gcpermcapacity pid`

perm 对象的信息及其占用量(元数据空间统计)

```shell
 MCMN       MCMX        MC       CCSMN      CCSMX       CCSC     YGC   FGC    FGCT     GCT
 0.0  1499136.0   516556.0        0.0  1048576.0    66700.0  1593   545  739.651  779.328
```

> - MCMN:最小元数据容量
> - MCMX：最大元数据容量
> - MC：当前元数据空间大小
> - CCSMN：最小压缩类空间大小
> - CCSMX：最大压缩类空间大小
> - CCSC：当前压缩类空间大小
> - YGC：年轻代垃圾回收次数
> - FGC：老年代垃圾回收次数
> - FGCT：老年代垃圾回收消耗时间
> - GCT：垃圾回收消耗总时间

### `jstat -class pid`

显示加载 class 的数量，及所占空间等信息。

```shell
Loaded  Bytes  Unloaded  Bytes     Time
 96015 186331.9    21819 30743.7     164.92
```

> - Loaded:加载 class 的数量
> - Bytes：所占用空间大小
> - Unloaded：未加载数量
> - Bytes:未加载占用空间
> - Time：时间

### `jstat -compiler pid`

显示 VM 实时编译的数量等信息。

```shell
Compiled Failed Invalid   Time   FailedType FailedMethod
  134608     10       0  1878.25          1 java/io/StreamTokenizer nextToken

```

> - Compiled：编译数量。
> - Failed：失败数量
> - Invalid：不可用数量
> - Time：时间
> - FailedType：失败类型
> - FailedMethod：失败的方法

### `jstat -printcompilation pid`

当前 VM 执行的信息。(JVM 编译方法统计)

```shell
Compiled  Size  Type Method
  132728    264    1 com/intellij/openapi/keymap/KeymapUtil getActiveKeymapShortcuts
```

> - Compiled：最近编译方法的数量
> - Size：最近编译方法的字节码数量
> - Type：最近编译方法的编译类型。
> - Method：方法名标识。

## JVM 参数说明

| **参数名称**                | **含义**                                                       | **默认值**            | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------------- | -------------------------------------------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -Xms                        | 初始堆大小                                                     | 物理内存的 1/64(<1GB) | 默认(MinHeapFreeRatio 参数可以调整)空余堆内存小于 40%时，JVM 就会增大堆直到-Xmx 的最大限制.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| -Xmx                        | 最大堆大小                                                     | 物理内存的 1/4(<1GB)  | 默认(MaxHeapFreeRatio 参数可以调整)空余堆内存大于 70%时，JVM 会减少堆直到 -Xms 的最小限制                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| -Xmn                        | 年轻代大小(1.4or lator)                                        |                       | **注意**：此处的大小是（eden+ 2 survivor space).与 jmap -heap 中显示的 New gen 是不同的。 整个堆大小=年轻代大小 + 年老代大小 + 持久代大小. 增大年轻代后,将会减小年老代大小.此值对系统性能影响较大,Sun 官方推荐配置为整个堆的 3/8                                                                                                                                                                                                                                                                                           |
| -XX:NewSize                 | 设置年轻代大小(for 1.3/1.4)                                    |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:MaxNewSize              | 年轻代最大值(for 1.3/1.4)                                      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:PermSize                | 设置持久代(perm gen)初始值                                     | 物理内存的 1/64       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:MaxPermSize             | 设置持久代最大值                                               | 物理内存的 1/4        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -Xss                        | 每个线程的堆栈大小                                             |                       | JDK5.0 以后每个线程堆栈大小为 1M,以前每个线程堆栈大小为 256K.更具应用的线程所需内存大小进行 调整.在相同物理内存下,减小这个值能生成更多的线程.但是操作系统对一个进程内的线程数还是有限制的,不能无限生成,经验值在 3000~5000 左右 一般小的应用， 如果栈不是很深， 应该是 128k 够用的 大的应用建议使用 256k。这个选项对性能影响比较大，需要严格的测试。（校长） 和 threadstacksize 选项解释很类似,官方文档似乎没有解释,在论坛中有这样一句话:"” -Xss is translated in a VM flag named ThreadStackSize” 一般设置这个值就可以了。 |
| -_XX:ThreadStackSize_       | Thread Stack Size                                              |                       | (0 means use default stack size) [Sparc: 512; Solaris x86: 320 (was 256 prior in 5.0 and earlier); Sparc 64 bit: 1024; Linux amd64: 1024 (was 0 in 5.0 and earlier); all others 0.]                                                                                                                                                                                                                                                                                                                                        |
| -XX:NewRatio                | 年轻代(包括 Eden 和两个 Survivor 区)与年老代的比值(除去持久代) |                       | -XX:NewRatio=4 表示年轻代与年老代所占比值为 1:4,年轻代占整个堆栈的 1/5 Xms=Xmx 并且设置了 Xmn 的情况下，该参数不需要进行设置。                                                                                                                                                                                                                                                                                                                                                                                             |
| -XX:SurvivorRatio           | Eden 区与 Survivor 区的大小比值                                |                       | 设置为 8,则两个 Survivor 区与一个 Eden 区的比值为 2:8,一个 Survivor 区占整个年轻代的 1/10                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| -XX:LargePageSizeInBytes    | 内存页的大小不可设置过大， 会影响 Perm 的大小                  |                       | =128m                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -XX:+UseFastAccessorMethods | 原始类型的快速优化                                             |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:+DisableExplicitGC      | 关闭 System.gc()                                               |                       | 这个参数需要严格的测试                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -XX:MaxTenuringThreshold    | 垃圾最大年龄                                                   |                       | 如果设置为 0 的话,则年轻代对象不经过 Survivor 区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在 Survivor 区进行多次复制,这样可以增加对象再年轻代的存活 时间,增加在年轻代即被回收的概率 该参数只有在串行 GC 时才有效.                                                                                                                                                                                                                                                      |
| -XX:+AggressiveOpts         | 加快编译                                                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:+UseBiasedLocking       | 锁机制的性能改善                                               |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -Xnoclassgc                 | 禁用垃圾回收                                                   |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:SoftRefLRUPolicyMSPerMB | 每兆堆空闲空间中 SoftReference 的存活时间                      | 1s                    | softly reachable objects will remain alive for some amount of time after the last time they were referenced. The default value is one second of lifetime per free megabyte in the heap                                                                                                                                                                                                                                                                                                                                     |
| -XX:PretenureSizeThreshold  | 对象超过多大是直接在旧生代分配                                 | 0                     | 单位字节 新生代采用 Parallel Scavenge GC 时无效 另一种直接在旧生代分配的情况是大的数组对象,且数组中无外部引用对象.                                                                                                                                                                                                                                                                                                                                                                                                         |
| -XX:TLABWasteTargetPercent  | TLAB 占 eden 区的百分比                                        | 1%                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:+_CollectGen0First_     | FullGC 时是否先 YGC                                            | false                 |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -XX:CICompilerCount         | 设置的相对较大可以一定程度提升 JIT 编译的速度，默认为 2        | 2                     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

**并行收集器相关参数**

| **参数名称**                | **含义**                                          | **默认值**                                 | **说明**                                                                                                                                                |
| --------------------------- | ------------------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+UseParallelGC          | Full GC 采用 parallel MSC (此项待验证)            | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 选择垃圾收集器为并行收集器.此配置仅对年轻代有效.即上述配置下,年轻代使用并发收集,而年老代仍旧使用串行收集.(此项待验证)                                   |
| -XX:+UseParNewGC            | 设置年轻代为并行收集                              |                                            | 可与 CMS 收集同时使用 JDK5.0 以上,JVM 会根据系统配置自行设置,所以无需再设置此值                                                                         |
| -XX:ParallelGCThreads       | 并行收集器的线程数                                |                                            | 此值最好配置与处理器数目相等 同样适用于 CMS                                                                                                             |
| -XX:+UseParallelOldGC       | 年老代垃圾收集方式为并行收集(Parallel Compacting) |                                            | 这个是 JAVA 6 出现的参数选项                                                                                                                            |
| -XX:MaxGCPauseMillis        | 每次年轻代垃圾回收的最长时间(最大暂停时间)        |                                            | 如果无法满足此时间,JVM 会自动调整年轻代大小,以满足此值.                                                                                                 |
| -XX:+UseAdaptiveSizePolicy  | 自动选择年轻代区大小和相应的 Survivor 区比例      |                                            | 设置此选项后,并行收集器会自动选择年轻代区大小和相应的 Survivor 区比例,以达到目标系统规定的最低相应时间或者收集频率等,此值建议使用并行收集器时,一直打开. |
| -XX:GCTimeRatio             | 设置垃圾回收时间占程序运行时间的百分比            |                                            | 公式为 1/(1+n)                                                                                                                                          |
| -XX:+_ScavengeBeforeFullGC_ | Full GC 前调用 YGC                                | true                                       | Do young generation GC prior to a full GC. (Introduced in 1.4.1.)                                                                                       |

**CMS 相关参数**

| **参数名称**                           | **含义**                                       | **默认值** | **说明**                                                                                                                                                                                                                        |
| -------------------------------------- | ---------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+UseConcMarkSweepGC                | 使用 CMS 内存收集                              |            | 测试中配置这个以后,-XX:NewRatio=4 的配置失效了,原因不明.所以,此时年轻代大小最好用-Xmn 设置.???                                                                                                                                  |
| -XX:+AggressiveHeap                    |                                                |            | 试图是使用大量的物理内存 长时间大内存使用的优化，能检查计算资源（内存， 处理器数量） 至少需要 256MB 内存 大量的 CPU／内存， （在 1.4.1 在 4CPU 的机器上已经显示有提升）                                                         |
| -XX:CMSFullGCsBeforeCompaction         | 多少次后进行内存压缩                           |            | 由于并发收集器不对内存空间进行压缩,整理,所以运行一段时间以后会产生"碎片",使得运行效率降低.此值设置运行多少次 GC 以后对内存空间进行压缩,整理.                                                                                    |
| -XX:+CMSParallelRemarkEnabled          | 降低标记停顿                                   |            |                                                                                                                                                                                                                                 |
| -XX+UseCMSCompactAtFullCollection      | 在 FULL GC 的时候， 对年老代的压缩             |            | CMS 是不会移动内存的， 因此， 这个非常容易产生碎片， 导致内存不够用， 因此， 内存的压缩这个时候就会被启用。 增加这个参数是个好习惯。 可能会影响性能,但是可以消除碎片                                                            |
| -XX:+UseCMSInitiatingOccupancyOnly     | 使用手动定义初始化定义开始 CMS 收集            |            | 禁止 hostspot 自行触发 CMS GC                                                                                                                                                                                                   |
| -XX:CMSInitiatingOccupancyFraction=70  | 使用 cms 作为垃圾回收 使用 70％后开始 CMS 收集 | 92         | 为了保证不出现 promotion failed(见下面介绍)错误,该值的设置需要满足以下公式 **[CMSInitiatingOccupancyFraction 计算公式](https://www.cnblogs.com/redcreen/archive/2011/05/04/2037057.html#CMSInitiatingOccupancyFraction_value)** |
| -XX:CMSInitiatingPermOccupancyFraction | 设置 Perm Gen 使用到达多少比率时触发           | 92         |                                                                                                                                                                                                                                 |
| -XX:+CMSIncrementalMode                | 设置为增量模式                                 |            | 用于单 CPU 情况                                                                                                                                                                                                                 |
| -XX:+CMSClassUnloadingEnabled          |                                                |            |                                                                                                                                                                                                                                 |

**辅助信息**

| **参数名称**                          | **含义**                                                 | **默认值** | **说明**                                                                                                                                                                                                                                              |
| ------------------------------------- | -------------------------------------------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+PrintGC                          |                                                          |            | 输出形式:`[GC 118250K->113543K(130112K), 0.0094143 secs] [Full GC 121376K->10414K(130112K), 0.0650971 secs]`                                                                                                                                          |
| -XX:+PrintGCDetails                   |                                                          |            | 输出形式:`[GC [DefNew: 8614K->781K(9088K), 0.0123035 secs] 118250K->113543K(130112K), 0.0124633 secs] [GC [DefNew: 8614K->8614K(9088K), 0.0000665 secs][Tenured: 112761K->10414K(121024K), 0.0433488 secs] 121376K->10414K(130112K), 0.0436268 secs]` |
| -XX:+PrintGCTimeStamps                |                                                          |            |                                                                                                                                                                                                                                                       |
| -XX:+PrintGC:PrintGCTimeStamps        |                                                          |            | 可与`-XX:+PrintGC -XX:+PrintGCDetails混合使用 输出形式:11.851: [GC 98328K->93620K(130112K), 0.0082960 secs] `                                                                                                                                         |
| -XX:+PrintGCApplicationStoppedTime    | 打印垃圾回收期间程序暂停的时间.可与上面混合使用          |            | 输出形式:`Total time for which application threads were stopped: 0.0468229 seconds `                                                                                                                                                                  |
| -XX:+PrintGCApplicationConcurrentTime | 打印每次垃圾回收前,程序未中断的执行时间.可与上面混合使用 |            | 输出形式:`Application time: 0.5291524 seconds `                                                                                                                                                                                                       |
| -XX:+PrintHeapAtGC                    | 打印 GC 前后的详细堆栈信息                               |            |                                                                                                                                                                                                                                                       |
| -Xloggc:filename                      | 把相关日志信息记录到文件以便分析. 与上面几个配合使用     |            |                                                                                                                                                                                                                                                       |
| -XX:+PrintClassHistogram              | garbage collects before printing the histogram.          |            |                                                                                                                                                                                                                                                       |
| -XX:+PrintTLAB                        | 查看 TLAB 空间的使用情况                                 |            |                                                                                                                                                                                                                                                       |
| XX:+PrintTenuringDistribution         | 查看每次 minor GC 后新的存活周期的阈值                   |            | `Desired survivor size 1048576 bytes, new threshold 7 (max 15) new threshold 7即标识新的存活周期的阈值为7`。                                                                                                                                          |
