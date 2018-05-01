结构模式（Structural Pattern）描述如何将类或者对象结合在一起形成更大的结构。
* 类的结构模式：（静态的）
使用继承来把类、接口等组合在一起，以形成更大的机构。一个类的结构模式的典型栗子就是类形式的适配器模式。
* 对象的结构模式：（动态的）
怎样把各种不同类型的对象组合在一起，以实现新的功能的方法。

> 适配器模式（Adapter Pattern）
> 缺省适配模式（Default Adapter Pattern）
> 合成模式（Composite Pattern）
> 装饰模式（Decorator Pattern）包裹模式
> 代理模式（Proxy Pattern）
> 享元模式（Flyweight Pattern）
> 门面模式（Facade Pattern）
> 桥梁模式（Bridge Pattern）
---
# 适配器模式
把一个类的接口变成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。
## 类的适配器模式
* 目标（Target）角色：这就是所期待得到的接口。（不能是类）
* 源（Adaptee）角色：现有需要适配的接口。
* 适配器（Adapter）角色：适配器类是本模式的核心。适配器把源接口转换成目标* 接口。（不能是接口，只能是具体类）
```
/**
 * <b>Description:</b> 目标 <br>
 */
public interface Target {

    /**
     * 这是源类也有的方法sampleOperation1
     */
    void sampleOperation1();
    /**
     * 这是源类没有的方法sampleOperation2
     */
    void sampleOperation2();
    
}
/**
 * <b>Description:</b> 源类 <br>
 */
public class Adaptee {
    /**
     * 源类含有方法sampleOperation1
     */
    public void sampleOperation1() {

    }
}
/**
 * <b>Description:</b> 适配器 <br>
 */
public class Adapter extends Adaptee implements Target {
    /**
     * 由于源类没有方法sampleOpration2
     * 因此适配器类补充上这个方法
     */
    @Override
    public void sampleOperation2() {
        //write ur code here
    }
}
```
## 对象的适配器模式
* 目标（Target）角色：这就是所期待得到的接口。（可以是具体的或者抽象类）
* 源（Adaptee）角色：现有需要适配的接口。
* 适配器（Adapter）角色：适配器类是本模式的核心。适配器把源接口转换成目标接口。（必须是具体类）
```
/**
 * <b>Description:</b> 目标 <br>
 */
public interface Target {

    /**
     * 这是源类也有的方法sampleOperation1
     */
    void sampleOperation1();
    /**
     * 这是源类没有的方法sampleOperation2
     */
    void sampleOperation2();
    
}
/**
 * <b>Description:</b> 源类 <br>
 */
public class Adaptee {
    /**
     * 源类含有方法sampleOperation1
     */
    public void sampleOperation1() {

    }
    /**
     * 源类含有方法sampleOperation3
     */
    public void sampleOperation3() {

    }
}
/**
 * <b>Description:</b> 适配器 <br>
 */
public class Adapter implements Target {

    private Adaptee mAdaptee;

    public Adapter(Adaptee adaptee) {
        mAdaptee = adaptee;
    }

    /**
     * 由于源类有方法sampleOpration1
     * 因此适配器类直接委派即可
     */
    @Override
    public void sampleOperation1() {
        mAdaptee.sampleOperation1();
    }

    /**
     * 由于源类没有方法sampleOpration2
     * 因此适配器类补充上这个方法
     */
    @Override
    public void sampleOperation2() {
        //write ur code here
    }
}
```
### 用意
将接口不同而功能相同或者相近的两个接口加以转换，这里面包括适配器角色补充了一个源角色没有的方法。但是目标接口需要的方法
### 效果
（1）一个适配器可以把多种不同的源适配到同一个目标。换言之，同一个适配器可以把源类和它的子类都适配到目标接口。
（2）与类的适配器模式相比，要想指环源类的方法就不容易。如果一定要指环带哦源类的一个或多个方法，就只好先做一个源类的子类，将源类的方法置换掉，然后再吧源类的子类当成真正的源进行适配。
（3）虽然要想置换源类的方法不容易，但是要想增加一些新的方法则方便得很，而且新增加的方法可同时适用于所有的源。
### 在什么情况下使用适配器模式
（1）系统需要使用现有的类，而此类的接口不符合系统的需要。
（2）想要建立一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作，这些源类不一定有很复杂的接口。
（3）（对对象的适配器模式而言）在设计里，需要改变多个已有的子类的接口，如果使用类的适配器模式，就要针对每一个子类有一个适配器类，而这不太实际。

