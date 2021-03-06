# Java 的初始化

## 成员初始化

在Java中，保证了类中的每个成员变量在被使用前都是被初始化过的。

* 如果类中的成员是原始类型，那么每个成员变量将会被初始化为默认值，对于布尔类型，会被初始化为`false`，而对于char类型，会被初始化为空格，对于数值类型，则会被初始化为0。
* 如果成员是一个对象的引用，则会被初始化为`null`。

Java中的成员变量，除了可以通过编译器初始化为默认值外，也可以通过在定义的时候指定初始值来进行初始化。通过在定义成员的时候指定初始值，会使得每个对象在创建以后具有相同的成员属性值。

## 构造器初始化

在构造器中，可以自由地对成员变量就行初始化，可以通过调用方法或者别的方式对成员变量进行初始化。构造器初始化在成员自动初始化之后发生。

## 静态成员初始化
不管通过一个类创建了多少对象，static类型的成员只会有一份存储空间。**static修饰符只能作用在类的成员上**。

如果一个原始类型的成员是static类型的，那么默认的初始化值为标准的初始化值（int类型是0，char类型是空格，boolean类型是false）。如果是一个引用类型的static成员，则默认值为null。

如果对static类型的值在定义的时候进行初始化，则初始化的形式和非static类型的成员的初始化方式是一样的。

## 显式static初始化

Java支持一种称为显式static初始化的机制，通过在`static`关键字后面加上一个代码块来表示。在这个代码块中的代码，和普通的static初始化一样，只能在类被加载的时候被执行一次。

```java
class Cup {
    static int a;
    static int b;

    static {
        a = 0;
        b = 0;    
    }
}

```

## 显式非static初始化（实例初始化）

和显式static初始化相对的，Java也提供了非static形式的显式初始化，称为实例初始化，区别是在代码块前不加static关键字。

```java

class Mug {
    int a;
    int b;

    {
        a = 0;
        b = 0;
    }
}

```
这种初始化语法，可以有效地支持匿名内部类的初始化，同样地，它也保证了无论调用了哪个构造方法，都会执行这个实例初始化。实例初始化是在构造方法初始化之前被执行的。

## 初始化顺序

下面，总结下一个对象的初始化顺序，以类Dog为例：

1. 首先，我们需要使用一个类对象来创建实例或者访问类的static成员的时候，需要定位这个类的位置，并且把这个类加载进来。
2. 当Dog.class被加载以后，所有的静态初始化会被执行，所以静态初始化只会被执行一次，只在类被加载的时候被执行。
3. 当创建了Dog对象的时候，会创建一块存放这个对象的堆内存空间，然后将这块内存空间初始化为0，自动将Dog对象中的成员设置为初始值，对于原始类型，整型会初始为0，boolean会初始为false。对于引用类型，则初始化为null。
4. 对在定义的时候指定了初始化值的成员，进行成员初始化。
5. 执行对象的构造方法。

### 继承下的初始化顺序

* 当在加载当前类的时候，加载器发现当前类包含了父类，则会继续加载它的父类，如果在加载父类的时候，加载器发现父类也包含了父类，则继续逐层往上加载继承的父类。
* 当父类加载完成以后，首先会在最顶层的父类进行static初始化，然后逐层往下进行static初始化，直到当前类static初始化完成为止（这个顺序保证了，在子类中引用父类的static成员的时候，保证成员是已经被初始化完成的）。在这个阶段，创建对象需要的所有类都已经被加载进来了，接下来开始创建对象。
* 创建对象以后，首先，会对实例成员进行初始化，将成员初始化为默认值（通过将对象的内存空间初始化为0）。接下来，会自动调用父类的构造器进行初始化，也可以通过在子类中通过`super`关键字手动调用父类的构造器。在父类构造完成以后，开始实例属性的初始化（通过在定义成员的时候指定的初始值进行初始化），最后执行构造器剩下的代码。这个过程是递归进行的，在父类层次中，也执行和子类一样的构造流程（如果父类也包含了父类的话)
