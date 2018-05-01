行为模式（Behavioral Pattern）是对在不同的对象之间划分责任和算法的抽象化。不仅仅是关于类和对象的，而且是关于它们之间的相互作用的。

* 类的行为模式：使用继承关系在几个类之间分配行为。

* 对象的行为模式：使用对象的聚合来分配行为。


> 不变模式（Immutable Pattern）
> 策略模式（Strategy Pattern）
> 模板方法模式（Template Method Pattern）
> 观察者模式（Observer Pattern）包裹模式
> 迭代子模式（Iterator Pattern）游标模式（Cursor）
> 责任链模式（Chain of Responsibility Pattern）
> 命令模式（Command Pattern）
> 备忘录模式（Memento Pattern）快照模式（Snapshot）
> 状态模式（State Pattern）
> 访问者模式（Visitor Pattern）
> 解析器模式（Interpreter Pattern）
> 调停者模式（Mediator Pattern）

# 不变模式
## 定义：
一个对象的状态对象被创建之后就不会再变化。

## 弱不变模式
### 定义：
子类具有可能变化的状态
### 具备：
1.没有任何方法会修改对象的状态。
2.所有的属性都是私有的。
3.
```
/**
 * <b>Description:</b> 弱不变 <br>
 */
public class AClass {
    public void operation(String param) {
        System.out.print("Incoming parameter =" + param);
    }
}
```
```
/**
 * <b>Description:</b> 弱不变 <br>
 */
public class BClass {
    private int state = 0;

    public int getState() {
        return state;
    }

    public void setState(int aState) {
        System.out.println("Parameter is" + aState);
    }
}
```

### 优点：
（1）容易维护
（2）线程是安全的，省掉处理同步化的开销。

### 缺点：
（1）子类是可变的。
（2）子类可以修改父类的状态，从而可能会允许外界修改父类的状态。

### 适用场景：
待思考
## 强不变模式

### 定义：
状态不会改变；子类也是。

### 具备：
1.所有方法应当都是final的。
2.这个类本身就是final的，这样的话不可能会有子类。
/**
 * <b>Description:</b> 强不变 <br>
 */
public final class Product {
    /**
     * final可以确保不会被继承
     * private确保不可被访问，final确保不可被修改
     */
    private final String a;
    private final String b;
    private final double c;

    public Product(String a, String b, double c) {
        //创建对象时必须指定值，因为创建后将无法修改  
        this.a = a;
        this.b = b;
        this.c = c;
    }

    public String getA() {
        return a;
    }

    public String getB() {
        return b;
    }

    public double getC() {
        return c;
    }
}  
### 优点：
（1）容易维护
（2）线程是安全的，省掉处理同步化的开销。

### 缺点：
一旦需要修改不变对象的状态，就只好创建一个新的同类对象。

### 适用场景：
待思考
## 总结：
在设计任何一个类的时候，应当慎重考虑其状态是否有需要变化的可能性。
除非其状态有变化的必要，不然应当将它设计成不变类。

## String类、封装类

---
# 策略模式
## 定义：
一组算法，将每个算法都封装起来，并且使他们之间可以互换。

## 类图：
以后补充
## 结构：
* 环境（Context）：也叫上下文，对策略进行二次封装，目的是避免高层模块对策略的直接调用。

* 抽象（Strategy）策略：通常情况下为一个接口，当各个实现类中存在着重复的逻辑时，则使用抽象类来封装这部分公共的代码，此时，策略模式看上去更像是模版方法模式。

* 具体（ConcreteStrategy）策略：具体策略角色通常由一组封装了算法的类来担任，这些类之间可以根据需要自由替换。
```
/**
 * <b>Description:</b> 环境 <br>
 */
public class Context {
    private Strategy mStrategy;

    public Context(Strategy strategy) {
        mStrategy = strategy;
    }

    /**
     * 策略方法
     */
    public void contextInterface() {
        mStrategy.strategyInterface();
    }
}
/**
 * <b>Description:</b> 抽象策略 <br>
 */
abstract public class Strategy {
    /**
     * 策略方法
     */
    public abstract void strategyInterface();
}

/**
 * <b>Description:</b> 具体策略 <br>
 */
public class ConcreteStrategy extends Strategy {
    /**
     * 策略方法
     */
    @Override
    public void strategyInterface() {
        //write ur algorithm(算法) code here
    }
}
public class Client {
    public static void main(String[] args) {
        Context context = new Context(new ConcreteStrategy());
        context.contextInterface();
    }
}
```
## 优点：
（1）策略类之间可以自由切换，由于策略类实现自同一个抽象，所以他们之间可以自由切换。

（2）易于扩展，增加一个新的策略对策略模式来说非常容易，基本上可以在不改变原有代码的基础上进行扩展。

（3）避免使用多重条件，如果不使用策略模式，对于所有的算法，必须使用条件语句进行连接，通过条件判断来决定使用哪一种算法，在上一篇文章中我们已经提到，使用多重条件判断是非常不容易维护的。
## 缺点：
（1）维护各个策略类会给开发带来额外开销，可能大家在这方面都有经验：一般来说，策略类的数量超过5个，就比较令人头疼了。有时候可以把Context保存到客户端里面，将策略类设计成可共享的，这样策略类实例可以被不同客户端适用，即是适用享元模式来减少对象的数量。

（2）必须对客户端（调用者）暴露所有的策略类，因为使用哪种策略是由客户端来决定的，因此，客户端应该知道有什么策略，并且了解各种策略之间的区别，否则，后果很严重。例如，有一个排序算法的策略模式，提供了快速排序、冒泡排序、选择排序这三种算法，客户端在使用这些算法之前，是不是先要明白这三种算法的适用情况？再比如，客户端要使用一个容器，有链表实现的，也有数组实现的，客户端是不是也要明白链表和数组有什么区别？就这一点来说是有悖于迪米特法则的。
## 适用场景：
（1）多个类中的一个行为。
（2）需要动态地用到这些算法。
（3）算法保密。
（4）当对象的一个行为很复杂时候，把复杂的逻辑放进具体策略里面。
## 总结：
很常用！很好用！
---
# 模板方法模式

