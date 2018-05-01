上集回顾：
上集我们稍做讲解了Future与Executor

# Executors
创建并得到ThreadPoolExecutor的工厂。


![ThreadPoolExecutor执行示意图.png](http://upload-images.jianshu.io/upload_images/3481116-161e20cbf1368758.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# ThreadPoolExecutor
线程池的真正实现

提供构造方法

![构造方法.png](http://upload-images.jianshu.io/upload_images/3481116-afefa19425261bad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#创建一个线程池时需要输入几个参数，如下创建一个线程池时需要输入几个参数，如下

## corePoolSize
线程池的核心线程数，默认情况下，核心线程会在线程池中一直存活，即使它们处于闲置状态。如果将ThreadPoolExecutor中的allowCoreThreadTimeOut属性设置为true，那么闲置的核心线程在等待新任务到来时会有超时策略，这个时间间隔由keepAliveTime所指定，当等待时间超过keepAliveTime所指定的时长后，核心线程就会被终止。
线程池中的核心线程数，当提交一个任务时，线程池创建一个新线程执行任务，直到当前线程数等于corePoolSize；如果当前线程数为corePoolSize，继续提交的任务被保存到阻塞队列中，等待被执行；如果执行了线程池的prestartAllCoreThreads()方法，线程池会提前创建并启动所有核心线程。

## maximumPoolSize
线程池所能容纳的最大线程数，当活动线程数达到这个数值后，后续的新任务将会被阻塞。
线程池中允许的最大线程数。如果当前阻塞队列满了，且继续提交任务，则创建新的线程执行任务，前提是当前线程数小于maximumPoolSize。

## keepAliveTime
非核心线程闲置时的超时时长，超过这个时长，非核心线程就会被回收。当ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true时，keepAliveTime同样会作用于核心线程。
线程空闲时的存活时间，即当线程没有任务执行时，继续存活的时间；默认情况下，该参数只在线程数大于corePoolSize时才有用。

## unit
用于指定keepAliveTime参数的时间单位，这是一个枚举，常用的有TimeUnit.MILLISECONDS(毫秒)、TimeUnit.SECONDS(秒)以及TimeUnit.MINUTES(分钟)等。
keepAliveTime的单位。

## BlockingQueue：用来暂时保存任务的工作队列。
线程池中的任务队列，通过线程池的execute方法提交的Runnable对象会存储在这个参数中。
维护着等待执行的Runnable对象。


当所有的核心线程都在干活时，新添加的任务会被添加到这个队列中等待处理，如果队列满了，则新建非核心线程执行任务

用来保存等待被执行的任务的阻塞队列，且任务必须实现Runable接口，在JDK中提供了如下阻塞队列：

### 1、ArrayBlockingQueue：
基于数组结构的**有界阻塞队列**，按FIFO排序任务；

可以限定队列的长度，接收到任务的时候，如果没有达到 corePoolSize 的值，则新建线程(核心线程)执行任务，如果达到了，则入队等候，如果队列已满，则新建线程(非核心线程)执行任务，又如果总线程数到了 maximumPoolSize，并且队列也满了，则发生错误。

**作用与应用**：帮助限制资源的消耗，但是不容易控制。队列长度和maximumPoolSize这两个值会相互影响，使用大的队列和小maximumPoolSize会减少CPU的使用、操作系统资源、上下文切换的消耗，但是会降低吞吐量，如果任务被频繁的阻塞如IO线程，系统其实可以调度更多的线程。使用小的队列通常需要大maximumPoolSize，从而使得CPU更忙一些，但是又会增加降低吞吐量的线程调度的消耗。总结一下是**IO密集型可以考虑多些线程来平衡CPU的使用，CPU密集型可以考虑少些线程减少线程调度的消耗。**

### 2、LinkedBlockingQuene：
基于链表结构的阻塞队列，按FIFO排序任务，吞吐量通常要高于ArrayBlockingQuene；

这个队列接收到任务的时候，如果当前线程数小于核心线程数，则新建线程(核心线程)处理任务；如果当前线程数等于核心线程数，则进入队列等待。由于这个队列没有最大值限制，即所有超过核心线程数的任务都将被添加到队列中，这也就导致了 maximumPoolSize 的设定失效，因为总线程数永远不会超过 corePoolSize。

**作用与应用**：没有指定最大容量的时候，将会引起当核心线程都在忙的时候，新的任务被放在队列上，因此，永远不会有大于corePoolSize的线程被创建，因此maximumPoolSize参数将失效。这种策略比较适合所有的任务都不相互依赖，独立执行。举个例子，如网页服务器中，每个线程独立处理请求。但是当任务处理速度小于任务进入速度的时候会引起队列的无限膨胀。

### 3、SynchronousQuene：
一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene；

注意：这个队列接收到任务的时候，会直接提交给线程处理，而不保留它，如果所有线程都在工作怎么办？那就新建一个线程来处理这个任务！所以为了保证不出现<线程数达到了 maximumPoolSize 而不能新建线程>的错误，使用这个类型队列的时候，maximumPoolSize 一般指定成 Integer.MAX_VALUE，即无限大。

**作用与应用**：会将提交的任务直接传送给工作线程，而不持有。如果当前没有工作线程来处理，即任务放入队列失败，则根据线程池的实现，会引发新的工作线程创建，因此新提交的任务会被处理。这种策略在当提交的一批任务之间有依赖关系的时候避免了锁竞争消耗。值得一提的是，这种策略最好是配合unbounded线程数来使用，从而避免任务被拒绝。同时我们必须要考虑到一种场景，当任务到来的速度大于任务处理的速度，将会引起无限制的线程数不断的增加。

### 4、priorityBlockingQuene：
具有优先级的无界阻塞队列；

### 5、DelayQueue：
队列内元素必须实现 Delayed 接口，这就意味着你传进去的任务必须先实现 Delayed 接口。这个队列接收到任务时，首先先入队，只有达到了指定的延时时间，才会执行任务。

DelayQueue封装了一个PriorityQueue，这个PriorityQueue会对队列中的ScheduledFutureTask进行排序。排序时，time小的排在前面（时间早的任务将被先执行）。如果两个 ScheduledFutureTask的time相同，就比较sequenceNumber，sequenceNumber小的排在前面（也就是说，如果两个任务的执行时间相同，那么先提交的任务将被先执行）。


## threadFactory
线程工厂，为线程池提供创建新线程的功能。ThreadFactory是一个接口，它只有一个方法：Thread newThread(Runnable r)。

##RejectedExecutionHandler（饱和策略）：
当队列和线程池都满了，说明线程池处于饱和状 态，那么必须采取一种策略处理提交的新任务。这个策略默认情况下是AbortPolicy，表示无法 处理新任务时抛出异常
除了上面的这些主要参数外，ThreadPoolExecutor还有一个不常用的参数RejectedExecutionHandler handler。当线程池无法执行新的任务时，这可能是犹豫任务队列已满或者是无法成功执行任务，这个时候ThreadPoolExecutor会调用handler的RejectedExecutionException。ThreadPoolExecutor为RejectedExecutionHandler 提供了几个可选值：
CallerRunsPolicy、AbortPolicy、DiscardPolicy和DiscardOldestPolicy，其中AbortPolicy是默认值，它会直接抛出RejectedExecutionException。

线程池的饱和策略，当阻塞队列满了，且没有空闲的工作线程，如果继续提交任务，必须采取一种策略处理该任务，线程池提供了4种策略：
1、AbortPolicy：直接抛出异常，默认策略；
2、CallerRunsPolicy：用调用者所在的线程来执行任务；
3、DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务，并执行当前任务；
4、DiscardPolicy：直接丢弃任务；
当然也可以根据应用场景实现RejectedExecutionHandler接口，自定义饱和策略，如记录日志或持久化存储不能处理的任务。

# ThreadPoolExecutor执行任务时大致遵循如下规则：
（1）如果线程池中的线程数量未达到核心线程的数量，那么会直接启动核心线程来执行任务。
（2）如果线程池中的线程数量已经达到或者超过核心线程的数量，那么任务会被出入到队列中等待执行。
（3）如果在步骤2中无法将任务插入到任务队列中，这往往是由于任务队列已满，这个时候如果线程数量未大道线程池规定的最大值，那么会立即启动一个非核心线程来执行任务。
（4）如果步骤3中线程数量已经达到线程池规定的最大值，那么就拒绝执行此任务，ThreadPoolExecutor会调用RejectedExecutionHandler的RejectedExecution方法来通知调用者。


![线程池的主要处理流程.png](http://upload-images.jianshu.io/upload_images/3481116-6ac16d45783b5905.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#  线程池解决的两个问题：
1）线程池通过减少每次做任务的时候产生的性能消耗来优化执行大量的异步任务的时候的系统性能。
2）线程池还提供了限制和管理批量任务被执行的时候消耗的资源、线程的方法。
另外ThreadPoolExecutor还提供了简单的统计功能，比如当前有多少任务被执行完了。

# 线程池的分类

## 1.FixedThreadPool

线程数量固定，当线程处于空闲状态时，不会被回收。
只有没有超时机制的核心线程。
任务队列并没有大小限制。（超出的线程会在队列中等待）
用的是LinkedBlockingQuene队列。

## 2.CachedThreadPool
只有非核心线程，并且最大线程数为Integer.MAX_VALUE（很大的数0x7fffffff）。
**适用于**执行量大，耗时较少的任务。
用的是SynchronousQuene队列。

## 3.ScheduledThreadPool
核心线程数量是固定的。
非核心线程数量没有限制。
当非核心线程限制时会被立即回收。
**适用于**执行定时任务和具有固定周期的重复任务。
用的是DelayQueue队列。
延迟启动任务schedule(Runnable command,long delay,TimeUnit unit)
延迟定时执行任务scheduleAtFixedRate(Runnable command,long initialDelay,long period,TimeUnit unit)
延迟执行任务scheduleWithFixedDelay(Runnable command,long initialDelay,long delay,TimeUnit unit)

## 4.SingleThreadExecutor
只有一个核心线程，确保所有任务都在同一个线程中按顺序执行。
**意义在于**统一所有外界任务到一个线程中，使得在这些任务之间不需要处理线程同步的问题。
用的是LinkedBlockingQuene队列。

|ThreadPoolExecutor|FixedThreadPool|CachedThreadPool|ScheduledThreadPool|SingleThreadExecutor|
|:--:|:--:|:--:|:--:|:--:|
|corePoolSize|nThreads|0|corePoolSize|1|
|maximumPoolSize|nThreads|Integer.MAX_VALUE|Integer.MAX_VALUE|1|
|keepAliveTime|0L|60L|DEFAULT_KEEPALIVE_MILLIS|0L|
|unit|TimeUnit.MILLISECONDS|TimeUnit.SECONDS|MILLISECONDS|TimeUnit.MILLISECONDS|
|workQueue|new LinkedBlockingQueue<Runnable>()|new SynchronousQueue<Runnable>()|new DelayedWorkQueue()|new LinkedBlockingQueue<Runnable>()|

![FixedThreadPool.png](http://upload-images.jianshu.io/upload_images/3481116-40957dca4853c7cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![CachedThreadPool.png](http://upload-images.jianshu.io/upload_images/3481116-b46075121f443342.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![ScheduledThreadPoolExecutor.png](http://upload-images.jianshu.io/upload_images/3481116-bfc5fbff592e1fe5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![SingleThreadExecutor.png](http://upload-images.jianshu.io/upload_images/3481116-6b909f861ad6eed5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 线程池其他常用方法
###向线程池提交任务
>**execute()** 
>**submit()** 
>一般情况下我们使用execute来提交任务，但是有时候可能也会用到submit，使用submit的好处是submit有返回值。
![submit方法.png](http://upload-images.jianshu.io/upload_images/3481116-9a173710a8379770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###关闭线程
>**shutDown()**  关闭线程池，不影响已经提交的任务。
>**shutDownNow()** 关闭线程池，并尝试去终止正在执行的线程。

>**allowCoreThreadTimeOut(boolean value)** 允许核心线程闲置超时时被回收。

# 更深入分析ScheduledThreadPoolExecutor
## ScheduledThreadPoolExecutor的执行主要分为两大部分。
1）当调用ScheduledThreadPoolExecutor的scheduleAtFixedRate()方法或者scheduleWithFixedDelay()方法时，会向ScheduledThreadPoolExecutor的DelayQueue添加一个实现了 RunnableScheduledFutur接口的ScheduledFutureTask。
2）线程池中的线程从DelayQueue中获取ScheduledFutureTask，然后执行任务。

## ScheduledThreadPoolExecutor为了实现周期性的执行任务，对ThreadPoolExecutor做了如下的修改。
·使用DelayQueue作为任务队列。
·获取任务的方式不同（后文会说明）。
·执行周期任务后，增加了额外的处理（后文会说明）。

前面我们提到过，ScheduledThreadPoolExecutor会把待调度的任务（ScheduledFutureTask） 放到一个DelayQueue中。
## ScheduledFutureTask主要包含3个成员变量，如下。
·long型成员变量time，表示这个任务将要被执行的具体时间。
·long型成员变量sequenceNumber，表示这个任务被添加到ScheduledThreadPoolExecutor中的序号。
·long型成员变量period，表示任务执行的间隔周期。
**DelayQueue**封装了一个PriorityQueue，这个PriorityQueue会对队列中的ScheduledFutureTask进行排序。排序时，time小的排在前面（时间早的任务将被先执行）。如果两个 ScheduledFutureTask的time相同，就比较sequenceNumber，sequenceNumber小的排在前面（也就是说，如果两个任务的执行时间相同，那么先提交的任务将被先执行）。
### 首先，让我们看看**ScheduledThreadPoolExecutor中的线程执行周期任务的过程**。
图是 ScheduledThreadPoolExecutor中的线程1执行某个周期任务的4个步骤。

![ScheduledThreadPoolExecutor的任务执行步骤.png](http://upload-images.jianshu.io/upload_images/3481116-767e7f1398185f1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下面是对这4个步骤的说明。
1）线程1从DelayQueue中获取已到期的ScheduledFutureTask(DelayQueue.take())。到期任务 是指ScheduledFutureTask的time大于等于当前时间。
2）线程1执行这个ScheduledFutureTask。
3）线程1修改ScheduledFutureTask的time变量为下次将要被执行的时间。
4）线程1把这个修改time之后的ScheduledFutureTask放回DelayQueue中(DelayQueue.add())。

### 接下来，让我们看看上面的步骤1）获取任务的过程。下面是**DelayQueue.take()**方法的源代码实现。
```
    public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();// 1 
        try {
            for (; ; ) {
                E first = q.peek();
                if (first == null) {
                    available.await();// 2.1 
                } else {
                    long delay = first.getDelay(TimeUnit.NANOSECONDS);
                    if (delay > 0) {
                        long tl = available.awaitNanos(delay);// 2.2  
                    } else {
                        E x = q.poll();// 2.3.1    
                        assert x != null;
                        if (q.size() != 0) available.signalAll();   // 2.3.2   
                        return x;
                    }
                }
            }
        } finally {
            lock.unlock();// 3 
        }
    }
```
DelayQueue.take()的执行示意图

![ScheduledThreadPoolExecutor获取任务的过程.png](http://upload-images.jianshu.io/upload_images/3481116-06d8df778376ad16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图所示，获取任务分为3大步骤。
1）获取Lock。
2）获取周期任务。
·如果PriorityQueue为空，当前线程到Condition中等待；否则执行下面的2.2。
·如果PriorityQueue的头元素的time时间比当前时间大，到Condition中等待到time时间；否 则执行下面的2.3。
·获取PriorityQueue的头元素（2.3.1）；如果PriorityQueue不为空，则唤醒在Condition中等待的所有线程（2.3.2）。
3）释放Lock。
ScheduledThreadPoolExecutor在一个循环中执行步骤2，直到线程从PriorityQueue获取到一 个元素之后（执行2.3.1之后），才会退出无限循环（结束步骤2）。

### 最后，让我们看看ScheduledThreadPoolExecutor中的线程执行任务的步骤4，把 ScheduledFutureTask放入DelayQueue中的过程。下面是**DelayQueue.add()**的源代码实现。

```
    public boolean offer(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();// 1
        try {
            E first = q.peek();
            q.offer(e);// 2.1        
            if (first == null || e.compareTo(first) < 0) available.signalAll();// 2.2
            return true;
        } finally {
            lock.unlock();// 3  
        }
    }
```
DelayQueue.add()的执行示意图:

![ScheduledThreadPoolExecutor添加任务的过程.png](http://upload-images.jianshu.io/upload_images/3481116-9bdf66bea81aca95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图所示，添加任务分为3大步骤。
1）获取Lock。
2）添加任务。
·向PriorityQueue添加任务。
·如果在上面2.1中添加的任务是PriorityQueue的头元素，唤醒在Condition中等待的所有线程。
3）释放Lock。

# BlockingQueue
# Worker
# 自定义线程池