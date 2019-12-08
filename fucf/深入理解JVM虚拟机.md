[TOC]
# 深入理解 JVM 虚拟机

## 第二章 Java内存区域与内存溢出异常

### 2.1 概述
对于Java程序员，由于不需要为new 操作去写配对的delete/free代码，而交由虚拟机自动内存管理机制去管理。因此不容易出现内存泄漏和内存溢出的问题。

但是一旦出现内存泄漏和溢出方面的问题，如果不了解虚拟机是怎样使用内存的，那么排查错误将会成为一项异常艰难的工作

### 2.2 运行时数据区域
![](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/24/15746077279329.jpg)

#### 2.2.1 程序计数器
* 当前线程所执行的字节码的行号指示器
* 线程私有

#### 2.2.2 Java虚拟机栈
* 线程私有

#### 2.2.3 Java堆
* Java堆（Java Heap）是Java虚拟机所管理的内存中最大的一块
* 被所有线程共享
* 存放对象实例
* 如果在堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常

#### 2.2.5 方法区 （Method Area）
* 各个线程共享的内存区域
* 存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
* 当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常

#### 2.2.6 运行时常量池
* 方法区的一部分
* 用于存放编译期生成的各种字面量和符号引用
* 当常量池无法再申请到内存时会抛出OutOfMemoryError异常

#### 2.2.7 直接内存
* 堆外内存
* 也可能出现OutOfMemoryError异常

### 2.3 HotSpot虚拟机对象探秘

#### 2.3.1 对象的创建
内存分配算法
* 指针碰撞（Java堆中内存规整）
    * Serial
    * ParNew
* 空闲列表（Java堆中内存并不规整）
    * CMS

#### 2.3.2 对象的内存布局
  * 对象头 (Header)
      * 对象自身的运行时数据
      * 类型指针（句柄访问需要）
  * 实例数据 (Instance Data)
      * 对象真正存储的有效信息
  * 对齐填充 (Padding)
      * 占位符。当对象实例数据部分没有对齐时，就需要通过对齐填充来补全
    
    
#### 2.3.3 对象的访问定位
* 句柄访问
    * 在对象被移动时只会改变句柄中的实例数据指针，而reference本身不需要修改

![-w767](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/24/15746039820872.jpg)

* 直接指针访问
    * 速度更快，节省了一次指针定位的时间开销。
    * Sun HotSpot虚拟机

![-w802](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/24/15746040019601.jpg)
    
### 2.4 实战 `OutOfMemoryError` 异常
* 内存映像分析工具 Eclipse Memory Analyzer

## 第三章 垃圾收集器与内存分配策略
### 3.1 概述
栈内存
* 每一个栈帧中分配多少内存是在类结构确定下来时就已知的。
* 程序计数器、虚拟机栈、本地方法栈 内存分配和回收都具备确定性

堆内存/方法区
* 只有程序在运行期间才能知道会创建哪些对象
* 这部分内存的分配和回收都是动态的。

### 3.2 对象已死吗？
* 引用计数法
    * 实现原理:给对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1
    * 优点:实现简单，判定效率也很高
    * 缺点: 无法解决对象之间的相互循环引用的问题
* 可达性分析算法
    * 实现原理:通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的
    * 在Java语言中，可作为GC Roots的对象包括下面几种:
        * 虚拟机栈中引用的对象
        * 方法区中类静态属性引用的对象
        * 方法区中常量引用的对象
        * 本地方法栈中JNI引用的对象




#### 3.2.3 再谈引用
![](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/30/15751234328999.jpg)

```java
// 强引用 ArrayList clear()方法
public void clear() {
    modCount++;

    // clear to let GC do its work
    for (int i = 0; i < size; i++)
        elementData[i] = null;

    size = 0;
}


// 弱引用 ThreadLocalMap
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

#### 3.2.4 生存还是死亡

要真正宣告一个对象死亡，至少要经历两次标记过程。

1. 第一次标记
在可达性分析后发现到GC Roots没有任何引用链相连时，被第一次标记；并且进行一次筛选：此对象是否必要执行finalize()方法；
    * 没有必要执行的情况：
        * 对象没有覆盖finalize()方法；
        * finalize()方法已经被JVM调用过；
        * 这两种情况就可以认为对象已死，可以回收；
    * 有必要执行
        * 对有必要执行finalize()方法的对象，被放入F-Queue队列中；
        * 稍后在JVM自动建立、低优先级的Finalizer线程（可能多个线程）中触发这个方法；

2. 第二次标记
    * GC将对F-Queue队列中的对象进行第二次小规模标记；
    * finalize()方法是对象逃脱死亡的最后一次机会：
    * 如果对象在其finalize()方法中重新与引用链上任何一个对象建立关联，第二次标记时会将其移出"即将回收"的集合；
    * 如果对象没有，也可以认为对象已死，可以回收了；
    * 一个对象的finalize()方法只会被系统自动调用一次，经过finalize()方法逃脱死亡的对象，第二次不会再调用；

```java
public class FinalizeEscapeGC {  
    public static FinalizeEscapeGC SAVE_HOOK = null;  

