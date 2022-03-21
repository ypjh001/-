> #  :heart_decoration: JavaSE温故而知新
>
> 温故而知新。结合jvm学习java。学自胡鑫喆老师。可能是你见过最好最通俗的java笔记，每一个例子每一个图都会让你有新的理解。不论是小白初学还是复习用，都会有新的收获。并非最终版，持续更新中。。。
>
> ![img](JavaSE复习.assets/hbimg.b0.upaiyun.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg)



[TOC]

------



# 一、Java基础

> :baby_chick: 为了简洁美观的代码而奋斗！
>
> - 可读性
> - 可用性
> - 高效性
> - 可维护性
> - 可重用性
> - 可拓展性
> - 弱耦合性

## JDK、JRE、JVM

![img](JavaSE复习.assets/79b8d43da03034c1d2094cdbda6a3893.png)

JDK(java程序开发包)=JRE +Tools

JRE=JVM(虚拟机)+API

`javac ``XXX.java` 生成 `.class `文件

`java XXX`运行

------

## **基础语法**

> ### Java编码规范
>
> 1. 标识符命名规范：望文知意、大小写规则(类名单词首字母大写、变量名或方法名首单词小写后面单词首字母大写、包名全小写、常量名全大写)
> 2. 缩进
> 3. 注释规范：注释量不低于100%

### 1.注释

```java
/**
 * @author 王泽
 * 这里是文档注释，可以与命令 javadoc 生成帮助文档
 */

public class Jichu01 {
    public void zhushi(){//这是行内注释
        System.out.println("第一节、注释");
    }
/* 这里是多行注释
   这里是多行注释
   这里是多行注释
 */

}

```

### 2.关键字

```markdown
# (1)用于数据类型。
　　用于数据类型的关键字有 boolean、byte、char、 double、 false、float、int、long、new、short、true、void、instanceof。
# (2)用于语句。
　　用于语句的关键字有break、case、 catch、 continue、 default 、do、 else、 for、 if、return、switch、try、 while、 finally、 throw、this、 super。
# (3)用于修饰
　　用于修饰的关键字有 abstract、final、native、private、 protected、public、static、synchronized、
　　transient、 volatile。
# (4)用于方法、类、接口、包和异常。
　　用于方法、类、接口、包和异常的关键字有 class、 extends、 implements、interface、 package、import、throws。
# 还有些关键字,如cat、 future、 generic、innerr、 operator、 outer、rest、var等都是Java保留的没有意义的关键字。
# 另外，Java还有3个保留字:true、false、null。它们不是关键字，而是文字。包含Java定义的值。和关键字一样,它们也不可以作为标识符使用。
```



### 3.数据类型

> :star: 八大基本数据类型：  // 1 字节 占 8位二进制位
>
> 基本数据类型 |  	存储大小   |     	取值范围                             				   |  默认值
> byte	         |  8 位有符号数	|-2^7−27 到 2^7-127−1			                  | 0
> short			| 16 位有符号数   |-2^{15}−215 到 2^{15}-1215−1			  	|0
> int				| 32 位有符号数   |-2^{31}−231 到 2^{31}-1231−1				  |0
> long			 | 64 位有符号数   |  -2^{63}−263 到 2^{63}-1263−1				|0L
> float			 | 32 位	             | 负数 -3.402823e+38 到 -1.401298e-45，正数 1.401298e-45 到 3.402823e+38	0.0f
> double		 | 64 位                 |负数 -1.797693e+308 到 -4.9000000e-324，正数 4.9000000e-324 到 1.797693e+308	0.0d
> char			  |16 位	              |0-65535						                              |'\u0000'
> boolean	    |1 位	               |true 和 false	                                             |false
>
> :eyes:  **注意：**
>
> - 整数类型的直接量默认是 int 类型，如果直接量超出了 int 类型的取值范围，则必须在其后面加上字母 L 或 l，将直接量显性声明为 long 类型，否则会导致编译错误。
>
> - 浮点类型的直接量默认是 double 类型，如果要将直接量表示成 float 类型，则必须在其后面加上字母 F 或 f。将 double 类型的直接量赋值给 float 类型的变量是不允许的，会导致编译错误。
>
> - 将小范围类型的变量转换为大范围类型称为拓宽类型，不需要显性声明类型转换。将大范围类型的变量转换为小范围类型称为缩窄类型，必须显性声明类型转换，否则会导致编译错误。
>
> - 布尔类型不能转换成其他基本数据类型，其他基本数据类型也不能转换成布尔类型。
>
> - **字符类型与数字类型之间可以进行转换。**
>
>   将数字类型转换成字符类型时，只使用整数的低 16 位（浮点数类型将整数部分转换成字符类型）。
>
>   将字符类型转换成数字类型时，字符的统一码转换成指定的数值类型。如果字符的统一码超出了转换成的数值类型的取值范围，则必须显性声明类型转换。

**特殊字符与转义字符序列**

![转义字符](JavaSE复习.assets/20181005145334860)

------

> :100: 例题：
>
> ![image-20210630171521801](JavaSE复习.assets/image-20210630171521801.png)
>
> ![image-20210630171559319](JavaSE复习.assets/image-20210630171559319.png)

```java
 public static void main(String[] args) {
        int a =4;
        long b =5L;
        int c =(int)b;
        long d = a;
        char e = 'A';
        int f = e;
        System.out.println(c); //5
        System.out.println(d); //4
        System.out.println(f); //65
     /*Unicode 编码方式
     char c ='A'; char c = 65; char c='/u0041';// 这三个都是相等的 都是A
     'A' -----65
     'a'------97
     '0'------48
     */

    }
```

对象数据类型：类，接口，数组

### 4.变量和常量

> 变量：`数据类型 变量名 [=值]；`  
>
> 作用域：
>
> - 类变量(静态变量) static
> - 成员变量(实例变量) 
> - 局部变量（方法内）==先赋值再使用==

> 常量 `final MAX_A=10;`

Java变量和常量的储存位置

1. 类常量和静态变量：基本类型的常量在运行时常量池中，引用类型的常量（例如字符串）在运行时常量池中保存自堆中的直接引用。静态变量也是（因为静态变量必须在类加载的时候就初始化，因为它是可以通过类名访问的）
2. 除静态变量之外的类变量：类变量只有在对象创建之后才分配内存，所以基本类型的话在堆中的对象中，引用类型的话在堆中、堆中的对象保存引用
3. 局部变量：局部变量也是只有对象才有的，但是它在方法中，所以其实它是在栈上的：如果是基本类型，就保存在栈上；如果是引用类型，就保存在堆上，然后在栈中保存一个引用

### 5.运算符

> - 算数运算符 `+ - * / % ++ --`
> - 赋值运算符 `=`
> - 关系运算符 `> < >= <= == != instanceof`
> - 逻辑运算符 `&& || !`
> - 位运算符`& | ^ ~ >> << >>>`
> - 条件运算符 ` xx? xx :xx`
> - 拓展运算符 `+= -= *=...`

### 6.流程控制

> 顺序结构 程序默认
>
> 选择结构
>
> - if 单选择
> - if-else 双选择
> - if-else if-else 多选择结构
> - switch case
>
> 循环结构
>
> - while
> - do..while
> - for
> - 增强for循环
>
> **几个关键字的使用 break 、continue**
>
> break语句在switch中是用来中止case的。实际上break语句在for、while、do···while循环语句中，用于强行**退出当前循环**，为什么说是当前循环呢，因为break只能跳出离它最近的那个循环的循环体，假设有两个循环嵌套使用，break用在内层循环下，则break只能跳出内层循环，如下：
>
> ```java
> for(int i=0; i<n; i++) {    // 外层循环
>     for(int j=0; j<n ;j++) {    // 内层循环
>         break;
>     }
> }
> ```
>
> **continue语句只能用于for、while、do···while循环语句中，用于让程序直接跳过其后面的语句，进行下一次循环。**
>
> 　　例：输出10以内的所有奇数
>
> ```java
>  1 public class ContinueDemo {
>  2 
>  3     public static void main(String[] args) {
>  4         int i = 0;
>  5         
>  6         while(i < 10) {
>  7             i++;
>  8             
>  9             if(i%2 == 0) {    // 能被2整除，是偶数
> 10                 continue;    // 跳过当前循环
> 11             }
> 12             
> 13             System.out.print(i + " ");    
> 14         }
> 15     }
> 16 
> 17 }
> ```
>
> 　　这里if条件判断是否为偶数，如果是偶数则执行continue，直接跳出本次循环，不进行continue后的步骤（即不执行输出语句），然后下一次循环为奇数，输出i

9*9乘法表

```java
public static void main(String[] args) {
        for (int i =1;i <= 9;i++){
            for (int j=1;j <= i;j++){
                System.out.print(j+"*"+i+"="+(i*j)+"\t");
            }
            System.out.println("");
        }
    }
```

　return语句可以从一个方法返回，并把控制权交给调用它的语句。

### 7.方法（函数）

完成特定功能的一部分独立存在的代码

- 函数的定义

  ```java
  修饰符 （static） 返回值类型 方法名（参数表）{
      方法体；
  }
  ```

- 方法的调用

  ```java
  函数名（参数表）
  ```

- 方法的参数

  ```java
  （数据类型 形参名1，数据类型 形参名2）
      形参：是一个在方法内部有效的局部变量
      赋值的时候会发生在函数调用的时候，也就是实参
  例如：
     static void xiaoWang(){
      xiaoliu("排骨",8);
  }
    static void xiaoliu(String eat,int x){
        System.out.println("今天给老公做了"+eat+",还买了"+x+"瓶啤酒")；
    }
  ```

- 方法的返回值

  return：程序流程会返回到方法的调用点。

### 8.方法与JVM结合分析

![image-20210702150557125](JavaSE复习.assets/image-20210702150557125.png)

栈空间：**用来存储函数内部调用时产生的数据。（局部变量）**

栈帧：每次函数调用时的数据 局部变量表

#### 嵌套调用分析

```java
public static void main(String[] args){
    int a =10;
    m1(a);
}
static void m1(int b){
    m2(b);
}
static void m2(int c){
    int d = 20;
}
```

运行时JVM分析：

![image-20210702151335196](JavaSE复习.assets/image-20210702151335196.png)

**局部变量随栈帧共存亡**

#### 递归调用分析

当把一个大问题分解成若干个小问题，发现小问题的求解方式和大问题相同，这时候就可以用递归来解决。

```java
/*这样会导致栈溢出*/
public static void main(String[] args){
    m1();
}
static void m1(){
    m1();
}
```

