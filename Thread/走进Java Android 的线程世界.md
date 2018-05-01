直奔主题

#首先我们来看看Java的线程通讯基础

```

    //产品
    static class ProductObject{
        //线程操作变量可见
        public volatile static String value;
    }

    //生产者线程
    static class Producer extends Thread{
        Object lock;

        public Producer(Object lock) {
            this.lock = lock;
        }

        @Override
        public void run() {
            //不断生产产品
            while(true){
                synchronized (lock) { //互斥锁
                    //产品还没有被消费，等待
                    if(ProductObject.value != null){
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    //产品已经消费完成，生产新的产品
                    ProductObject.value = "NO:"+System.currentTimeMillis();
                    Log.i(TAG,"生产产品："+ProductObject.value);
                    lock.notify(); //生产完成，通知消费者消费
                }
            }

        }
    }

    //消费者线程
    static class Consumer extends Thread{
        Object lock;
        public Consumer(Object lock) {
            this.lock = lock;
        }

        @Override
        public void run() {
            while(true){
                synchronized (lock) {
                    //没有产品可以消费
                    if(ProductObject.value == null){
                        //等待，阻塞
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    Log.i(TAG,"消费产品："+ProductObject.value);
                    ProductObject.value = null;
                    lock.notify(); //消费完成，通知生产者，继续生产
                }
            }
        }
    }
```
调用:
```
        Object lock = new Object();
        new Producer(lock).start();
        new Consumer(lock).start();
```
Log:
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-c1ece70b1fe68a36?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这是一些简单的线程通讯基础，两个线程进行交互的用法。


#接下来分析一下 Java中的FutureTask
我们先来看看类图
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-65ae0b43d47018c5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再看看怎么用的
```
    //异步任务
    static class Task implements Callable<Integer> {
        //返回异步任务的执行结果
        @Override
        public Integer call() throws Exception {
            int i = 0;
            for (; i < 10; i++) {
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                Log.i(TAG, Thread.currentThread().getName() + "_" + i);
            }
            return i;
        }
    }
```
初始化执行
```
        Task work = new Task();
        FutureTask<Integer> future = new FutureTask<Integer>(work) {
            //异步任务执行完成，回调
            @Override
            protected void done() {
                try {
                    Log.i(TAG, "done:" + get());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
        };
        //线程池（使用了预定义的配置）
        ExecutorService executor = Executors.newCachedThreadPool();
        executor.execute(future);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
        //取消异步任务
//        future.cancel(true);
        try {
            //阻塞，等待异步任务执行完毕
            Log.i(TAG, "" + future.get());

        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
```
Log：
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-ada8e94692ffd835?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从上面代码可以看出
这个FutureTask 解决异步任务执行的结果，主线程无法轻易的获取
这是怎么做到的呢-->我们就需要翻越FutureTask的代码
### Future
```
package java.util.concurrent;

public interface Future<V> {

    boolean cancel(boolean mayInterruptIfRunning);

    boolean isCancelled();

    boolean isDone();

    V get() throws InterruptedException, ExecutionException;

    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```
### Runnable 
```
package java.lang;

public interface Runnable {
    public abstract void run();
}
```
### RunnableFuture
```
package java.util.concurrent;

public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```
## FutureTask
```

package java.util.concurrent;

import java.util.concurrent.locks.LockSupport;

public class FutureTask<V> implements RunnableFuture<V> {
	......以下只截取部分代码
    /** The underlying callable; nulled out after running */
    private Callable<V> callable;

    /** The thread running the callable; CASed during run() */
    private volatile Thread runner;

    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
    }

    public boolean cancel(boolean mayInterruptIfRunning) {
        if (!(state == NEW &&
              U.compareAndSwapInt(this, STATE, NEW,
                  mayInterruptIfRunning ? INTERRUPTING : CANCELLED)))
            return false;
        try {    // in case call to interrupt throws exception
            if (mayInterruptIfRunning) {
                try {
                    Thread t = runner;
                    if (t != null)
                        t.interrupt();
                } finally { // final state
                    U.putOrderedInt(this, STATE, INTERRUPTED);
                }
            }
        } finally {
            finishCompletion();
        }
        return true;
    }

    /**
     * @throws CancellationException {@inheritDoc}
     */
    public V get() throws InterruptedException, ExecutionException {
        int s = state;
        if (s <= COMPLETING)
            s = awaitDone(false, 0L);
        return report(s);
    }



    
    protected void done() { }

   
    protected void set(V v) {
        if (U.compareAndSwapInt(this, STATE, NEW, COMPLETING)) {
            outcome = v;
            U.putOrderedInt(this, STATE, NORMAL); // final state
            finishCompletion();
        }
    }

    
    protected void setException(Throwable t) {
        if (U.compareAndSwapInt(this, STATE, NEW, COMPLETING)) {
            outcome = t;
            U.putOrderedInt(this, STATE, EXCEPTIONAL); // final state
            finishCompletion();
        }
    }

    public void run() {
        if (state != NEW ||
            !U.compareAndSwapObject(this, RUNNER, null, Thread.currentThread()))
            return;
        try {
            Callable<V> c = callable;
            if (c != null && state == NEW) {
                V result;
                boolean ran;
                try {
                    result = c.call();
                    ran = true;
                } catch (Throwable ex) {
                    result = null;
                    ran = false;
                    setException(ex);
                }
                if (ran)
                    set(result);
            }
        } finally {
            // runner must be non-null until state is settled to
            // prevent concurrent calls to run()
            runner = null;
            // state must be re-read after nulling runner to prevent
            // leaked interrupts
            int s = state;
            if (s >= INTERRUPTING)
                handlePossibleCancellationInterrupt(s);
        }
    }

   
    private void finishCompletion() {
        // assert state > COMPLETING;
        for (WaitNode q; (q = waiters) != null;) {
            if (U.compareAndSwapObject(this, WAITERS, q, null)) {
                for (;;) {
                    Thread t = q.thread;
                    if (t != null) {
                        q.thread = null;
                        LockSupport.unpark(t);
                    }
                    WaitNode next = q.next;
                    if (next == null)
                        break;
                    q.next = null; // unlink to help gc
                    q = next;
                }
                break;
            }
        }

        done();

        callable = null;        // to reduce footprint
    }
    ......以上只截取部分代码
}
```
从上面代码可以看出
Java FutureTask 异步任务操作提供了便利性
1.获取异步任务的返回值--> V get()
2.监听异步任务的执行完毕-->finishCompletion()-->done()
3.取消异步任务-->cancel(boolean mayInterruptIfRunning)

