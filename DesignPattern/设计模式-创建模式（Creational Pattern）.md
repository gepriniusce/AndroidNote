创建模式（Creational Pattern）是对类的实例化过程的抽象化。
创建模式分为**类创建模式**和**对象的创建模式**两种。

> 简单工厂模式（Simple Factory）
静态工厂方法模式（Static Factory Method Pattern）

> 工厂方法模式（Factory Method）
多态性工厂模式（Polymorphic Factory）
虚拟构造子模式（Virtual Constructor）

> 抽象工厂模式（Abstract Factory）
工具箱模式（Kit/Toolkit）

> 单例模式（Singleton）

> 多例模式（Multition Pattern）

> 建造模式（Builder）

> 原始模型模式（Protorype）


# 简单工厂模式
```
/**
 * <b>Description:</b> 抽象产品Concrete Product <br>
 */
public interface Fruit {
    void grow();

    void harvest();

    void plant();
}
/**
 * <b>Description:</b> 具体产品Product <br>
 */
public class Apple implements Fruit {
    private int treeAge;
    @Override
    public void grow() {
        Log.i("","Apple is growing...");
    }

    @Override
    public void harvest() {
        Log.i("","Apple has been harvested.");
    }

    @Override
    public void plant() {
        Log.i("","Apple  has been planted.");
    }

    public int getTreeAge() {
        return treeAge;
    }

    public void setTreeAge(int treeAge) {
        this.treeAge = treeAge;
    }
}
/**
 * <b>Description:</b> 具体产品Product <br>
 */
public class Grape implements Fruit {

    private boolean seedless;

    @Override
    public void grow() {
        Log.i("", "Grape is growing...");
    }

    @Override
    public void harvest() {
        Log.i("", "Grape has been harvested.");
    }

    @Override
    public void plant() {
        Log.i("", "Grape  has been planted.");
    }

    public boolean isSeedless() {
        return seedless;
    }

    public void setSeedless(boolean seedless) {
        this.seedless = seedless;
    }
}
/**
 * <b>Description:</b> 具体产品Product <br>
 */
public class Strawberry implements Fruit {
    @Override
    public void grow() {
        Log.i("", "Apple is growing...");
    }

    @Override
    public void harvest() {
        Log.i("", "Apple has been harvested.");
    }

    @Override
    public void plant() {
        Log.i("", "Apple  has been planted.");
    }
}
/**
 * <b>Description:</b> 工厂类Creator（上帝类God Class） <br>
 * 模式的核心。含有必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例。
 */
public class FruitGardener {

    public static Fruit factory(String which) throws BadFruitException {
        if (which.equalsIgnoreCase("apple")) {
            return new Apple();
        }
        if (which.equalsIgnoreCase("strawberry")) {
            return new Strawberry();

        }
        if (which.equalsIgnoreCase("grape")) {
            return new Grape();
        } else {
            throw new BadFruitException("");
        }
    }
}
/**
 * <b>Description:</b> 异常类 <br>
 */
public class BadFruitException extends Exception {

    public BadFruitException(String message) {
        super(message);
    }
}

```
# 工厂方法模式
```
/**
 * <b>Description:</b> 抽象工厂Creator <br>
 * 模式的核心。
 */
public interface FruitGardener {
    Fruit factory();
}
/**
 * <b>Description:</b> 具体工厂Concrete Creator <br>
 */
public class AppleGardener implements FruitGardener {
    @Override
    public Fruit factory() {
        return new Apple();
    }
}
/**
 * <b>Description:</b> 具体工厂Concrete Creator <br>
 */
public class GrapeGardener implements FruitGardener {
    @Override
    public Fruit factory() {
        return new Grape();
    }
}
/**
 * <b>Description:</b> 具体工厂Concrete Creator <br>
 */
public class StrawberryGardener implements FruitGardener {
    @Override
    public Fruit factory() {
        return new Strawberry();
    }
}

```
# 抽象工厂模式
```
/**
 * <b>Description:</b> 抽象产品 <br>
 */
public interface Fruit {
}
/**
 * <b>Description:</b> 具体产品类 <br>
 */
public class NorthernFruit implements Fruit {
    private String name;

    public NorthernFruit(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
/**
 * <b>Description:</b> 具体产品类 <br>
 */
public class TropicalFruit implements Fruit {

    private String name;

    public TropicalFruit(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
/**
 * <b>Description:</b> 抽象产品 <br>
 */
public interface Veggie {
}
/**
 * <b>Description:</b> 具体产品类 <br>
 */
public class NorthernVeggie implements Veggie {
    
    private String name;

    public NorthernVeggie(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
/**
 * <b>Description:</b> 具体产品类 <br>
 */
public class TropicalVeggie implements Veggie {
    private String name;

    public TropicalVeggie(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

/**
 * <b>Description:</b> 抽象工厂 <br>
 * 模式的核心。
 * 1.一个系统不应当依赖于产品类的实例如何被创建、组合和表达的细节。（所有工厂模式）
 * 2.这个系统的产品有多余一个的产品族，而系统只消费其中某一个产品族的产品。（原始用意）
 * 3.同属于同一个产品族的产品是在一起使用的，这一约束必须在系统的设计中体现出来。
 * 4.系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于实现。
 */
public interface Gardener {
    Fruit createFruit(String name);

    Veggie createVeggie(String name);
}
/**
 * <b>Description:</b> 具体工厂类 <br>
 */
public class NorthernGardener implements Gardener {


    @Override
    public Fruit createFruit(String name) {
        return new NorthernFruit(name);
    }

    @Override
    public Veggie createVeggie(String name) {
        return new NorthernVeggie(name);
    }
}
/**
 * <b>Description:</b> 具体工厂类 <br>
 */
public class TropicalGardener implements Gardener{
    @Override
    public Fruit createFruit(String name) {
        return new TropicalFruit(name);
    }

    @Override
    public Veggie createVeggie(String name) {
        return new TropicalVeggie(name);
    }
}
```