## 定义：
定义一个操作中算法的框架，而将一些步骤延迟到子类中，使得子类可以不改变算法的结构即可重定义该算法中的某些特定步骤。
## 类图：
## 结构：
* 抽象模板（Abstract Template）：
抽象类中的方法分为三种：
 抽象方法：父类中只声明但不加以实现，而是定义好规范，然后由它的子类去实现。

 模版方法：由抽象类声明并加以实现。一般来说，模版方法调用抽象方法来完成主要的逻辑功能，并且，模版方法大多会定义为final类型，指明主要的逻辑功能在子类中不能被重写。

 钩子方法：由抽象类声明并加以实现。但是子类可以去扩展，子类可以通过扩展钩子方法来影响模版方法的逻辑。

* 具体模板（Concrete Template）：
实现类用来实现细节。抽象类中的模版方法正是通过实现类扩展的方法来完成业务逻辑。只要实现类中的扩展方法通过了单元测试，在模版方法正确的前提下，整体功能一般不会出现大的错误。

```
/**
 * <b>Description:</b> 抽象模板 <br>
 */
abstract public class AbstractClass {

    /**
     * 模板方法的声明和实现
     */
    public void TemplateMethod() {
        //调用基本方法（由子类实现）
        doOperation1();
        //调用基本方法（由子类实现）
        doOperation2();
        //调用基本方法（已实现）
        doOperation3();
    }

    /**
     * 基本方法的声明（由子类实现）
     */
    protected abstract void doOperation1();

    /**
     * 基本方法的声明（由子类实现）
     */
    protected abstract void doOperation2();

    /**
     * 基本方法（已实现）
     */
    private final void doOperation3(){
        
    }

}

/**
 * <b>Description:</b> 具体模板 <br>
 */
public class ConcreteClass extends AbstractClass {
    /**
     * 基本方法的实现
     */
    @Override
    protected void doOperation1() {
        System.out.println("doOperation1();");
    }

    /**
     * 基本方法的实现
     */
    @Override
    protected void doOperation2() {
        System.out.println("doOperation2();");
    }
}
```
Demo
```
/**
 * <b>Description:</b> 抽象模板 <br>
 */
abstract public class Account {
    protected String accountNumber;

    public Account() {
        accountNumber = null;
    }

    public Account(String accountNumber) {
        this.accountNumber = accountNumber;
    }

    /**
     * 模板方法，计算利息数额
     *
     * @return
     */
    final public double calculateInterest() {
        double interestRate = doCalculateInterestRate();
        String accountType = doCalculateAccountType();
        double amount = calculateAmount(accountType, accountNumber);
        return amount * interestRate;
    }

    /**
     * 计算账户类型
     *
     * @return
     */
    protected abstract String doCalculateAccountType();

    /**
     * 计算利率
     *
     * @return
     */
    protected abstract double doCalculateInterestRate();

    /**
     * 计算金额
     *
     * @param accountType
     * @param accountNumber
     * @return
     */
    final public double calculateAmount(String accountType, String accountNumber) {
        //retrieve amount from database...
        return 7243.00D;
    }
}

/**
 * <b>Description:</b> 定期存款账号 <br>
 * Certificate of Deposite
 */
public class CDAccount extends Account {
    @Override
    protected String doCalculateAccountType() {
        return "Certificate of Deposite";
    }

    @Override
    protected double doCalculateInterestRate() {
        return 0.065D;
    }
}

/**
 * <b>Description:</b> 货币市场账号 <br>
 */
public class MoneyMarketAccoount extends Account {
    @Override
    protected String doCalculateAccountType() {
        return "Money Market";
    }

    @Override
    protected double doCalculateInterestRate() {
        return 0.045D;
    }
}

public class Client {
    private static Account account = null;

    public static void main(String[] args) {
        account = new MoneyMarketAccoount();
        System.out.println("Interest from Money Market account" + account.calculateInterest());
        account = new CDAccount();
        System.out.println("Interest from CD account" + account.calculateInterest());

    }
}
```
## 优点：
1.容易扩展。一般来说，抽象类中的模版方法是不易反生改变的部分，而抽象方法是容易反生变化的部分，因此通过增加实现类一般可以很容易实现功能的扩展，符合开闭原则。

2.便于维护。对于模版方法模式来说，正是由于他们的主要逻辑相同，才使用了模版方法，假如不使用模版方法，任由这些相同的代码散乱的分布在不同的类中，维护起来是非常不方便的。

3.比较灵活。因为有钩子方法，因此，子类的实现也可以影响父类中主逻辑的运行。但是，在灵活的同时，由于子类影响到了父类，违反了里氏替换原则，也会给程序带来风险。这就对抽象类的设计有了更高的要求。

## 缺点：
需要考虑的比较全面。

## 适用场景：
在多个子类拥有相同的方法，并且这些方法逻辑相同时，可以考虑使用模版方法模式。在程序的主框架相同，细节不同的场合下，也比较适合使用这种模式。
## 总结：
很常用，很好用。
---
# 观察者模式

## 定义：
定义对象间一种一对多的依赖关系，使得当每一个对象改变状态，则所有依赖于它的对象都会得到通知并自动更新。

## 类图：
## 结构：
* 被观察者：从类图中可以看到，类中有一个用来存放观察者对象的Vector容器（之所以使用Vector而不使用List，是因为多线程操作时，Vector在是安全的，而List则是不安全的），这个Vector容器是被观察者类的核心，另外还有三个方法：attach方法是向这个容器中添加观察者对象；detach方法是从容器中移除观察者对象；notify方法是依次调用观察者对象的对应方法。这个角色可以是接口，也可以是抽象类或者具体的类，因为很多情况下会与其他的模式混用，所以使用抽象类的情况比较多。

* 观察者：观察者角色一般是一个接口，它只有一个update方法，在被观察者状态发生变化时，这个方法就会被触发调用。

