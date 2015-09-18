# Java 内部类

在Java中，允许在一个类定义中嵌套另一个类的定义，这在Java中被称为内部类（inner class）。

在Java中引入内部类，可以让类的设计者对类进行分组，并且可以控制一个类相对于其他类的可见性。创建内部类的语法非常简单，只是在一个类的内部再定义一个类。在内部类中，它的外围类中定义的成员，无论是私有的还是公有的，对这个内部类而言都是可见的。

引用内部类的语法是 ` OuterClass.InnerClass `

## 内部类和外部类之间的联系

内部类的实现只是一种名字绑定和代码组织方式。当在一个类中创建了一个内部类以后，内部类实例化以后的对象和外部类的实例化对象之间是相关关联的。在内部类中可以访问外部类中的任何成员。

在实现上，内部类会隐式地包含一个指向负责创建这个内部类对象实例的外部类的实例对象。当引用外部实例对象的成员的时候，会通过这个引用来访问这个成员。这些操作都是编译器负责处理的，对用户是透明的。所以，我们只能通过外部类的实例对象来创建一个内部类的实例对象。内部类的构造过程中需要负责创建这个内部类对象的外部类对象的实例的引用，所以如果没有对应的引用，则编译器将会报错。

## .this 和 .new 关键字

如果需要引用外部类对象，则可以通过关键字**OutClass.this**（外部类的名字后跟一个**.this**）来进行访问，这会在编译期进行检查，并且获得正确的类型。

```java


public class DotThis {
      void f() { System.out.println("DotThis.f()"); }
      public class Inner {
        public DotThis outer() {
          return DotThis.this;
          // A plain "this" would be Inner’s "this"
        }
      }
      public Inner inner() { return new Inner(); }
      public static void main(String[] args) {
        DotThis dt = new DotThis();
        DotThis.Inner dti = dt.inner();
        dti.outer().f();
      }
} 

```

如果需要通过一个外部类的实例对象来创建它拥有的内部类的实例，则需要在**new**表达式中提供一个指向外部类对象的引用。可以通过**outObj.new**（外部类的对象后面跟**.new**）的语法来创建

```java

public class DotNew {
      public class Inner {}
      public static void main(String[] args) {
        DotNew dn = new DotNew();
        DotNew.Inner dni = dn.new Inner();
      }
}


```

所以，为了创建一个内部类，我们必须提供一个外部类的实例对象，我们不能在没有外部类实例对象的情况下创建一个内部类对象。因为在内部类对象中，我们需要包含一个指向外部类对象的引用。

## 匿名内部类

```java

public class Parcel7 {
      public Contents contents() {
        return new Contents() { // Insert a class definition
          private int i = 11;
          public int value() { return i; }
        }; // Semicolon required in this case
      }
      public static void main(String[] args) {
        Parcel7 p = new Parcel7();
        Contents c = p.contents();
      }
} 

```

上面的代码，在contents()方法中，我们创建了一个类对象，然后实例化了这个临时创建的类对象，并将这个实例作为返回值返回。这里，我们创建的类，被称为匿名类，因为这个类没有名字。上面创建匿名类的语法表示：创建一个匿名类的对象，这个匿名类继承了 **Contents**类。创建的这个匿名类对象在返回的时候类型提升为**Contents**类型。

在一个匿名类中,如果我们继承的父类的构造函数包含参数，我们可以在创建内部类的时候通过传递参数的方式传递给父类的构造函数（参照下面的Base类）。如果我们需要对这个匿名类实例化的对象进行初始化，我们将不能使用这个匿名类的构造方法，因为这个匿名类没有名字，所以也就不存在所谓的构造方法了（在Java中，构造方法是和类名相同的）。所以，在匿名类中，为了可以初始化一个对象，我们需要使用Java初始化中的一种称为**实例初始化**的语法，这种语法在匿名类的初始化中非常有用。

```java

abstract class Base {
    public Base(int i) {
        print("Base constructor, i = " + i);
    }
    public abstract void f();
}
public class AnonymousConstructor {
    public static Base getBase(int i) {
        return new Base(i) {
            { 
                print("Inside instance initializer"); 
            }
            public void f() {
                print("In anonymous f()");
            }
        }; 
    }
    public static void main(String[] args) {
        Base base = getBase(47);
        base.f();
    }
}

```

虽然通过实例初始化的语法，我们可以弥补匿名类不能创建构造方法的缺点，但是，实例初始化不支持方法重载，所以匿名类只能具有一种是初始化方式。

匿名类可以通过继承一个父类或者实现一个接口的方式创建，但是，和普通类的继承和实现不同，匿名类要么只能继承一个类，要么只能实现一个接口，但是不能同时实现一个接口和继承一个类。并且，实现接口的时候，只能实现一个接口。

## 嵌套类

如果内部类的实例对象不需要和外部类的实例对象之间建立联系，那么可以将内部类定义为`static`。静态的内部类被称为**嵌套类**。由于嵌套类并不需要包含对外部类实例对象的引用，所以嵌套类具有以下的2点特性：

1. 在创建嵌套类对象的时候，并不需要通过外部类的实例对象来创建
2. 不能在嵌套类的实例对象中访问外部类的非静态成员

嵌套类和内部类具有一些区别，在内部类中的成员，由于只能存在于一个类的外部层次中，所以内部类中不能包含静态成员，也不能包含嵌套类。但是，在嵌套类中，这些都没有做限制。

```java

public class Parcel11 { 
    private static class ParcelContents implements Contents { 
        private int i = 11; 
        public int value() {
            return i; 
        } 
    } 
}    

```

## 在接口中定义的类

一般情况下，我们不能在接口（interface）中加入任何实现代码。但是，在接口中，我们可以定义嵌套类。任何定义在接口中的类，默认都是`public`和`static`的。所以在接口中定义的嵌套类，实际上只是把这个类放在接口定义的名字空间中。在嵌套类中，甚至可以实现包围它的接口。

```java

public interface ClassInInterface {
    void howdy(); class Test implements ClassInInterface {
        public void howdy() {
            System.out.println("Howdy!");
        }
       
        public static void main(String[] args) {
            new Test().howdy(); 
        }
    }
} 

```

## 局部内部类

一个内部类可以被定义在局部作用域中，也就是可以定义在代码块中。这样的内部类被称为**局部内部类**。一个局部内部类在定义的时候不能包含访问限定符（public、private、protected），因为它不是类定义的一部分。但是，局部内部类可以访问所在代码块中的所有**final**修饰的变量，以及这个代码块所在的外围类中的所有成员。

```java

interface Counter {
    int next(); 
} 

public class LocalInnerClass {
    private int count = 0; 

    Counter getCounter(final String name) {
        // A local inner class: 
        class LocalCounter implements Counter {
            public LocalCounter() {
                // Local inner class can have a constructor 
                print("LocalCounter()"); 
            } 
            
            public int next() {
                printnb(name); // Access local final 
                return count++; 
            } 
        } 
        
        return new LocalCounter(); 
    }
}

```

## 内部类的表示方式

在Java中，每个类在编译以后都会产生一个**.class**文件。在这些文件中包含了类相关的元信息，这些元信息会构成一个称为**Class**的对象。而内部类也会产生一个对应的**.class**文件。内部类class文件的命名规则是：`OuterClass$InnerClass.class`，中间是通过美元符`$`进行分隔的。