**递归必备**：==回归条件==

计算n的阶乘

```java
package com.jichu;

/**
 * @author 王泽
 * 计算n的阶乘
 * n！ = n* （n-1）！
 */

public class Fact {
    public static void main(String[] args) {
        System.out.println(fact(4));
    }
    /**计算n的阶乘*/
    static int fact(int n){
        if (n==1){ return 1;}
        int result = n*fact(n-1);
        return result;
    }

}

```

![image-20210702171006974](JavaSE复习.assets/image-20210702171006974.png)

> 计算斐波那契数列的第n项
>
> ```java
> static int fobo (int n){
>     if (n == 1){ return 0;}
>     if (n == 2) {return 1;}
>     return fobo(n-1) + fabo(n-2);
> }
> ```
>
> 

### 9.数组

#### 数组的语法：

:one:数组的定义：

```java
//数据类型[] 数组名；
    int[] a;
```

:two: 定义后JVM不会为其分配内存，所以我们需要为数组分配空间

——隐式初始化

```java
a = new int[5];
JVM会自动给每个数据给一个默认值 0（数值类型）false（boolean）  空指针null（对象类型） ，不同的数据类型分配不同默认值。
```

——显示初始化

```java
a = new int[ ]{5,6,8,9};
数组长度由大括号里的数据决定。
```

合并写法：

```java
int[] a =new int [5]; //隐式

int[] a = {5,6,8,9}; //显示
```

:three: 使用

```java
数组名[下标]
```

:star: 常用数组操作

> 1.查询数组长度  `数组名.length`  a.length  
>
> 2.遍历数组 （按顺序访问数组中的所有元素）
>
> ```java
> for(int i = 0; i<a.length;i++){
>     操作.....
> }
> ```
>
> 

#### 数组的内存结构

```java
public static void main(String[] args){
    int[] a = new int[5];  //只要有new 就是在堆空间开辟空间
}
```

JVM中分析

这个数组在堆中的空间是连续的。

![image-20210702220126778](JavaSE复习.assets/image-20210702220126778.png)

`new int[5];`就是开辟这些空间，然后将首地址赋值给a

![image-20210702220235912](JavaSE复习.assets/image-20210702220235912.png)

> ==数组的空间在内存中是连续的==
>
> - 数组的长度固定，不可改变
> - 数组的下标从0开始 （追求最快的寻址速度）
> - 查询效率是最高的，增加元素或删除元素是最低效率
>
> ==改变数组长度的方法==
>
> - 不可能在原数组中改变
> - 只能创建一个新数组
> - `a=b;` 把新数组的地址给原数组
> - 再把原数组里的元素赋值到新数组`b[i]=a[i];` 
>
> **简写：`java.util.Arrays.copyOf(原数组，新数组长度)；`**
>
> ==多个数组变量中可以存储相同地址==
>
> ```java
> package com.jichu;
> 
> import java.util.Arrays;
> 
> /**
>  * @author 王泽
>  */
> 
> public class ShuZu {
> 
>     public static void main(String[] args) {
>         int[] a;
>         a = new int[5];
>         int[] d = new int[10];
>         for (int i = 0; i < a.length; i++) {
>             a[i]=i;
>             System.out.println(a[i]);
>         }
>         System.out.println("-----------");
>         a = d;
>         System.out.println(a.length);
>         System.out.println("------------");
>         for (int i = 0; i < a.length; i++) {
>             a[i]=i;
>             System.out.println(a[i]);
>         }
>     }
> 
> }
> 
> ```

#### 二维数组

```java
int[][] a; //由若干个整形数组组成 
a = new int[3][4];  // 长度为三
```

![image-20210703094933228](JavaSE复习.assets/image-20210703094933228.png)

> 二维数组的遍历：
>
> ```java
> for(int i = 0; i<a.length ; i++){
>     for(int j=0; j<a[i].length ;j++){
>         sout(a[i][j]+"\t");
>     }
> }
> ```
>
> 二维数组的赋值：
>
> ```java
> int[][] a={{1,2,3,4},{5,6,7,8},{0,5,6,8}}
> ```

- `int[][] a =new int[3][];  //不指定二维数组长度的`

  `a[0] = new int[5];`

  `a[1] = new int[3];`

  `a[2] = new int[4];`

- `int[][] a =new int[][4]; 这句话是错误的，不能空缺高维长度`



## **面向对象**

> :o: 面向对象的思想
>
> ```markdown
> 世界是由对象组成，对象:一切客观存在的事物。
> # 1.对象的组成:
> - 属性  对象有什么
> - 方法   对象能做什么，行为
> # 2.对象之间的联系
> - is a 继承关系
> - has a 关联关系（一个对象成为另一个对象的属性）
> - use a 依赖关系（一个对象调用另一个对象的方法）
> # 3.面向对象的思想
> - 面对需求，我们先找出解决问题的对象，让对象之间建立适当的关系。
> ```
>
> :computer: 面向对象的编程思想
>
> 用计算机中的对象，模拟现实中的对象。
>
> 用计算机中类型的概念，对应现实世界中的类的概念
>
> 类：对象共性的抽象，人对一类事物的认识。 `人(类)-李沁（对象）`

### 1.定义一个Java类

> 成员变量与局部变量对比：（定义位置不同）
>
> - 成员变量有默认值 在对象创建的过程中，属性会被自动分配到默认值
> - 成员比那里的作用范围，至少是全类内部
> - 成员变量和局部变量可以同名，同名时局部变量优先访问
>
> 方法：行为(对象的功能) 函数
>
> 方法声明：
>
> ```java
> // 修饰符（多个，顺序不重要) 返回值类型 方法名（参数表）抛出的异常
> public static void main(String[] args) 
> ```
>
> 方法实现：{  }
>
> - 构造方法 特殊的方法
>
>   - 没有返回值类型
>   - 方法名必须和类名相同
>   - 不允许手工调用，在对象的构造过程中自动调用
>   - 如果不写，会自动生成无参构造
>
> - **方法的重载**
>
>   ==一个类的同一个行为，由于参数的不同所造成的实现差异，对对象的调用者屏蔽==
>
>   方法名相同，参数表不同（**只有参数名不同**，**不构成重载**）
>
>   **理解:例如我们有一个人的类，类里有吃的方法，根据方法的重载传不同的食物对应不同的吃法。**

### 2.对象的创建和使用

#### 对象的创建：

一个类就相当于自定义的数据类型，创建对象就像基础数据类型一样的声明方法，只不过要用new来创建。

通过类创建对象==new==

```java
new 类名（）;
//() ---> 构造方法
```

**==结合JVM对象的创建过程==**

1. 分配堆空间  属性 被赋值为 默认值
2. 初始化属性  属性 被赋值为 初始值
3. 调用构造方法 （通常构造方法用来给属性赋值）属性 通常被赋值为 构造参数

#### 对象的使用

```java
//类  对象名 = new 类（）；
 Girl baby = new Girl(18,"D+");  
//baby 存贮对象的地址
baby.属性；
baby.方法（）；
```

### 3.变量的JVM分析

#### 简单变量 VS 引用

看变量类型

![image-20210703145832644](JavaSE复习.assets/image-20210703145832644.png)

- 8种基本类型的变量  简单变量 存在栈中，存值
- 引用类型(对象，数组......)  存在栈中的是地址，对象存在堆中

![image-20210703150414650](JavaSE复习.assets/image-20210703150414650.png)

#### 成员变量 VS 局部变量

看定义的位置

- 局部变量：函数内部定义，存在栈空间
- 成员变量：函数外部定义，存在堆空间

**案例分析：**

![image-20210703151609712](JavaSE复习.assets/image-20210703151609712.png)

![image-20210703151746501](JavaSE复习.assets/image-20210703151746501.png)

### 4.this

特殊的引用，代表对象自身，或者当前对象

this.属性/方法  代表调用当前对象的属性/方法

- 可写可不写
- 必须写的时候：局部变量与成员变量同名的时候，并且想访问成员变量

```markdown
# 调用构造方法可以直接写this.() :调用本类其他构造方法 

**必须是写在构造方法的第一行**
```

### 5.面向对象的三大特性

#### 封装

任何对象都会有一个明确的边界，这个边界对对象内部的数据起到保护隔离的作用。

| **访问级别** | **访问控制修饰符** | **同类** | **同包** | **子类(不同包)** | **不同包(其他类)** |
| ------------ | ------------------ | -------- | -------- | ---------------- | ------------------ |
| 公共         | public             | 允许     | 允许     | 允许             | 允许               |
| 受保护       | protected          | 允许     | 允许     | 允许             | 不允许             |
| 默认         | 缺省修饰符         | 允许     | 允许     | 不允许           | 不允许             |
| 私有         | private            | 允许     | 不允许   | 不允许           | 不允许             |

属性设置为private，为属性提供公开的setXXX 和getXXX方法

#### 继承

is a 关系

`Class A extends B`

**结合jvm分析：**

![image-20210703212526050](JavaSE复习.assets/image-20210703212526050.png)

age属性如果是私有的不可以访问。

> :three: 方法的覆盖（方法的重写）
>
> 子类用自己的方法实现，替换掉父类继承给他的方法实现。
>
> 语法要求：
>
> - 方法名相同
> - 返回值类型相同
> - 参数表相同
> - 子类和父类相比，访问修饰符只能更宽或相同

#### 多态

> :one: 基础语法
>
> **子类对象可以赋值给父类引用**
>
> ```markdown
> Animal a;
> 
> - 编译时，只能确定a中存储的必然是Animal对象 或者是Animal的某个子类对象
> 
> - 运行时，a中的对象类型才会被确定
> ```
>
> :star: `引用类型 a = new 对象类型(); ` 对象类型可以是引用类型的子类
>
> **编译看左变，运行看右边（原理）：**
>
> :star: 只能调用引用类型中声明的方法
>
> :star: 通过引用找到对象，方法的结果是对象中的方法==运行时，根据对象类型调用子类覆盖之后的方法==
>
> ![image-20210704133647046](JavaSE复习.assets/image-20210704133647046.png)

