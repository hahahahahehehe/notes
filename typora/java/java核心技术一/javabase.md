# 一、java基本程序设计

源代码的文件名必须与`公共类`的名字相同(一个java文件中不能出现两个public修饰的class)



## 1、数据类型

1、在java中有8中基本类型(primitive type)

1. 4种整形
2. 2种浮点型
3. 1种表示Unicode编码的字符单元的字符类型char
4. 1种表示真值的boolean类型

`1字节=8bit`

### 1、4种整型

| 类型  | 存储需求 | 取值范围                              |
| ----- | -------- | ------------------------------------- |
| byte  | 1字节    | -128-127                              |
| short | 2字节    | -32768-32767                          |
| int   | 4字节    | -2147483648--2147483647(正好超过20亿) |
| long  | 8字节    |                                       |

长整型数值有一个后缀 **L** 或 **1** ( 如 **4000000000L**。) 

十六进制数值有一个前缀 **Ox** 或 **0X** (如**OxCAFEL**)

 八进制有一个前缀 **0** **,** 例如， **010** 对应八进制中的 **8**。 很显然， 八进制表示法比较容易混淆， 所以建议最好不要使用八进制常数。

java7开始，前缀0b 或 0B可以表示一个二进制数值 (如Ob1001就是 **9**)

同样是从 **Java** **7** 开始，还可以为数字字面量加下划线，如用 1_000_000(或0b1111_0100_0010_0100_0000)

表示一百万。这些下划线只是为了让人更易读。**Java** 编译器会去除这些下划线



### 2、2种浮点型

| 类型   | 存储需求 | 取值范围                                        |
| ------ | -------- | ----------------------------------------------- |
| float  | 4字节    | 大约 ± 3.402 823 47E+38F (有效位数为 6 ~ 7 位） |
| double | 8字节    | 有效位数为 15 位                                |

用于表示溢出和出错情况的三个特殊的浮点数值：

- 正无穷大
- 负无穷大
- NaN(不是一个数字)

例如， 一 个正整数除以 0 的结果为正无穷大。计算 0/0 或者负数的平方根结果为 NaN

常量 Double.POSITIVE**_**INFINITY、Double.NEGATIVE_INFINITY 和 Double.NaN( 以及相应的 **Float** 类型的常量） 分别表示这三个特殊的值

不能这样检测一个特定值是否等于 **Double**.**NaN**

if (x = Double.NaN) // is never true

所有“ 非数值” 的值都认为是不相同的。然而，可以使用 **Double**.**isNaN** 方法来判断：

if (Double.isNaN(x)) // check whether x is "not a number"

### 3、输入输出

#### 1、Scanner

要想通过控制台进行输人，首先需要构造一个 Scanner 对象，并与“ 标准输人流” System.in 关联

**Scanner** **in** **=** **new** Scanner(System.in);

- String name = in.nextLine()；

  - nextLine()获取的是输入的一行数据(不管有没有空格都会打出)

  - ```java
    public class MainTest {
        public static void main(String[] args) {
            Scanner scanner = new Scanner(System.in);
            System.out.println("whats your name ?");
            String s = scanner.nextLine();
            System.out.println("hello,"+s);
        }
    }
    ```

  - 输出结果(输入什么结果就输出什么)

  - ![image-20200516091746501](D:\notes\notes\typora\java\java核心技术一\images\image-20200516091746501.png)

- String name = in.next()以空格分隔，获取第一个非空格字符

  - ```java
    public class MainTest {
        public static void main(String[] args) {
            Scanner scanner = new Scanner(System.in);
            System.out.println("whats your name ?");
            String s = scanner.next();
            System.out.println("hello,"+s);
        }
    }
    ```

  - 输出结果

  - ![image-20200516092007393](D:\notes\notes\typora\java\java核心技术一\images\image-20200516092007393.png)

- String name = in.nextInt()

- String name = in.nextDouble()

  - 以上两种是获取输入的整数和小数，如果输入的不是相应类型，则抛出异常

#### 2、利用Scanner读取文件

```java
public class MainTest {
    public static void main(String[] args) {
        try{
            Scanner scanner = new Scanner(Paths.get("C:\\Users\\Administrator\\Desktop\\aa.txt"),"utf-8");
            while(scanner.hasNext()){
                System.out.println(scanner.nextLine());
            }
        }catch(Exception e){
            e.printStackTrace();
        }
```



# 六、泛型程序设计

## 1、为什么使用泛型

泛型程序设计（Generic programming) 意味着编写的代码可以被很多不同类型的对象所重用



## 2、定义简单的泛型类(generic class)

Pair

```java
package subclass;

public class Pair<T> {

    private T first ;
    private T second;
    
    public Pair(T first , T second){
        this.first = first ;
        this.second = second ;
    }

    public T getFirst(){
        return first ;
    }

    public T getSecond(){
        return second ;
    }

    public void setFirst(T first){
        this.first = first ;
    }

    public void setSecond(T second){
        this.second = second ;
    }
}
```

Pair 类引人了一个类型变量 T，用尖括号 ( < >) 括起来，并放在类名的后面。泛型类可以有多个类型变量。例如， 可以定义 Pair 类，其中第一个域和第二个域使用不同的类型

```java
package subclass;

public class Pair<T,U> {

    private T first ;
    private U second;

    public T getFirst(){
        return first ;
    }

    public U getSecond(){
        return second ;
    }

    public void setFirst(T first){
        this.first = first ;
    }

    public void setSecond(U second){
        this.second = second ;
    }
}
```

类型变量使用大写形式，且比较短， 这是很常见的。在 Java 库中， 使用变量 E 表示集合的元素类型， K 和 V 分别表示表的关键字与值的类型。T ( 需要时还可以用临近的字母 U 和 S) 表示“ 任意类型”。



用具体的类型替换类型变量就可以实例化泛型类型， 例如：

```java
Pair<String,String> pair = new Pair<String,String>();
```

换句话说，泛型类可看作普通类的工厂



## 3、泛型方法

```java
package subclass.genericmethod;

public class ArrayAlg {

    public static <T> T getMiddle(T... a){
        return a[a.length/2];
    }
}
```

泛型方法可以定义在普通类中，也可以定义在泛型类中,类型变量放在修饰符（这里是 public static) 的后面，返回类型的前面。

