## 第二部分：自动内存管理机制
### §2 - Java垃圾收集器与内存分配策略-GC
#### Author：Sirice
[Sirice-Github](https://github.com/Sirice19/Understanding-the-JVM-reading-notes)

### 垃圾收集器：

#### 七种垃圾收集器:
- Serial（串行GC）-复制
- ParNew（并行GC）-复制
- Parallel Scavenge（并行回收GC）-复制
- Serial Old（MSC）（串行GC）-标记-整理
- CMS（并发GC）-标记-清除
- Parallel Old（并行GC）--标记-整理
- G1（JDK1.7update14才可以正式商用）

说明：
- 1~3用于年轻代垃圾回收：年轻代的垃圾回收称为minor GC
- 4~6用于年老代垃圾回收（当然也可以用于方法区的回收）：年老代的垃圾回收称为full GC
- G1独立完成"分代垃圾回收"

注意：并行与并发
- 并行：多条垃圾回收线程同时操作
- 并发：垃圾回收线程与用户线程一起操作

#### 常用五种组合:
- Serial/Serial Old
- ParNew/Serial Old：与上边相比，只是比年轻代多了多线程垃圾回收而已
- ParNew/CMS：当下比较高效的组合
- Parallel Scavenge/Parallel Old：自动管理的组合
- G1：最先进的收集器，但是需要JDK1.7update14以上

##### (2.1)Serial/Serial Old：

![pic1](https://files.jb51.net/file_images/article/201702/2017021415541227.png)

**特点**
- 年轻代Serial收集器采用单个GC线程实现"复制"算法（包括扫描、复制）
- 年老代Serial Old收集器采用单个GC线程实现"标记-整理"算法
- Serial与Serial Old都会暂停所有用户线程（即STW）

**说明**

STW（stop the world）：编译代码时为每一个方法注入safepoint（方法中循环结束的点、方法执行结束的点），在暂停应用时，需要等待所有的用户线程进入safepoint，之后暂停所有线程，然后进行垃圾回收。

**适用场合**
- CPU核数<2，物理内存<2G的机器（简单来讲，单CPU，新生代空间较小且对STW时间要求不高的情况下使用）
- -XX:UseSerialGC：强制使用该GC组合
- -XX:PrintGCApplicationStoppedTime：查看STW时间
- 由于它实现相对简单，没有线程相关的额外开销（主要指线程切换与同步），因此非常适合运行于客户端PC的小型应用程序，或者桌面应用程序（比如swing编写的用户界面程序），以及我们平时的开发、调试、测试等。

##### (2.2)ParNew/Serial Old：

![pic2](https://files.jb51.net/file_images/article/201702/2017021415541328.png)

**说明**

ParNew除了采用多GC线程来实现复制算法以外，其他都与Serial一样，但是此组合中的Serial Old又是一个单GC线程，所以该组合是一个比较尴尬的组合，在单CPU情况下没有Serial/Serial Old速度快（因为ParNew多线程需要切换），在多CPU情况下又没有之后的三种组合快（因为Serial Old是单GC线程），所以使用其实不多。

-XX:ParallelGCThreads：指定ParNew GC线程的数量，默认与CPU核数相同，该参数在于CMS GC组合时，也可能会用到

##### (2.3)Parallel Scavenge/Parallel Old：

![pic3](https://files.jb51.net/file_images/article/201702/2017021415541529.png)

**特点**
- 年轻代Parallel Scavenge收集器采用多个GC线程实现"复制"算法（包括扫描、复制）
- 年老代Parallel Old收集器采用多个GC线程实现"标记-整理"算法
- Parallel Scavenge与Parallel Old都会暂停所有用户线程（即STW）

**说明**
- 吞吐量：CPU运行代码时间/(CPU运行代码时间+GC时间)
- CMS主要注重STW的缩短（该时间越短，用户体验越好，所以主要用于处理很多的交互任务的情况）
- Parallel Scavenge/Parallel Old主要注重吞吐量（吞吐量越大，说明CPU利用率越高，所以主要用于处理很多的CPU计算任务而用户交互任务较少的情况）

**参数设置**
- -XX:+UseParallelOldGC：使用该GC组合
- -XX:GCTimeRatio：直接设置吞吐量大小，假设设为19，则允许的最大GC时间占总时间的1/(1 +19)，默认值为99，即1/(1+99)
- -XX:MaxGCPauseMillis：最大GC停顿时间，该参数并非越小越好
- -XX:+UseAdaptiveSizePolicy：开启该参数，-Xmn/-XX:SurvivorRatio/-XX:PretenureSizeThreshold这些参数就不起作用了，虚拟机会自动收集监控信息，动态调整这些参数以提供最合适的的停顿时间或者最大的吞吐量（GC自适应调节策略），而我们需要设置的就是-Xmx，-XX:+UseParallelOldGC或-XX:GCTimeRatio两个参数就好（当然-Xms也指定上与-Xmx相同就好）

**适用场合**
- 很多的CPU计算任务而用户交互任务较少的情况
- 不想自己去过多的关注GC参数，想让虚拟机自己进行调优工作
- 对吞吐量要求较高，或需要达到一定的量。

##### (2.4)ParNew/CMS：

![pic5](https://files.jb51.net/file_images/article/201702/2017021415541630.png)

**说明**
- 以上只是年老代CMS收集的过程，年轻代ParNew看"2.2、ParNew/Serial Old"就好
- CMS是多回收线程的，不要被上图误导，默认的线程数：(CPU数量+3)/4
- CMS主要注重STW的缩短（该时间越短，用户体验越好，所以主要用于处理很多的交互任务的情况）

**特点**

1.年轻代ParNew收集器采用多个GC线程实现"复制"算法（包括扫描、复制）

2.年老代CMS收集器采用多线程实现"标记-清除"算法
- 初始标记：标记与根集合节点直接关联的节点。时间非常短，需要STW
- 并发标记：遍历之前标记到的关联节点，继续向下标记所有存活节点。时间较长。
- 重新标记：重新遍历trace并发期间修改过的引用关系对象。时间介于初始标记与并发标记之间，通常不会很长。需要STW
- 并发清理：直接清除非存活对象，清理之后，将该线程占用的CPU切换给用户线程

3.初始标记与重新标记都会暂停所有用户线程（即STW），但是时间较短；并发标记与并发清理时间较长，但是不需要STW

关于并发标记期间怎样记录发生变动的引用关系对象，在重新标记期间怎样扫描这些对象

**缺点**
- 并发标记与并发清理：按照说明的第二点来讲，假设有2个CPU，那么其中有一个CPU会用于垃圾回收，而另一个用于用户线程，这样的话，之前是两CPU运行用户线程，现在是一个，那么效率就会急剧下降。也就是说，降低了吞吐量（即降低了CPU使用率）。
- 并发清理：在这一过程中，产生的垃圾无法被清理（因为发生在重新标记之后）
- 并发标记与并发清理：由于是与用户线程并发的，所以用户线程可能会分配对象，这样既可能对象直接进入年老代（例如，大对象），也可能进入年轻代后，年轻代发生minor GC，这样的话，实际上要求我们的年老代需要预留一定空间，也就是说要在年老代还有一定空间的情况下就要进行垃圾回收，留出一定内存空间来供其他线程使用，而不能等到年老代快爆满了才进行垃圾回收，通过-XX:CMSInitiatingOccupancyFraction来指定当年老代空间满了多少后进行垃圾回收
- 标记-清理算法：会产生内存碎片，由于是在老年代，可能会提前触发Full GC（这正是我们要尽量减少的）

**参数设置**
- -XX:+UseConcMarkSweepGC：使用该GC组合
- -XX:CMSInitiatingOccupancyFraction：指定当年老代空间满了多少后进行垃圾回收
- -XX:+UseCMSCompactAtFullCollection：（默认是开启的）在CMS收集器顶不住要进行FullGC时开启内存碎片整理过程，该过程需要STW
- -XX:CMSFullGCsBeforeCompaction：指定多少次FullGC后才进行整理
- -XX:ParallelCMSThreads：指定CMS回收线程的数量，默认为：(CPU数量+3)/4

**适用场合**

用于处理很多的交互任务的情况

方法区的回收一般使用CMS，配置两个参数：-XX:+CMSPermGenSweepingEnabled与-XX:+CMSClassUnloadingEnabled

适用于一些需要长期运行且对相应时间有一定要求的后台程序

##### (2.5)G1:

![pic6](https://files.jb51.net/file_images/article/201702/2017021415541731.png)

**说明**
- 从上图来看，G1与CMS相比，仅在最后的"筛选回收"部分不同（CMS是并发清除），实际上G1回收器的整个堆内存的划分都与其他收集器不同。
- CMS需要配合ParNew，G1可单独回收整个空间

**原理**
- G1收集器将整个堆划分为多个大小相等的Region
- G1跟踪各个region里面的垃圾堆积的价值（回收后所获得的空间大小以及回收所需时间长短的经验值），在后台维护一张优先列表，每次根据允许的收集时间，优先回收价值最大的region，这种思路：在指定的时间内，扫描部分最有价值的region（而不是扫描整个堆内存），并回收，做到尽可能的在有限的时间内获取尽可能高的收集效率。

**运作流程**
- 初始标记：标记出所有与根节点直接关联引用对象。需要STW
- 并发标记：遍历之前标记到的关联节点，继续向下标记所有存活节点。在此期间所有变化引用关系的对象，都会被记录在Remember Set Logs中
- 最终标记：标记在并发标记期间，新产生的垃圾。需要STW
- 筛选回收：根据用户指定的期望回收时间回收价值较大的对象（看"原理"第二条）。需要STW

**优点**
- 停顿时间可以预测：我们指定时间，在指定时间内只回收部分价值最大的空间，而CMS需要扫描整个年老代，无法预测停顿时间
- 无内存碎片：垃圾回收后会整合空间，CMS采用"标记-清理"算法，存在内存碎片
- 筛选回收阶段：
- 由于只回收部分region，所以STW时间我们可控，所以不需要与用户线程并发争抢CPU资源，而CMS并发清理需要占据一部分的CPU，会降低吞吐量。
- 由于STW，所以不会产生"浮动垃圾"（即CMS在并发清理阶段产生的无法回收的垃圾）

**适用范围**
- 追求STW短：若ParNew/CMS用的挺好，就用这个；若不符合，用G1
- 追求吞吐量：用Parallel Scavenge/Parallel Old，而G1在吞吐量方面没有优势
