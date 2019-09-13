---
layout: mypost
title: C#之类和对象
tags: C#
---
## 类和对象

类是最基本的C#类型，可在一个单元中就将状态和方法结合起来。

```
public class Point
{
  public int x, y;
  public Point(x, y)
  {
    this.x = x;
    this.y = y;
  }
}
```
类的实例通过`new`运算符来创建，此运算符为实例分配内存。

```
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
以上语句创建了两个`Point`对象，并将这个两个对象的引用存储在两个变量中；再创建这两个对象的时候，默认的会调用类的构造方法。

> 当无法再访问对象时，对象占用的内存会自动回收。

### 成员

类成员分两种，静态成员和非静态成员。静态成员属于类，非静态成员属于类实例。

````
Class Animal
{
  public int x = 1;
  public static int y = 2;
  public void say1(){}
  public static void say2(){}
}
````
上面的`Animal`类包含四个成员，非静态字段`x`，静态字段`y`,非静态方法`say1`，静态方法`say2`，那么在类外想使用这四个成员，对于静态成员，可以直接通过类名使用`Animal.y`,`Animal.say2()`，对于非静态成员则必须先实例化一个对象再使用
```
Animal dog = new Animal();
Console.WriteLine(dog.x);
dog.say1();
```

### 可访问性

可访问性共有以下形式：
1. `public`        
访问不受限制
2. `protected`   
子类可以访问
3. `internal`    
访问限于当前程序集(.exe,.dll等)
4. `protected internal`    
内部保护访问，只限于当前程序集和子类访问
5. `private`    
只能在类内部使用


### 类型参数
类定义的时候可以制定一组类型参数，然后在类主体里可以用这个类型来定义类成员。
```
class Pair<TFfirst, TFsecond>
{
  public TFfirst first;
  public TFsecond second;
}
```
创建实例的时候传入类型
```
Pair<int, string> pair = new Pair<int, string>
{
  first = 1;
  second = "two";
}
```
### 继承

```
class Animal
{
  public int x , y;
  public Animal(int x, int y)
  {
    this.x = x;
    this.y = y;
  }
}
class Dog: Animal
{
  public int z;
  public Dog(int x, int y, int z):base(x, y)
  {
    this.z = z;
  }
}
```
上面类`Dog`继承了`Animal`,`Animal`称为基类，`Dog`称为派生类。`Dog`中构造函数后面的`:base`是为了调用基类的构造函数，因为实例化派生类的时候，不会去执行基类的构造函数。

### 参数

参数有四类：值参数、引用参数、输出参数和参数数组

#### 引用参数ref
```
class Example
{
  static void Swap(ref int x, ref int y)
  {
    int temp = x;
    x = y;
    y = temp;
  }
  public static void SwapExample()
  {
    int i = 1, j = 2;
    Swap(ref i, ref j);
    Console.WriteLine("{0},{1}", i, j);  // 输出 “2,1”
  }
}
```
当参数前面有`ref`修饰符的时候，表明传进去的是一个引用，当这个引用的值变化时，它真正的值也随之变化。

#### 输出参数out
```
 class Example
 {
   static void Swap(int x, int y, out int result)
   {
     result = x/y;
   }
   public static void SwapExample()
   {
     int rem;
     Swap(10, 3, out rem);
     Console.WriteLine(rem);  //输出 3
   }
 }
```
`out`跟`ref`有一点像，区别就是`out`传入的时候不需要赋值，`ref`必须先赋值才能传入

#### 数组参数

```
class Example
{
  public static Swap(string a, params object[] args){}
}
```
当向方法传入的参数数量未知的时候，可以用`params`定义个数组参数，这个数组必须是一维数组，且必须放在参数的最后一个位置。
`Console.WriteLine()`接受的最后一个参数就是数组参数。

### 虚方法、重写方法

声明中有`virtual`修饰符的实例方法就是虚方法，虚方法的存在主要是为了派生类来重写方法，派生类则要使用`override`修饰符

```
class Animal
{
  public virtual void Fuc()
  {
    Console.WriteLine("Animal");
  }
  public void non_virtual()
  {
    Console.WriteLine("Animal non_virtual");
  }
}
class Dog: Animal
{
  public override void Fuc()
  {
    Console.WriteLine("Dog");
  }
  public void non_virtual()
  {
    Console.WriteLine("Dog non_virtual");
  }
}
```

基类`Animal`声明了一个虚拟方法`Fuc`和非虚拟方法`non_virtual`，派生类`Dog`重写了基类的方法`Fuc`同时也定义了一个非虚拟方法`non_virtual`，下面实例化一个对象

```
Animal puppy = new Dog();
puppy.Fuc();                        // 输出 Dog
puppy.non_virtual();                // 输出 Animal non_virtual

