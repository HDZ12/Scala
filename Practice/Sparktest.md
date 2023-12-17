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
val df1=Score.groupBy("s_id").agg(sum("s_score")as"score",count("c_id")as"c_count").join(Student,"s_id").select("s_id","s_name","score","c_count")
```
(4)
```Scala
val df=Teacher.where(Teacher("t_name")like"李%").count()
```
(5)
```Scala
val df=Score.join(course,"c_id").join(Teacher,"t_id").where($"t_name"==="张三").select("s_id").join(Student,"s_id")
```
(6)
```Scala
val df=Score.groupBy("c_id").count().where($"count">5).orderBy(desc("count"),$"c_id".asc)
```
17. 给定下列数据，数组中的每个元素都由一个城市名称和温度组成，求每个城市的平均温度：\
val data1 = Array(("Changsha", 35.1), ("Beijing", 27.7), ("Shanghai", 32.8), ("Shenyang", 24.6))
val data2 = Array(("Changsha", 36.3), ("Beijing", 30.4), ("Shanghai", 33.5))
val data3 = Array(("Changsha", 34.5), ("Beijing", 31.1), ("Shanghai", 32.0), ("Shenyang", 22.7))
```Scala
//combineByKey实现方法
val data0 = data1+data2+data3
val data=sc.makeRDD(data0)
val result = data.combineByKey(
    count=>(count,1)
    (acc:(Int,Int),count)=>(acc._1+count,acc._2+1)
    (acc1:Int,acc2:Int)=>(acc1._1+acc2._1,acc1._2+acc2._2)
)
val avg = result.map(x=>(x._1,x._2._1toDouble()/x._2._2)
```
```Scala
//groupBy实现方法
val data0=data1+data2+data3
val city = data.groupBy(x=>x._1)
val result=city.map(tp=>{
  val total:Double=tp._2.map(_._2).reduce(_+_)
  val month:Int=tp._2.length
  (tp._1,total/month)
})
```
18. HDFS上有三份文件，分别为student.txt（学生信息表）， result_bigdata.txt （大数据基础成绩表），result_math.txt（数学成绩表）\
加载result_bigdata.txt为名称为bigdata的RDD数据，result_math.txt为名称为math的RDD数据\
![image](https://github.com/HDZ12/Scala/assets/99587726/c459afa6-7956-4eb4-aafb-a90ee2f9ea5b)![image](https://github.com/HDZ12/Scala/assets/99587726/6504a60a-94c2-4075-bed6-a6ee6d6db085)![image](https://github.com/HDZ12/Scala/assets/99587726/e13c97f8-211d-4f9d-a002-1b405fd5f957)\
(1) 分别读取两份学生成绩表创建RDD
```Scala
val bigdata=sc.textFile("result_bigdata.txt")
val math=sc.textFile("result_math.txt")
```
(2) 据任务得到的RDD bigdata及math，分别取出成绩排名前5的学生及成绩信息
```Scala
val m_bigdata = bigdata.map{x=>val line=x.split("\t");(line(0),line(1),line(2).toInt)}
val m_math = math.map{x=>val line=x.split("\t");(line(0),line(1),line(2))}
val sort_bigdata = m_bigdata.sortBy(x=>x._3,false)
val sort_math = m_math.sortBy(x=>x._3,false)
sort_bigdata.take(5)
sort_math.take(5)
```
(3) 找出单科成绩为100的学生ID，最终的结果需要集合到一个RDD中，这涉及两个RDD，一个是数学成绩RDD，一个是大数据成绩RDD，需要对两个RDD进行合并操作。
```Scala
val bigdata_Id = bigdata.filter(x=>x._3==100).map(x=>x._1)
val math_Id = math.filter(x=>x._3==100).map(x=>x._1)
val id = bigdata_Id.union(math_Id).distinct()
```
(4) 输出每位学生的总成绩，要求将两个成绩表中学生ID相同的成绩相加。这一步要求计算过程能对同一个学生ID的数据识别并相加，可以通过将学生ID设为键的方式完成，对同一个键的数据进行统计
```Scala
val all_score = bigdata.union(math)
val score = all_score.map(x=>(x._1,x._3)).reduceByKey((a,b)=>a+b)
```
19. 通过基站信息追踪某个手机号码出现的位置及时长\
根据手机信号可以计算其所在的位置，手机和附近的基站建立连接和断开连接都会被记录到服务器的日志上，据此可以定位手机所在的位置。于是可以根据这些位置信息做一些推荐广告，比如附近的商家，手机用户可能喜欢的商品或者服务。 \
为了便于理解，模拟了一些简单的日志数据存放在A.txt中，共4个字段：手机号码，时间戳，基站id，连接类型（1表示建立连接，0表示断开连接）。日志数据如下所示：\
![image](https://github.com/HDZ12/Scala/assets/99587726/c470f53a-b83c-4b33-9c4b-4d78958c2e52)![image](https://github.com/HDZ12/Scala/assets/99587726/1d060ed6-95d6-48f7-96c4-256086745f55)\
基站表的数据共4个字段，分别代表基站id和经纬度以及信号的辐射类型（比如2G信号、3G信号和4G信号）：\
![image](https://github.com/HDZ12/Scala/assets/99587726/6e176dd9-81a2-4188-a46f-d901715f1fcd)![image](https://github.com/HDZ12/Scala/assets/99587726/af4a26f8-b40f-482e-8a0c-b9f10ff17140)\
结果形式：\
（手机号，基站id，所在基站的时长，基站经度，基站纬度）
```Scala
val lines=sc.textFile(“A.txt”)
val split=lines.map(line=>{
	val fields=line.split(“,”)
	val time = if(fields(3)==“1”) –fields(1).toLong else fields(1).toLong
	((fields(0),fields(2)),time)
})
val reduced=split.reduceByKey(_+_)
val lmt = reduced.map(x=>(x._1._2),(x._1._1,x._2))

val lac=sc.textFile(“B.txt”)
val splitlac=lac.map(line=>{
	val fields=line.split(“,”)
	(fields(0),(fields(1),fields(2)))
})

val joined=lmt.join(splitlac)
joined.collect
```
20. 数据文件word.txt存储在HDFS上（路径为/user/word.txt），文件中包含了多行句子，现在要求对文档中的单词计数，并把单词计数超过3的结果以Json格式存储到HDFS上（路径为/Json/Wordcount，其中单词字段名称为“Word”，次数字段名称为“Count” ），且保证输出结果文件只有一个。\
```Scala
val data = sc.textFile("user/word.txt")
val wordCounts = data.flatMap(x=>x.split("\\s+")).filter(word=>word.nonEmpty).map(word=>(word,1)).reduceByKey(_+_)
val filteredWordCounts = wordCounts.filter(x=>x._2>3)
val resultDF = filteredWordCounts.toDF("Word", "Count")
resultDF.coalesce(1).write.json("/Json/WordCount")
```
