* 具体的被观察者：使用这个角色是为了便于扩展，可以在此角色中定义具体的业务逻辑。

* 具体的观察者：观察者接口的具体实现，在这个角色中，将定义被观察者对象状态发生变化时所要处理的逻辑。

```
/**
 * <b>Description:</b> 抽象主题 <br>
 */
public interface Subject {
    /**
     * 调用这个方法登记一个新的观察者对象
     *
     * @param observer
     */
    public void attach(Observer observer);

    /**
     * 调用这个方法删除一个已经登记过的观察者对象
     *
     * @param observer
     */
    public void detach(Observer observer);

    /**
     * 调用这个方法通知所有登记过的观察者对象
     */
    void notifyObservers();
}/**
 * <b>Description:</b> 抽象主题 <br>
 */
public interface Subject {
    /**
     * 调用这个方法登记一个新的观察者对象
     *
     * @param observer
     */
    public void attach(Observer observer);

    /**
     * 调用这个方法删除一个已经登记过的观察者对象
     *
     * @param observer
     */
    public void detach(Observer observer);

    /**
     * 调用这个方法通知所有登记过的观察者对象
     */
    void notifyObservers();
}
/**
 * <b>Description:</b> 抽象观察者 <br>
 */
public interface Observer {
    /**
     * 调用这个方法会更新自己
     */
    void update();
}
/**
 * <b>Description:</b> 具体主题 <br>
 */
public class ConcreteSubject implements Subject {
    private Vector observersVector = new Vector();

    /**
     * 调用这个方法登记一个新的观察者对象
     *
     * @param observer
     */
    @Override
    public void attach(Observer observer) {
        observersVector.addElement(observer);
    }

    /**
     * 调用这个方法删除一个已经登记过的观察者对象
     *
     * @param observer
     */
    @Override
    public void detach(Observer observer) {
        observersVector.removeElement(observer);
    }

    /**
     * 调用这个方法通知所有登记过的观察者对象
     */
    @Override
    public void notifyObservers() {
        Enumeration enumeration = observers();
        while (enumeration.hasMoreElements()) {
            ((Observer) enumeration.nextElement()).update();
        }
    }

    /**
     * 这个方法给出观察者聚集的Enumeration对象
     *
     * @return
     */
    private Enumeration observers() {
        return ((Vector) observersVector.clone()).elements();
    }
}
/**
 * <b>Description:</b> 具体观察者 <br>
 */
public class ConcreteObserver implements Observer {
    /**
     * 调用这个方法会更新自己
     */
    @Override
    public void update() {
        System.out.println("I am notified");
    }
}
```
另一种实现：
```
/**
 * <b>Description:</b> 抽象主题 <br>
 */
abstract public class Subject {
    /**
     * 这个聚集保存了所有对观察者对象的引用
     */
    private Vector observersVector = new Vector();

    /**
     * 调用这个方法登记一个新的观察者对象
     *
     * @param observer
     */
    public void attach(Observer observer) {
        observersVector.addElement(observer);
        System.out.println("Attached an observer.");

    }

    /**
     * 调用这个方法删除一个已经登记过的观察者对象
     *
     * @param observer
     */
    public void detach(Observer observer) {
        observersVector.removeElement(observer);
    }

    /**
     * 调用这个方法通知所有登记过的观察者对象
     */
    public void notifyObservers() {
        Enumeration enumeration = observers();
        while (enumeration.hasMoreElements()) {
            ((Observer) enumeration.nextElement()).update();
        }
    }

    /**
     * 这个方法给出观察者聚集的Enumeration对象
     *
     * @return
     */
    private Enumeration observers() {
        return ((Vector) observersVector.clone()).elements();
    }
}

/**
 * <b>Description:</b> 具体主题 <br>
 */
public class ConcreteSubject extends Subject {
    private String state;

    /**
     * 调用这个方法更改主题的状态
     *
     * @param newState
     */
    public void change(String newState) {
        state = newState;
        this.notifyObservers();
    }
}

public class Client {
    private static ConcreteSubject subject;
    private static Observer observer;

    public static void main(String[] args) {
        //创建主题对象
        subject = new ConcreteSubject();
        //创建观察者对象
        observer = new ConcreteObserver();
        //将观察者对象登记到主题对象上
        subject.attach(observer);
        //改变主题对象的状态
        subject.change("new state");
    }
}
```
Java:
```
/**
 * <b>Description:</b> 被观察者 <br>
 */
public class Watched extends Observable {
    private String data = "";

    /**
     * 取值方法
     *
     * @return
     */
    public String retrieveData() {
        return data;
    }

    /**
     * 改值方法
     *
     * @return
     */
    public void changeData(String data) {
        if (!this.data.equals(data)) {
            this.data = data;
            setChanged();
        }
        notifyObservers();
    }
}

/**
 * <b>Description:</b> 观察者 <br>
 */
public class Watcher implements Observer {
    public Watcher(Watched w) {
        w.addObserver(this);
    }

    @Override
    public void update(Observable o, Object arg) {
        System.out.println("Data has been changed to:" + ((Watched) o).retrieveData() + "");
    }
}

public class Tester {
    static private Watched watched;
    static private Observer watcher;

    public static void main(String[] args) {
        //创建被观察者对象
        watched = new Watched();
        //创建观察者对象，并将被观察者对象登记
        watcher = new Watcher(watched);
        //给被观察者对象的状态赋值4次
        watched.changeData("In C,we create bugs.");
        watched.changeData("In Java,we inherit bugs.");
        watched.changeData("In Java,we inherit bugs.");
        watched.changeData("In Visual Basic,we visualize bugs.");
    }
}
```
## 优点：
1.抽象耦合。
2.广播通知。

## 缺点：
1.耗时。
2.如果观察者跟被观察者有循环依赖，那要预防死循环。
3.线程问题。
4.观察者仅仅能知道被观察者发生了变化，而不知道被观察者是具体是怎样变化的。
## 适用场景：
自己想想吧。
## 总结：
AWT中的事件处理DEM（委派事件模型Delegation Event Model）就是使用观察者模式实现的。

