---
layout: post
title: 设计模式
date: 2020-03-10
tag: 编程
---

 * [单例模式](#singlepattern)
 * [工厂模式](#factorypattern)
 * [建造者模式](#builderpattern)
 * [适配器模式](#adapterpattern)
 * [装饰器模式](#decoratorpattern)
 * [观察者模式](#observerpattern)

### 单例模式
<a id="singlepattern"></a>
提到单例模式就不得不说下 **static** 这个关键字。

- 静态变量：又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来访问它。静态变量在内存中只存在一份。
- 静态方法：在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。只能访问所属类的静态字段和静态方法，方法中不能有 this 和 super 关键字，因此这两个关键字与具体对象关联。
- 非静态内部类：依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类。而静态内部类不需要。

实现单例模式的思路是：一个类能返回对象一个引用（永远是同一个）和一个获得该实例的方法（必须是静态方法，通常使用 **getInstance** 这个名称）；当我们调用这个方法时，如果类持有的引用不为空就返回这个引用，如果类保持的引用为空就创建该类的实例并将实例的引用赋值给该类保持的引用；同时我们还将该类的构造函数定义为私有方法，这样就无法通过调用该类的构造函数来实例化该类的对象，只有通过该类提供的静态方法来得到该类的唯一实例。

单例模式在多线程的环境下，若两个线程同时调用获取实例的方法，它们同时检测到没有唯一实例的存在，同时创建了一个实例，这样就会创建出两个实例，违背了单例模式中的唯一原则。一般解决方式是针对判断类是否已实例化的变量加一个互斥锁。

C# 单线程环境下的单例模式的简单示例
``` C#
public class SinglePackageMachineConfig
{
    /// <summary>
    /// 自动打包机配置单例
    /// </summary>
    private static SinglePackageMachineConfig PackageMachineConfigInstance;
    /// <summary>
    /// 请求端口号
    /// </summary>
    private int autoPackageMachineLANPort;
    /// <summary>
    /// 接口名
    /// </summary>
    private string autoPackageMachineInterfaceName;

    public int AutoPackageMachineLANPort
    {
        get
        {
            return autoPackageMachineLANPort;
        }
    }

    public string AutoPackageMachineInterfaceName
    {
        get
        {
            return autoPackageMachineInterfaceName;
        }
    }

    // 定义私有构造函数，使外界不能创建该类实例
    private SinglePackageMachineConfig()
    {
        autoPackageMachineLANPort = 8080;
        autoPackageMachineInterfaceName = "/SmartPack/Examine";
    }

    public static SinglePackageMachineConfig GetInstance()
    {
        if (PackageMachineConfigInstance == null)
        {
            PackageMachineConfigInstance = new SinglePackageMachineConfig();
        }
        return PackageMachineConfigInstance;
    }
}
```

### 工厂模式
<a id="factorypattern"></a>
工厂模式的目的跟名字一样，为了批量生产出产品，而这里的产品可以理解 **OOP** 语言中的对象。你不需要自己学会生产产品(创建对象)，你只需要告诉工厂产品名字，工厂就会把产品生产好给你。
实现工厂模式的思路是：定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪个类。工厂方法让类的实例化推迟到子类中进行。工厂通常是一个用来创建其他对象的对象。工厂是构造方法的抽象，用来实现不同的分配方案。

简单工厂模式示例
```C#
public class VideoReaderFactory {
    public static VideoReader videoReaderFactoryMethod(InputStream is) {
        VideoReader product = null;

        int videoType = determineVideoType(is);
        switch (videoType) {
            case VideoReaderFactory.MP4:
                product = new MP4Reader(is);
            case VideoReaderFactory.AVI:
                product = new AVIReader(is);
            //...
        }
        return product;
    }
}
```

适用性

- 创建对象需要大量重复的代码。
- 创建对象需要访问某些信息，而这些信息不应该包含在复合类中。
- 创建对象的生命周期必须集中管理，以保证在整个程序中具有一致的行为。

### 建造者模式
<a id="builderpattern"></a>
建造者模式也叫生成器模式，它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。当一些基础部分不变，但基础的组合经常改变时可以使用这种模式。比如我们购买的保险产品，里面的基础保险内容不变，但是有多种多样的保险组合。

- **Builder**：为创建一个 Product 对象的各个部件指定抽象接口。
- **ConcreteBuilder**：实现 Builder 的接口以构造和装配该产品的各个部件。
- **Director**：构造一个使用 Builder 接口的对象。
- **Product**：表示被构造的复杂对象。

建造者模式代码示例
```java
/** "Product" */
 class Pizza {
   private String sauce = "";
 
   public void setSauce (String sauce)     { this.sauce = sauce; }
 }
 
 /** "Abstract Builder" */
 abstract class PizzaBuilder {
   protected Pizza pizza;
 
   public Pizza getPizza() { return pizza; }
   public void createNewPizzaProduct() { pizza = new Pizza(); }
 
   public abstract void buildSauce();
 }
 
 /** "ConcreteBuilder" */
 class HawaiianPizzaBuilder extends PizzaBuilder {
   public void buildSauce()   { pizza.setSauce("mild"); }
 }
 
 /** "ConcreteBuilder" */
 class SpicyPizzaBuilder extends PizzaBuilder {
   public void buildSauce()   { pizza.setSauce("hot"); }
 }
 
 
 /** "Director" */
 class Waiter {
   private PizzaBuilder pizzaBuilder;
 
   public void setPizzaBuilder (PizzaBuilder pb) { pizzaBuilder = pb; }
   public Pizza getPizza() { return pizzaBuilder.getPizza(); }
 
   public void constructPizza() {
     pizzaBuilder.createNewPizzaProduct();
     pizzaBuilder.buildSauce();
   }
 }
 
 /** 服务员制作批萨 */
 class BuilderExample {
   public static void main(String[] args) {
     Waiter waiter = new Waiter();
     PizzaBuilder hawaiianPizzabuilder = new HawaiianPizzaBuilder();
     PizzaBuilder spicyPizzabuilder = new SpicyPizzaBuilder();
 
     waiter.setPizzaBuilder(hawaiianPizzabuilder);
     waiter.constructPizza();
 
     Pizza pizza = waiter.getPizza();
   }
 }
```

### 适配器模式
<a id="adapterpattern"></a>
适配器模式：一个适配使得因接口不兼容而不能在一起工作的类能在一起工作，是两个不兼容接口的桥梁。就像现在的手机耳机转换头一样，通过耳机转换头让 type-c 接口适配 3.5mm 耳机接口。

- **Target**（目标抽象类）：目标抽象类定义所需要的功能，可以是一个抽象类、接口或具体类。
- **Adapter**（适配器类）：对 Adaptee 和 Target 进行适配，通过继承 Target 并关联一个 Adaptee 对象使二者产生联系。
- **Adaptee**（适配者类）：被适配的角色

适配器模式代码示例
```java

public class Adaptee {
    public void adapteeMethod() {
        System.out.println("被适配的角色");
    }
}

public interface Target {
    void Method();
}

// 类适配
public class Adapter extends Adaptee implements Target{
    @Override
    public void Method() {
        super.adapteeMethod();
    }
}

// 对象适配
// public class Adapter implements Target{
//     // 适配者是对象适配器的一个属性
//     private Adaptee adaptee = new Adaptee();

//     @Override
//     public void Method() {
//         adaptee.adapteeMethod();
//     }
// }

public class AdaptTest {
    public static void main(String[] args) {
        Target adapterTarget = new Adapter();
        adapterTarget.Method();
        /*
        # outputs:
        #   被适配的角色
        */
    }
}

```

### 装饰器模式
<a id="decoratorpattern"></a>
装饰器模式也叫修饰模式，一种动态地往一个类中添加新的行为的设计模式，通过装饰器模式可以在运行是扩充一个类的功能。装饰器模式是类继承的另外一种选择。类继承在编译时候增加行为，而装饰器模式是在运行时增加行为。wiki 百科上有个能明显说明装饰器模式功能的例子`一个窗口系统中的窗口，允许这个窗口内容滚动，我们希望给它添加水平或垂直滚动条。假设窗口通过 Window 类实例来表示，并且假设它没有添加滚动条功能。我们可以创建一个子类 ScrollingWindow 来提供，或者我们可以创建一个 ScrollingWindowDecorator 来为已存在的 Window 对象添加这个功能。在这点上，只要是解决方案就可以了。 现在我们假设希望选择给我们的窗口添加边框，同样，我们的原始Window类不支持。ScrollingWindow 子类现在会造成一个问题，因为它会有效的创建一种新的窗口。如果我们想要给所有窗口添加边框，我们必须创建 WindowWithBorder 和 ScrollingWindowWithBorder 子类。显然，这个问题由于被添加类而变得更糟了。对于修饰模式，我们简单的创建一个新类 BorderedWindowDecorator，在运行时，我们能够使用 ScrollingWindowDecorator 或 BorderedWindowDecorator 或两者结合来修饰已存在的窗口。 `

装饰器模式代码示例
```java

public interface Window {
    public void draw();
    public String getDescription();
}


// 无水平和垂直移动的滚动条
public class SimpleWindow implements Window {
    public void draw() {
    }

    public String getDescription() {
        return "simple window";
    }
}

public abstract class WindowDecorator implements Window {
    protected Window decoratedWindow;

    public WindowDecorator (Window decoratedWindow) {
        this.decoratedWindow = decoratedWindow;
    }
    
    @Override
    public void draw() {
        decoratedWindow.draw();
    }

    @Override
    public String getDescription() {
        return decoratedWindow.getDescription();
    }
}

public class VerticalScrollBar extends WindowDecorator {
    public VerticalScrollBar(Window windowToBeDecorated) {
        super(windowToBeDecorated);
    }

    @Override
    public void draw() {
        super.draw();
        drawVerticalScrollBar();
    }

    private void drawVerticalScrollBar() {
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", including vertical scrollbars";
    }
}

public class HorizontalScrollBar extends WindowDecorator {
    public HorizontalScrollBar (Window windowToBeDecorated) {
        super(windowToBeDecorated);
    }

    @Override
    public void draw() {
        super.draw();
        drawHorizontalScrollBar();
    }

    private void drawHorizontalScrollBar() {
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", including horizontal scrollbars";
    }
}

public class Main {
    static void printInfo(Window w) {
        System.out.println("description:" + w.getDescription());
    }

    public static void main(String[] args) {
        SimpleWindow sw = new SimpleWindow();
        printInfo(sw);
        
        HorizontalScrollBar hbw = new HorizontalScrollBar(sw);
        printInfo(hbw);
        
        VerticalScrollBar vbw = new VerticalScrollBar(hbw);
        printInfo(vbw);

        /*
        # outputs:
        #   description:simple window
        #   description:simple window, including horizontal scrollbars
        #   description:simple window, including horizontal scrollbars, including vertical scrollbars
        */
    }
}


```

### 观察者模式
<a id="observerpattern"></a>
观察者模式就像是一种实时事件处理系统。比如你(被观察者)做完了某件事情告诉我(发出通知)，我(观察者)收到了你的通知采取相应的动作，这样的一套简单的过程也能理解成观察者模式。一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。在 MVC 设计中，观察者模式还被用来降低 model 与 view 的耦合程度。一般而言，model 的改变会触发通知其他身为观察者的 model，而这些 model 实际上是 view 。

观察者模式代码示例
```C#
using System;
using System.Collections;

namespace Observer.Patterns.Strategy
{
    // IObserver --> 观察者接口
    public interface IObserver
    {
        void Update(string message);
    }

    // 被观察者的类
    public class Subject
    {
        // 观察者数组，用来发送通知
        private ArrayList observers;

        public Subject()
        {
            observers = new ArrayList();
        }

        public void Register(IObserver observer)
        {
            if (!observers.Contains(observer))
            {
                observers.Add(observer);
            }
        }

        public void Deregister(IObserver observer)
        {
            if (observers.Contains(observer))
            {
                observers.Remove(observer);
            }
        }

        public void Notify(string message)
        {
            // 根据观察者数组内容分别发送通知
            foreach (IObserver observer in observers)
            {
                observer.Update(message);
            }
        }
    }

    public class Observer1 : IObserver
    {
        public void Update(string message)
        {
            Console.WriteLine("Observer1:" + message);
        }
    }

    public class Observer2 : IObserver
    {
        public void Update(string message)
        {
            Console.WriteLine("Observer2:" + message);
        }
    }

    public class ObserverTester
    {
        [STAThread]
        public static void Main()
        {
            Subject mySubject = new Subject();
            IObserver myObserver1 = new Observer1();
            IObserver myObserver2 = new Observer2();

            mySubject.Register(myObserver1);
            mySubject.Register(myObserver2);

            mySubject.Notify("message 1");
            mySubject.Notify("message 2");
            /*
            # outputs:
            #     Observer1:message 1
            #     Observer2:message 1
            #     Observer1:message 2
            #     Observer2:message 2
            */
        }
    }
}
```