## 缺省的适配器模式
为一个接口提供个缺省实现，这样子类型可以从这个缺省实现进行扩展，而不必从原有的接口进行拓展。
```
/**
 * <b>Description:</b>  <br>
 */
public interface AbstractService {
    void serviceOperation1();
    int serviceOperation2();
    String serviceOperation3();
}
/**
 * <b>Description:</b> 就是将这个adapter abstract化 <br>
 */
public abstract class Adapter implements AbstractService {

}
/**
 * <b>Description:</b> 三个方法都提供了平庸实现 <br>
 */
public class ServiceAdapter implements AbstractService {
    @Override
    public void serviceOperation1() {
        
    }

    @Override
    public int serviceOperation2() {
        return 0;
    }

    @Override
    public String serviceOperation3() {
        return null;
    }
}
```
### 在什么情况下使用该模式
如果不准备实现一个接口的所有方法时。
适配器模式的“平庸化（ abstract）”形式可以使所考察的类不必实现不需要的那部分接口。
## JavaI/O库
---
# 合成模式
## 角色
* 抽象构件（Component）角色：抽象角色，它参加组合的对象规定一个接口。这个角色给出共有的接口及其默认行为。
* 树叶构件（Leaf）角色：参加组合的树叶对象。一个树叶没有下级的子对象。
定义出参加组合的原始对象的行为。
* 树枝构件（Composite）角色：参加组合的有子对象的对象，并给出树枝构件对象的行为。
```

```
合成模式可以不提供父对象的管理方法，但是合成模式必须在合适的地方提供子对象的管理方法。（add()、remove()、getChild()）
## 安全式和透明式的合成模式
### 透明式
```
/**
 * <b>Description:</b> 抽象控件 <br>
 */
public interface Component {
    /**
     * 返回自己的实例
     *
     * @return
     */
    Composite getComposite();

    /**
     * 某个商业方法
     */
    void sampleOperation();


    /**
     * 聚集管理方法，返还聚集的Enumeration对象
     *
     * @return
     */
    public Enumeration components();

    /**
     * 聚集管理方法，增加一个子构件对象
     *
     * @param component
     */
    public void add(Component component);

    /**
     * 聚集管理方法，删除一个子构件对象
     *
     * @param component
     */
    public void remove(Component component);
}
/**
 * <b>Description:</b> 树枝构件 <br>
 */
public class Composite implements Component {

    private Vector componentVector = new Vector();

    /**
     * 返回自己的实例
     *
     * @return this
     */
    @Override
    public Composite getComposite() {
        return this;
    }

    /**
     * 某个商业方法
     */
    @Override
    public void sampleOperation() {
        Enumeration enumeration = components();
        while (enumeration.hasMoreElements()) {
            ((Component) enumeration.nextElement()).sampleOperation();
        }
    }

    /**
     * 聚集管理方法，返还聚集的Enumeration对象
     *
     * @return
     */
    @Override
    public Enumeration components() {
        return componentVector.elements();
    }

    /**
     * 聚集管理方法，增加一个子构件对象
     *
     * @param component
     */
    @Override
    public void add(Component component) {
        componentVector.addElement(component);
    }

    /**
     * 聚集管理方法，删除一个子构件对象
     *
     * @param component
     */
    @Override
    public void remove(Component component) {
        componentVector.removeElement(component);
    }
}
/**
 * <b>Description:</b> 树叶构件 <br>
 */
public class Leaf implements Component {
    /**
     * 返回自己的实例
     *
     * @return this
     */
    @Override
    public Composite getComposite() {
        //Write ur code here
        return null;
    }

    /**
     * 某个商业方法
     */
    @Override
    public void sampleOperation() {
        //Write ur code here
    }

    @Override
    public Enumeration components() {
        return null;
    }

    @Override
    public void add(Component component) {

    }

    @Override
    public void remove(Component component) {

    }
}
```
Component里面生命所有的用来管理子类对象的方法。
好处：所有构件类都有相同的接口。
缺点：不够安全。编译时不出错。运行时出错。
### 安全式
```
/**
 * <b>Description:</b> 抽象控件 <br>
 */
public interface Component {
    /**
     * 返回自己的实例
     *
     * @return
     */
    Composite getComposite();

    /**
     * 某个商业方法
     */
    void sampleOperation();
}
/**
 * <b>Description:</b> 树枝构件 <br>
 */
public class Composite implements Component {

    private Vector componentVector = new Vector();

    /**
     * 返回自己的实例
     *
     * @return this
     */
    @Override
    public Composite getComposite() {
        return this;
    }

    /**
     * 某个商业方法
     */
    @Override
    public void sampleOperation() {
        Enumeration enumeration = components();
        while (enumeration.hasMoreElements()) {
            ((Component) enumeration.nextElement()).sampleOperation();
        }
    }

    /**
     * 聚集管理方法，返还聚集的Enumeration对象
     *
     * @return
     */
    public Enumeration components() {
        return componentVector.elements();
    }

    /**
     * 聚集管理方法，增加一个子构件对象
     *
     * @param component
     */
    public void add(Component component) {
        componentVector.addElement(component);
    }

    /**
     * 聚集管理方法，删除一个子构件对象
     *
     * @param component
     */
    public void remove(Component component) {
        componentVector.removeElement(component);
    }
}
/**
 * <b>Description:</b> 树叶构件 <br>
 */
public class Leaf implements Component {
    /**
     * 返回自己的实例
     *
     * @return this
     */
    @Override
    public Composite getComposite() {
        //Write ur code here
        return null;
    }

    /**
     * 某个商业方法
     */
    @Override
    public void sampleOperation() {
        //Write ur code here
    }
}
```
Composite类里面生命所有的用来管理子类的对象的方法。