Dog kitty = new Dog();
kitty.Fuc();                       // 输出 Dog
kitty.non_virtual();               // 输出 Dog non_virtual
```

#### 申明类和实例类

虚拟函数跟一般函数编译的时候会有所不同：一般函数在编译的时候就静态的编译在了文件中，其相对地址在运行时是不变的，而虚拟函数在运行时的相对地址是不确定的，它会根据运行时期对象实例来判断要调用的函数。其中申明定义的类是`申明类`，执行实例化的类是`实例类`。

```
Animal puppy = new Dog();
```

`Animal`就是申明类，`Dog`就是实例类。

具体的检查流程如下：
1. 当调用一个对象的函数时，首先去检查申明类，看所调用的函数是否为虚函数；
2. 如果不是虚函数，那么直接执行这个函数。如果是虚函数，那么它就会去检查实例类；
3. 在实例类中，看是否有`override`该虚函数，有就执行。如果没有，系统会不停的往上找该实例类的父类，直到找到重载了该虚函数的父类为止。

> 上面代码中，派生类`Dog`与基类`Animal`中都有一个`non_virtual`函数，实际运行中不会出错，但是编辑器会警告，为了消除警告同时为了开发中的混乱，需要在派生类的函数前面加一个`new`修饰符，表示覆盖基类的函数。

### 抽象类

```
public abstract class Animal
{
  public abstract void Eat();
  public abstract void Walk();
}
```
前面有`abstract`修饰符的类就是抽象类，抽象类有个特点，就是不能被实例化，只能被继承。抽象类存在的意义就是：            
** 我是老子（抽象类），你要是跟了（继承）老子，你就必须得会干什么（实际实现）**

抽象类有以下几个特点：
1. 包含抽象方法的都是抽象类；
2. 抽象类中不必非得包含抽象方法；
3. 抽象类不能被实例化，但可以有构造方法；
4. 在实现抽象类的时候，子类必须实现其中的抽象方法；
5. 如果继承的之类没有实现全部的抽象方法，那么这个之类一定也是抽象类；
6. 抽象方法没有主体内容；
7. 抽象方法等同于虚方法，之类重写的时候需要加`override`修饰符。

### 方法重载

同一个类种可以有多个相同名称的方法名，只要这些方法具有唯一签名即可。但调用该名称的方法时，编译器会使用**重裁决策**来确定调用的特定方法。

```
class Animal
{
  static void F()
  {
    Console.WriteLine("F()");                        // F()
  }
  static void F(int x)
  {
    Console.WriteLine("F(int x)");                  // F(1)
  }
  static void F(double x, double y)
  {
    Console.WriteLine("F(double x, double y)");    // F(1.0, 2.0)
  }
  static void F<T>(T x)
  {
    Console.WriteLine("F<T>(T x)");               // F<int>(1)
  }
}
```

### 密封类

当使用`sealed`修饰符类时，表示它是一个密封类，密封类是不能够被继承。此外，也可以密封派生类中的重写方法，这样派生类的派生类将不能够重写虚成员。
```
class Animal
{
   public virtual void say()
   {
     Console.WriteLine("aaaaa");
   }
}
 class Dog: Animal
{
   public sealed  override void say()
   {
     base.say();
     Console.WriteLine("bbbbb");
   }
}
 class Cat : Dog
{
  public override void say()
  {
    base.say();
    Console.WriteLine("cccc");
  }
}
```
派生类`Dog`中密封了重写方法`say`，当它的派生类`Cat`要想再次重写虚函数`say`时就会报错。

### 属性

属性是字段的自然扩展，用于访问属性的语法也和字段一致。但不同的是属性不指明存储位置，，而且属性包含访问器，用于指定在读取或者写入属性值时要执行的语句。

```
class SwapExample
{
    int[] items;
    public int cap
    {
      get { return items.length; }
      set
      {
        if(value < 5)
        {
          value = 5;
        }
        else
        {
          value = 10;
        }
      }
    }
}
```