> :two: 强制类型转换（Java是强类型语言）
>
> 上面的存在问题如果我想调用m3(),但是因为引用类型是A，所以我们不能调用。如何解决呢？
>
> 引用类型为B，B中还得装B或者B的子类的对象，但是A是父类。所以引出下面原则：
>
> - **子类引用可以直接赋值给父类引用**
>
>   `Animal a = new Dog();`
>
> - 父类引用赋值给子类引用的时候必须强转
>
>   `Dog d = (Dog) a;`
>
> 但是如果`Animal a = new Cat();` ，那么运行的时候会抛出类型转换错误。为什么会强转失败呢？？因为对象类型是无法改变的，我们改变的只是引用。
>
> ==总结：==
>
> **1.子类引用可以直接赋值给父类引用**
>
> **2.父类引用可以通过强制类型转换赋值给子类引用，保证编译通过，运行时，可能发生类型转换异常，对象类型不变，无法通过强转改变对象的类型**
>
> **3.没有继承关系的两个类的引用之间，不能赋值，即使强转也不行，编译不可能通过。**

> :three: 引出另一个问题​ 
>
> ```java
> Animal a = new Dog();
> Person p =(Person) a;  //为什么这句话是错的？
> ```
>
> 如果a中装的是Person 的子类对象，赋值就可能成功；
>
> 实际上a中装的一定是Animal 或Animal 子类对象
>
> 由于Java 是单继承语言，Animal 的子类一定不是Person的子类，所以肯定不能成功。**不可能有一个对象既能装a又能装p**

> :four: **instanceof 关系运算符**
>
> `用在强转之前，避免类型转换错误`
>
> `引用 instanceof 类名`  布尔表达式
>
> 判断 引用中的对象 和 类名 是否兼容
>
> ```java
> Animal a = new Dog();
> a instanceof Dog    // true a中引用的对象是不是dog？？
> a instanceof Cat    //false
> a instanceof Animal //true
> ```
>
> ==在强转之前先用instanceof判断一下==
>
> ```java
> if(a instanceof Dog){
>     Dog d = (Dog) a;
> }
> ```

> :five: 多态的典型应用
>
> 1. 把不同的子类对象统一放在父类数组中，屏蔽了不同子类的差异。
>
>    ```java
>    Animal[] a = new Animal[5];
>    a[0] = new Dog();
>    a[1] = new Cat();
>    a[2] = new Tiger();
>    a[3] = new Wolf();
>    a[4] = new Cock();-
>    for(int i =0; i < a.length ; i++){
>        a[i].eat();
>    }
>    ```
>
>    
>
> 2. 把多态用在方法的参数上
>
>    ```java
>    // m(A a) m 方法允许使用A或者A 的某个子类对象作为实参
>    public static void main(String[] args){
>        feed(new Dog());
>        feed(new Cat());
>    }
>    static void feed(Animal a){
>        a.eat();
>    }
>    ```
>
>    
>
> 3. 把多态用在方法的返回值上
>
>    ```java
>    public static void main(String[] args){
>        Animal a= getAnimal();
>    }
>    static Animal getAnimal(){
>        return new Cat();
>    }
>    ```
>
>    

### 6.super

- 引用：指向父类对象

  可以访问父类的属性(少见)调用父类被覆盖的方法(常见)

- 用在构造方法中，构造父类对象

  1）程序员主动在构造方法第一行写了super（...）

  2）程序员主动的在构造方法的第一行写了this() ，那么真正第一个执行的还是super

  3）构造方法第一行不是super也不是this 那么编译器会自动为该构造方法第一行添加super

  ====>任何一个构造方法第一行一定是super()

  **最先执行的一定是构造父类对象**

==构造子类对象，必须先构造父类对象，如果代码没有执行调用父类哪个构造方法，默认调用父类无参构造方法。==

建议给每一个类都写一个无参构造。

### 7.JVM分析对象的创建过程

1. 分配堆空间 一次性分配好
2. 创建父类对象
3. 初始化属性
4. 执行构造方法中的剩余语句

>  A ---- B ------ C （A是爷爷B是爸爸C是儿子）
>
> 创建C对象  --先分配空间
>
> 1. 初始化A类的属性
> 2. 执行A类的构造方法
> 3. 初始化B类的构造方法
> 4. 执行B类的构造方法
> 5. 初始化C类的属性
> 6. 执行C类的构造方法

### 8.单继承

一个类只能有一个直接父类

![image-20210703222134240](JavaSE复习.assets/image-20210703222134240.png)

为了保证类之间会形成简单的树状结构。

但是java可以通过其他一些方式实现多继承例如接口

### 9.static和final

==private，static，final都不能和abstract同时出现==

#### 9.1 static静态的

- 成员变量(static修饰的成员变量在内存中有独自一块，属于类)

  - 静态属性，全类公有，不属于任何对象。
  - 可以直接用 `类名.属性` 来访问

- 方法（静态方法）

  - 可以直接用类名调用
  - 静态方法只能访问类的静态成员
  - 不能出现this
  - 子类可以用静态方法覆盖父类的静态方法 没有多态

- 初始代码块

  - 初始代码块：初始化过程中会执行，先于构造方法执行
  - 静态初始代码块：在类加载的时候执行一次
  - 类加载：读取.class文件并保存到jvm中的过程，在jvm运行的过程中，一个类只会加载一次

  > :question: 什么时候会类加载？
  >
  > 第一次创建该累的对象或第一次访问类的静态成员 或 加载子类时，也可能是需要先加载父类。
  >
  > 如果仅仅是声明类的引用，不需要进行类加载
  >
  > :question: 类加载的过程？
  >
  > 1. 如有必要，先加载父类
  > 2. 为类的静态属性分配空间，分配默认值
  > 3. 按照代码的顺序初始化静态属性或是运行静态初始代码块。![image-20210704222030602](JavaSE复习.assets/image-20210704222030602.png)

#### 9.2 final

- 类   final不能被继承

- 方法  final修饰的方法不能被子类覆盖

- 变量 成员变量+ 局部变量

  final修饰变量 ===> 常量。只有一次赋值机会。

  **当final修饰成员变量的时候，系统就不会提供默认值，就必须在初始化属性或构造方法中赋值一次**

#### 9.3 修饰符总结

![image-20210704223913820](JavaSE复习.assets/image-20210704223913820.png)

### 10.抽象类

#### 10.1 抽象的abstract

- 类

  - 抽象类只能声明引用，不能创建对象
  - 供子类继承
  - 例如动物，交通工具 他们的对象都是其子类的对象

- 方法

  抽象方法没有方法体只有声明，只声明了对象具有什么功能，没有定义对象如何实现该功能。

- 抽象类和抽象方法的关系

  - 如果一个类具有抽象方法，那么这个类就必须是抽象类
  - 抽象类中可以有或没有抽象方法

- **子类必须==实现（特殊的重写）==父类中的抽象方法**（不想成为抽象类的话）

#### 10.2 依赖倒转原则(弱耦合)

继承与多态与抽象的组合延伸

![image-20210704151928083](JavaSE复习.assets/image-20210704151928083.png)

优化后：

![image-20210704152251155](JavaSE复习.assets/image-20210704152251155.png)

抽象类可以作为标准，约定了对象应该具备的方法 规格

隔离了标准的使用者 和标准的实现者 从而实现弱耦合

### 11.接口

特殊的抽象类 `interface`

- 所有的方法都是 公开抽象方法 public abstract
- 所有的属性都是公开静态的常量 Public static final
- 没有构造方法

实现接口：`implements`

接口与抽象类的区别：

- 一个类可以在继承另外一个类的同时，实现多个接口
- 接口之间可以定义多个继承关系

```java
interface IE extends IC,ID{}

class MyClass extends ClassF implements IA,IB{}
```

多继承下的多态

![image-20210704225323976](JavaSE复习.assets/image-20210704225323976.png)

#### JDK1.8+接口的新语法

利用接口实现多继承。java中的类之间是单继承。

类:一个累的主要共性 放在父类中

接口：一个类的次要共性，附加信息。

例如手机实现相机游戏机两个接口。

> :question: 由于接口中都是抽象方法，所以接口作为次要类型，却不能把方法实现继承给实现类。
>
> JDK8版本中 接口的语法
>
> 1. 接口中可以定义方法的默认实现，手动加上修饰符default
> 2. 接口中还可以定义静态方法 默认public
>
> JDK9版本中 接口还可以定义私有方法。

#### 开闭原则

一个软件要对修改关闭，对扩展开放，提高程序的可维护性。

造成可维护性低下的原因是不断修改代码，不改代码，通过更换，拓展软件组件来满足需求的变化。

例如我们可以用多态，然后覆盖原有方法。

#### 接口回调

我们要让代码变通用，我写类，被别人调用。

![image-20210705095721612](JavaSE复习.assets/image-20210705095721612.png)

案例：

```java
interface Callback{              //回调方类A的接口
    boolean Consider(int money); //思考是否付钱，就是方法c
    void PayFor(int money);      //付钱,就是方法c

}
class TaxiDriver{               //调用方类B
    public int Answer(Callback callback){      //回答多少钱，就是方法b
        System.out.println("去那的话要100块");
        if(callback.Consider(100) == true){
            callback.PayFor(100);
        }
        return 100;
    }
}
class Passenger implements Callback{  //回调方类A
    @Override
    public boolean Consider(int money) {
        boolean result = true;
        if(money > 80){
            System.out.println(money+"太贵了，您看80行不行？");
            result = false;
        }
        return result;
    }
    @Override
    public void PayFor(int money) {
       System.out.println("好的，这是您的"+money);
    }
    public void TakeTaxi(TaxiDriver td){        //询问多少钱，就是方法a
        System.out.println("师傅，去天一多少钱？");
        td.Answer(this);
    }
}
public class CallBackTest {                    
   @Test
   public void test(){
       TaxiDriver td = new TaxiDriver();
       Passenger passenger = new Passenger();
       passenger.TakeTaxi(td);
   }
}
```

### 12.内部类

在类的里面可以再定义一个类。

![image-20210705100738065](JavaSE复习.assets/image-20210705100738065.png)

还有一个匿名内部类。

**编译后会形成独立的.class文件，与外部类分别独立存在**

> :one: 成员内部类？
>
> 可以访问外部类的私有成员
>
> 用`外部类名.this.xxx` 访问外部类的属性或方法

> :two: 静态内部类？
>
> 只能访问外部类的静态成员

> :three: **局部内部类**
>
> 定义在方法内部；与局部变量有相同的作用范围。
>
> 可以访问外部类的私有成员
>
> 可以访问外部类的有效的局部变量，编译器会将该变量变为final，其值不能给变（只能访问外部类的局部常量）
>
> ![image-20210705104219733](JavaSE复习.assets/image-20210705104219733.png)

