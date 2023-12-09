1. 编写一个方法method1，输入分数之后能够打印出不及格（小于60分）、及格（60-80分）、良好（80-90分）优秀（大于90分）
```Scala
def method1(x: Int):Unit{
    if (x < 60) println("不及格")
    else if (60 <= x && x <= 70) println("及格")
    else if (80 <= x && x <= 90) println("良好")
    else println("优秀")
}
```
2. 编写一个方法method2，输入一段字符串，判断该名字是否zhangsan，如果是，打印ture，如果不是，打印false
```Scala
  def method2(x:String): Unit = {
    if(x=="zhangsan")println("true")
    else println("false")
  }
```
3. 编写方法method3，要求输入一个整数，打印1到该数字范围的所有数。
```Scala
  def method3(x: Int): Unit = {
    for(i<-1 to x){
      println(i)
    }
  }
```
4. 编写方法method4，要求可以输入3个参数（整型），将每个参数乘以10后放入数组，并打印数组内容。
```Scala
  def method4(x:Int,y: Int,z:Int): Unit = {
    val arr = Array(x*10,y*10,z*10)
    for(i<-arr){
      println(i)
    }
  }
```
5. 定义方法method5，输入三个参数，前两个为数字类型，第三个为f函数(（Int，Int）=>Int)，调用method5，分别实现两个数字的和，两个数字的差，两个数字的平方和。
```Scala
def method5(x：Int,y: Int,f:(Int,Int)=>Int)=f(x,y)
val res1=method5(1,2,_+_)
val res2=method5(1,2,_-_)
val res3=method5(1,2,(x,y)=>x*x+y*y)
```
6. 定义数组arr1（1，2，3，4，5），过滤其中的偶数。
```Scala
val arr1=Array(1,2,3,4,5)
val arr2=arr1.filter(x=>x%2==0)
```
7. 用reduceLeft获得数组arr1中的最大值。
```Scala
val arr3=arr1.reduceLeft((x: Int,y: Int)=>{if(x>y)x else y})
```
8. 使用map计算arr1数组中每一个元素的平方。
```Scala
val arr4=arr1.map(x=>x*x)
```
9. 创建一个数组Y2，内部含有(zhangsan，20，1) ,（lisi，30，0）, （wangwu,40，1），（zhaoliu，60，0) 3个数据，分别表示姓名，年龄，性别，获取张三的信息。
```Scala
val Y2=Array(("zhangsan",20,1),("lisi",30,0),("wangwu",40,1),("zhaoliu",60,0))
val zhangsan=Y2.filter(x=>x._1=="zhangsan")
```
10. 对元组Y2内的数据依据年龄排序（降序）
```Scala
val res=Y2.sortBY(_._2).reverse
```
11. 获取Y2内wangwu的性别信息
```Scala
val wan = Y2.filter(_._1=="wangwu").map(_._3)
```
12. 定义一个高阶函数，对数组中的元素按照指定的规则进行过滤,比如：数组Array（1，4，7，10，6，9，8）,规则：只保留偶数数据。
```Scala
def filter(arr1: Array[Int],f:Int=>Boolean)={
val res=for(i<-arr if f(i)) yield i
res
}
val array=Array(1,4,7,10,6,9,8)
val a=filter(array,(x=>x%2==0))
```