---
# 迭代子模式
## 定义：
提供一种方法访问一个容器对象中各个元素，而又不暴露该对象的内部细节。

如果要问java中使用最多的一种模式，答案不是单例模式，也不是工厂模式，更不是策略模式，而是迭代器模式，先来看一段代码吧：
```
public static void print(Collection coll){  
   Iterator it = coll.iterator();  
   while(it.hasNext()){  
       String str = (String)it.next();  
       System.out.println(str);  
   }  
}
这个方法的作用是循环打印一个字符串集合，里面就用到了迭代器模式，java语言已经完整地实现了迭代器模式，Iterator翻译成汉语就是迭代器的意思。提到迭代器，首先它是与集合相关的，集合也叫聚集、容器等，我们可以将集合看成是一个可以包容对象的容器，例如List，Set，Map，甚至数组都可以叫做集合，而迭代器的作用就是把容器中的对象一个一个地遍历出来。
```
## 类图：

## 结构：
* 抽象迭代子（Iterator）抽象容器：定义出遍历元素所需的接口。一般是一个接口，提供一个iterator()方法，例如java中的Collection接口，List接口，Set接口等。

* 具体迭代子（ConcreteIterator）具体容器：实现了Iterator接口，并保持迭代过程中的游标位置。比如List接口的有序列表实现ArrayList，List接口的链表实现LinkList，Set接口的哈希列表的实现HashSet等。

* 聚集（Aggregate）抽象迭代器：给出创建迭代子对象的接口。定义遍历元素所需要的方法，一般来说会有这么三个方法：取得第一个元素的方法first()，取得下一个元素的方法next()，判断是否遍历结束的方法isDone()（或者叫hasNext()），移出当前对象的方法remove(),

* 具体聚集（ConcreteAggregate）迭代器实现：实现创建迭代子对象的接口，返回一个合适的具体迭代子实例。

* 客户端（Client）：持有对象聚集及其迭代子对象的有引用，掉哟呵你个迭代子对象的迭代接口，也有可能通过迭代子操作聚集元素的增加和删除。
```
/**
 * <b>Description:</b> 抽象聚集角色 <br>
 */
abstract public class Aggregate {
    /**
     * 工厂方法：返还一个迭代子对象
     *
     * @return
     */
    public abstract Iterator createIterator();
}

/**
 * <b>Description:</b> 抽象迭代子角色 <br>
 */
public interface Iterator {
    /**
     * 迭代方法：移动到第一个元素
     */
    void first();

    /**
     * 迭代方法：移动到下一个元素
     */
    void next();

    /**
     * 迭代方法：是否是最后一个元素
     */
    boolean isDone();

    /**
     * 迭代方法：返还当前元素
     */
    Object currentItem();
}

/**
 * <b>Description:</b> 客户端 <br>
 */
public class Client {
    private Iterator it;
    /**
     * 创建一个聚集类实例
     */
    private Aggregate agg = new ConcreteAggregate();

    public void opration() {
        //得到一个迭代子对象
        it = agg.createIterator();
        while (!it.isDone()) {
            System.out.println(it.currentItem().toString());
            it.next();
        }
    }

    public static void main(String[] args) {
        Client client = new Client();
        client.opration();
    }
}

```
### 白箱聚集与外禀迭代子
```
/**
 * <b>Description:</b> 具体迭代子角色 <br>
 * 向外界提供访问自己内部元素的接口（称做遍历方法或者Traversing Method），从而使外禀迭代子可以通过聚集遍历方法实现迭代功能。
 */
public class ConcreteIterator implements Iterator {
    private ConcreteAggregate agg;
    private int index = 0;
    private int size = 0;

    /**
     * 构造子
     *
     * @param agg
     */
    public ConcreteIterator(ConcreteAggregate agg) {
        this.agg = agg;
        this.index = 0;
        this.size = agg.size();
    }

    @Override
    public void first() {
        index = 0;
    }

    @Override
    public void next() {
        if (index < size) {
            index++;
        }
    }

    @Override
    public boolean isDone() {
        return (index >= size);
    }

    @Override
    public Object currentItem() {
        return agg.getElement(index);
    }
}
/**
 * <b>Description:</b> 具体聚集角色 <br>
 * 白箱聚集，可以是不变对象。
 */
public class ConcreteAggregate extends Aggregate {

    /**
     * 聚集对象。类被加载时就被初始化的。在被加载后，聚集的元素就不再变化。
     */
    private Object[] mObjects = {"Monk Tang", "Monkey", "Pigsy", "Sandy", "Horse"};

    /**
     * 工厂方法：返还一个迭代子对象
     *
     * @return
     */
    @Override
    public Iterator createIterator() {
        return new ConcreteIterator(this);
    }

    /**
     * 取值方法：向外界提供聚集元素
     *
     * @param index
     * @return
     */
    public Object getElement(int index) {
        if (index < mObjects.length) {
            return mObjects[index];
        } else {
            return null;
        }
    }

    /**
     * 取值方法：向外界提供聚集的大小
     *
     * @return
     */
    public int size() {
        return mObjects.length;
    }
}
```
仅有一个游标，
### 黑箱聚集与内禀迭代子
```
/**
 * <b>Description:</b> 具体聚集角色 <br>
 * 黑箱聚集，不可能是不变对象。
 */
public class ConcreteAggregate extends Aggregate {
    /**
     * 聚集对象。类被加载时就被初始化的。在被加载后，聚集的元素就不再变化。
     */
    private Object[] mObjects = {"Monk Tang", "Monkey", "Pigsy", "Sandy", "Horse"};

    /**
     * 工厂方法：返还一个迭代子对象
     *
     * @return
     */
    @Override
    public Iterator createIterator() {
        return new ConcreteIterator();
    }

    /**
     * 内部成员类：具体迭代子类
     */
    private class ConcreteIterator implements Iterator {
        private int currentIndex = 0;

        @Override
        public void first() {
            currentIndex = 0;
        }

        @Override
        public void next() {
            if (currentIndex < mObjects.length) {
                currentIndex++;
            }
        }

        @Override
        public boolean isDone() {
            return currentIndex == mObjects.length;
        }

        @Override
        public Object currentItem() {
            return mObjects[currentIndex];
        }
    }
}
```

