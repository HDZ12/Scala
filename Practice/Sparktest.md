1. 创建一个1-10数组的RDD，将所有元素*2形成新的RDD
```Scala
val arr=Range(1 to 10)
val rdd=sc.makeRDD(arr)
val rdd=sc.makeRDD(1 to 10)
```