当调用一个泛型方法时,在方法名前的尖括号中放人具体的类型

```java
int middle = ArrayAlg.<Integer>getMiddle(1,2,3,4);
```

调用方法的类型参数也可以省略

```java
int middle = ArrayAlg.getMiddle(1,2,3,4);
```



## 4、类型变量的限定

```java
class ArrayAlg
	public static <T> T iin(T[] a){ // almost correct
		if (a null || a.length = 0) return null ; T smallest = a[0];
		for (int i = 1; i < a.length; i ++){
			if (smallest.compareTo(a[i]) > 0) smallest = a[i];
        }
		return smallest;
	}
}
```

变量 smallest 类型为 T, 这意味着它可以是任何一个类的对象。怎么才能确信 T 所属的类有 compareTo 方法呢？

解决这个问题的方案是将 T 限制为实现了 Comparable 接口（只含一个方法 compareTo 的标准接口）的类。可以通过对类型变量 T 设置限定（bound) 实现这一点

```java
public static <T extends Comparable>T min(T[] a){}
```



一个类型变量或通配符可以有多个限定， 例如：

```java
T extends Comparable & Serializable
```

限定类型用“ &” 分隔，而逗号用来分隔类型变量



```java
package subclass;

import java.time.LocalDate;

public class MainTest {
    public static void main(String[] args) {
        LocalDate[] birthday={
          LocalDate.of(1906,12,9),
          LocalDate.of(1815,12,10),
          LocalDate.of(1903,12,3),
          LocalDate.of(1910,6,22)
        };
        Pair<LocalDate> minmax = ArrayAlg.minmax(birthday);
        System.out.println("min:"+minmax.getFirst());
        System.out.println("max:"+minmax.getSecond());
    }
}

class ArrayAlg{

    public static <T extends Comparable> Pair<T> minmax(T[] a){
        if(a == null || a.length == 0){
            return null ;
        }
        T min = a[0];
        T max = a[0];

        for(int i=1;i<a.length;i++){
            if(min.compareTo(a[i]) > 0){
                min = a[i] ;
            }
            if(max.compareTo(a[i]) < 0){
                max = a[i] ;
            }
        }
        return new Pair<>(min,max);
    }
}
```



## 5、泛型代码和虚拟机

虚拟机没有泛型类型对象—所有对象都属于普通类



### 5.1 类型擦除