### 静态迭代子和动态迭代子

### 过滤迭代子

## 优点：
1.对聚集的操作清晰。
2.遍历算法封装在迭代子里面。
3.用户只需要得到迭代器就可以遍历，而对于遍历算法则不用去关心。

## 缺点：
对于比较简单的遍历（像数组或者有序列表），使用迭代器方式遍历较为繁琐，大家可能都有感觉，像ArrayList，我们宁可愿意使用for循环和get方法来遍历集合。

## 适用场景：
        迭代器模式是与集合共生共死的，一般来说，我们只要实现一个集合，就需要同时提供这个集合的迭代器，就像java中的Collection，List、Set、Map等，这些集合都有自己的迭代器。假如我们要实现一个这样的新的容器，当然也需要引入迭代器模式，给我们的容器实现一个迭代器。
       但是，由于容器与迭代器的关系太密切了，所以大多数语言在实现容器的时候都给提供了迭代器，并且这些语言提供的容器和迭代器在绝大多数情况下就可以满足我们的需要，所以现在需要我们自己去实践迭代器模式的场景还是比较少见的，我们只需要使用语言中已有的容器和迭代器就可以了。

## 总结：
看情况。感觉如果是比较系统的管理（一些算法）集合的话用它还是不错的。
如果是零碎的话就算了。
---
# 责任链模式（Chain of Responsibility Pattern）
## 定义：
为请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。

## 类图：
## 结构：
* 抽象处理者（Handler）：抽象方法。
* 具体处理者（ConcreteHandler）：接受到请求后，可以选择将请求处理掉，或者将请求传给下架。
```
abstract public class Handler {
    /**
     * 处理方法，调用此方法处理请求
     */
    protected Handler successor;

    public abstract void handleRequest();

    /**
     * 复制方法，调用此方法设定下家
     *
     * @param successor
     */
    public void setSuccessor(Handler successor) {
        this.successor = successor;
    }

    /**
     * 取值方法
     *
     * @return
     */
    public Handler getSuccessor() {
        return successor;
    }
}
public class ConcreteHandler extends Handler {
    /**
     * 处理方法，调用此方法处理请求
     */
    @Override
    public void handleRequest() {
        if (getSuccessor() != null) {
            System.out.println("The request is passed to " + getSuccessor());
            getSuccessor().handleRequest();
        } else {
            System.out.println("The request is handled here.");
        }
    }
}
public class Client {
    static private Handler handler1, handler2;

    public static void main(String[] args) {
        handler1 = new ConcreteHandler();
        handler2 = new ConcreteHandler();
        handler1.setSuccessor(handler2);
        handler1.handleRequest();
    }
}

```
## 优点：
1、降低耦合度。它将请求的发送者和接收者解耦。 
2、简化了对象。使得对象不需要知道链的结构。
 3、增强给对象指派职责的灵活性。通过改变链内的成员或者调动它们的次序，允许动态地新增或者删除责任。 
4、增加新的请求处理类很方便。
## 缺点：
1、不能保证请求一定被接收。
 2、系统性能将受到一定影响，而且在进行代码调试时不太方便，可能会造成循环调用。
 3、可能不容易观察运行时的特征，有碍于除错。
## 适用场景：
1、有多个对象可以处理同一个请求，具体哪个对象处理该请求由运行时刻自动确定。
 2、在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。
 3、可动态指定一组对象处理请求。
## 总结：
一些链式的请求出现。（http请求框架）
---
# 命令模式（Command Pattern）
## 定义：
把一个请求或者操作封装到一个对象中。把发出命令的责任和执行命令的责任分割开，为派给不同的对象。
请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。
## 类图：
## 结构：
* 客户（Client）：创建一个具体命令对象并确定其接受者。
* 命令（Command）：声明了一个给所有具体命令类的抽象接口。
* 具体命令（ConcreteCommand）：定义一个接受者和行为之间的弱耦合；实现execute()方法，负责调用接受者的相应操作。
* 请求者（Invoker）：负责调用命令对象执行请求，相关的方法叫做行动方法。
* 接受者（Receiver）：负责具体实施和执行一个请求。任何一个类都可以成为接受者，实施和执行请求的方法叫做行动方法。
```
/**
 * <b>Description:</b> 客户 <br>
 */
public class Client {
    public static void main(String[] args){
        Receiver receiver=new Receiver();
        Command command=new ConcreteCommand(receiver);
        Invoker invoker=new Invoker(command);
        invoker.action();
    }
}
/**
 * <b>Description:</b> 请求者 <br>
 */
public class Invoker {
    private Command mCommand;

    public Invoker(Command command) {
        mCommand = command;
    }

    /**
     * 行动方法
     */
    public void action()  {
        mCommand.execute();
    }
}
/**
 * <b>Description:</b> 接收者 <br>
 */
public class Receiver {

    public Receiver() {
    }

    public void action() {
        System.out.println("Action has been taken.");
    }
}
/**
 * <b>Description:</b> 抽象命令 <br>
 */
public interface Command {

    void execute();
}
/**
 * <b>Description:</b> 具体命令 <br>
 */
public class ConcreteCommand implements Command {
    private Receiver mReceiver;

    public ConcreteCommand(Receiver receiver) {
        mReceiver = receiver;
    }

    @Override
    public void execute() {
        mReceiver.action();
    }
}
```
## 优点：
1、降低了系统耦合度。 
2、新的命令可以很容易添加到系统中去。
## 缺点：
使用命令模式可能会导致某些系统有过多的具体命令类
## 适用场景：
认为是命令的地方都可以使用命令模式
## 总结：
Invoker命令Receiver 去做事，感觉有点像广播跟广播接收器。
---
# 备忘录模式（Memento Pattern）
## 定义：
保存一个对象的某个状态，以便在适当的时候恢复对象。
## 类图：
## 结构：
* 备忘录（Memento）：
将发起人对象的内部状态存储起来。
保护内容不被发起人对象之外的任何对象所读取。
有两个等效的接口：窄接口（黑箱）和宽接口（白箱）
* 发起人（Originator）：
创建一个含有当前内部状态的备忘录对象。
适用备忘录对象存储其内部状态。
* 负责任（Caretaker）：
负责保存备忘录对象。
不检查备忘录对象的内容。

