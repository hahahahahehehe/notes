一、idea中运行groovy

1、新建一个项目

2、点击Tools-->Groovy Console

![image-20200507135824177](D:\notes\notes\typora\gradle\images\image-20200507135824177.png)

3、写语法，在里面运行

![image-20200507135927748](D:\notes\notes\typora\gradle\images\image-20200507135927748.png)

二、groovy基础语法

```
//介绍groovy编程语言
//println("helloworld");

//groovy可以省略最末尾的分号
//println("helloworld")

//groovy可以省略括号
//println "helloworld"

//groovy定义变量
// def是弱类型，groovy会根据情况来给变量赋于相应的类型
/*def i = 18
def name = "zhangsan"
println i
println name*/


/*//定义一个集合类型
def list = ['a' , 'b']

//往list添加元素
list << 'c'

//去除list内的第三个元素
println list.get(2)*/



/*//定义一个map
def map = ["k1":"v1" , "k2":"v2"]

//向map中添加键值对
map.k3 = 'v3'

//打印出k3的值
println map.get("k3")*/




//groovy中的闭包
//闭包其实就是一段代码块,在gradle中，我们主要是把闭包当参数使用

//定义一个闭包
def b1 = {
    println "hello b1"
}

//定义一个方法，方法里面需要闭包类型的参数
//Closure就是表示一个闭包类型，不要导包，导包就会出错
def method1(Closure closure){
    closure()
}

//调用方法method1
method1(b1)

//定义一个带参数的闭包,v在里面表示一个参数
def b2 = {
    v ->
        println "hello ${v}"
}

def method2(Closure closure){
    closure("liuhaha")
}

method2(b2)
```