## AWT库
---
# 装饰模式
用原来被装饰的类的一个子类的实例，把客户端的调用委派到被装饰的类。
## 角色
* 抽象构件（Component）角色：抽象接口，以规范准备接受附加责任的对象。
* 具体构件（Concrete Component）角色：定义一个酱油接收附加责任的类。
* 装饰（Decorator）角色：持有一个构件（Component）对象的实例，并定义与抽象构件接口一直的接口。
* 具体装饰（Concrete  ）角色：负责给构件对象“贴上”附加的责任。
```
/**
 * <b>Description:</b> 实现构件的接口 <br>
 *     齐天大圣
 *     尽量作为一个“轻”类。不要把太多的逻辑和状态放在这里。
 */
public interface Component {
    /**
     * 商业方法
     */
    void sampleOperation();
}
/**
 * <b>Description:</b> 抽象装饰 <br>
 *     七十二般变化
 */
public class Decorator implements Component {

    private Component mComponent;

    /**
     * 构造子
     *
     * @param component
     */
    public Decorator(Component component) {
        this.mComponent = component;
    }

    /**
     * 构造子
     */
    public Decorator() {
    }

    /**
     * 商业方法，委派给构件
     */
    @Override
    public void sampleOperation() {
        if (mComponent != null) {
            mComponent.sampleOperation();
        }
    }
}
/**
 * <b>Description:</b> 具体构件 <br>
 *     大圣本尊
 *     可以不implements Component
 */
public class ConcreteComponent implements Component {

    /**
     * 构造子
     */
    public ConcreteComponent() {
        //Write ur code here
    }

    /**
     * 商业方法
     */
    @Override
    public void sampleOperation() {
        //Write ur code here
    }
}
/**
 * <b>Description:</b> 具体装饰 <br>
 * 花
 * 必须继承自一个共同的父类Component
 * 只有一个的话，可以与Decorator结合。
 */
public class ConcreteDecorator extends Decorator {
    public ConcreteDecorator(Component component) {
        super(component);
    }

    public ConcreteDecorator() {
    }

    /**
     * 商业方法，委派给构件，增强功能
     */
    @Override
    public void sampleOperation() {
        super.sampleOperation();
    }
}
/**
 * <b>Description:</b> 具体装饰 <br>
 * 草
 */
public class ConcreteDecorator1 extends Decorator {

    public ConcreteDecorator1(Component component) {
        super(component);
    }

    public ConcreteDecorator1() {
    }

    /**
     * 商业方法，委派给构件，增强功能
     */
    @Override
    public void sampleOperation() {
        super.sampleOperation();
    }
}
/**
 * <b>Description:</b> 具体装饰 <br>
 * 树
 */
public class ConcreteDecorator2 extends Decorator {
    public ConcreteDecorator2(Component component) {
        super(component);
    }

    public ConcreteDecorator2() {
    }

    /**
     * 商业方法，委派给构件，增强功能
     */
    @Override
    public void sampleOperation() {
        super.sampleOperation();
    }
}
/**
 * <b>Description:</b> 具体装饰 <br>
 * 木
 */
public class ConcreteDecorator3 extends Decorator {
    public ConcreteDecorator3(Component component) {
        super(component);
    }

    public ConcreteDecorator3() {
    }

    /**
     * 商业方法，委派给构件，增强功能
     */
    @Override
    public void sampleOperation() {
        super.sampleOperation();
    }
}
/**
 * <b>Description:</b>  <br>
 */
public class Client {
    public static void main(String[] args) {
        Component component = new ConcreteComponent();
        Component Flowers = new ConcreteDecorator(component);
        Component grass = new ConcreteDecorator1(component);
        Component trees = new ConcreteDecorator2(component);
        Component wood = new ConcreteDecorator3(component);
        Polymorphism();
        SemiTransparent();
    }

    /**
     * 半透明
     */
    private static void SemiTransparent() {
        Component component = new ConcreteComponent();
        ConcreteDecorator Flowers = new ConcreteDecorator(component);
        ConcreteDecorator1 grass = new ConcreteDecorator1(Flowers);
        ConcreteDecorator2 trees = new ConcreteDecorator2(grass);
        ConcreteDecorator3 wood1 = new ConcreteDecorator3(trees);
    }

    /**
     * 透明
     * 静态
     */
    private static void Polymorphism() {
        Component component = new ConcreteComponent();
        Component Flowers = new ConcreteDecorator(component);
        Component grass = new ConcreteDecorator1(Flowers);
        Component trees = new ConcreteDecorator2(grass);
        Component wood1 = new ConcreteDecorator3(trees);
    }
}
```
## 在什么情况下应当使用装饰模式
（1）需要扩展一个类的功能，或给一个类增加附加责任。
（2）需要动态地给一个对象增加功能，这个功能可以再动态地撤销。
（3）需要增加由一些基本功能的排列组合而产生的非常大量的功能，从而使继承关系变得不现实。
## 优缺点
### （1）优点：
1.扩展对象的功能，比继承更多的灵活性。
2.可以创造出很多不同行为的组合。

