# 一、java基础

## 1、流程控制

### 1.1 循环结构

#### 1.1.1 循环的四要素

① 初始化条件

② 循环条件   -->是boolean类型

③ 循环体

④ 迭代条件

说明：通常情况下，循环结束都是因为②中循环条件返回false



### 1.2 三种循环结构

#### 1.2.1 for循环

for(①;②;④){

​	③

}

执行过程①-②-③-④-②-③-④-...-②





# 继承

## 1、有三种情况下让类无法有继承的子类

1. 存取控制
   1. 类不能被标记为私有，但是类可以不标记公有，非公有的类只能被同一包下的类做出子类
2. 使用final修饰类
   1. 这表示它是继承树的末端，不能被继承
3. 让类只拥有private的构造函数



如果想要父类的方法不被子类覆盖

1. 将方法加上final修饰符





## 2、子类**重写**父类的方法需遵循的规则:

1、参数必须一样，且返回类型必须兼容

子类继承父类的方法，覆盖的方法要使用和父类一样的参数类型，不论父类方法返回什么，子类必须生命返回一样的类型或者该类型的子类型



2、不能降低方法的存取权限

重写的方法必须要有比父类更开放的权限



## 3、重载(overload)

定义:方法名称相同，参数不同



重载方法返回类型可以不同，但不能不改变参数类型，只改变返回类型，编译器无法识别



# 抽象类

抽象类被创建出来就是为了被其他类继承和产生多态的,所以抽象类应该不能被实例化

1、抽象类无法被初始化

2、抽象的方法必须得被子类覆写，抽象的方法没有方法体，如果一个方法被标记成了抽象方法，那它所在的类必须为抽象类，不能再非抽象类中定义抽象方法





# 构造器与垃圾回收器

堆(heap)：对象的生存空间

栈(stack):方法的调用及变量的生存空间

![image-20200618185128741](D:\notes\notes\typora\java\images\image-20200618185128741.png)



构造器:

​	构造函数在执行的时候，首先回去执行它的父类的构造函数，这个连锁反应一直到Object为止

![image-20200619172402788](D:\notes\notes\typora\java\images\image-20200619172402788.png)



![image-20200619172526130](D:\notes\notes\typora\java\images\image-20200619172526130.png)

![image-20200619172857509](D:\notes\notes\typora\java\images\image-20200619172857509.png)

![image-20200619173333814](D:\notes\notes\typora\java\images\image-20200619173333814.png)

![image-20200619173347701](D:\notes\notes\typora\java\images\image-20200619173347701.png)

![image-20200619173605776](D:\notes\notes\typora\java\images\image-20200619173605776.png)

