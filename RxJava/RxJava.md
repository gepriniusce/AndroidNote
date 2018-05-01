# RxJava

> RxJava是啥？呵呵！？
> 关键是事件变换，线程调度,响应式编程
> 而怎么实现这些呢：最关键的就是接口泛型！
> 实现这些有什么用呢？
```

```

# 基本概念

**Observable：** 被观察者
**OnSubscrible：** 观察者
**Operator：** 观察者
**Subscriber：** 订阅者->其实也是一个观察者

首先我们要搞懂这三者之间的关系，
其实很简单
![这里写图片描述](http://img.blog.csdn.net/20170531003350150?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**Action：**  Call方法无返回值
**Func：** Call方法有返回值
![这里写图片描述](http://img.blog.csdn.net/20170531005151404?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这是基本的对象概念。
这样子看其实也挺抽象的，怎样才能真正理解呢？
其实他们是有引入泛型的
然而+上泛型以后就复杂多了
接下来我们看看+上泛型的关系图
![这里写图片描述](http://img.blog.csdn.net/20170531010215049?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 具体化 举个栗子
上面说的太抽象了-->接下来具体化一下来深入理解这里的概念
**Observable：** 被观察者
抽象的
**OnSubscrible<T>：** 观察者-->产品T
**Operator< T, R> ：**   观察者-->项目组
**Subscriber<T>：** 订阅者->其实也是一个观察者-->抽象的技术 
**Action<T>-->void call(T t)：**  产品技能-->提需求
**Func< T, R>-->R call(T t)：**  需求-->转化成-->功能
具体的
**OnSubscribleImpl< T, R> implements OnSubscrible<R>** 技术的老大
**MapSubscriber< T , R>** 具体的技术


# 事件变换
接下来，咋们就看看事件变换是啥玩意！
其实也就是把< T >跟< R >做交换！
这么6！？
那么是怎样把他们做交换的呢？
这时候我们就可以通过
实现 **OnSubscrible：**  与 **Operator：**  两个接口来实现！
当然这里面我们应该需要用到 **Subscriber：** 来帮助！

那么怎么搞法呢？！哈哈！

我们可以用心想想！
这里其实是比较难的，我就直接贴代码了！
怎么理解呢？
其实关键在于接口对象的使用。
```
/**
 * <b>Project:</b> ${file_name}<br>
 * <b>Create Date:</b> 2017/5/29<br>
 * <b>Author:</b> Tongson<br>
 * <b>Description:</b> ObservableDistinctUntilChanged  OnSubscrible对象交换者 供subscrible(Subscriber<? super T> subscriber)调用 <br>
 * <b>Description:</b>  技术的老大-->分派任务 <br>
 */
public class OnSubscribleImpl<T, R> implements OnSubscrible<R> {

    /**
     * 产品
     */
    OnSubscrible<T> onSubscrible;

    /**
     * 交换操纵者 -->需求换成-->具体的功能
     * extends   返回类型的限定
     * super     参数类型的限定
     * T  需求
     * R 功能
     */
    Operator<? extends R, ? super T> operator;

    public OnSubscribleImpl(OnSubscrible<T> onSubscrible, Operator<? extends R, ? super T> operator) {
        this.onSubscrible = onSubscrible;
        this.operator = operator;
    }

    /**
     * 外部调用
     * 让交换操纵者 返回Subscriber<? super T> tSubscriber
     * 老大  让产品提需求给技术去做功能（分派任务）
     *
     * @param subscriber
     */
    @Override
    public void call(Subscriber<? super R> subscriber) {
        /**
         *  operator  项目组
         *  tSubscriber  技术
         */
        Subscriber<? super T> tSubscriber = operator.call(subscriber);
        /**
         * 老大 直接把产品分派给技术（分派任务）
         */
        onSubscrible.call(tSubscriber);
    }
}
```
```
/**
 * <b>Project:</b> ${file_name}<br>
 * <b>Create Date:</b> 2017/5/27<br>
 * <b>Author:</b> Tongson<br>
 * <b>Description:</b> 方法交换操纵者 项目组 具体的观察者-->具体的项目组<br>
 * 项目组
 * T  需求
 * R  功能
 */
public class OperatorMap<T, R> implements Operator<R, T> {

    Func<? super T, ? extends R> transformer;

    public OperatorMap(Func<? super T, ? extends R> transformer) {
        this.transformer = transformer;
    }

    @Override
    public Subscriber<? super T> call(Subscriber<? super R> subscriber) {
        /**
         * 返回 Subscriber<? super T> 对象
         */
        return new MapSubscriber<>(subscriber, transformer);
    }

    /**
     * 具体的技术
     *
     * @param <T>
     * @param <R>
     */
    private class MapSubscriber<T, R> extends Subscriber<T> {

        private Subscriber<? super R> actual;

        private Func<? super T, ? extends R> mapper;

        public MapSubscriber(Subscriber<? super R> actual, Func<? super T, ? extends R> mapper) {
            this.actual = actual;
            this.mapper = mapper;
        }


        @Override
        public void onNext(T t) {
            /**
             * Map中call方法的调用
             */
            R r = mapper.call(t);
            /**
             * Subscriber<? super R> 对象中的方法的调用  技术-->做功能
             */
            actual.onNext(r);
        }
    }
}
```
# 线程调度
然后我们就来看看
线程是怎样去控制的？！
其实也不是很难（比起上面的事件变换容易）
> 主要是通过继承OnSubscrible中也就是OnSubscrible的儿子提供的线程控制的方法
> Schedulers

在call方法中我们用 **Observable：** subscrible（）->调起订阅的时候在里面把这方法转成想要的线程就可以了！

```
                /**
                 * String  类型  代表需求
                 * Bitmap  代表 功能
                 * new OnSubscrible  产品
                 */
                Observable.create(new OnSubscrible<String>() {
                    @Override
                    public void call(Subscriber<? super String> subscriber) {
                        spannableStringBuilder.append("Observable -->Subscriber call:1" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");

                        subscriber.onNext("http://www.baidu.com");
                        spannableStringBuilder.append("Observable -->Subscriber call:5" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");
                        runOnUiThread(new Runnable() {
                            @Override
                            public void run() {
                                resultTv.setText(spannableStringBuilder);
                            }
                        });
                    }
                }).subscribleOnIO().map(new Func<String, Bitmap>() {
                    //具体的转换类型角色
                    //老大让产品提需求给技术去做功能
                    @Override
                    public Bitmap call(String s) {
                        spannableStringBuilder.append("map-->OnSubscribleImpl call:" + "s:" + s + "\n");
                        spannableStringBuilder.append("currentThread:" + Thread.currentThread().getName() + "\n");
                        Bitmap bitmap = BitmapFactory.decodeResource(MainActivity.this.getResources(), R.mipmap.ic_launcher);
                        return bitmap;
                    }
                }).subscrible(new Subscriber<Bitmap>() {
                    @Override
                    public void onNext(Bitmap bitmap) {
                        spannableStringBuilder.append("Subscriber onNext -->" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");
                    }
                });
```
![这里写图片描述](http://img.blog.csdn.net/20170609090931907?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 被观察者 Observable
```
/**
 * <b>Project:</b> ${file_name}<br>
 * <b>Create Date:</b> 2017/5/27<br>
 * <b>Author:</b> Tongson<br>
 * <b>Description:</b> 被观察者 <br>
 */
public class Observable<T> {

    /**
     * 接口 回调
     */
    private OnSubscrible<T> onSubscrible;

    /**
     * 构造器
     *
     * @param onSubscrible
     */
    private Observable(OnSubscrible<T> onSubscrible) {
        this.onSubscrible = onSubscrible;
    }


    /**
     * 开始
     *
     * @param onSubscrible
     * @param <T>
     * @return
     */
    public static <T> Observable<T> create(OnSubscrible<T> onSubscrible) {
        return new Observable<>(onSubscrible);
    }


    /**
     * 订阅
     *
     * @param subscriber
     */
    public void subscrible(Subscriber<? super T> subscriber) {
        onSubscrible.call(subscriber);
    }

    /**
     * 实例化 项目组
     * 实例化 交换者
     *
     * @param func
     * @param <R>
     * @return
     */
    public <R> Observable<R> map(Func<? super T, ? extends R> func) {
        return lift(new OperatorMap<>(func));
    }

    /**
     * 实例化 老大
     * 实例化 对象交换者
     *
     * @param trOperatorMap
     * @param <R>
     * @return
     */
    public <R> Observable<R> lift(OperatorMap<T, R> trOperatorMap) {
        return new Observable<>(new OnSubscribleImpl<>(onSubscrible, trOperatorMap));
    }

    public Observable<T> subscribleOnIO() {
        return create(new OnSubscribleOnIO<T>(this));
    }

    public Observable<T> subscribleMain() {
        return create(new OnSubscrbleMain<T>(new Handler(Looper.getMainLooper()), this));
    }

}
```
这个被观察者贯穿所有的存在！

首先
## create

## 订阅
subscrible
map
lift

## 线程
subscribleOnIO
subscribleMain

## return
Observable
这里的方法都是返回Observable为了可以继续调用下去吧

# 响应式编程
```
                /**
                 * 响应式编程
                 */
                spannableStringBuilder.append("btn_2-------------------\n");
                Observable.create(new OnSubscrible<String>() {
                    @Override
                    public void call(Subscriber<? super String> subscriber) {
                        spannableStringBuilder.append("Observable -->Subscriber call:1" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");
                        subscriber.onNext("http://www.baidu.com");
                        spannableStringBuilder.append("Observable -->Subscriber call:5" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");
                    }
                }).map(new Func<String, Bitmap>() {
                    //具体的转换类型角色
                    @Override
                    public Bitmap call(String s) {
                        spannableStringBuilder.append("map-->OnSubscribleImpl call:" + "s:" + s + "\n");
                        spannableStringBuilder.append("currentThread:" + Thread.currentThread().getName() + "\n");
                        Bitmap bitmap = BitmapFactory.decodeResource(MainActivity.this.getResources(), R.mipmap.ic_launcher);
                        return bitmap;
                    }
                }).subscrible(new Subscriber<Bitmap>() {
                    @Override
                    public void onNext(Bitmap bitmap) {
                        spannableStringBuilder.append("Subscriber onNext -->" + "\n" + "currentThread:" + Thread.currentThread().getName() + "\n");
                    }
                });
                spannableStringBuilder.append("btn_2-------------------\n\n");
                resultTv.setText(spannableStringBuilder);
```
![这里写图片描述](http://img.blog.csdn.net/20170609091036445?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 手写RxJavaDemo 
https://github.com/gepriniusce/TongsonPlay/tree/rxjava

# RxJava2用法Demo
https://github.com/amitshekhariitbhu/RxJava2-Android-Samples

RxJava的思想很值得学习
而RxJava框架也是很好，对于复杂逻辑的代码我们可以直接用Rx这样子就算换一个同事来看这代码也比较清晰。