### （2）缺点：
1.比继承容易出错。
2.复杂，难管理，难阅读。

## 模式简化
（1）一个装饰类的接口必须与被装饰类的接口相容。
ConcreteDecorator必须继承自一个共同的父类Component
（2）interface Component尽量作为一个“轻”类。不要把太多的逻辑和状态放在这里。
（3）ConcreteComponent可以不implements Component
（4）ConcreteDecorator只有一个的话，可以与Decorator结合。
## 透明性
### 透明的装饰模式
### 半透明的装饰模式（退化的（degenerate）装饰模式）

## JavaI/O库
---
# 代理模式（Proxy/Surrogate）
## 代理的种类
* 远程（Remote）代理：为一个位于不同的地址空间的对象提供一个局域代表对象。也叫做大使（Ambassador）
优点：系统可以将网络的细节隐藏起来，使得客户端不比考虑网络的存在。
* 虚拟（Virtual）代理：根据需要创建一个资源消耗较大的对象，使得此对象只有需要时才会被真正创建。
优点：代理对象可以再必要的时候才将代理的对象加载。代理可以对加载的过程加以别要的优化。当一个模块的加载十分消耗资源的时候，虚拟代理的有点就非常明显。
* Copy-on-Write代理：虚拟代理的一种。把复制（克隆）拖延到只有在客户端需要时，才真正采取行动。
* 保护（Protect or Access）代理：控制对一个对象的访问，如果需要，可以给不同的用户提供不同级别的使用权限。
优点：可以再运行时间对用户的有关权限进行检查，然后在核实后决定将调用传递给被代理的对象。
* Cache代理：为某一个目标操作的结果提供临时的存储空间，一遍多个客户端可以共享这些结果。
* 防火墙（Firewall）代理：保护目标，不让而已用户接近。
* 同步化（Synchoronization）代理：使几个用户能够同时使用一个对象而没有冲突。
* 智能引用（Smart Reference）代理：当一个对象呗引用时，提供一些额外的操作，比如将对此对象调用的次数记录下来等。
优点：在访问一个对象时可以执行一些内务处理操作，比如计数操作等。

