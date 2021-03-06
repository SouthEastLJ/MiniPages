# MiniPages

使用静态工厂方法不必在每次调用时都创建一个对象。

缺点1: 如果一个类只能通过静态工厂方法来获得实例，那么该类的构造函数就不能是共有的或受保护的，那么该类就不会有子类，即该类不能被继承。单例模式中首先要私有化构造器。
缺点2：静态工厂方法和其他静态方法从名字上看无法区分，所以我们可以约定静态工厂方法名字使用valueOf或者getInstance。

2.遇到多个构造器参数时考虑使用构建器
（1）直观的解决方案：重叠构造器
（2）javaBeans : 调用无参的构造器来创建对象，然后调用setter方法设置每个必要的参数，以及相关的可选参数。
可读性好，但是javabeans阻止了把类做成不可变的可能。这需要额外的工作保证线程安全。
（3）builder模式：可以有多个可变的参数

3.单例模式



4.通过私有构造器强化不可实例化的能力
只有当类不包含显式的构造器时，编译器才会产生缺省的构造器，因此只要让这个类包含私有的构造器，它就不能被实例化了。
Public class Utility class{
	Private Utility(){
		Throw new AssertionError();  //避免在类的内部调用构造器
	}
}

5. 避免创建不必要的对象
要优先使用基本类型而不是装箱基本类型，要当心无意识的自动装箱。
现代JVM有高度优化的垃圾回收器，其性能很容易超过轻量级对象池的性能。

6.消除对象的过期引用
如果一个对象引用被无意识地保存起来了，那么，垃圾回收机制不仅不会处理这个对象，而且不会处理这个对象引用的所有其他对象。
清空对象引用时一种例外，而不是必须的。如果类自己管理内存，程序员就应该警惕内存泄露的问题。

WeakHashMap


7．避免使用终结方法（finalize）
终结方法的缺点在于不能保证会被及时地执行。这意味着，注重时间的方法不应该由终结方法完成。
覆盖并使用终结方法会有严重的性能损失。 
及时地执行终结方法是垃圾回收算法的一个主要功能，但是在不同的JVM实现中会大相庭径，使用终结方法可能会丧失平台无关性。

但是，当对象持有者忘记调用前面段落中建议的显式终结方法，使用finalize 方法充当“安全网”

对于所有对象通用的方法
8.覆盖equals
不覆盖equals方法的话，每个实例都与它自身相等。
不关心是否覆盖equals:
1 类的实例本质上都是唯一的。
2 不关心类是否提供了“逻辑相同”的测试功能
3 超类已经实现了equals，从超类继承来的行为对于子类是合适的。
4 类是私有的或者包级私有的。
什么时候要覆盖equals呢
比较逻辑相等，并且超类没有覆盖equals以实现相应的功能。希望知道他们在逻辑上是否相同，而不是仅仅想了解是否指向同一个对象。这样做，可以使类的实例可以被用做映射表的键，或者集合的元素，使集合或者映射表现出预期的行为。
对于枚举类型，逻辑和对象相等是一回事。
高质量的equals：
1.	使用==检查“参数是否为这个对象的引用”
2.	使用instanceof检查“参数是否为正确的类型”
3.	把参数转换为正确的类型
4.	是否为对称的，传递的，一致的。
5.	覆盖equals时总要覆盖hashcode
6.	不要将equals声明中的Object对象替换为其他的类型

9．覆盖equals时总要覆盖hashcode
如果不这样做的话，会违反Object.hashcode的通用约定，从而使该类无法结合所有基于散列的集合一起正常工作。包括hashmap,hashset,hashtable
一个好的散列函数通常倾向于“为不相等的对象产生不相等的散列码”。

10.始终覆盖toString
toString应该返回值得关注的信息。

11. 谨慎覆盖clone
其他接口不应该覆盖Cloneable这个接口，为了继承而设计的类也不应该实现这个接口。甚至你可以从不覆盖clone方法，也从不调用它。

12.实现comparable接口

Java平台的所有的值类都实现了Comparable接口。
如果你编写一个值类，它具有很明显的内在排序关系，比如按字母排序，按数值排序或者按年代排序，你就应该实现这个接口。
强烈建议(x.compareTo(y)==0)==(x.equals(y)),但是这个不是必要的。
违反compareTo约定的类会破坏其他依赖与比较关系的类。比如说TreeSet,TreeMap,Collections,Arrays。
考虑BigDecimal类，他的compareTO方法和equals方法不一致。如果你创建hashset实例，添加new BigDecimal(“1.0”) 和 new BigDecimal(“1.00”)，这个集合将包含2个元素，因为他们是通过equals来比较是否相等的。
如果使用TreeSet来比较，集合只包含一个元素，因为他们通过comapreTo来比较。

