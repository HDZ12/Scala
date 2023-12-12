1. 创建一个1-10数组的RDD，将所有元素*2形成新的RDD
```Scala
val arr=Range(1 to 10)
val rdd=sc.makeRDD(arr)
val rdd=sc.makeRDD(1 to 10)
```
2.创建一个元素为 1-5 的RDD，运用 flatMap创建一个新的 RDD，新的 RDD 为原 RDD 每个元素的 平方和三次方 来组成 1,1,4,8,9,27…。
```Scala
val rdd=sc.makeRDD(1 to 5)
val res1=rdd.flatMap(x=>array(x*x,x*x*x))
```
3. 创建一个 RDD数据为Array(1, 3, 4, 20, 4, 5, 8)，按照元素的奇偶性进行分组
```Scala
val rdd=sc.makeRDD(Array(1,3.4,20,4,5,8))
val res=rdd.groupBy(x=>x%2==0)
```
4. 创建一个 RDD（由字符串组成）Array(“xiaoli”, “laoli”, “laowang”, “xiaocang”, “xiaojing”, “xiaokong”)，过滤出一个新 RDD（包含“xiao”子串）
```Scala
val rdd=sc.makeRDD(Array(“xiaoli”, “laoli”, “laowang”, “xiaocang”, “xiaojing”, “xiaokong”))
val res=rdd.filter(x=>x.contaions("xiaoli"))
```
5. 创建一个 RDD数据为Array(10,10,2,5,3,5,3,6,9,1),对 RDD 中元素执行去重操作
```Scala
val rdd=sc.makeRDD(Array(10,10,2,5,3,5,3,6,9,1))
val res=rdd.distinct()
```
6. 创建一个分区数为5的 RDD，数据为0 to 100，之后使用repartition再重新减少分区的数量至 3
```Scala
val rdd=sc.makeRDD(0 to 100,5)
val res=rdd.repartition(3)
```
7.创建一个 RDD数据为1,3,4,10,4,6,9,20,30,16,请给RDD进行分别进行升序和降序排列
```Scala
val rdd=sc.makeRDD(Array(1,3,4,10,4,6,9,20,30,16))
val rdd1=rdd.sortBy(x=>x)
val rdd2=rdd.sortBy(x=>x,false)
```
8. 创建两个RDD，分别为rdd1和rdd2数据分别为1 to 6和4 to 10，求并集
```Scala
val rdd=sc.makeRDD(1 to 6)
val rdd1=sc.makeRDD(4 to 10)
Val rdd2=rdd.union(rdd1)
Val rdd3=rdd2.distinct()
```
9. 创建两个RDD，分别为rdd1和rdd2数据分别为1 to 6和4 to 10，计算差集，两个都算
```  Scala
val rdd=sc.makeRDD(1 to 6)
val rdd1=sc.makeRDD(4 to 10)
val rdd2=rdd.subtract(rdd1)
val rdd3=rdd1.subtract(rdd)
```
10. 创建两个RDD，分别为rdd1和rdd2数据分别为1 to 6和4 to 10，计算交集
```Scala
val rdd=sc.makeRDD(1 to 6)
val rdd1=sc.makeRDD(4 to 10)
val rdd2=rdd.intersection(rdd1)
```
11. 创建两个RDD，分别为rdd1和rdd2数据分别为1 to 6和4 to 10，计算 2 个 RDD 的笛卡尔积
```Scala
val rdd=sc.makeRDD(1 to 6)
val rdd1=sc.makeRDD(4 to 10)
val rdd2=rdd.cartesian(rdd1)
```
12. 创建一个RDD数据为List((“female”,1),(“male”,5),(“female”,5),(“male”,2))，请计算出female和male的总数分别为多少
```Scala
val rdd=sc.makeRDD(List((“female”,1),(“male”,5),(“female”,5),(“male”,2)))
val rdd1=rdd.map(x=>x._1,1).reduceByKey(_+_)
```
13. 创建一个有两个分区的 RDD数据为List((“a”,3),(“a”,2),(“c”,4),(“b”,3),(“c”,6),(“c”,8))，取出每个分区相同key对应值的最大值，然后相加
```Scala
val rdd=sc.makeRDD(List((“a”,3),(“a”,2),(“c”,4),(“b”,3),(“c”,6),(“c”,8)),2)
val rdd1=rdd.combineByKey(
  value=>value,
  (max,value)=>{if(max>value) max else value},
  (max1,max2)=>max1+max2
)
```
14.  创建一个有两个分区的 pairRDD数据为Array((“a”, 88), (“b”, 95), (“a”, 91), (“b”, 93), (“a”, 95), (“b”, 98))，根据 key 计算每种 key 的value的平均值
```Scala
val rdd=sc.makeRDD(Array((“a”, 88), (“b”, 95), (“a”, 91), (“b”, 93), (“a”, 95), (“b”, 98)), 2)
val rdd1=rdd.combineByKey(
  count=>(count,1),
  (acc:(Int,Int),count)=>(acc._1+count,acc._2+1),
  (acc1:(Int,Int),acc2:(Int,Int))=>(acc1._1+acc2._1,acc1._2+acc2._2)
)
val result=rdd1.map(x=>x._1,x._2._1.toDouble/x._2._2)
```
15. 有学生课程成绩文件，存储在“\student\score.txt”中，通过Spark编程完成以下问题：
![image](https://github.com/HDZ12/Scala/assets/99587726/c560786a-cbf5-446a-9457-c0b8556f428e)
（1）该系共有多少名学生\
（2）Tom的总成绩平均分\
（3）每名同学选修的课程门数\
（4）该系Database课程共有多少人选修\
（5）各门课程的平均分是多少\
（6）每位同学的总成绩
```Scala
val rdd=sc.textFile("\student\score.txt").map(x=>x.split(",")),map(x=>(x(0),x(1),x(2)))
```
(1) 
```Scala
val count=rdd.map(x=>x._1).distinct().count()
```
(2)
```Scala
val Tom=rdd.filter(x=>x._1=="Tom").map(x=>(x._1,x._3))
val score=Tom.combineByk=Key(
  count=>(count,1),
  (acc:(Int,Int),count)=>(acc._1+count,acc._2+1),
  (acc1:(Int,Int),acc2:(Int,Int))=>(acc1._1+acc2._1,acc1._2+acc2._2)
)
val result=score.map(x=>x._1,x._2._1.toDouble/x._2._2)
```
(3)
```Scala
val count=rdd.map(x=>(x._1,x.3)).countByKey()
```
(4)
```Scala
val database=rdd.map(x=>x._2=="Database").count()
```
(5)
```Scala
val course=rdd.map(x=>(x._2,x._3)).combineByKey(
  count=>(count,1)
  (acc:(Int,Int),count)=>(acc._1+count,acc._2+1),
  (acc1:(Int,Int),acc2:(Int,Int))=>(acc1._1+acc2._1,acc1._2+acc2._2)
)
val result=course.map(x=>(x._1,x._2._1.toDouble()/x._2._2)
```
(6)
```Scala
val student=rdd.map(x._1,x._3)).reduceByKey(_+_)
```
16. 
有课程表，学生表，教师表，成绩表，各表已建立生成DataFrame，通过编程完成以下问题：\
课程表:\
Course\
c_id：课程编号\
c_name：课程名称\
t_id：教师编号

学生表:\
Student\
s_id：学号\
s_name：姓名\
s_birth：出生日期\
s_sex：性别

教师表:\
Teacher\
t_id：教师编号\
t_name：教师姓名

成绩表:\
Score\
s_id：学生编号\
c_id：课程编号\
s_score：分数\
————————————————\
（1）查询"01"课程比"02"课程成绩高的学生的信息及课程分数\
（2）查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩\
（3）查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩\
（4）查询"李"姓老师的数量\
（5）查询学过"张三"老师授课的同学的信息\
（6）统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列\
(1)
```Scala
val course1=Score.where($"c_id"===01).select(Score("s_id"),Score("c_id")as"c_id_01",Score("s_score")as"s_score_01")
val course2=Score.where($"c_id"===02).select(Score("s_id"),Score("c_id")as"c_id_02",Score("s_core")as"s_score_02")
val df1=course1.join(course2,"s_id").filter($"c_id_01">$"c_id_02")
val df2=df1.join(student,"s_id")
```
(2)
```Scala
val df1=Score.groupBy("s_id").agg(avg("s_core") as "score").filter($"score">=60).join(Student,"s_id").select("s_id","s_name","score")
```
(3)
```Scala
val df1=Score.groupBy("s_id").agg(sum("s_score")as"score",count("c_id")as"c_count").join(Student