    public void isAlive() {  
        System.out.println("yes, I am still alive");  
    }  

    protected void finalize() throws Throwable {  
        super.finalize();  
        System.out.println("finalize method executed!");  
        FinalizeEscapeGC.SAVE_HOOK = this;  
    }  

    public static void main(String[] args) throws InterruptedException {  
        SAVE_HOOK = new FinalizeEscapeGC();  

        //对象第一次成功拯救自己  
        SAVE_HOOK = null;  
        System.gc();  

        //因为finalize方法优先级很低，所有暂停0.5秒以等待它  
        Thread.sleep(500);  
        if (SAVE_HOOK != null) {  
            SAVE_HOOK.isAlive();  
        } else {  
            System.out.println("no ,I am dead QAQ!");  
        }  

        //-----------------------  
        //以上代码与上面的完全相同,但这次自救却失败了！！！  
        SAVE_HOOK = null;  
        System.gc();  

        //因为finalize方法优先级很低，所有暂停0.5秒以等待它  
        Thread.sleep(500);  
        if (SAVE_HOOK != null) {  
            SAVE_HOOK.isAlive();  
        } else {  
            System.out.println("no ,I am dead QAQ!");  
        }  
    }  
}

//运行结果：

//finalize mehtod executed！

//yes,i am still alive：）

