# 设计模式之里氏替换原则 LSP（Liskov Substitution Principle）

## 定义
> If for each object o1 of type T there is an object o2 of type S such that for all programs P defines in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T. （对于每个类型为T的对象o1，存在一个类型为S的对象o2，使得以T定义的所有程序P在当对象o1被替换为o2时，程序P的行为并没发生改变，那么类型S是类型T的子类型。）

> 所有使用基类的地方必须能透明地使用其子类的对象。

通俗点讲，也就是只要父类能出现的地方，子类也可以出现，而且替换为子类也不会产生任何错误或异常。使用者可能根本就不需要知道是父类还是子类，但是，反过来就不行了，子类出现的地方，父类未必就能适应。

## 问题
> 设功能P1由类A实现，现需要将功能P1扩展为P，其中P由原来的功能P1和新的功能P2组成。新功能P2由类A的子类类B来实现，那么子类B在实现新功能P2时，可能会导致原有功能P1出现故障。

## 解决方案
当然是要使用**LSP(里氏替换原则)**了。也就是说当你使用**继承**时，要遵循**LSP**。即类B继承类A时，除了增加一些新的方法完成新的功能外，尽量**不要重写父类的方法**，也**不要重载父类的方法**。

## 实例说明
定义一个操作类，里面包含一个方法，该方法将两个数相加，代码如下：

```java
/**
 * 实现两数相加
 */
public class Operation {

    public int m1(int a, int b) {
        return a + b;
    }
}

public class OperationClient {
    public static void main(String[] args) {
        Operation operation = new Operation();
        System.out.println("60+40=" + operation.m1(60, 40));
    }
}
```

运行结果：

> 60+40=100

此时有了新的需求，需要扩展该操作类，使其具有将两个数相减然后再与100求和的功能。我们考虑通过**扩展**Operation类来实现该功能。那么这就意味着扩展后的类具有以下功能：

- 实现两数相加 （从父类继承下来的功能）
- 实现两数相减，然后再与100求和 （新扩展的功能）

代码如下：

```java
/**
 * 实现两个数相减，再与100求和
 */
public class SubOperation extends Operation {
    /**
     * 两个数相减
     */
    public int m1(int a, int b) {
        return a - b;
    }

    /**
     * 再与100求和
     */
    public int m2(int a, int b) {
        return m1(a, b) + 100;
    }
}

public class OperationClient {
    public static void main(String[] args) {
        SubOperation subOperation = new SubOperation();
        System.out.println("60+40=" + subOperation.m1(60, 40));
        System.out.println("60-40+100=" + subOperation.m2(60, 40));
    }
}
```

此处假定我们**重写**了父类的operate()方法，并且新增了一个方法，用来实现两个数相减的功能（这才是我们真正要实现的需求）。
看看运行结果：

> 60+40=20
> 60-40+100=120

如上可以看出，原来正常的功能出现故障了。就是因为我们在子类中为新方法起名时无意中**重写**了父类的方法，导致原来的功能异常。在实际开发中，类似重写或重载父类方法的情况是很常见的，这就存在潜在的风险，导致类似的异常问题出现。因此，在实际开发中，我们应当尽可能的遵守里氏替换原则。

## 总结

**LSP**为良好的**继承**定义了一个规范，通俗来讲就是，**子类可以扩展父类的功能，但不能改变父类原有的功能**。其包含了4层含义：

1. 子类可以实现父类的抽象方法，但不能覆盖父类的非抽象方法。子类必须完全实现父类的方法。
> 在类中调用其他类时务必要使用父类或接口，如果不能使用父类或接口，则说明类的设计违背了**LSP原则**。如果子类不能完整的实现父类的方法，或者父类的某些方法在子类中已经发生“畸变”，则建议**断开父子继承关系**，采用**依赖、聚集、组合**等关系代替继承。

2. 子类可以有自己的个性，即可以增加自己特有的方法。
> **向下转型**是不安全的，有子类出现的地方父类未必就可以出现。

3. 覆盖或实现父类的方法时输入参数可以被放大
> 子类中方法的前置条件必须与超类中被覆盖的方法的前置条件相同或者更宽松。

4. 覆写或实现父类的方法时输出结果可以被缩小