## 代理模式的结构（角色）
* 抽象主题角色：声明了真是主题和代理主题的共同接口，这样一来任何可以使用真是主题的地方都可以使用代理主题。
* 代理主题角色：代理主题角色内部含有对真是主题的引用，从而可以再任何时候操作真是主题对象；代理主题角色提供一个与真实主题角色相同的接口，以便可以再任何时候都可以替代真是主题；控制对真实主题的引用，负责在需要的时候创建真实主题之前或者之后，都要执行某个操作，而不是单纯地将调用传递给真是主题对象。
* 真实主题角色：定义了代理角色所代表的真是对象。
```
/**
 * <b>Description:</b>  <br>
 */
abstract public class Subject {
    /**
     * 声明一个抽象的请求方法
     */
    abstract public void request();
}
/**
 * <b>Description:</b>  <br>
 */
public class RealSubject extends Subject {

    /**
     * 构造子
     */
    public RealSubject() {
    }

    /**
     * 实现请求方法
     */
    @Override
    public void request() {
        System.out.println("From real subject.");
    }
}
/**
 * <b>Description:</b>  <br>
 */
public class ProxySubject extends Subject {

    private RealSubject mRealSubject;
    /**
     * 构造子
     */
    public ProxySubject() {
    }

    /**
     * 实现请求方法
     */
    @Override
    public void request() {
        preRequest();
        if (mRealSubject == null) {
            mRealSubject = new RealSubject();
        }
        mRealSubject.request();
        postRequest();
    }


    /**
     * 请求前的操作
     */
    private void preRequest() {

    }

    /**
     * 请求后的操作
     */
    private void postRequest() {

    }
}
/**
 * <b>Description:</b>  <br>
 */
public class Client {
    public static void main(String[] args) {
        Subject subject = new ProxySubject();
        subject.request();
    }
}

```
## Java 2.0对代理模式的支持-反身映射(Reflection)与动态代理
Java语言通过在java.lang.reflect库中提供三个类直接支持代理模式：
Proxy,InvocationHandler和Method。
```
/**
 * <b>Description:</b> 一个例子 <br>
 */
public class VectorProxy implements InvocationHandler {

    private Object proxyObj;

    public VectorProxy(Object proxyObj) {
        this.proxyObj = proxyObj;
    }


    /**
     * 静态工厂方法
     *
     * @param obj
     * @return
     */
    public static Object factory(Object obj) {
        Class cls = obj.getClass();
        return Proxy.newProxyInstance(cls.getClassLoader(), cls.getInterfaces(), new VectorProxy(obj));
    }

    /**
     * 调用某个方法
     *
     * @param proxy
     * @param method
     * @param args
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("before calling" + method);
        if (args != null) {
            for (int i = 0; i < args.length; i++) {
                System.out.println(args[i] + "");
            }
        }
        Object o = method.invoke(proxyObj, args);
        System.out.println("after calling" + method);
        return o;
    }

    public static void main(String[] args) {
        List v = null;
        v = (List) factory(new Vector(10));
        v.add("New");
        v.add("York");
    }
}
```
---
# 享元模式（Flyweight Pattern）
采用一个共享来避免大量拥有相同内容对象的开销。这种开销中最常见、直观的就是内存的损耗。享元模式以共享的方式高效的支持大量的细粒度对象。
享受元素。
## 内蕴状态（Internal State）
内蕴状态就是共性
内蕴状态存储在享元内部，不会随环境的改变而有所不同，是可以共享的。
## 外蕴状态（External State）
外蕴状态就是个性
外蕴状态是不可以共享的，它随环境的改变而改变的，因此外蕴状态是由客户端来保持（因为环境的变化是由客户端引起的）。
## 种类：**单纯享元模式**、**复合享元模式**
### **单纯享元模式**
1)抽象享元角色：超类，为具体享元角色规定了必须实现的方法，而外蕴状态就是以参数的形式通过此方法传入。在Java中可以由抽象类、接口来担当。
2)具体享元角色：实现抽象角色规定的方法。如果存在内蕴状态，就负责为内蕴状态提供存储空间。
3)享元工厂角色：负责创建和管理享元角色。要想达到共享的目的，这个角色的实现是关键！
4)客户端角色：维护对所有享元对象的引用，而且还需要存储对应的外蕴状态。
```
/**
 * <b>Description:</b> 抽象享元 <br>
 */
abstract public class Flyweight {
    /**
     * 外蕴状态作为参量传入到方法中
     *
     * @param state
     */
    abstract public void operation(String state);
}
/**
 * <b>Description:</b> 具体享元 <br>
 */
public class ConcreteFlyweight extends Flyweight {

    private Character intrinsicState = null;

    /**
     * 构造子，内蕴状态作为参量传入
     *
     * @param state
     */
    public ConcreteFlyweight(Character state) {
        this.intrinsicState = state;
    }

    /**
     * 外蕴状态作为参量传入到方法中
     *
     * @param state
     */
    @Override
    public void operation(String state) {
        System.out.println("\nIntrinsicState state=" + intrinsicState + ",Extrinsic state=" + state);
    }
}
/**
 * <b>Description:</b> 享元工厂 <br>
 */
public class FlyweightFactory {
    private HashMap files = new HashMap();

    /**
     * 默认构造子
     */
    public FlyweightFactory() {
    }

    /**
     * 构造子，内蕴状态作为参量传入
     *
     * @param state
     * @return
     */
    public Flyweight factory(Character state) {
        if (files.containsKey(state)) {
            return (Flyweight) files.get(state);
        } else {
            Flyweight fly = new ConcreteFlyweight(state);
            files.put(state, fly);
            return fly;
        }
    }

    /**
     * 辅助方法，打印所有已经创建的享元对象清单
     */
    public void checkFlyweight() {
        int i = 0;
        System.out.println("\n==========checkFlyweight==========");
        for (Iterator it = files.entrySet().iterator(); it.hasNext(); ) {
            Map.Entry e = (Map.Entry) it.next();
            System.out.println("Item" + (++i) + ":" + e.getKey());
        }
        System.out.println("\n==========checkFlyweight==========");
    }
}
/**
 * <b>Description:</b> 客户端 <br>
 */
public class Client {
    public static void main(String[] args) {
        //创建一个享元工厂对象
        FlyweightFactory factory = new FlyweightFactory();
        //向享元工厂对象请求一个内蕴状态为'a'的享元对象
        Flyweight flyweight = factory.factory(new Character('a'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("First Call");
        //向享元工厂对象请求一个内蕴状态为'b'的享元对象
        flyweight = factory.factory(new Character('b'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("Second Call");
        //向享元工厂对象请求一个内蕴状态为'a'的享元对象
        flyweight = factory.factory(new Character('a'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("Third Call");

        //打印出所有的独立的享元对象，为系统设计过程提供辅助信息
        factory.checkFlyweight();


        pr.tongson.pattern2.Flyweight.Composite.FlyweightFactory factory2 = new pr.tongson.pattern2.Flyweight.Composite.FlyweightFactory();
        Flyweight flyweight1 = factory2.factory("aba");
        flyweight1.operation("Composite Call");
        factory2.checkFlyweight();
    }
}
```
### **复合享元模式**
1)抽象享元角色：为具体享元角色规定了必须实现的方法，而外蕴状态就是以参数的形式通过此方法传入。在Java中可以由抽象类、接口来担当。
2)具体享元角色：实现抽象角色规定的方法。如果存在内蕴状态，就负责为内蕴状态提供存储空间。
3)复合享元角色：它所代表的对象是不可以共享的，并且可以分解成为多个单纯享元对象的组合。
4)享元工厂角色：负责创建和管理享元角色。要想达到共享的目的，这个角色的实现是关键！
5)客户端角色：维护对所有享元对象的引用，而且还需要存储对应的外蕴状态。
```
/**
 * <b>Description:</b> 具体复合享元角色 <br>
 */
public class ConcreteCompositeFlyweight extends Flyweight {
    private HashMap files = new HashMap(10);
    private Flyweight mFlyweight;

    /**
     * 默认构造子
     */
    public ConcreteCompositeFlyweight() {
    }

    /**
     * 增加一个新的单纯响元对象到聚集中
     *
     * @param key
     * @param fly
     */
    public void add(Character key, Flyweight fly) {
        files.put(key, fly);
    }

    /**
     * 外蕴状态作为参量传入到方法中
     *
     * @param state
     */
    @Override
    public void operation(String state) {
        Flyweight flyweight = null;
        for (Iterator iterator = files.entrySet().iterator(); iterator.hasNext(); ) {
            Map.Entry entry = (Map.Entry) iterator.next();
            flyweight = (Flyweight) entry.getValue();
            flyweight.operation(state);
        }
    }
}
/**
 * <b>Description:</b> 享元工厂 <br>
 */
public class FlyweightFactory {
    private HashMap files = new HashMap();

    /**
     * 默认构造子
     */
    public FlyweightFactory() {
    }

    /**
     * 复合享元工厂方法，所需状态以参量形式传入
     * 这个参量可以使用String类型。
     * 也可以使用一个聚集，如Vector对象等。
     *
     * @param compositeState
     * @return
     */
    public Flyweight factory(String compositeState) {
        ConcreteCompositeFlyweight concreteCompositeFlyweight = new ConcreteCompositeFlyweight();
        int length = compositeState.length();
        Character state = null;
        for (int i = 0; i < length; i++) {
            state = new Character(compositeState.charAt(i));
            System.out.println("state(" + state + ")");
            concreteCompositeFlyweight.add(state, this.factory(state));
        }
        return concreteCompositeFlyweight;
    }

    /**
     * 单纯享元工厂方法，所需的状态以参量形式传入
     *
     * @param state
     * @return
     */
    public Flyweight factory(Character state) {
        //检查具有此状态的享元是否已经存在
        if (files.containsKey(state)) {
            //具有此状态的享元已经存在，因此直接将它返还
            return (Flyweight) files.get(state);
        } else {
            //具有此状态的享元不存在，因此创建新实例
            Flyweight fly = new ConcreteFlyweight(state);
            //将实例存储到聚集中
            files.put(state, fly);
            //将实例返还
            return fly;
        }
    }

    /**
     * 辅助方法，打印所有已经创建的享元对象清单
     */
    public void checkFlyweight() {
        int i = 0;
        System.out.println("\n==========checkFlyweight==========");
        for (Iterator it = files.entrySet().iterator(); it.hasNext(); ) {
            Map.Entry e = (Map.Entry) it.next();
            System.out.println("Item" + (++i) + ":" + e.getKey());
        }
        System.out.println("\n==========checkFlyweight==========");
    }
}
```
## 使用不便模式实现享元角色
以后再说
## 使用备忘录模式实现享元工厂角色
以后再说
## 使用单利模式实现享元工厂角色
### 单纯享元模式中的享元工厂角色
```
/**
 * <b>Description:</b> 使用单例模式实现享元工厂角色 <br>
 */
public class FlyweightFactorySingleton {
    private HashMap files = new HashMap();

    private static FlyweightFactorySingleton myself = new FlyweightFactorySingleton();

    private FlyweightFactorySingleton() {

    }

    public static FlyweightFactorySingleton getInstance() {
        return myself;
    }


    /**
     * 工厂方法，向外界提供含有指定内蕴状态的对象
     *
     * @param state
     * @return
     */
    public synchronized Flyweight factory(Character state) {
        //检查具有此状态的享元是否已经存在
        if (files.containsKey(state)) {
            //具有此状态的享元已经存在，因此直接将它返还
            return (Flyweight) files.get(state);
        } else {
            //具有此状态的享元不存在，因此创建新实例
            Flyweight fly = new ConcreteFlyweight(state);
            //将实例存储到聚集中
            files.put(state, fly);
            //将实例返还
            return fly;
        }
    }

    /**
     * 辅助方法，打印所有已经创建的享元对象清单
     */
    public void checkFlyweight() {
        int i = 0;
        System.out.println("\n==========checkFlyweight==========");
        for (Iterator it = files.entrySet().iterator(); it.hasNext(); ) {
            Map.Entry e = (Map.Entry) it.next();
            System.out.println("Item" + (++i) + ":" + e.getKey());
        }
        System.out.println("\n==========checkFlyweight==========");
    }
}
/**
 * <b>Description:</b>  <br>
 */
public class ClientSingleton {
    private static FlyweightFactorySingleton flyweightFactorySingleton;

    public static void main(String[] args) {
        //创建一个享元工厂对象
        flyweightFactorySingleton= FlyweightFactorySingleton.getInstance();
        //向享元工厂对象请求一个内蕴状态为'a'的享元对象
        Flyweight flyweight = flyweightFactorySingleton.factory(new Character('a'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("First Call");
        //向享元工厂对象请求一个内蕴状态为'b'的享元对象
        flyweight = flyweightFactorySingleton.factory(new Character('b'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("Second Call");
        //向享元工厂对象请求一个内蕴状态为'a'的享元对象
        flyweight = flyweightFactorySingleton.factory(new Character('a'));
        //以参数方式传入一个外蕴状态
        flyweight.operation("Third Call");

        //打印出所有的独立的享元对象，为系统设计过程提供辅助信息
        flyweightFactorySingleton.checkFlyweight();
    }
}
```
### 复合享元模式中的享元工厂角色
```

/**
 * <b>Description:</b> 使用单例模式实现享元工厂角色 <br>
 */
public class FlyweightFactorySingleton {
    private HashMap files = new HashMap();

    private static FlyweightFactorySingleton myself = new FlyweightFactorySingleton();

    private FlyweightFactorySingleton() {

    }

    public static FlyweightFactorySingleton getInstance() {
        return myself;
    }

    /**
     * 工厂方法，向外界提供含有指定内蕴状态的复合享元对象
     *
     * @param compositeState
     * @return
     */
    public Flyweight factory(String compositeState) {
        //创建符合享元对象
        ConcreteCompositeFlyweight concreteCompositeFlyweight = new ConcreteCompositeFlyweight();
        int length = compositeState.length();
        Character state = null;
        //设定复合享元对象的内部成分（也就是内部的享元）
        for (int i = 0; i < length; i++) {
            state = new Character(compositeState.charAt(i));
            System.out.println("state(" + state + ")");
            concreteCompositeFlyweight.add(state, this.factory(state));
        }
        return concreteCompositeFlyweight;
    }

    /**
     * 工厂方法，向外界提供含有指定内蕴状态的对象
     *
     * @param state
     * @return
     */
    public synchronized Flyweight factory(Character state) {
        //检查具有此状态的享元是否已经存在
        if (files.containsKey(state)) {
            //具有此状态的享元已经存在，因此直接将它返还
            return (Flyweight) files.get(state);
        } else {
            //具有此状态的享元不存在，因此创建新实例
            Flyweight fly = new ConcreteFlyweight(state);
            //将实例存储到聚集中
            files.put(state, fly);
            //将实例返还
            return fly;
        }
    }

    /**
     * 辅助方法，打印所有已经创建的享元对象清单
     */
    public void checkFlyweight() {
        int i = 0;
        System.out.println("\n==========checkFlyweight==========");
        for (Iterator it = files.entrySet().iterator(); it.hasNext(); ) {
            Map.Entry e = (Map.Entry) it.next();
            System.out.println("Item" + (++i) + ":" + e.getKey());
        }
        System.out.println("\n==========checkFlyweight==========");
    }
}/**
 * <b>Description:</b> 使用单例模式实现享元工厂角色 <br>
 */
public class FlyweightFactorySingleton {
    private HashMap files = new HashMap();

    private static FlyweightFactorySingleton myself = new FlyweightFactorySingleton();

    private FlyweightFactorySingleton() {

    }

    public static FlyweightFactorySingleton getInstance() {
        return myself;
    }

    /**
     * 工厂方法，向外界提供含有指定内蕴状态的复合享元对象
     *
     * @param compositeState
     * @return
     */
    public Flyweight factory(String compositeState) {
        //创建符合享元对象
        ConcreteCompositeFlyweight concreteCompositeFlyweight = new ConcreteCompositeFlyweight();
        int length = compositeState.length();
        Character state = null;
        //设定复合享元对象的内部成分（也就是内部的享元）
        for (int i = 0; i < length; i++) {
            state = new Character(compositeState.charAt(i));
            System.out.println("state(" + state + ")");
            concreteCompositeFlyweight.add(state, this.factory(state));
        }
        return concreteCompositeFlyweight;
    }

    /**
     * 工厂方法，向外界提供含有指定内蕴状态的对象
     *
     * @param state
     * @return
     */
    public synchronized Flyweight factory(Character state) {
        //检查具有此状态的享元是否已经存在
        if (files.containsKey(state)) {
            //具有此状态的享元已经存在，因此直接将它返还
            return (Flyweight) files.get(state);
        } else {
            //具有此状态的享元不存在，因此创建新实例
            Flyweight fly = new ConcreteFlyweight(state);
            //将实例存储到聚集中
            files.put(state, fly);
            //将实例返还
            return fly;
        }
    }

    /**
     * 辅助方法，打印所有已经创建的享元对象清单
     */
    public void checkFlyweight() {
        int i = 0;
        System.out.println("\n==========checkFlyweight==========");
        for (Iterator it = files.entrySet().iterator(); it.hasNext(); ) {
            Map.Entry e = (Map.Entry) it.next();
            System.out.println("Item" + (++i) + ":" + e.getKey());
        }
        System.out.println("\n==========checkFlyweight==========");
    }
}
/**
 * <b>Description:</b>  <br>
 */
public class ClientSingleton {
    private static FlyweightFactorySingleton flyweightFactorySingleton;

    public static void main(String[] args) {
        //创建一个享元工厂对象
        flyweightFactorySingleton = FlyweightFactorySingleton.getInstance();
        //向享元工厂对象请求一个状态为"aba"的复合享元对象
        Flyweight flyweight = flyweightFactorySingleton.factory("aba");
        //以一个外蕴状态传入享元对象中
        flyweight.operation("Composite Call");
        //打印出所有的独立的享元对象，为系统设计过程提供辅助信息
        flyweightFactorySingleton.checkFlyweight();
    }
}
```
## 什么情况下使用
（1）一个系统有大量的对象。
（2）这些对象耗费大量的内存。
（3）这些对象的状态中的大部分都可以外部化。
（4）这些对象可以按照内蕴状态分成很多的组，当把外蕴对象从对象中剔除时，每一个组都可以仅用一个对象代替。
（5）软件系统不依赖于这些对象的身份，换言之，这些对象可以是不可分辨的。
（6）使用此模式需要维护一个记录了系统已有的所有享元的表，而这需要耗资源。因此，应当在有足够多的享元实例可供共享时才值得使用享元模式。