### 白箱
```
/**
 * <b>Description:</b> 发起人角色 <br>
 */
public class Originator {
    private String state;

    public Memento createMemento() {
        //返回一个新的备忘录对象
        return new Memento(state);
    }

    public void restoreMemento(Memento memento) {
        //将发起人恢复到备忘录对象所记载的状态
        this.state = memento.getState();
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
        System.out.println("Current state=" + this.state);
    }
}
/**
 * <b>Description:</b> 备忘录角色 <br>
 */
public class Memento {
    private String state;

    public Memento(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }
}
/**
 * <b>Description:</b> 负责人角色 <br>
 */
public class Caretaker {
    private Memento mMemento;

    public Memento retrieveMemento() {
        return this.mMemento;
    }

    public void saveMemento(Memento memento) {
        this.mMemento = memento;
    }
}
public class Client {
    private static Originator o = new Originator();
    private static Caretaker c = new Caretaker();

    public static void main(String[] args) {
        //改变负责人对象的状态
        o.setState("On");
        //创建备忘录对象，并将发起人对象的状态存储起来
        c.saveMemento(o.createMemento());
        //修改发起人对象的状态
        o.setState("Off");
        //修复发起人对象的装填
        o.restoreMemento(c.retrieveMemento());
    }
}
```
### 黑箱
```
public class Originator {
    private String state;

    public Originator() {
    }
    public MementoIF createMemento() {
        return new Memento(state);
    }

    public void restoreMemento(MementoIF memento) {
        Memento aMemento= (Memento) memento;
        this.state = aMemento.getState();
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }
}
public interface MementoIF {
}
public class Memento implements MementoIF{
    private String saveState;

    public Memento(String someState) {
        this.saveState = someState;
    }

    public String getState() {
        return saveState;
    }

    public void setState(String someState) {
        this.saveState = someState;
    }
}
public class Caretaker {
    private MementoIF mMemento;

    public MementoIF retrieveMemento() {
        return this.mMemento;
    }

    public void saveMementto(MementoIF memento) {
        this.mMemento = memento;
    }
}
public class Client {
    private static Originator o = new Originator();
    private static Caretaker c = new Caretaker();

    public static void main(String[] args) {
        //改变负责人对象的状态
        o.setState("On");
        //创建备忘录对象，并将发起人对象的状态存储起来
        c.saveMementto(o.createMemento());
        //修改发起人对象的状态
        o.setState("Off");
        //修复发起人对象的装填
        o.restoreMemento(c.retrieveMemento());
    }
}
```


## 优点：
1、给用户提供了一种可以恢复状态的机制，可以使用户能够比较方便地回到某个历史的状态。
 2、实现了信息的封装，使得用户不需要关心状态的保存细节。
## 缺点：
消耗资源。如果类的成员变量过多，势必会占用比较大的资源，而且每一次保存都会消耗一定的内存。
## 适用场景：
1、需要保存/恢复数据的相关状态场景。
2、提供一个可回滚的操作。
## 总结：
1、后悔药。 2、打游戏时的存档。 3、Windows 里的 ctri + z。 4、IE 中的后退。 4、数据库的事务管理。
---
# 状态模式（State Pattern）
## 定义：
类的行为是基于它的状态改变的。
## 类图：
## 结构：
* 抽象状态（State）：定义一个接口，用以封装环境（Context）对象的一个特定的状态所对应的行为。
* 具体状态（ConcreteState）：每一个具体状态类都实现了环境（Context）的一个状态所对应的行为。
* 环境状态（Context）:定义客户端所感兴趣的接口，并且保修一个具体状态类的实例。这个具体状态类的实例给出此环境对象的现有状态。
```
/**
 * <b>Description:</b> 环境类 <br>
 */
public class Context {
    private State mState;

    public void sampleOperation() {
        mState.sampleOperation();
    }

    public void setState(State state) {
        mState = state;
    }
}
/**
 * <b>Description:</b> 抽象状态 <br>
 */
public interface State {
    void sampleOperation();
}
/**
 * <b>Description:</b> 具体状态类 <br>
 */
public class ConcreteState implements State {
    @Override
    public void sampleOperation() {
    }
}
```
## 优点：
1、封装了转换规则。
2、枚举可能的状态，在枚举状态之前需要确定状态种类。
3、将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为。 
4、允许状态转换逻辑与状态对象合成一体，而不是某一个巨大的条件语句块。
5、可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数。
## 缺点：
1、状态模式的使用必然会增加系统类和对象的个数。 
2、状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱。
3、状态模式对"开闭原则"的支持并不太好，对于可以切换状态的状态模式，增加新的状态类需要修改那些负责状态转换的源代码，否则无法切换到新增状态，而且修改某个状态类的行为也需修改对应类的源代码。
## 适用场景：
1、行为随状态改变而改变的场景。
2、条件、分支语句的代替者。
## 总结：把对象的状态抽象出来。
一个TCP的例子
```
public interface TcpState {
    void open();
    void close();
    void acknowledge();
}
public class TcpEstablished implements TcpState{
    @Override
    public void open() {
    }
    @Override
    public void close() {
    }
    @Override
    public void acknowledge() {
    }
}
public class TcpConnection {
    private TcpState mTcpState;
    public void setTcpState(TcpState tcpState) {
        mTcpState = tcpState;
    }
    public void open(){
        mTcpState.open();
    }
    public void close(){
        mTcpState.close();
    }
    public void acknowledge(){
        mTcpState.acknowledge();
    }
}
```
---
# 访问者模式（Visitor Pattern）
## 定义：
封装一些施加于某种数据结构元素之上的操作。
## 类图：
## 结构：
* 抽象访问者（Visitor）：声明了一个或者多个访问操作，形成所有的具体元素角色必须实现的接口。
* 具体访问者（ConcreteVisitor）：实现抽象访问者角色所声明的接口，也就是抽象访问者所声明的各个访问操作。
* 抽象节点（Node）：声明一个接受操作，接受一个访问者对象作为一个参量。
* 具体节点（Node）：实现了抽象元素所规定的接受操作。
* 结构对象（ObjectStructure）：遍历结构中所有元素。提供一个高层次的接口让访问者对象可以访问每一个元素；；设计成一个复合对象或者一个聚集。
```
public interface Visitor {
    void visit(NodeA node);

    void visit(NodeB node);
}
public class VisitorA implements Visitor {
    @Override
    public void visit(NodeA node) {
        System.out.println(node.operationA());
    }

    @Override
    public void visit(NodeB node) {
        System.out.println(node.operationB());
    }
}
public class VisitorB implements Visitor {
    @Override
    public void visit(NodeA node) {
        System.out.println(node.operationA());
    }

    @Override
    public void visit(NodeB node) {
        System.out.println(node.operationB());
    }
}
abstract public class Node {
    public abstract void accept(Visitor visitor);
}
public class NodeA extends Node {
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }

    public String operationA() {
        return "NodeA is visited";
    }
}
public class NodeB extends Node {
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }

    public String operationB() {
        return "NodeB is visited";
    }
}
public class ObjectStructure {
    private Vector nodes;
    private Node node;

    public ObjectStructure() {
        nodes = new Vector();
    }

    public void action(Visitor visitor) {
        for (Enumeration e = nodes.elements(); e.hasMoreElements(); ) {
            node = (Node) e.nextElement();
            node.accept(visitor);
        }
    }

    public void add(Node node) {
        nodes.addElement(node);
    }
}
public class Client {
    private static ObjectStructure aObjects;
    private static Visitor visitor;

    static public void main(String[] args) {
        //创建一个结构对象
        aObjects = new ObjectStructure();
        //给结构增加一个节点
        aObjects.add(new NodeA());
        //给结构增加一个节点
        aObjects.add(new NodeB());
        //创建一个新的访问者
        visitor = new VisitorA();
        //让访问者访问结构
        aObjects.action(visitor);
    }
}
```
## 优点：
1、符合单一职责原则。 
2、优秀的扩展性。
 3、灵活性。