# 从上面的例子，我们可以看到Executors去调用这个Future那么这个Executors又是啥玩意呢？！

像刚才看Future那样我们点进去看看这个Executors我们可以发现很多东西，然而，其实这个就是鼎鼎大名的 **线程池** ！
我们还是先看看类图
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-934f71661c705380?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为实现类方法太多了，还是直接看api文档比较好。


一个任务
```
    static class MyTask implements Runnable {

        int i;

        public MyTask(int i) {
            this.i = i;
        }

        @Override
        public void run() {
            Log.i(TAG, Thread.currentThread().getName() + ";i:" + i);
        }
        
    }
```
初始化-->运行线程池
```
        int CPU_COUNT = Runtime.getRuntime().availableProcessors();  //可用的CPU个数
        Log.i(TAG, "CPU_COUNT-->" + CPU_COUNT);
        int CORE_POOL_SIZE = CPU_COUNT + 1; //5(corePoolSize - 池中所保存的线程数，包括空闲线程。)
        Log.i(TAG, "CORE_POOL_SIZE-->" + CORE_POOL_SIZE);
        int MAXIMUM_POOL_SIZE = CPU_COUNT * 2 + 1; //9(maximumPoolSize - 池中允许的最大线程数。)
        Log.i(TAG, "MAXIMUM_POOL_SIZE-->" + MAXIMUM_POOL_SIZE);

        int KEEP_ALIVE = 1;//(keepAliveTime - 当线程数大于核心时，此为终止前多余的空闲线程等待新任务的最长时间。)

        //任务队列（128）(workQueue - 执行前用于保持任务的队列。此队列仅由保持 execute 方法提交的 Runnable 任务。)
        final BlockingQueue<Runnable> sPoolWorkQueue = new LinkedBlockingQueue<Runnable>(128);

        //线程工厂(threadFactory - 执行程序创建新线程时使用的工厂。)
        ThreadFactory sThreadFactory = new ThreadFactory() {
            private final AtomicInteger mCount = new AtomicInteger(1);

            public Thread newThread(Runnable r) {
                String name = "Thread #" + mCount.getAndIncrement();
                Log.i(TAG, "newThread-->" + name);
                return new Thread(r, name);
            }
        };

        //线程池
        Executor THREAD_POOL_EXECUTOR = new ThreadPoolExecutor(CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE,
                //(unit - keepAliveTime 参数的时间单位。)
                TimeUnit.SECONDS, sPoolWorkQueue, sThreadFactory);
        
        
        /*  如果运行的线程少于 corePoolSize，则 Executor 始终首选添加新的线程，而不进行排队。
            如果运行的线程等于或多于 corePoolSize，则 Executor 始终首选将请求加入队列，而不添加新的线程。
            如果无法将请求加入队列，则创建新的线程，除非创建此线程超出 maximumPoolSize，在这种情况下，任务将被拒绝。*/
        //执行异步任务
        //如果当前线程池中的数量大于corePoolSize，缓冲队列workQueue已满，
        //并且线程池中的数量等于maximumPoolSize，新提交任务由Handler处理。
        //RejectedExecutionException
        for (int i = 0; i < 200; i++) {
            //相当于new AsyncTask().execute();
            THREAD_POOL_EXECUTOR.execute(new MyTask(i));
        }
```
Log如下
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-689fa71658ca1cab?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从Log我们可以看出每个任务都有一个线程去完成。

#其实这个就是Android中AsyncTask的原理我们不妨分析一下AsyncTask：
### 初始化
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-6fc9953a2275bcf0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 执行
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-7429a2f91a9bcf3d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 返回
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-6740ac94043322cb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 返回UI线程
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-b362431d4759e872?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Progress更新UI
![这里写图片描述](http://upload-images.jianshu.io/upload_images/3481116-9fb6495187df59c8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


JAVA API 文档：http://tool.oschina.net/apidocs/apidoc?api=jdk-zh
Android API文档：http://tool.oschina.net/apidocs/apidoc?api=android/reference
文章代码：https://github.com/gepriniusce/TongsonPlay/tree/Threah