13.使类和成员的可访问性最小
封装可以独立开发，测试，优化，使用，理解和修改。
尽可能地使每个类或成员不被外界访问。如果类或者接口可以被做成包级私有的，那它就应该被做成包级私有的。

15 使可变性最小
使类变成不可变的：
不要提供任何会修改对象状态的方法
保证类不会被扩展
使所有的域都是final的
使所有的域都是私有的
确保对于任何可变组件的互斥访问

16．复合优于继承
对普通的具体类，进行跨越包边界的继承，是很危险的。
与方法调用不同，继承打破了封装性。
对于你准备扩展的类，你的API是否有缺陷呢，如果有，你是否愿意把这些缺陷传播到类的API中呢。
17 要不为继承设计，并提供文档说明。要不就禁止继承
有文档说明它可覆盖的方法的自用性。
好的API文档说明了一个给定的文档做了什么工作，而不是如何做到的。



45 将局部变量的作用域最小化
46 尽量使用foreach
47 了解和使用类库 尤其是java.lang, java.util, java.io, 注意 java.util.concurrent, java.util.concurrent .
48 精确操作 不要使用 float double
49 基本类型优于装箱基本类型
int —— Integer
double —— Double
boolean —— Boolean
对装箱基本类型运用==操作符几乎总是错误的。
作为集合中的键，元素和值，不能将基本类型放在集合中，必须使用装箱基本类型。
50 如果其他类型更合适，尽量避免使用字符窜
51.当心字符串链接的性能
不适合大规模的运用场景 使用StringBulider来连接字符串
52 使用接口而不是类来作为参数的类型
灵活
特例：
类没有相关联的接口
类实现了接口，但是有接口中不存在的额外方法。
53 接口优于反射机制
反射机制提供了 通过程序来访问关于已加载的类的信息 的能力。给定一个class实例，你可以获得Constructor，Method，Field实例。

55. 谨慎的进行优化
不要优化
在每次试图优化之前和之后，要对性能进行测量。



并发
66 
同步可以阻止一个线程看到对象处于不一致的状态之中，它还可以保证进入同步方法或者同步代码的每个线程。都看到由同一个锁保护的之前所有的修改效果。

写方法和读方法都被同步

http://colobu.com/2017/04/06/dive-into-gRPC-streaming/
import java.lang.ref.Reference;
import java.lang.ref.ReferenceQueue;
import java.lang.ref.WeakReference;
import java.util.LinkedList;

/**
 * Created by l84104863 on 2018/7/20.
 */

public class reference {

    private static ReferenceQueue<VeryBig> rq = new ReferenceQueue<VeryBig>();

    public static void checkQueue() {
        Reference<? extends VeryBig> ref = null;
        while ((ref = rq.poll()) != null) {
            if (ref != null) {
                System.out.println("In queue: "	+ ((VeryBigWeakReference) (ref)).id);
            }
        }
    }

    public static void main(String args[]) {
        int size = 3;
        LinkedList<WeakReference<VeryBig>> weakList = new LinkedList<WeakReference<VeryBig>>();
        for (int i = 0; i < size; i++) {
            weakList.add(new VeryBigWeakReference(new VeryBig("Weak " + i), rq));
            System.out.println("Just created weak: " + weakList.getLast());

        }

        System.gc();
        try { // 下面休息几分钟，让上面的垃圾回收线程运行完成
            Thread.currentThread().sleep(6000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        checkQueue();
    }
}

class VeryBig {
    public String id;
    // 占用空间,让线程进行回收
    byte[] b = new byte[2 * 1024];

    public VeryBig(String id) {
        this.id = id;
    }

    protected void finalize() {
        System.out.println("Finalizing VeryBig " + id);
    }
}

class VeryBigWeakReference extends WeakReference<VeryBig> {
    public String id;

    public VeryBigWeakReference(VeryBig big, ReferenceQueue<VeryBig> rq) {
        super(big, rq);
        this.id = big.id;
    }

    protected void finalize() {
        System.out.println("Finalizing VeryBigWeakReference " + id);
    }
}