## 缺点：
1、具体元素对访问者公布细节，违反了迪米特原则。
2、具体元素变更比较困难。
 3、违反了依赖倒置原则，依赖了具体类，没有依赖抽象。
## 适用场景：
## 总结：
---
# 解析器模式（Interpreter Pattern）
## 定义：
这种模式实现了一个表达式接口，该接口解释一个特定的上下文。这种模式被用在 SQL 解析、符号处理引擎等。
## 类图：
## 结构：
* 抽象表达式（Expression）：声明一个所有的具体表达式角色都需实现的抽象接口。这个接口主要是一个interpret()方法，称做解析操作
* 终结符表达式（Terminal Expression）：这是一个具体角色。
实现了抽象表达式角色所要求的接口，主要是一个interpret()方法；
文法中的每一个终结符都有一个具体终结表达式与之相对应。
* 非终结符表达式（Nonterminal Expression）：这是一个具体角色。

* 客户端（Client）：
建造一个抽象语法树
调用解析操作interpret()
* 环境（Context）：提供解析器之外的一些全局信息。
```
/**
 * <b>Description:</b> 这个抽象类代表终结类和非终结类的抽象化<br>
 */
abstract public class Expression {
    /**
     * 以环境类为准，本方法解析给定的任何一个表达式
     *
     * @return
     */
    public abstract boolean interpret(Context ctx);

    /**
     * 检验两个表达式再结构上是否相同
     *
     * @param o
     * @return
     */
    @Override
    public abstract boolean equals(Object o);

    /**
     * 返回表达式的hash code
     *
     * @return
     */
    @Override
    public abstract int hashCode();

    /**
     * 将表达式转换成字符串
     *
     * @return
     */
    @Override
    public abstract String toString();
}
public class Constant extends Expression {
    private boolean value;

    public Constant(boolean value) {
        this.value = value;
    }

    @Override
    public boolean interpret(Context ctx) {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof Constant) {
            return this.value == ((Constant) o).value;
        }
        return false;
    }

    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return new Boolean(value).toString();
    }
}

public class Constant extends Expression {
    private boolean value;

    public Constant(boolean value) {
        this.value = value;
    }

    @Override
    public boolean interpret(Context ctx) {
        return value;
    }


    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof Constant) {
            return this.value == ((Constant) o).value;
        }
        return false;
    }


    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return new Boolean(value).toString();
    }
}
public class Variable extends Expression {
    private String name;

    public Variable(String name) {
        this.name = name;
    }
    
    @Override
    public boolean interpret(Context ctx) {
        return ctx.lookup(this);
    }

    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof Variable) {
            return this.name.equals(((Variable) o).name);
        }
        return false;
    }

    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return name;
    }
}
public class And extends Expression {
    private Expression left, right;

    public And(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public boolean interpret(Context ctx) {
        return ctx.interpret(this) && ctx.interpret(this);
    }

    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof And) {
            return this.left.equals(((And) o).left) && this.right.equals(((And) o).right);
        }
        return false;
    }

    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return "(" + left.toString() + "AND" + right.toString() + ")";
    }
}
public class Or extends Expression {
    private Expression left, right;

    public Or(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public boolean interpret(Context ctx) {
        return ctx.interpret(this) || ctx.interpret(this);
    }

    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof Or) {
            return this.left.equals(((Or) o).left) && this.right.equals(((Or) o).right);
        }
        return false;
    }

    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return "(" + left.toString() + "OR" + right.toString() + ")";
    }
}
public class Not extends Expression {
    private Expression exp;

    public Not(Expression exp) {
        this.exp = exp;
    }

    @Override
    public boolean interpret(Context ctx) {
        return !exp.interpret(ctx);
    }

    @Override
    public boolean equals(Object o) {
        if (o != null && o instanceof Not) {
            return this.exp.equals(((Not) o).exp);
        }
        return false;
    }

    @Override
    public int hashCode() {
        return (this.toString()).hashCode();
    }

    @Override
    public String toString() {
        return "(Not" + exp.toString() + ")";
    }
}
public class Context {
    private HashMap map = new HashMap();

    public void assign(Variable var, boolean value) {
        map.put(var, new Boolean(value));
    }

    public boolean lookup(Variable var) throws IllegalArgumentException {

        Boolean value = (Boolean) map.get(var);
        if (value == null) {
            throw new IllegalArgumentException();
        }
        
        return value.booleanValue();
    }

    public boolean interpret(Expression expression) {
        return false;
    }
}
public class Client {
    private static Context ctx;
    private static Expression exp;

    public static void main(String[] args) {
        ctx = new Context();
        Variable x = new Variable("x");
        Variable y = new Variable("y");
        Constant c = new Constant(true);
        ctx.assign(x, false);
        ctx.assign(y, true);
        exp = new Or(new And(c, y), new And(y, new Not(x)));
        System.out.println("x=" + x.interpret(ctx));
        System.out.println("y=" + y.interpret(ctx));
        System.out.println(exp.toString() + "=" + exp.interpret(ctx));
    }
}
```
## 应用实例：

 1、中国加入 WTO 之前是各个国家相互贸易，结构复杂，现在是各个国家通过 WTO 来互相贸易。

 2、机场调度系统。

 3、MVC 框架，其中C（控制器）就是 M（模型）和 V（视图）的中介者。