## 享元模式优点和缺点
享元模式优点就在于它能够大幅度的降低内存中对象的数量；而为了做到这一步也带来了它的缺点：它使得系统逻辑复杂化，而且在一定程度上外蕴状态影响了系统的速度。
所以一定要切记使用享元模式的条件：
1)        系统中有大量的对象，他们使系统的效率降低。
2)        这些对象的状态可以分离出所需要的内外两部分。

# 门面模式
要求一个子系统的外部与其内部的通信必须通过一个统一的门面对象进行。
门面模式提供一个高层次的接口，使得子系统更易于使用。
是实现代码重构以便达到迪米特法则要求的一个强有力的武器。
## 门面模式的结构
* 门面角色：客户端可以调用这个角色的方法。
* 子系统角色：可以同时有一个或者多个子系统。
![普通系统类图.png](http://upload-images.jianshu.io/upload_images/3481116-0e791f532dac51f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![门面模式系统类图.png](http://upload-images.jianshu.io/upload_images/3481116-6bfef02346fc8083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
SecurityFacade使得客户端与子系统之间的关系变得简单和易于管理。（体面多了）

# 桥梁模式
又称：柄体（Handle and Body）模式或者接口（Interface）模式
将抽象化（Abstraction）与实现化（Implementation）脱耦，使得二者可以独立地变化。
## 桥梁模式的结构
由抽象角色和修正抽象角色组成的抽象化等级结构。
由实现化角色和两个具体实现化角色所组成的实现化等级结构。
* 抽象化（Abstraction）角色：抽象化给出的定义，并保存一个对实现化对象的引用。
* 修正抽象化（Refined Abstraction）角色：扩展抽象化角色，改变和修正父类对抽象化的定义。
* 实现化（Implementor）角色：这个角色给出实现化角色的接口，但不给出具体的实现。
* 具体实现化（Concrete Implementor）角色：这个角色给出实现化角色接口的具体实现。
```
/**
 * <b>Description:</b> 抽象化 <br>
 */
abstract public class Abstraction {
    protected Implementor imp;

    public void operation() {
        imp.operationImp();
    }
}
/**
 * <b>Description:</b> 修正抽象化 <br>
 */
public class RefinedAbstraction extends Abstraction {
    /**
     * 某个商业方法修正抽象化角色的实现
     */
    @Override
    public void operation() {
        //improved logic
        System.out.println("operation improved logic...");

    }
}
/**
 * <b>Description:</b> 实例化 <br>
 */
abstract public class Implementor {
    /**
     * 某个商业方法的实现化声明
     */
    public abstract void operationImp();
}
/**
 * <b>Description:</b> 具体实例化 <br>
 */
public class ConcreteImplementorA extends Implementor {
    /**
     * 某个商业方法的实现化实现
     */
    @Override
    public void operationImp() {
        System.out.println("operationImp Do something...");
    }
}
```
## 实现化角色的退化
## 抽象化角色的的行为
## 多个实现类的情况
## 共享具体实现化角色

## 在什么情况下应当使用桥梁模式
* 如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的联系。
* 设计要求实现化橘色的任何改变不应当影响客户端，或者说实现化角色的改变对客户端是完全透明的。
* 一个构件有多于一个的抽象化角色和实现化角色，系统需要他们之间进行动态耦合。
* 虽然系统中使用继承是没有问题的，但是由于抽象化角色和具体化角色需要独立变化，设计需求需要独立管理这两者。
## AWT库

# 总结
只是对结构模式做了一些最简单的认识了解。
如果要真正的精通的话，还是需要多敲代码。