//no,i am dead：（
```

* finalize()方法只会被系统自动调用一次，如果对象面临下一次回收，它的finalize()方法不会被再次执行
* finalize（）方法运行代价高昂，不确定性大，应尽量避免使用

#### 3.2.5 回收方法区
* 废弃常量
    * 没有任何对象引用常量池中的常量
    * & 没有任何地方引用这个字面量
* 无用的类
    * 该类所有的实例都已经被回收
    * 加载该类的ClassLoader已经被回收
    * 该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法

### 3.3 垃圾收集算法
#### 3.3.1 标记-清除算法
* 算法分为标记和清除两个阶段:首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象
* 优点: 不需要移动对象，简单高效
* 缺点：标记-清除过程效率低。GC产生内存碎片

#### 3.3.2 复制算法
* 内存分为一块较大的Eden空间和两块较小的Survivor空间，每次使用Eden和其中一块Survivor。当回收时，将Eden和Survivor中还存活着的对象一次性地复制到另外一块Survivor空间上，最后清理掉Eden和刚才用过的Survivor空间
* 优点:简单高效，不会产生内存碎片
* 缺点:内存使用率低，且有可能产生频繁复制问题

#### 3.3.3 标记-整理算法
* 标记过程与标记-清楚算法一样。但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存
* 优点: 综合了标记清除算法与复制算法的优点
* 缺点:仍需移动局部对象

#### 3.3.4 分代收集算法
* 根据对象存活周期的不同将内存划分为几块。根据各个年代的特点采用最适当的收集算法。
* 在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。
* 老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“标记—清除”或者“标记—整理”算法来进行回收。


### 3.4 HotSpot的算法实现

### 3.5 垃圾收集器
![](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/30/15751260474789.jpg)
#### 3.5.1 `Serial` 收集器
* 单线程复制回收，简单高效，但会暂停程序导致停顿

#### 3.5.2 `ParNew` 收集器
* 多线程复制回收，降低了停顿时间，但容易增加上下文切换

#### 3.5.3 `Parallel Scavenge` 收集器
* 并行收集器，追求高吞吐量，高效利用CPU

#### 3.5.6 CMS收集器
`CMS` 收集器仅作用于老年代的收集，是基于标记-清除算法的，它的运作过程分为 4 个步骤：
* 初始标记（CMS initial mark）
* 并发标记（CMS concurrent mark）
* 重新标记（CMS remark）
* 并发清除（CMS concurrent sweep）    

其中，初始标记、重新标记这两个步骤仍然需要 Stop-the-world。初始标记仅仅只是标记一下 GC Roots 能直接关联到的对象，速度很快，并发标记阶段就是进行 GC Roots Tracing 的过程，而重新标记阶段则是为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始阶段稍长一些，但远比并发标记的时间短。

CMS 收集器优点：并发收集、低停顿。

CMS 收集器缺点:
* CMS 收集器对 CPU 资源非常敏感。
* CMS 收集器无法处理浮动垃圾（Floating Garbage）。
* CMS 收集器是基于标记 - 清除算法，该算法的缺点都有。

为什么 CMS 只能用作老年代收集器，而不能应用在新生代的收集？
* 清除算法将产生大量的内存碎片这对新生代来说是难以接受的，因此新生代的收集器并未提供 CMS 版本。

#### 3.5.7 G1收集器（Garbage First）
* 回收算法：标记-整理 + 复制算法
* 优点：高并发、低停顿、可预测停顿时间

#### 3.5.8 理解GC日志
![](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/12/01/15752055864345.jpg)



### 3.6 内存分配与回收策略
#### 3.6.1 对象优先在`Eden`分配
* 大多数情况下，对象在新生代`Eden`中分配。当`Eden`区没有足够空间进行分配时，虚拟机将发起一次`Minor GC`
* 新生代GC（Minor GC）：指发生在新生代的垃圾收集动作，因为Java对象大多都具备朝生夕灭的特性，所以Minor GC非常频繁，一般回收速度也比较快。
* 老年代GC（Major GC/Full GC）：指发生在老年代的GC，出现了Major GC，经常会伴随至少一次的Minor GC（但非绝对的，在Parallel Scavenge收集器的收集策略里就有直接进行Major GC的策略选择过程）。Major GC的速度一般会比Minor GC慢10倍以上。

#### 3.6.2 大对象直接进入老年代
* 所谓的大对象是指，需要大量连续内存空间的Java对象，最典型的大对象就是那种很长的字符串以及数组
* 虚拟机提供了一个`-XX：PretenureSizeThreshold`参数，令大于这个设置值的对象直接在老年代分配。这样做的目的是避免在Eden区及两个Survivor区之间发生大量的内存复制

#### 3.6.3 长期存活的对象将进入老年代
虚拟机给每个对象定义了一个对象年龄（Age）计数器。如果对象在Eden出生并经过第一次Minor GC后仍然存活，并且能被Survivor容纳的话，将被移动到Survivor空间中，并且对象年龄设为1。对象在Survivor区中每“熬过”一次Minor GC，年龄就增加1岁，当它的年龄增加到一定程度（默认为15岁），就将会被晋升到老年代中。对象晋升老年代的年龄阈值，可以通过参数-XX：MaxTenuringThreshold设置。

#### 3.6.4 动态对象年龄判定
为了能更好地适应不同程序的内存状况，虚拟机并不是永远地要求对象的年龄必须达到了`MaxTenuringThreshold`才能晋升老年代，

如果在`Survivor`空间中相同年龄所有对象大小的总和大于`Survivor`空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代，无须等到`MaxTenuringThreshold`中要求的年龄。


#### 3.6.5　空间分配担保
在发生Minor GC 之前，虚拟机会检查老年代最大可用的连续空间是否大于新生代所有对象的总空间。
* 如果大于，则此次`Minor GC`是安全的
* 如果小于，则虚拟机会查看HandlePromotionFailure设置值是否允许担保失败。
    * 如果`HandlePromotionFailure=true`，那么会继续检查老年代最大可用连续空间是否大于历次晋升到老年代的对象的平均大小，如果大于，则尝试进行一次`Minor GC`，但这次`Minor GC`依然是有风险的；
        * 取平均值仍然是一种概率性的事件
    * 如果小于或者`HandlePromotionFailure=false`，则改为进行一次`Full GC`。

>注：JDK 6 Update 24之后的规则变为只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小就会进行Minor GC，否则将进行Full GC。

## 第4章 虚拟机性能监控与故障处理工具

### 4.2 JDK的命令行工具
#### 4.2.1 `jps`: 虚拟机进程状况工具
* 列出正在运行的虚拟机进程
* 显示虚拟机执行朱磊名称
* 显示进程的本地虚拟机唯一ID
    * 与操作系统的进程ID是一致的

#### 4.2.2 `jstat`: 虚拟机统计信息监视工具
* 监视虚拟机各种运行状态信息的命令行工具
* 显示本地或者远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据

#### 4.2.3 `jinfo`：Java配置信息工具
* `jps -v`
    * 查看虚拟机启动时显式指定的参数列表
* `jinfo PID`
    * 查看未被显式指定的参数的系统默认值

#### 4.2.4 `jmap`:Java内存映像工具
命令格式:
* `jmap PID`

#### 4.2.6 jstack:Java堆栈跟踪工具
`jstack PID`

### 4.3 JDK 的可视化工具
* JConsole
* VisualVM
   

