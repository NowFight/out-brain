# Java的Interface机制

在面向对象中，接口和抽象类提供了更加结构化的方法使得具体的实现和接口分离。

## 抽象类和抽象方法

Java提供了一种称为抽象方法的机制，可以将实现和接口分离。所谓的抽象方法（abstract method），就以一种不完整地方法，它只是声明了一个方法，但是并没有方法的实现。

```java

abstract void method();

```

如果在一个类中出现了抽象方法，那么这个类就被称为抽象类。一个类如果是抽象类，那么就需要在类的定义的时候加上`abstract`修饰符。

一个抽象类不能进行实例化，也就是说不能通过一个抽象类来创建对象。在从一个抽象类继承的时候，如果子类需要进行实例化，那么在子类中需要实现父类中所有的抽象方法。否则，子类将不能进行实例化，并且该子类还是一个抽象类，所以必须使用`abstract`进行修饰这个类。

在一个抽象类中可以不包含任何抽象方法，这么做的目的，可以阻止这个抽象类实例化对象。

## 接口（Interfaces）

Java的接口机制是对抽象类的进一步的抽象。在抽象类中，Java允许声明一部分接口，然后实现一部分通用的接口。而Java的interface，提供了一个纯抽象的类，在这个类中没有实现，只有抽象方法。

Java通过`interface`关键字创建一个接口，在接口中的成员，默认都是隐式`static`和`final`修饰的。在实现一个接口的时候，使用`implements`关键字实现接口。

### Java中的多继承实现

在Java中，不支持像C++那样的多继承，也就是说，一个Java类只能有一个直接父类。但是通过Java的接口机制，可以实现多继承的效果，而这也是Java实现类似多继承这种机制的唯一途径。

在我们实现一个类的时候，我们只能继承自一个唯一的父类，在这之后，我们可以实现多个接口。所以，在实现的时候，接口必须出现在父类的后面，也就说，`extends`关键字必须在`implements`关键字之前。如果有多个接口，那么通过逗号`,`进行分隔。

### 扩展接口

Java接口，可以通过`extends`关键字实现继承。`extends`关键字在类基础和接口继承中，具有不同的用法。在类上使用`extends`进行继承，只能指定一个父类。而在接口上使用`extends`进行继承，可以指定多个接口，并且通过逗号`,`进行分隔。

```java

interface Monster {
    void menace();
}

interface Lethal {
    void kill();
}

interface Vimpire extends Monster, Lethal {
    void drinkBlood();
}

```

### 接口嵌套

Java的接口是可以在类和接口中嵌套定义，也就是说，在一个接口或类内部，可以定义别的接口，并且可以在定义接口的时候指定接口的访问权限。

在一个接口内定义的接口，定义的方式和非嵌套的接口是一样的，除了我们可以给这些嵌套的接口指定访问权限。

```java

class A {
    interface B {
        void bMethod();
    }

    public class BImpl implements B {
        public void bMethod() {}
    }

    public interface C {
        void cMethod();
    }

    class CImple implements C {
        public void cMethod() {}
    }

    private interface D {
        public void dMethod();
    }

    public class DImple implements D {
        public void dMethod() {}
    }
    
    public D getD() { return new DImple(); }
    private D dRef;
    public void receivecD(D objD) {
        dRef = objD;
        dRef.dMethod();
    }
}

```

在类中定义的嵌套接口，可以用`private`修饰。使用`private`修饰的接口，有一些好处。一个private修饰的嵌套接口，只能在定义这个接口的类的内部使用，内部的嵌套类可以实现这个接口。当内部的嵌套类实现这个接口，如果定义的这个内部类是public类型的，那么即使它具有public修饰，在类的外部，它只能作为自己定义的类型被使用，不能像其他类型一样向上转型为父类类型。因为它实现了一个private接口，而这个接口在外部是不可见的，所以我们不同在类的外部使用这个私有接口，所以也就不能进行向上转型(upcasting)。在类外部，我们只能使用它的原来的类型。

在上面的代码中，我们可以看到，类A中的`getD()`方法返回的对象，我们在类的外部不能定义一个变量来存储它，因为它返回的是一个private接口类型的对象，所以我们在类的外部不能访问这个接口。只能通过类A提供的`receiveD()`接口来接受这个对象。


```java

interface E {
    interface G {
        void gMethod();
    }

    public interface H {
        void hMethod();
    }
}

public class ABImpl implements A.B {
    public void bMethod() {} 
}

public class EGImpl implements E.G {
    public void gMethod() {}
}

```

在上面的代码中，接口E中定义了两个接口G和H。在一个接口中定义的接口，要求所有的接口必须是public类型的，所以在一个接口中定义的接口不能是private类型的。

当我们实现一个接口的时候，我们并不要求实现所有在这个接口中定义的嵌套的接口。并且，`private`类型的接口，不能在定义它们的类的外面实现这些接口。