> :four: **匿名内部类**
>
> - 特殊的局部内部类
> - 必须用在继承某个类，或实现某个接口
> - 只会创建一次对象
> - 可以访问外部类的私有成员和局部变量
> - 不能写构造方法（因为没有类名）只有默认公开无参构造方法
>
> ```java
> new 接口名(){接口实现类的代码}
> 用一个匿名的局部内部类实现接口，同时创建了实现类的一个对象
>     
>   interface IA{
>     void m1();
>     void m2();
> }
> new IA{
>     public void m1(){}
>     public void m2(){}
> }
> ```
>
> 匿名内部类的优点：不会打断程序的思路。不需要写实现类再创建对象。大量的接口回调都会使用匿名内部类。
>
> 缺点：降低代码的可读性

------

# 二、**Java高级**

> :dancer: 高级篇，很重要！！面试常客！！！

## 1.Lambda表达式(语法糖)

```markdown
# 代码的参数化-----> 形式上，代码可以直接作为参数传递。
# 只能用于函数式接口
# 增加可读性
```

本质上就是**匿名内部类**的新颖写法，如果一个接口中只有一个抽象方法，这个接口称为**“函数式接口”**。

**:one:.语法：**

```markdown
# (参数表) ->{方法实现}   //利用匿名颞部类创建接口的实现类对象
- 1.参数表中，参数类型也可以省略
- 2.参数表中，如果只有一个参数，圆括号也可以省略
- 3.如果方法的实现只有一条语句(无返回值)，‘{}’也可以省略
- 4.如果方法实现只有一条语句，且该方法有返回值，且只有return语句，那么return和{}都可以省略。
```

例如：

```java
Test 是一个interface，里面仅有一个方法test(Student s);

/*1.匿名内部类初始写法*/
Test t1 = new Test(){
    public boolean test(Student s){
        return s.getAge()==18;
    }
}

/*2.简化写法*/
Test t2 = (Student s)->{return s.getAge()==18;};

/*3.简化参数列表*/
Test t3 = s->{return s.getAge()==18;};

/*3.简化方法体*/
Test t4 = s->s.getAge()==18;

/*4.在参数中使用*/
/*4.1 以前内部类的使用方法*/
stu = find(ss,new Test(){
   			 public boolean test(Student s){
                    return s.getAge()==18;
             			}
             });

/*4.2 lambda 表达式参数使用*/
 stu = find(ss, s->s.getAge()==18);
```



## 2.Object类和常用类

> :dancer: 每一个开发者都在改变世界。

### object类

java中所有类的父亲 如果一个类没有指定父类，默认继承Object类。

> 根据多态，如果我们创建一个Object类型的引用，那么可以代表任何对象！
>
> :train2:**Object o = 任何对象** 不包括8中基本类型
>
> :train: Object类中定义的方法是所有Java对象都具有的方法（public 或 protected）【11个方法】

下面介绍Object中的方法：

| 方法名       | 作用                       | 用法                                                         |
| ------------ | -------------------------- | ------------------------------------------------------------ |
| getClass（） | 获得对象的**实际类型**     | o1.getClass()==o2.getClass()判断o1和o2类型是否相同           |
| finalize()   | 对象垃圾回收时调用         | 对象的全生命周期里最后一个方法就是finalize()。编码中没啥用:joy: |
| toString()   | 返回对象的字符串形式       | 通常重写此方法，打印o就是打印o.toString()                    |
| equals()     | 判断两个对象的内容是否相同 | 首先重写equals方法，equals(e1.equals(e2));                   |
| hashCode()   | 返回对象的哈希码           |                                                              |

注释：

- getClass 与 instanceof 的区别：instanceof判断对象是不是某一个类型。例如 `a instanceof Animal` 不知道是狗还是猫。

- 垃圾回收：Java中有垃圾自动收集的机制，由JVM中的垃圾收集器自动回收垃圾对象，释放对应的堆空间.

  > :question: 什么对象会被认为是垃圾对象？？
  >
  > 答：零引用算法，如果一个对象没有任何引用指向它，则这个对象就是垃圾对象。
  >
  > :question: 垃圾对象何时被回收？
  >
  > 答：在不得不回收，内存耗尽，无法创建新对象时候，才会一次性回收之前被认定为垃圾对象的对象。

- System.gc(); 唤醒垃圾收集器，有延迟回收策略，sun公司JDK可能不会启动。就像是你妈叫你去洗碗，你可以不去，也可以去，仅仅是告诉你。

- toString 返回对象的字符串形式，通常重写toString()

- equals() 与 `==` 的区别就是，`==`比的是对象的地址,而equals() 才是比较的真实的对象。

  > :astonished: 引用之间用==判断的是地址是否相同(是否指向同一个对象)
  >
  > :earth_asia: 用equals() 判断得是对象内容是否相同
  >
  > **Object类中的equals方法，依然比较地址，如果我们想让其比较对象内容，我们需要重写**
  >
  > ```java
  > //this 和 o对象比较内容
  > public boolean equals(Object o){
  >     //判断this和o是不是同一个对象
  >     if(this == o) return true;
  >     //判断 o是不是 null
  >     if(o == null)  return false;
  >     //判断this和o是不是同一个类
  >     if(this.getClass() != o.getClass()) return false;
  >     //对o做强制类型转换，变为this类型
  >     Employee e = (Employee)o;
  >     //逐个比较属性，基本数据类型用==比较，对象类型用equals比
  >     if(this.age == e.age && this.name.equals(e.name)) return true;
  >     else return false;
  >     //注意这个不是递归，两个equals不是一个，是字符串中的equals
  > }
  > ```
  >
  > 



### 包装类

Object o = 任何对象，但是基本数据类型不属于对象的范围，所以Object o = 10 是错误的！所以我们需要将基本类型转换。

为8中基本数据类型 各自提供一个类。这时候Object o = 任何数据了。

| 基本类型 | 包装类    |
| -------- | --------- |
| byte     | Byte      |
| short    | Short     |
| int      | Integer   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |
| boolean  | Boolean   |

> :one: 基本类型和包装类之间的转换
>
> - int——>Integer
>
>   ```java
>   /* 不一定要用构造方法获得对象，可以用静态方法，用构造方法会造成内存的浪费。而且Integer类提供的静态方法还能直接返回Integer的；*/
>   
>   Integer a = Integer.valueOf(i);
>   Integer b = Integer.valueOf(i);
>   // a==b
>   ```
>
>   Integer.valueOf(i); 获得一个Integer对象
>
>   Integer类内部会预先创建-128 ~ 127 256个对象，如果在这个区间内，不会产生新对象。
>
> - Integer——>int
>
>   > a.intValue();  //拆箱
>
> - 从jdk5开始，自动装箱拆箱，由编译器自动处理基本类型和包装类型的转换。
>
> :two: 基本类型和String之间的转换
>
> - int——> String
>
>   > int i =12;
>   >
>   > String s=i+"";  或者 String s=String.valueOf(i);
>
> - String——>int(重点)
>
>   > String s ="1234"; 
>   >
>   > int i = Integer.parseInt(s);
>
> :three: 包装类和String之间的转换
>
> - Integer——>String
>
>   > Integer a = 1234;
>   >
>   > String s = a.toString();
>
> - String——>Integer
>
>   > String s ="1234";
>   >
>   > Integer a = Integer.valueOf(s);

案例：统计成绩，为了区分0分与缺考。

```java
class Student{
    Integer score =null；
    /*对象类型默认是null，如果不给赋值成绩那就是null，那就是缺考。如果是0分那么就是0；*/
}
```



### 日期处理

util包里有一个日期类`Class Calendar `



## 3.字符串处理String

凡是没有数值含义的属性都不要做成数值，例如手机号。

`public final class String`我们不能更改，只能用。本质上一个字符串就是一个字符数组的封装。

### 字符串中的常用方法

> :one: 与字符数组相关的方法
>
> - String(char[] cs) 利用字符数组cs来构造String
>
> - toCharArray()把字符串转正字符数组
>
>   ```java
>   把字符串全部变为大写：因为 大写+32=小写
>       String str="HelloWorld";
>      char[] cs = str.toCharArray();
>   	for(int i =0; i<cs.length; i++){
>           if(cs[i] >= 'a' && cs[i] <='z'){
>               cs[i] -= 32;}
>           String str2 = new String(cs);
>           System.out.println(str2);
>       }
>   ```
>
> :two:基础的方法
>
> - toUpperCase();  //转大写
> - toLowerCase(); //转小写
> - charAt(int index); //获得字符串下标
> - length()  //获得字符串的长度
> - trim()  //去掉字符串前后的换行符和空格符
> - equals() //比较两个字符串中的内容
> - equalsIgnoreCase(String str) //判断当前字符串和str内容是否相同，忽略大小写。例如验证码验证的时候。
>
> :three: 与子串相关
>
> - starsWith(String str) / endsWith(String str)  //判断当前字符串是否以str这个子串开头 /结尾
> - contains(String str) //判断当前字符串是否包含子串str  `s.contains("BC");s中有没有BC子串`
> - indexOf(String str) 返回str子串在当前字符串中第一次出现的下标,不存在返回-1
> - substring(int fromIndex) 找出当前字符串从fromIndex开始，到结尾的子串
> - substring(int fromIndex,int endIndex) 返回从from到end的子串
> - replace(String str1,String str2) 将字符串中str1替换为str2
> - split(String str) 以str为分隔符，将字符串切割为String[]

### String常量池

> 把相同内容的字符串只保留一份。
>
> :yin_yang: 引入字符串常量池：
>
> **一个String对象的内容是不可变的**，只有不可变才可以让多个引用指向同一个对象。如果更改就创建新的对象，引用指向新的。

**串池**：字符串常量池，用来存放字符串对象，以供多个引用共享（通常存储在方法区）

创建一个String对象，先去字符串常量池里找，有的话直接引用赋值，没有的话在串池创建。

![image-20210705185347581](JavaSE复习.assets/image-20210705185347581.png)

如果有==new== 那么一定是在堆空间中分配内存！

![image-20210705185810754](JavaSE复习.assets/image-20210705185810754.png)

==intern() 返回字符串在串池中的对应字符串地址==

我们如果用数组的方式创建了String,肯定用到了new String() 这时候肯定在堆空间中，我们可以用intern()来找串池对象地址，然后把引用赋值。`s4 = s4.intern();`

```java
String s3 =new String(cs).intern();  
```

### StringBuilder

> 当字符串的内容改变时，只会创建新的对象。
>
> ![image-20210705192414243](JavaSE复习.assets/image-20210705192414243.png)
>
> 显然，费时且占地儿。

**用StringBuilder完成字符串的拼接，内容变化时，不会产生新的对象**