# 单例模式

## 特点
1.单例类只能有一个实例。
2.单例类必须自己创建自己的唯一实例。
3.单例类必须给所有其他对象提供这一实例。
虽然单例模式中的单例类被限定只能有一个实例，但是单例模式和单例类可以很容易被推广到任意且有有限多个实例的情况，这时候称它为多例模式和多例类。
> 饿汉式单例类
> 懒汉式单例类
> 登记式单例类

## 饿汉式单例类
```
/**
 * <b>Description:</b> 饿汉式单例类 <br>
 * 饿汉式单例类在自己被加载时将自己实例化。
 * 即便加载器是静态的，在饿汉式单例类加载时仍会将自己实例化。
 * 单从资源利用率角度来讲，这个比懒汉式单例类稍差些。
 * 从速度和反应时间角度来讲，则比懒汉式单例类稍好些。
 * <p>
 * 不可继承
 */
public class EagerSingleton {

    private static final EagerSingleton mInstance = new EagerSingleton();

    /**
     * 私有的默认构造子
     */
    private EagerSingleton() {
    }

    /**
     * 静态工厂方法
     *
     * @return
     */
    public static EagerSingleton getInstance() {
        return mInstance;
    }
}
```
## 懒汉式单例类
```
/**
 * <b>Description:</b> 懒汉式单例类 <br>
 * 懒汉式单例类在实例化时，必须处理好在多个线程同时抽次引用此类时访问限制问题，特别是当单例类作为资源控制器在实例化时必然涉及资源初始化，而资源初始化很有可能耗费时间。
 * 这意味着出现多线程同时首次引用此类的几率变得较大。
 * <p>
 * 不可继承
 */
public class LazySingleton {

    private static LazySingleton mInstance = null;

    /**
     * 私有的默认构造子，保证外界无法直接实例化
     */
    private LazySingleton() {
    }

    /**
     * 静态工厂方法，返还此类的唯一实例
     *
     * @return
     */
    synchronized public static LazySingleton getInstance() {
        if (mInstance == null) {
            mInstance = new LazySingleton();
        }
        return mInstance;
    }
}
```
## 登记式单例类
```
/**
 * <b>Description:</b> 登记式单例类 <br>
 * 饿汉式
 * 可继承
 */
public class RegSingleton {
    static private HashMap mRegistry = new HashMap();

    static {
        RegSingleton x = new RegSingleton();
        mRegistry.put(x.getClass().getName(), x);
    }

    /**
     * 保护的默认构造子
     */
    protected RegSingleton() {

    }

    /**
     * 静态工厂方法，返还此类的唯一实例
     *
     * @param name
     * @return
     */
    static public RegSingleton getInstance(String name) {
        if (name == null) {
            name = "pr.tongson.pattern.Singleton.RegSingleton";
        }
        if (mRegistry.get(name) == null) {
            try {
                mRegistry.put(name, Class.forName(name).newInstance());
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        }


        return (RegSingleton) mRegistry.get(name);
    }

    /**
     * 一些示意性的商业方法
     *
     * @return
     */
    public String about() {
        return "Hello, I am RegSigleton.";
    }
}
/**
 * <b>Description:</b> 登记式单例类 <br>
 * 懒汉式
 * 子类
 */
public class RegSigletonChild extends RegSingleton {

    public RegSigletonChild() {
    }

    static public RegSigletonChild getInstance() {
        return (RegSigletonChild) RegSingleton.getInstance("pr.tongson.pattern.Singleton.RegSigletonChild");
    }

    /**
     * 一些示意性的商业方法
     *
     * @return
     */
    @Override
    public String about() {
        return "Hello, I am RegSigletonChild.";
    }
}

```
## 在什么情况下使用单例模式
必要条件：在一个系统要求一个类只有一个实例时才应当使用单例模式。
### 有状态的单例类
User类
### 没有状态的单例类（提供工具性函数的对象）
Uitl类
### 属性类