无论何时定义一个泛型类型， 都自动提供了一个相应的原始类型 （ raw type )。原始类型

的名字就是删去类型参数后的泛型类型名。擦除（ erased) 类型变量, 并替换为限定类型（无

限定的变量用 Object)

无限定类型的泛型Pair<T> 

```java
package subclass;

public class Pair<T> {

    private T first ;
    private T second;
    
    public Pair(T first , T second){
        this.first = first ;
        this.second = second ;
    }

    public T getFirst(){
        return first ;
    }

    public T getSecond(){
        return second ;
    }

    public void setFirst(T first){
        this.first = first ;
    }

    public void setSecond(T second){
        this.second = second ;
    }
}
```

他的原始类型为

```java
public class Pair{
	private Object first;
	private Object second;
	public Pair(Object first, Object second) {
		this,first = first;
		this.second = second;
    }
	public Object getFirstO { 
        return first; 
    }
	public Object getSecondO { 
        return second; 
    }
	public void setFirst(Object newValue) { 
        first = newValue; 
    }
	public void setSecond(Object newValue) { 
        second = newValue; 
    } 
}
```



有限定类型的泛型 Pair<T extends Comparable>

```java
package subclass;

public class Pair<T extends Comparable> {

    private T first ;
    private T second;
    
    public Pair(T first , T second){
        this.first = first ;
        this.second = second ;
    }

    public T getFirst(){
        return first ;
    }

    public T getSecond(){
        return second ;
    }

    public void setFirst(T first){
        this.first = first ;
    }

    public void setSecond(T second){
        this.second = second ;
    }
}
```



他的原始类型为

```java
package subclass;

public class Pair {

    private Comparable first ;
    private Comparable second;
    
    public Pair(Comparable first , Comparable second){
        this.first = first ;
        this.second = second ;
    }

    public Comparable getFirst(){
        return first ;
    }

    public Comparable getSecond(){
        return second ;
    }

    public void setFirst(Comparable first){
        this.first = first ;
    }

    public void setSecond(Comparable second){
        this.second = second ;
    }
}
```



### 5.2 翻译泛型表达式

当程序调用泛型方法时，如果擦除返回类型， 编译器插入强制类型转换。例如，下面这个语句序列

```java
Pair<Employee> buddies = . .
Employee buddy = buddies.getFirstO；
```

擦除 getFirst 的返回类型后将返回 Object 类型。编译器自动插人 Employee 的强制类型转换。也就是说，编译器把这个方法调用翻译为两条虚拟机指令：

- 对原始方法 Pair.getFirst 的调用。
- 将返回的 Object 类型强制转换为 Employee 类型。

### 5.3 约束和局限性

**1、不能用基本类型实例化类型参数**

​		不能用类型参数代替基本类型。因此， 没有 Pair<double>, 只 有 Pair<Double>。 当然,其原因是类型擦除。擦除之后， Pair 类含有 Object 类型的域， 而 Object 不能存储 double 值



**2、运行时类型查询只适用于原始类型**

​		虚拟机中的对象总有一个特定的非泛型类型。因此，所有的类型查询只产生原始类型



例如：

​		if (a instanceof Pair<String>) // Error

实际上仅仅测试 a 是否是任意类型的一个 Pair。下面的测试同样如此：

​		if (a instanceof Pair<T>) // Error

倘若使用 instanceof 会得到一个编译器错误



getClass 方法总是返回原始类型。例如

```java
Pair<Employee> employeePair = . .
Pair<String> stringPair = . .
if (stringPair.getClass == employeePair.getClass) // they are equal
```

其比较的结果是 true, 这是因为两次调用 getClass 都将返回 Pair.class



**3、不能创建参数化类型的数组**

不能实例化参数化类型的数组， 例如：Pair<String>[] table = new Pair<String>[10]; // Error

这有什么问题呢？ 擦除之后， table 的类型是 Pair[] ，可以把它转换为 Object[]:

Object[] objarray = table;

数组会记住它的元素类型， 如果试图存储其他类型的元素， 就会抛出一个 Array-StoreException 异常：

objarray[0] = "Hello"; // Error component type is Pair

不过对于泛型类型， 擦除会使这种机制无效。以下赋值：

objarray[0] = new Pair<Employee>0；

能够通过数组存储检査， 不过仍会导致一个类型错误。出于这个原因， 不允许创建参数化类型的数组。