![image-20210705194823121](JavaSE复习.assets/image-20210705194823121.png)

```java
/*案例:统计两种方式的运行时间比较*/
static long stringAppend(){
    long t1 = System.nanoTime();
    String s= "";
    for(int i =1; i<= 100000; i++){
        s +="A";
    }
    long t2 =System.nanoTime();
    return t2-t1;
}

/*StringBuilder拼接*/
static long stringBuilderAppend(){
     long t1 = System.nanoTime();
    String s= "";
    StringBuilder sb = new StringBuilder(s);
    for(int i =1; i<=100000; i++){
        sb.append("A");
    }
    s = sb.toString();
     long t2 =System.nanoTime();
    return t2-t1;
}
```

草，这个结果太慢了，创建对象，垃圾回收太慢了（主要是垃圾回收）：

![image-20210705200901675](JavaSE复习.assets/image-20210705200901675.png)

明显StringBuilder比加的方式快太多了。

## 4.集合(容器)

> 集合：用来存储对象的对象(容器) 。在生活中例如书架...
>
> Object[]  最最最基础的集合。但是数组有局限性。
>
> 1.数组长度是固定的，数组扩充时需要复杂的拷贝操作。
>
> 2.数组在元素的插入和删除时使用不方便

集合：对基础数据结构的封装，由Java类库提供。

### 集合的组成

![image-20210706111608998](JavaSE复习.assets/image-20210706111608998.png)

#### :one:Collection

- 接口特点：元素是对象（Object）

- 常用方法

  | 方法                 | 说明                            |
  | -------------------- | ------------------------------- |
  | add(Object o)        | 把对象o放入当前集合             |
  | addAll(Collection c) | 把集合c中所有的元素放入当前集合 |
  | clear()              | 清空集合                        |
  | contains(Object o)   | 判断集合中是否存在元素o         |
  | remove(Object o)     | 在当前集合中删除元素o           |
  | size()               | 获取集合的长度                  |
  | toArray()            | 将当前集合转换为Object[]        |
  | forEach()            | 遍历当前集合                    |

- 遍历

  > 1. 迭代遍历（陈旧了）
  >
  >    ```java
  >    /**
  >    *Iterator 迭代器 
  >    * hasNext() 看集合中还有没有下一个元素
  >    *next() 从迭代器中拿出下一个没被遍历的元素
  >    */
  >    Iterator it =list.Iterator();
  >    while(it.hasNext()){
  >        Object o = it.next();
  >        String s = (String)o;
  >        System.out.println(s.toUpperCase());
  >    }
  >    ```
  >
  >    
  >
  > 2. for-each jdk1.5
  >
  >    ```java
  >    /**
  >    *把list中的每个元素放在o里
  >    */
  >    for(Object o : list){
  >        String s=(String)o;
  >        System.out.println(s.toUpperCase());
  >    }
  >    ```
  >
  > 3. 自遍历 jdk1.8
  >
  >    ```java
  >    class MyConsumer implements Consumer{
  >        public void accept(Object o){
  >            System.out.println(o)
  >        }
  >    }
  >    list.forEach(new MyConsumer()); //通用的逻辑
  >                
  >    //=====>终极版
  >                
  >    list.forEach(o ->System.out.println(o));
  >    //list.forEach(System.out::println); 方法引用
  >    ```
  >
  >    

- 实现类

  没有直接实现类，但是它的子接口有实现类

  

#### :two:List  

  Collection的子接口

- 接口特点：元素是有顺序，有下标的，元素是可以重复的。

- 常用方法

  | 方法                   | 说明                                        |
  | ---------------------- | ------------------------------------------- |
  | add(int pos,Object o)  | 把元素o插入到当前集合的pos下标上            |
  | get(int pos)           | 获得集合中pos下标的元素                     |
  | indexOf(Object o)      | 获得元素o在集合中的下标，如果不存在，返回-1 |
  | remove(int pos)        | 删除集合中pos下标的元素                     |
  | set(int pos,Object o ) | 把元素o设置到当前集合的pos下标上            |

- 遍历

  > 1. 下标遍历
  >
  >    ```java
  >    for(int i = 0;i<list.size();i++){
  >        Object o =list.get(i);
  >        String s =(String) o;
  >        System.out.println(s.toUpperCase);
  >    }
  >    ```
  >
  > 2. 迭代遍历
  >
  > 3. for-each 
  >
  > 4. forEach();

- 实现类

  > 1.ArrayList 底层用数组实现  查询快 ，增删慢
  >
  > 2.LinkedList  用链表实现     查询慢，增删快
  >
  > 3.Vector      数组实现 1.0古老版本（舍弃）
  >
  > 与ArrayList的区别：Vector线程安全（不安全好），并发效率低
  >
  > 例如高速公路一直发生事故，那就限制路上只能有一个车。这就是Vector线程安全的样子。



#### :three:Set  

  Collection的子接口

- 接口特点：元素是无顺序，无下标的，元素的内容不可重复

- 常用方法：无特有，Collection中的方法

- 遍历：

  > 1. 迭代遍历
  > 2. for-each遍历
  > 3. forEach();

- 实现类

  存储原理：不同的实现类，有不同的方法保证元素内容不同

  > 1. HashSet(散列表)查询也快，增删也快，占据空间稍大。为了最好的利用，我们要让其均匀分配。尽量保证不同对象返回不同整数。
  >
  >    底层**链表数组**，不按下标顺序存放，是根据hashCode() 与长度取余，放到相应的地方。
  >
  >    ![image-20210706092642113](JavaSE复习.assets/image-20210706092642113.png)
  >
  >    假如遇到相同的怎么办？？？
  >
  >    ![image-20210706092726426](JavaSE复习.assets/image-20210706092726426.png)
  >
  >    会先对s3和s4进行内容的比对，如果equals为true，就拒绝加入。如果内容不相同，s4也会放在2下标，并且s3和s4形成**链表**。
  >
  >    ![image-20210706092954612](JavaSE复习.assets/image-20210706092954612.png)
  >
  >    现在问题来了：哈希码不同，即使内容相同也会放入set集合里。这就不能构成不重复。
  >
  >    解决办法就是我们重写hashCode(),让内容相同的哈希码也相同。
  >
  >    ```java
  >    public int hashCode(){
  >        return age; //例如用年龄作为哈希码，那么两个人就会比较
  >        return name.hashCode()+age;
  >    }
  >    ```
  >
  >    **如果将自定义的对象放入HashSet,为了保证内容不重复，我们需要==覆盖equals对象==，保证内容相同的对象返回true；还要==覆盖hashCode方法==，保证内容相同的对象返回相同的整数，尽量保证不同对象返回不同整数。**
  >
  > 2. LinkedHashSet HashSet的子类，元素遍历的时候可以按顺序遍历
  >
  > 3. TreeSet 自动对元素进行排序，根据排序规则过滤重复元素(很少用)

#### :four:Map

- 接口特点

  > 元素是**键值对** 键对象：key    值对象：value
  >
  > key：无顺序，内容不可重复
  >
  > value：可重复

- 常用方法

  | 方法                   | 说明                                                         |
  | ---------------------- | ------------------------------------------------------------ |
  | put(k key,V value)     | 把key-value键值对放入Map,如果key已经存在，新的value覆盖原有的value |
  | V get( k key)          | 查找key所对应的value                                         |
  | containsKey(K key)     | 判断Map中是否存在key这个键                                   |
  | containsValue(V value) | 判断Map是否存在value这个值                                   |
  | size()                 | 返回Map的长度，键值对的个数                                  |
  | remove(K key)          | 删除key 所对应的键值对                                       |
  | keySet()               | 返回Map中所有key的集合 就是一个Set                           |
  | values()               | 返回map中所有值得集合 就是一个Collection                     |

- 遍历

  > 1. keySet()
  >
  >    ```java
  >    Set<Integer> ks = map.keySet();
  >    for(Integer key ：ks){
  >        String calue = map.get(key);
  >        System.out.println(key +":"+value);
  >    }
  >    ```
  >    
  > 2. values() 遍历所有的值组成的集合
  >
  >    ```java
  >    Collection<String> vs = map.values();
  >    for(String value :vs){
  >        sout(value);
  >    }
  >    ```
  >
  > 3. 自遍历
  >
  >    ```java
  >    map.forEach( (k,v)->System.out.println(K+"---"+v) );
  >    ```
  >
  >    
  
- 实现类

  > 1. HashMap 
  >
  >    与hashSet类似，就是hashMap有值。 链表数组，高版本中修改了，用的红黑树。在原始基础上做了优化。
  >
  > 2. linkedHashMap HashMap的子类，维护键值对的添加顺序
  >
  > 3. TreeMap 自动对key做排序
  >
  > 4. Hashtable 1.0线程安全 慢 不允许用null作为key 或value
  >
  > 5. Properties Hashtable的子类 key和value都是string，通常用语早起的配置文件处理

总结图：

![image-20210706111743944](JavaSE复习.assets/image-20210706111743944.png)

蓝线表示学习的时候对比记忆。

### 泛型

> 集合存储是Object，无法对元素的类型做出约束，导致类型不安全。

利用泛型，可以预定集合中元素的类型。

```java
interface A <T,V>{
    void m1();
}
/*使用的时候*/
class B implements A<Double , String>{
    /*谁使用，谁指定*/
}
--------------------------------------------------------------
    集合中使用泛型：
interface LIst<E>{
    void add(E o);
    E get(int pos);
}
List<String> ls=new ArrayList<String>();
ls.add("abc");
String s=ls.get(0);
```

> LIst<Type1> ls = new ArrayList<Type2>();
>
> 必须：Type1==Typ2
>
> List<Animal> ls = new ArrayList<Animal>();
>
> 既然前后必须一致，那么后面可以省略；
>
> LIst<Type1> ls = new ArrayList<>();    Jdk7

### 线程安全的集合

ArrayList 所有方法都不是同步方法。

Vector 所有方法都是同步方法。

:one: **ConcurrentHashMap**​ 

> 如何既保证线程安全，又保证并发效率很高？
>
> JDK7 以前，采用分段锁。控制锁的力度，把Map分为16个片段，针对每个片段加锁。
>
> (举例：map是一个大厕所，有很多小隔间，一个人进去直接把大门锁了，显然影响效率，而ConcurrentHashMap就相当于锁小隔间的门，只会锁其中的一个片段。)
>
> JDK8以后，采用**CAS算法**(比较交换)，**无锁算法**，存储效率非常接近于HashMap.