# 多例模式
多例模式中的多例类可以有多个实例，而且多例类必须自己创建、管理自己的实例，并向外界提供自己的实例。
所谓多例模式实际上就是单例模式的自然推广。
## 特点：
1.多例类可有多个实例。
2.多例类必须自己创建、管理自己的实例，并向外界提供自己的实例。

```
public class Die {
    private static Die die1 = new Die();
    private static Die die2 = new Die();

    /**
     * 私有的构造子保证外界无法直接将此类实例化
     */
    private Die() {
    }

    /**
     * 工厂方法
     *
     * @param whichOne
     * @return
     */
    public static Die getInstance(int whichOne) {
        if (whichOne == 1) {
            return die1;
        } else {
            return die2;
        }
    }

    /**
     * 掷骰子，返回一个在1~6之间的随机数
     *
     * @return
     */
    public synchronized int dice() {
        Date date = new Date();
        Random random = new Random(date.getTime());
        int value = random.nextInt();
        value = Math.abs(value);
        value = value % 6;
        value += 1;
        return value;
    }
}
public class Client {
    private static Die die1, die2;

    public static void main(String[] args) {
        die1 = Die.getInstance(1);
        die2 = Die.getInstance(2);
        die1.dice();
        die2.dice();
    }
}
```
## 无上限多例模式
## 有状态的和没有状态的多例类
Lingual Resource

# 序列键生成器
> 没有数据库的情况
> 有数据库的情况
> 键值的缓存方案
> 有缓存的多序列键生成器

