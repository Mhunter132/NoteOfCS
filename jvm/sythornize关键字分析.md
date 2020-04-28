## 锁关键字的分析

![1559689456969](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559689456969.png)

```java
private synchronized void setX(int x){ }
```

![1559689601158](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559689601158.png)

moniterenter

moniterexit

moniterexit

jvm要能观察到所有异常信息，在出现异常前执行moniterexit

### 成员变量与构造方法的关系

成员变量在构造方法内完成初始化没有构造方法在默认构造方法中初始化，有几个定义的构造方法就执行几次成员变量初始化

----

不管有多少个静态变量和静态代码块都是在 [^<clinit>]中进行初始化

---

### 分析this关键字及异常表

![1559695686298](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559695686298.png)

![1559696285692](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559696285692.png)![1559733963511](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559733963511.png)

为什么局部变量是4个 ： this ---is --- serversocket ---  ex(异常表)

![1559696012412](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559696012412.png)最多放三个操作数

![1559696091375](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559696091375.png)![1559696119255](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559696119255.png)

### 栈帧与操作数剖析及符号引用

栈帧本身是种数据结构

![1559738506379](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559738506379.png)

**符号引用**转换为**直接引用**（编译期拿不到，符号引用存放在常量池当中的，方法调用就是将符号引用作为参数，在第一次使用时转换为直接引用**（静态解析）**，或在每次运行时转换为直接引用**（动态链接）**）

> **在JVM中类加载过程中，在解析阶段，Java虚拟机会把类的二进制数据中的符号引用替换为直接引用。**
>
> 1.符号引用（Symbolic References）：
>
> 　　符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定位到目标即可。例如，在Class文件中它以CONSTANT_Class_info、CONSTANT_Fieldref_info、CONSTANT_Methodref_info等类型的常量出现。符号引用与虚拟机的内存布局无关，引用的目标并不一定加载到内存中。在[Java](http://lib.csdn.net/base/javaee)中，一个java类将会编译成一个class文件。在编译时，java类并不知道所引用的类的实际地址，因此只能使用符号引用来代替。**比如org.simple.People类引用了org.simple.Language类，在编译时People类并不知道Language类的实际内存地址**，因此只能使用符号org.simple.Language（假设是这个，当然实际中是由类似于CONSTANT_Class_info的常量来表示的）来表示Language类的地址。**各种虚拟机实现的内存布局可能有所不同，但是它们能接受的符号引用都是一致的**，因为符号引用的字面量形式明确定义在Java虚拟机规范的Class文件格式中。
>
>  
>
> 2.直接引用：（**就是地址引用**）
>
>  直接引用可以是
>
> （1）直接指向目标的指针（比如，指向“类型”【Class对象】、类变量、类方法的直接引用可能是指向方法区的指针）
>
> （2）相对偏移量（比如，指向实例变量、实例方法的直接引用都是偏移量）
>
> （3）一个能间接定位到目标的句柄
>
> 直接引用是和虚拟机的布局相关的，同一个符号引用在不同的虚拟机实例上翻译出来的直接引用一般不会相同。如果有了直接引用，那引用的目标必定已经被加载入内存中了。

**slot**:存取局部变量最小的单位

一个32位，double用两个slot来存储。

![1559735910174](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559735910174.png)

### 方法重载和invokevirtual

![1559739012211](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559739012211.png)![1559739073874](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559739073874.png)![1559739144901](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559739144901.png)

以上四种是非虚方法，他们在类加载阶段就可以将符号引用转换为直接引用。

![1559739515465](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559739515465.png)

猜测是：Father ，Son 

结果：Grandpa ，Grandpa

Grandpa是G1的**静态类型**，G1的实际类型**Father.**

我们可以得出结论：变量的静态类型是不会变得，但是变量的实际类型则是可以发生改变的。在运行期确定

方法重载：对于jvm来说是一种重载。进行方法重载是在编译期确定的，然后拿静态类型去重载。

### 方法的动态分派（方法重写）

方法重载是在静态的，是编译期行为；方法的重写是动态的，是运行期行为。

![1559741205162](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1559741205162.png)

是在调用的Fruit的方法。

**invokevirtual( )**方法在运行期去操作数栈顶去寻找实际的类型并在常量池查找相同的方法描述符号，并且权限校验也通过就执行方法。如果没有就去父类找最后没找到就报错。