:two: CopyOnWriteArrayList *永远不会改变原始集合中的数据*

适用于读操作。并发效率接近于ArrayList,线程安全

> - 读操作：获取集合的数据(不需要加锁)
> - 写操作：改变集合的数据( 复制新的集合来实现写，效率低 )

:three:CopyOnWriteArraySet 原理和CopyOnWriteArrayList一致

### 队列Queue

队尾进，对头出。

:one:LinkedList 同时也实现了队列接口

```java
Queue<String> queue = new LinkedList<>();
```

线程不安全的类。

:two: **ConcurrentLinkedQueue**

线程安全的  CAS无锁算法

> BlockingQueue 阻塞队列，队列的子接口：当队列为空的时候，从队列中取元素的线程会阻塞，而当队列满的时候，向队列中添加元素的线程会阻塞
>
> - 有界队列：ArrayBlockingQueue有上限，存在满的问题，当队列满的时候，添加元素线程会阻塞
> - 无界队列：LinkedBlockingQueue

集合总结：

![image-20210707164541784](JavaSE复习.assets/image-20210707164541784.png)

![image-20210707164807515](JavaSE复习.assets/image-20210707164807515.png)



## 5.异常

提高程序的容错性:解决程序运行时的问题

### 异常的分类

![image-20210706121333586](JavaSE复习.assets/image-20210706121333586.png)

避免异常：尽可能使异常不出现，不发生

处理异常：当异常发生时，应对这种异常

**Throwable** 所有异常的父类

​	|- error   错误  严重的底层错误，不可避免 无法处理

​	|- Exception 异常  可以处理

​		|- RuntimeException 子类 运行时异常 未检查异常 可以**避免**（空指针、数组下标越界、类型转换异常） 可以处理可以不处理

​		|-非 RuntimeException子类 已检查异常  **不可避免**，需要处理异常

### 异常对象的产生和传递

**异常对象的产生：** 

>  **throw 异常对象**：抛出一个异常
>
> 抛出异常：方法以异常对象作为返回值，返回等同于return
>
> *后面的语句就不打印了*

**异常对象的传递**

> 沿着方法调用链，沿着方法的调用链，逐层返回，直到JVM
>
> ![image-20210706124413399](JavaSE复习.assets/image-20210706124413399.png)
>
> 这是中止运行，jvm会显示异常名，异常内容和异常路径

自定义异常：

如果想写已检查异常需要`extends Exception`

如果想写未检查异常需要`extends RuntimeException `

### 异常的处理

#### 声明抛出

> public void method() throws IOException
>
> throws 异常类名1，异常类名2，...异常类名n
>
> 意思：本方法中出现IO异常，我不做任何处理，抛给上层调用处理！

方法覆盖时，子类不能比父类抛出种类更多的异常

#### 捕获异常

> ```java
> try{
>     语句1
>     语句2
>         ...
>         //遇到异常 去catch处理
> }
> catch(异常类型 e){
>     
> }
> catch(异常类型 e){
>     e.printStackTrace();//打印异常对象的栈追踪信息
> }
> finally{
>     这里面的无论有没有异常都运行
> }
> ......
> ```
>
> 可以同时捕获父类异常和子类异常，但是必须先捕获子类异常，再捕获父类异常。
>
> Java中，有三种try-catch结构：
>
> ```java
> try{} catch(){}
> 
> try{} catch(){} finally{}
> 
> try{} finally{}  //无法捕获异常，利用finally必须执行的特点完成特定操作
> ```
>
> 

## 6.B IO

Java程序的输入和输出

### IO流的分类

:one: 流的概念：用来传递东西的对象

![image-20210706141329138](JavaSE复习.assets/image-20210706141329138.png)

:two: 流的分类：

- 流的方向：输入流(读取数据)/输出流(写数据)

  ![image-20210706141549607](JavaSE复习.assets/image-20210706141549607.png)

- 数据单位: 字节流(以字节为单位，可以处理一切数据)

  字符流(字符为单位 = 2字节 ，用来处理文本数据)

  ![image-20210706142032601](JavaSE复习.assets/image-20210706142032601.png)

- 按照流的功能：

  节点流：实际负责数据的传输

  过滤流：为节点流增强功能

  ![image-20210706142720112](JavaSE复习.assets/image-20210706142720112.png)



### IO编程的基本顺序：

1. 创建节点流
2. 构造过滤流
3. 使用过滤流
4. 关闭外层流

### 字节流

InputStream :字节输入流     抽象类，字节流的父类

OutputStream: 字节输出流	抽象类，字节流的父类

子类：文件字节流

- FileInputStream/FileOutputStream

#### 写文件