## 优点：
1、可扩展性比较好，灵活。 
2、增加了新的解释表达式的方式。 
3、易于实现简单文法。
## 缺点：
 1、可利用场景比较少。
 2、对于复杂的文法比较难维护。
 3、解释器模式会引起类膨胀。
 4、解释器模式采用递归调用方法。
## 适用场景：
## 总结：
---
# 调停者模式（Mediator Pattern）
## 定义：
中介者模式（Mediator Pattern）是用来降低多个对象和类之间的通信复杂性。这种模式提供了一个中介类，该类通常处理不同类之间的通信，并支持松耦合，使代码易于维护。中介者模式属于行为型模式。
## 类图：
## 结构：
* 抽象调停者：
* 具体调停者：
* 抽象同事类：
* 具体同事类：
```
/**
 * <b>Description:</b> 抽象同事类 <br>
 */
abstract public class Colleague {
    private Mediator mMediator;

    public Colleague(Mediator mediator) {
        mMediator = mediator;
    }

    /**
     * 取值方法，提供调停者对象
     *
     * @return
     */
    public Mediator getMediator() {
        return mMediator;
    }

    /**
     * 行动方法，由子类实现
     */
    public abstract void action();

    /**
     * 示意性的商业方法，调用此方法可以改变对象的内部状态
     */
    public void change() {
        mMediator.colleagueChanged(this);
    }
}
/**
 * <b>Description:</b> 具体同事类 <br>
 */
public class Colleague1 extends Colleague {

    /**
     * 构造子，作为参量接收调停者对象
     *
     * @param mediator
     */
    public Colleague1(Mediator mediator) {
        super(mediator);
    }

    /**
     * 行动方法的具体实现
     */
    @Override
    public void action() {
        System.out.println("This is an action from Colleague 1");
    }
}
/**
 * <b>Description:</b> 具体同事类 <br>
 */
public class Colleague2 extends Colleague {

    /**
     * 构造子，作为参量接收调停者对象
     *
     * @param mediator
     */
    public Colleague2(Mediator mediator) {
        super(mediator);
    }

    /**
     * 行动方法的具体实现
     */
    @Override
    public void action() {
        System.out.println("This is an action from Colleague 2");
    }
}

/**
 * <b>Description:</b> 抽象调停者类 <br>
 */
abstract public class Mediator {
    /**
     * 事件方法，由子类实现
     * @param colleague
     */
    abstract void colleagueChanged(Colleague colleague);

    public static void main(String[] args) {
        ConcreteMediator mediator = new ConcreteMediator();
        mediator.createConcreteMediator();
        Colleague c1 = new Colleague1(mediator);
        Colleague c2 = new Colleague2(mediator);
        mediator.colleagueChanged(c1);
    }
}
/**
 * <b>Description:</b> 具体调停者类 <br>
 */
public class ConcreteMediator extends Mediator {
    private Colleague1 mColleague1;
    private Colleague2 mColleague2;

    /**
     * 事件方法的具体实现
     *
     * @param colleague
     */
    @Override
    public void colleagueChanged(Colleague colleague) {
        mColleague1.action();
        mColleague2.action();
    }

    /**
     * 工厂方法，创建同事对象
     */
    public void createConcreteMediator() {
        mColleague1 = new Colleague1(this);
        mColleague2 = new Colleague2(this);
    }

    /**
     * 取值方法，提供同事对象
     *
     * @return
     */
    public Colleague1 getColleague1() {
        return mColleague1;
    }

    /**
     * 取值方法，提供同事对象
     *
     * @return
     */
    public Colleague2 getColleague2() {
        return mColleague2;
    }
}
```
## 优点：
 1、降低了类的复杂度，将一对多转化成了一对一。 2、各个类之间的解耦。 3、符合迪米特原则。
## 缺点：
中介者会庞大，变得复杂难以维护。
## 适用场景： 
1、系统中对象之间存在比较复杂的引用关系，导致它们之间的依赖关系结构混乱而且难以复用该对象。
 
2、想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。
## 总结：注意事项：不应当在职责混乱的时候使用。