# 建造者模式
```
/**
 * <b>Description:</b> 产品 <br>
 * 复杂对象
 * 一般来说，每有一个产品类，就有一个相应的具体建造类。
 * 这些产品应当有一样数目的零件，而每有一个零件就相当地在所有的建造者角色里有一个构造方法。
 */
public class Product {
    //Anything pertaining to this product
}
/**
 * <b>Description:</b> 抽象建造者 <br>
 * 给出一个抽象接口，以规范产品对象的各个组成成分的建造。
 * 一般而言，此接口独立于应用程序的商业逻辑。
 */
abstract public class Builder {
    /**
     * 产品零件建造方法
     */
    public abstract void buildPart1();

    /**
     * 产品零件建造方法
     */
    public abstract void buildPart2();

    /**
     * 产品返还方法
     *
     * @return
     */
    public abstract Product retrieveResult();
}
/**
 * <b>Description:</b> 具体建造者 <br>
 * 创建产品实例
 */
public class ConcreteBuilder extends Builder {
    private Product mProduct = new Product();

    /**
     * 产品零件建造方法
     */
    @Override
    public void buildPart1() {
        //build the first part of the product
    }

    /**
     * 产品零件建造方法
     */
    @Override
    public void buildPart2() {
        //build the second part of the product
    }

    /**
     * 产品返还方法
     *
     * @return
     */
    @Override
    public Product retrieveResult() {
        return mProduct;
    }
}
/**
 * <b>Description:</b> 导演者 <br>
 * 调用具体建造者以创建产品对象。
 */
public class Director {
    private Builder mBuilder;

    /**
     * 产品构造方法，负责调用各个零件构造方法
     */
    public void construct() {
        mBuilder = new ConcreteBuilder();
        mBuilder.buildPart1();
        mBuilder.buildPart2();
        mBuilder.retrieveResult();
        //continue with othor code
    }
}
```
## 空的零件建造方法（ConcreteBuilder）
## 省略抽象建造者角色（Builder）
## 省略导演者角色（Director）
## 过渡到模板（Template）方法模式
## 合并建造者角色和产品角色
## 建造者角色可以有多个产品构造方法
## 在什么情况下使用建造模式
在以下情况下应当使用建造模式：
1.需要生成的产品对象有复杂的内部结构。每一个内部成分本身可以是对象，也可以仅仅是一个对象（即产品对象）的一个组成成分。
2.需要生成的产品对象的属性相互依赖。建造者模式可以强制实行一种分步骤进行的建造过程，因此，如果产品对象的一个属性必须在另一个属性被赋值之后才可以被赋值，使用建造模式便是一个很好的设计思想。
有时产品对象的属性并无彼此依赖的关系，但是在产品的属性没有确定之前，产品对象不能使用。这时产品对象的实例化，属性的赋值和使用仍然是分步骤进行的。因此，创建模式仍然有意义。
3.在对象创建过程中会使用到系统中的其他一些对象，这些对象在产品对象的创建过程中不易得到。

同时，使用建造模式主要有以下的效果：
* 建造模式的使用使得产品的内部表象可以独立地变化。使用建造模式可以使客户端不必知道产品内部组成的细节。
* 每一个Builder 都相对独立，而与其他的Builder无关。
* 模式所建造的最终产品更易于控制。

# 原始模型模式
## 简单形式
```
/**
 * <b>Description:</b> 抽象原型 <br>
 * 给出所有的具体原型类所需的接口
 */
public interface Prototype extends Cloneable {
    Prototype clone();
}

/**
 * <b>Description:</b> 具体原型 <br>
 * 被复制的对象
 * 需要实现抽象的原型角色所要求的接口。
 */
public class ConcretePrototype implements Prototype {
    /**
     * 克隆方法
     *
     * @return
     */
    @Override
    public Prototype clone() {
        try {
            return (Prototype) super.clone();
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
}

/**
 * <b>Description:</b> 客户 <br>
 * 提出创建对象的请求
 */
public class Client {
    private Prototype mPrototype;

    public void operation(Prototype example) {
        Prototype copytype = example.clone();
    }
}
```
## 登记形式
```
/**
 * <b>Description:</b> 原型管理器 <br>
 * 记录每一个被创建的对象
 */
public class PrototypeManager {
    private Vector objects = new Vector();

    /**
     * 聚集管理方法：增加一个新的对象
     *
     * @param object
     */
    public void add(Prototype object) {
        objects.add(object);
    }

    /**
     * 聚集管理方法：取出聚集中的一个对象
     *
     * @param i
     * @return
     */
    public Prototype get(int i) {
        return (Prototype) objects.get(i);
    }

    /**
     * 聚集管理方法：给出聚集的大小
     *
     * @return
     */
    public int getSize() {
        return objects.size();
    }
}

/**
 * <b>Description:</b> 客户端 <br>
 * 客户端类向管理员提出创建对象的请求
 */
public class Client {
    private PrototypeManager mPrototypeManager;
    private Prototype mPrototype;

    public void registerPrototype() {
        mPrototype = new ConcretePrototype();
        Prototype copyType = (Prototype) mPrototype.clone();
        mPrototypeManager.add(copyType);
    }
}
```
## 浅复制
有一些不同
## 深复制
完全相同，但也不是同一个对象，需要java.io.Serializable