```java
/*输出流*/
public class IOStream {
    public static void main(String[] args) {
        try {
            OutputStream outputStream = new FileOutputStream("a.txt");
            outputStream.write('A'); //向a.txt中写一个A
            outputStream.close(); //关闭流（这个不该写在这里！后面有讲）
        } catch (FileNotFoundException e) {
            System.out.println("文件没找到");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

![image-20210706145955581](JavaSE复习.assets/image-20210706145955581.png)



`OutputStream outputStream = new FileOutputStream("a.txt",true);`追加的方式来打开流

![image-20210706150305512](JavaSE复习.assets/image-20210706150305512.png)

如果wirte('王')；汉字已经超过了字节的范围



#### 读文件

先不考虑异常的情况下读文件：

```java
public class DuFile {
    public static void main(String[] args) throws IOException {
        InputStream inputStream = new FileInputStream("a.txt");
        while(true){
            int a = inputStream.read();
            if(a == -1){
                break;
            }
            System.out.println((char) a);
        }
        inputStream.close();
    }

}
```

![image-20210706154050866](JavaSE复习.assets/image-20210706154050866.png)



#### IO流处理异常的方法

常规处理：

```java
public class IOStream {
    public static void main(String[] args) {
        OutputStream outputStream = null;
        try {
            outputStream = new FileOutputStream("a.txt",true);
            outputStream.write('D'); //向a.txt中写一个A
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (outputStream != null){
                try {
                    outputStream.close(); //关闭流
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
代码缺点：太多，麻烦 jdk1.7 提供了 try-with-resource

```

 try-with-resource：带资源的都需要关闭，一定在finally中。

```java
try(定义资源 必须是实现类 AutoCloseable接口的对象){ 代码}
catch(Exception e){}   
//定义在try里的资源会自动关闭

```

**写文件异常处理写法：**

```java
public class IOStream {
    public static void main(String[] args) {
//        OutputStream outputStream = null;
//        try {
//            outputStream = new FileOutputStream("a.txt",true);
//            outputStream.write('D'); //向a.txt中写一个A
//        } catch (IOException e) {
//            e.printStackTrace();
//        } finally {
//            if (outputStream != null){
//                try {
//                    outputStream.close(); //关闭流
//                } catch (IOException e) {
//                    e.printStackTrace();
//                }
//            }
//        }

        try(OutputStream outputStream = new FileOutputStream("a.txt",true)){
            outputStream.write('A');
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```



**读文件异常处理**

```java
public class DuFile {
    public static void main(String[] args) {
        try (InputStream inputStream = new FileInputStream("a.txt")) {
            while (true) {
                int a = inputStream.read();
                if (a == -1) {
                    break;
                }
                System.out.println((char) a);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 文件拷贝

```java
public class FileCopy {
    public static void main(String[] args) {
        FileOutputStream fos = null;
        FileInputStream fis = null;
        //要复制的文件
        String filename = "a.txt";
        try{
            fis = new FileInputStream(filename);
            fos = new FileOutputStream("new"+filename);
            while(true){
                int a = fis.read();
                if (a == -1){break;}
                fos.write(a);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        finally {
            if( fis !=null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if( fos !=null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }


}
```

**IO效率最重要!,但是上面的代码执行效率很低，接下来就是提升性能的方法——加一个过滤流**

### 缓冲字节流

![image-20210706162040586](JavaSE复习.assets/image-20210706162040586.png)



**过滤流**：只能给节点流增加功能

PrintStream可以取代BufferedOutputStream带缓冲的字节输出流，这个就是sout使用的。

> System.out.println
>
> - System：类名 java.long.System
> - System.out：System类中公开静态常量，是PrintStream类的对象
> - println：方法名

**BufferedInputStream/BufferedOutputStream** 缓冲功能，提高I/O效率

> 一个流中的构造参数是另外一个流，这个流就是一个过滤流

flush() //关流或者清空缓冲区都可以让缓冲区的该去哪去哪  对于缓冲输出流

**改造后的文件拷贝：**

```java
public class FileCopy {
    public static void main(String[] args) {
        FileOutputStream fos = null; //节点流
        FileInputStream fis = null; //节点流
        BufferedOutputStream out = null; //输出缓冲流，这是过滤流
        BufferedInputStream in = null; //输入缓冲流

        //要复制的文件
        String filename = "a.txt";
        try{
            fis = new FileInputStream(filename);
            in = new BufferedInputStream(fis); //加强节点流的功能，带缓冲
            fos = new FileOutputStream("new"+filename);
            out = new BufferedOutputStream(fos);//带缓冲的输出流
            while(true){
                int a = in.read(); //用换出流读数据，存到缓冲区
                if (a == -1){break;}
                out.write(a); //缓冲输出流王外写
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        //关流的时候只需要关最外层的流，例如过滤流包裹节点流，关过滤流就行
        finally {
            if( in !=null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if( out !=null) {
                try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}
```



### 对象序列化

我们想把对象写入文件要怎么写呢？

把对象通过流来传输叫做**对象的序列化**。

> 有些对象可以序列化，有些不能。生活中的例子，例如我搬家的时候，有的可以拆成小的然后搬，有的却不行。

**只有实现了Serializable的接口才能序列化。**就是个标记，告诉IO可以切（序列化）

**可以给对象加transient修饰属性，称为临时属性不参与序列化**

我们有相应的过滤流：ObjectOutputStream/ObjectInputStream

有out.wirteObject();方法是写对象的。

readObject(); 读对象。

![image-20210706171514084](JavaSE复习.assets/image-20210706171514084.png)



### 对象的克隆

就是在java堆空间中复制一个新对象；

最简单的办法就是用Object中的clone(); 但是要重写，修改修饰符。而且对象必须是一个支持克隆的对象实现Cloneable接口。

注意：他的克隆方式：浅拷贝

![image-20210706174159566](JavaSE复习.assets/image-20210706174159566.png)

teacher指向的同一个对象。

浅拷贝：对象复制，但是对象内的属性不会复制

深拷贝：对象复制，里面的属性也复制，就会有两个teacher对象。

> 如何实现深拷贝？对象序列化。这样的地址完全不同



### 字符流

专门处理char，String数据，而且可以方便的处理字符的编解码

‘A’ ----> 65  ：编码 `str.getBytes（"GBK"）`

65 ----> ‘A’  ：解码 `String(  ,"GBK")`

不同国家有不同字符集，当编码格式与解码格式不同就会出现乱码现象。

> - ASCII 	美国 字符集：英文字符
> - ISO-8859-1 西欧字符
> - GB2312 GBK简体中文 
> - Big5 繁体中文
> - Unicode 全球统一的编码方式
>   - UTF-16（java采用的编码）
>   - UTF-8 （常用的）

#### Reader/writer

抽象类 字符流的父类

子类：**FileReader/FileWriter** 文件字节流，节点流

**BufferedReader/BufferdWriter** 过滤流，缓冲作用

**PrintWriter**带缓冲的字符输出流，通常可以取代BufferdWriter

![image-20210706184451594](JavaSE复习.assets/image-20210706184451594.png)

![image-20210706184506426](JavaSE复习.assets/image-20210706184506426.png)

读：

![image-20210706184728031](JavaSE复习.assets/image-20210706184728031.png)

**技巧：先创建字节流然后用InputStreamReader/OutputStreamWriter过滤流把字节流构造成字符流 （桥转换类）**

==可以在桥转换的过程中指定编解码的方式==

```java
OutputStream fos = new FileOutputStream("a.txt");
Wrirer fw = new OutputStreamWriter(fos,"GBK");
```



### File类

file对象代表了磁盘上的一个文件 或 目录(文件夹)，用来实现一些文件操作。

```java
public class FileTest {
    public static void main(String[] args) throws Exception {
        File file = new File("1.txt");
        file.createNewFile();//创建1.txt文件
        file.delete();//删除1.txt //空目录
        file.mkdirs(); //创建目录
        file.delete(); //删除目录
        System.out.println(file.exists());//file.exists();判断是否存在
        file.getAbsolutePath();//获得绝对路径
        file.lastModified();//获取最后修改时间
        file.length();//获取长度
        File f = new File("D:\\");
        //file.listFiles();//返回文件数组
    }

}
```

无法访问文件内容。

## 7.多线程(并发编程)

> 注重思想，我们写一个页面，如果大量用户访问，那么有上亿个线程，是否会出现数据出错，卡顿等。

并发编程：多个任务同时执行

进程:OS中并发的一个任务

线程：在一个进程中，并发的顺序执行流程。

并发原理：CPU分时间片交替执行，宏观并行，微观串行，由OS负责调度。如今的CPU已经发展到了多核CPU，真正存在并行。

线程的三要素：

- CPU  OS负责调度
- 数据  堆空间，栈空间（多线程堆空间共享，栈空间独立）
- 代码  (主函数就是主线程)

### 如何创建线程？

#### 方法1(初始)：

1. 实现Runnable接口，实现run方法

2. 创建Runnable对象（任务对象）

3. 通过Runnable对象，创建Thread对象(线程对象)、

   现在只是有线程对象：

   ![image-20210706195053998](JavaSE复习.assets/image-20210706195053998.png)

4. 调用线程对象start()启动线程(向操作系统申请创建线程)

   ![image-20210706195216125](JavaSE复习.assets/image-20210706195216125.png)

   现在的代码中就有三个线程了，main，t1，t2

#### 方法2(老式)：

1. 直接继承Thread类，覆盖run方法
2. 创建继承的Thread对象
3. 调用start()方法

### 线程的基本状态

:one:程序最开始的时候主线程处于运行状态

![image-20210706200657211](JavaSE复习.assets/image-20210706200657211.png)

:two: 主线程执行创建对象，t1，t2处于初始状态

![image-20210706200833488](JavaSE复习.assets/image-20210706200833488.png)

:three: 调用start()

![image-20210706200915281](JavaSE复习.assets/image-20210706200915281.png)

:four: main结束，OS调度，运行run里的代码，选谁呢？？

> 选择的规则：不同OS不同任务调度策略。但是因为start()耗时，所以经常看见t1先执行，是因为这时候t1还没准备好

![image-20210706202317962](JavaSE复习.assets/image-20210706202317962.png)

:five: t1时间片到期，t1回到可运行，OS选择下一个线程(不一定选谁)可能还是t1。

:six: 线程结束

![image-20210706202540142](JavaSE复习.assets/image-20210706202540142.png)

### 线程的等待

等待状态：线程执行过程中发现执行不下去了，缺少条件，没有条件之前进入等待状态。(例如等红绿灯)

- 等待数据的输入 scanner/B io 不确定时间

  ![image-20210706203552344](JavaSE复习.assets/image-20210706203552344.png)

  

- sleep() 限时等待，给休眠时间

  ![image-20210706203816165](JavaSE复习.assets/image-20210706203816165.png)

线程中的sleep()异常只能用try-catch解决，因为方法覆盖不能抛出比父类多的异常。

还有一个join() 线程同步中讲解。

### 线程池 JDK5

> 创建一个线程只为一个任务所服务会造成资源浪费。（频繁创建销毁线程就是资源浪费）

**线程池的概念**：(就像高铁站出租车区域，来客人就去拉客，送到后还回来继续等下一个任务，或者办公室的例子)**当一个任务结束时，对应的线程资源不会销毁，而是回到线程池中等待下一个任务。**

**线程池的好处：**可以控制最高并发数

**有了线程池之后，Thread类就不推荐使用了！**

#### 线程池的编码：

**一个ExecutorService对象就代表一个线程池**

![image-20210706212145554](JavaSE复习.assets/image-20210706212145554.png)

`newCachedThreadPool();长度不固定的，有几个任务就有几个线程 `

#### 用匿名内部类简化编码

```java
 executorService.submit(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i <= 100; i++) {
                    System.out.println("CCC"+i);
                }
            }
        });
```

**用lambda表达式简化**

```java
executorService.submit(()->{
            for (int i = 0; i <= 100; i++) {
                System.out.println("CCC"+i);
            }
        });
```

### Callable和Future接口

用来取代Runnable接口、

Runnable的缺点：

1. run没有返回值
2. 不能抛异常

Callable接口允许线程有返回值，也允许线程抛出异常

Future接口用来接受返回值

案例：计算1~10000的和，用两个线程，一个算基数，一个算偶数，最后相加。

主要问题：如何获得返回值

![image-20210706215722854](JavaSE复习.assets/image-20210706215722854.png)

返回值存在Future对象中

```java
package com.gaoji;

import java.util.concurrent.*;

import static java.util.concurrent.Executors.*;

/**
 * @author 王泽
 */

public class AddTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService es = newFixedThreadPool(2);
//        es.submit(new task1());
//        es.submit(new task2());
        //如何拿到两个任务的返回值呢？我们只是扔给了线程池
        Future<Integer> f1 = es.submit(new task1());
        Future<Integer> f2 = es.submit(new task1());

        //...主线程执行自己的任务
        //获取返回值,能取到就取，取不到就等待。
        Integer result1 = f1.get();//从future中取到返回值
        Integer result2 = f2.get();//从future中取到返回值
        System.out.println(result1+result2);
        es.shutdown();
    }


}
/**所有基数的和*/
class task1 implements Callable<Integer>{
    @Override
    public Integer call(){
        int result = 0;
        for (int i = 1; i < 10000; i+=2) {
            result +=i;
        }
        return result;
    }
}
/**所有偶数的和*/
class task2 implements Callable<Integer>{
    @Override
    public Integer call(){
        int result = 0;
        for (int i = 2; i < 10000; i+=2) {
            result +=i;
        }
        return result;
    }
}
```



### 线程的同步(重点)

两个线程同时往一个集合中放元素，

当一个线程对另一个线程join()的时候，这个线程就会等待状态

例如：主线程中写t1.join() t1结束，主线程才继续。

```java
t1.join();  //mian线程等待t1结束
```

引出问题案例分析：

正确的过程(假设t1先启动)：

![image-20210707084522266](JavaSE复习.assets/image-20210707084522266.png)

实际上的顺序

![image-20210707084920851](JavaSE复习.assets/image-20210707084920851.png)

因为sleep，出现了上面的情况，但是不sleep依旧有可能会出现(要执行index++的时候时间片到期了)

**多线程共同访问同一个对象，由于多线程中堆空间共享，如果破坏了不可分割的操作，就可能导致数据不一致。**

:man_astronaut: 临界资源：被多线程共同访问的对象

:sassy_man: 原子操作：不可分割的操作，要么全部执行要么都不执行

**怎么解决呢？——加锁**

#### synchronized

开发中不鼓励使用

```java
//对obj对象加锁的同步代码块
synchronized (obj) {}
```

> Java中，每个对象都有一个互斥锁标记(monitor)，用来分配一个线程
>
> 只有拿到对象obj锁标记的线程，才能进入到obj加锁的同步代码块。
>
> ```java
> t2
>     synchronized (obj){
>     t1//即使sleep，t2依旧进不来
> }
> t2想进来进不来，这就是阻塞状态了
> ```
>
> 必须进入到所标记代码块才能执行，线程离开同步代码块时，会释放对象的锁标记。
>
> ```java
> // 修电线的代码：
> synchronized (闸盒对象){
>     将闸盒变为OFF
>         爬上电线杆
>         检修电路
>         爬下电线杆
>         将闸盒变为ON
> }
> 只有这一套完成后，别人才能拿到闸盒的钥匙。
> ```

#### 线程的阻塞状态

![image-20210707091129689](JavaSE复习.assets/image-20210707091129689.png)

### 线程安全的对象

> 该对象成为临界资源，被多线程访问时，不会发生原子操作被破坏，导致数据不一致的问题。

==锁的是对象==

```java
Girl ：对象
Boy：线程
拿到Girl锁标记的Boy，成为Girl的男朋友
    
synchronized(girl){
        拥抱；
        接吻；
        壁咚；
        .........
}// 分手之后，才能让别人成为男朋友
```

![image-20210707093001595](JavaSE复习.assets/image-20210707093001595.png)



### 同步方法

> 只有获得对象obj所标记的线程，才能调用obj的同步方法
>
> 调用之前竞争obj的锁标记
>
> 方法调用结束时，会释放obj的锁标记
>
> ```java
> public class A{
>     public synchronized void m(){} //同步方法 对this加锁的同步代码块
> }
> A a1 = new A();
> A a2 = new A();
> a1.m(); //线程获得a1的锁标记
> a1.m();//线程获得a2的锁标记
> ```
>
> ```java
> synchronized void m(){}
> ======
> void m(){
>     synchronized(this){
>         
>     }
> }
> ```
>
> 

### 死锁

synchronized不仅仅会造成并发效率低，还会造成死锁问题。

```java
synchronized(a){
    //t1
    synchronized(b){
        
    }
}
------死锁了
   synchronized(b){
    //t2
    synchronized(a){
        
    }
}
```

死锁靠线程之间的通信来解决，t1调用Object中的wait(),会释放t1所有锁标记,进入等待状态。 t2调用notify，通知所有调用过wait()的线程，离开等待状态。

> wait() 和sleep() 有什么区别？？
>
> 都会让线程进入等待状态，wait会释放锁标记，sleep不会释放锁标记。
>
> (电话亭打电话，你在里面睡着了，锁依旧是锁着，这是sleep，如果无人接听，你先出去让别人打，这就是wait)



**避免多线程访问临界资源，造成数据不一致的办法：**

1. 用数据最好用局部变量，这样就可以没有临界资源。（局部变量存在栈空间，栈空间是线程独立的）
2. 尽量使用无锁算法，使用线程安全同时并发效率也很高的对象
3. 使用锁标记来控制线程安全



## 8.反射和设计模式

反射是底层技术 通常用来实现框架和工具

> 运行时的类型判定。
>
> 目前创建对象都是采用硬编码`new Dog` 。

### 类对象

:one:**类加载：当JVM第一次使用一个类的时候，它需要将这个类对应的字节码文件读入JVM，从而获取这个类的全部信息(类的父类，拥有哪些属性，方法，构造方法等等)并保存起来(保存在JVM方法区)**

:two:**类对象：类加载的产物，包含了一个类的全部信息。**

:three: 类对象所属的类叫 Class类

> - 类的对象：该类的对象   Dog类的对象---->一只狗
> - 类对象：记录类信息的对象 Dog类对象，相当于动物园的牌子
> - 类加载：就相当于你不认识这个动物，你把这个动物是啥记忆在脑子里

**:star:获得类对象的三种方式：**

> - **类名.class**  直接获取类对象，也可以获取到8种基本类型的对象
> - **类的对象.getClass()** 获取类对象，例如你去动物园看见一直候，你问他你啥对象？？:monkey::自己看牌子！ 
>
> - class.forName("类的全名") 通过类名字符串获得类对象
>
> ==一个类的类对象只能有一个，在JVM中只会产生一个==

*拿到了类对象之后*  **获取类的信息**

> 类对象.getName() //查看类名
>
> 类对象.getSuperclass().getName() //查看父类名
>
> Class[] cs = 类对象.getInterfaces();//获得一个类实现的所有接口
>
> **属性方法构造方法又会被封装**
>
> getMethods() //获取所有**公开**的方法的Method对象(子类父类都可以拿到)
>
> getDecalredMethods()//获得本类中**所有方法**的Method对象
>
> getMethod()/getDeclaredMethod() //根据方法名和参数表，获得类中的某个方法

Class：封装了类的信息

Method：封装了方法的信息

:three:**获取类的对象**我们可以对类对象调用`newInstance() 通过类对象，创建类的对象`（手里有个美女介绍，我直接newInstance 直接来一个，美女的对象）

```java
String classname="com.girl.FullMoneyGirl"; //类名
Class c = Class.forName(classname); //获得类对象
Object o = c.newInstance(); //创建类的对象
```

:four: 调用方法

利用Method对象.invoke()  通过Method调用该方法

```java
String methodname="look"; //方法名
Method m = c.getMethod(methodname); //通过类对象c获取方法
m.invoke(o);
```

调用对象的私有方法

```java
String methodname="ppp"; //方法名
Method m = c.getDeclaredMethod(methodname); //通过类对象c获取方法
m.setAccessible(true);
m.invoke(o);
```



------



# 三、设计模式

> :dagger:共有23种设计模式，好书推荐：《java与模式》

## 1.单例设计模式

**编写一个类，这个类只能有一个对象。**

:one: 饿汉式

```java
ClassA a1 = ClassA.newInstance();   //获得对象

class ClassA{
    private static ClassA instance = new ClassA(); //先创建一个对象，作为静态的属性，ClassA类的唯一对象
    public static ClassA newInstance(){
        return instance;
    }//调用静态方法 返回已经创建的对象
    private ClassA(){}   //防止用户用构造方法创建对象导致不是单例
}
```

应用场景：比较常用，不会有并发问题，但是浪费了存储空间

:two:懒汉

对象的创建时机不一样，当需要对象时，创建对象，有并发效率问题

```java
class ClassB{
    private static ClassB newInstance(){
        if(instance == null) instance = new ClassB();
        return instance;
    }
    private ClassB(){}
}
```

如果是多线程调用的话，这种懒汉模式就不是单例的。考虑并发的问题，导致数据不一致。

```java
/*启动两个线程，创建懒汉模式*/
new Thread(()->ClassB.newInstacnce()).start();
new Thread(()->ClassB.newInstacnce()).start();

正确写法：
   
class ClassB{
    private static ClassB instance = null;
    public static synchronized ClassB newInstance(){
        if(instance == null) instance = new ClassB();
        return instance;
    }
    private ClassB(){}
}
```

但是加了锁之后，就降低了并发效率。

:three: **集合了饿汉式懒汉式的优点**

```java
//类加载的时候线程安全，只有需要的时候才创建对象。
//Holder只会加载一次
class ClassC{
    static class Holder{
        static ClassC instance = new ClassC();
    } 
    public static ClassC newInstance(){
        return Holder.instance;
    }
    private ClassC(){}
}
```

## 2.工厂模式

使用反射进行对象创建，IO流读取文件

开闭原则：软件对修改关闭，对拓展开放。

利用工厂创建对象。

```java
public static Animal createAnimal(){
    String classname = null;
    Properties ps = new Properties();
    try(
        FileInputStream fis = new FileInputStream("config.txt")
    ){
        ps.load(fis);
        classname = ps.getProperty("animal");
        class c = Class.forName(classname);
        Object o = c.newInstance();
        return (Animal)o;
    }
    catch(Exception e){
        e.printStackTrace();
        return null;
    }
}
```



# 四、**常见算法**

## 1.汉诺塔问题

> :question: 有A B C 三个柱子，和若干个大小不同的圆盘，起初圆盘都在A柱上，小盘在上。现在要把A柱的盘子转到B柱上。（每次只能移动一个，且小盘子在上面）
>
> 思考过程：把大问题化小。如果A柱上有两个盘子，先把A上的小盘子移动到C，然后把A 上的大盘子移动到B ，然后把C柱上的小盘子移动到B。 这样就是每次移动一个盘子。下面是步骤：
>
> 1. 先移动（n-1）个盘子  从A 到 C  -----小问题
>
> 2. 将最底层大盘子移动到B         
>
> 3. n-1 个盘子 从C 到 B              -------大问题
>
> 4. 设计函数
>
>    ```java
>    package com.jichu;
>                   
>    /**
>     * @author 王泽
>     */
>                   
>    public class HanNuoTa {
>        public static void main(String[] args) {
>            transfer(5,'A','B','C');
>                   
>        }
>        /**
>         * 汉诺塔方法:
>         * n:移动几个盘子
>         *form:从哪个柱子离开
>         *to:移动到哪个柱子
>         *temp:借助哪个柱子
>         * */
>        static void transfer( int n, char from , char to, char temp){
>        //返回条件
>            if (n == 0) {
>                return;
>            }
>        //把n-1个盘子从 from 移动到temp 借助 to
>            transfer(n-1,from,temp,to);
>        //将大盘子从from 移动到 to
>            System.out.println(from+"----->"+to);
>        //把n-1 个从temp 移动到 to 借助 from
>            transfer(n-1,temp,to,from);
>        }
>    
>    
>    }
>    
> 



## 2.排序

### 1.冒泡排序

> 用相邻的两个元素比较，按照排序要求对调位置。
>
> :eyes: 我们会发现，每一轮冒泡排序会把最大(最小)的排到最后，第二次冒泡排序就不包含最后一个。**n个元素要做n-1次排序。**
>
> ```java
> package com.suanfa.paixu;
> 
> /**
>  * @author 王泽
>  */
> 
> public class MaoPao {
> 
>     public static void main(String[] args) {
>         int[] a = {3,8,6,9,2,7};
>         System.out.println("数组的长度=="+a.length);
>         //冒泡排序
>         for (int i = 0; i < a.length-1; i++) {
>             //相邻元素比较
>             for (int j = 0; j <a.length-1-i ; j++) {
>                 if(a[j]>a[j+1]){
>                     int temp = a[j];
>                     a[j]=a[j+1];
>                     a[j+1] = temp;
>                 }
>             }
>         }
>         //打印
>         for (int i = 0; i < a.length; i++) {
>             System.out.println(a[i]+"  ");
>         }
>     }
> 
> }
> 
> ```
>
> 

------



# 五、**常见机试题**

> :money_mouth_face: 主要用于提升编码能力，重要的编码在于集合与IO。更新中。。。。



------



# 六、**常见面试题**

------

> :first_quarter_moon:本章节罗列javaSE相关高频面试题，每一道题基本都可以在上面的笔记中找到答案。面试题目答案可私聊。

1. 什么是面向对象？
2. JDK JRE JVM
3. ==和equals比较
4. hashCode与equals
5. final，static
6. String、StringBuffer、StringBuilder
7. 重载和重写的区别
8. 接口和抽象类的区别
9. List和Set的区别
10. ArrayList和LinkedList区别
11. HashMap和HashTable有什么区别？其底层实现是什 么？
12. ConcurrentHashMap原理，jdk7和jdk8版本的区别
13. 什么是字节码？采用字节码的好处是什么？
14. Java中的异常体系
15. Java类加载器
16. GC如何判断对象可以被回收
17. 线程的生命周期？线程有几种状态
18. sleep()、wait()、join()、yield()的区别
19. 对线程安全的理解
20. Thread、Runable的区别
21. 对守护线程的理解
22. ThreadLocal的原理和使用场景
23. ThreadLocal内存泄露原因，如何避免
24. 并发、并行、串行的区别
25. 并发的三大特性
26. volatile
27. 为什么用线程池？解释下线程池参数？
28. 简述线程池处理流程
29. 线程池中阻塞队列的作用？为什么是先添加列队而不是先 创建最大线程？
30. 线程池中线程复用原理
