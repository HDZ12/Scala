1. 百度搜索信息通过9999端口发送到IP地址为“192.128.2.10“服务器上，通过监听，每1秒对该信息进行获取，模拟百度热搜排行榜，统计最近10s的热搜词Top3，每隔5秒计算一次。
```Scala
val ssc = new StreamingContext(sc.Seconds(1))
val data = ssc.socketTextStream("192.128.2.10",9999)
val windowrange = data.window(Seconds(10),Second(5))
val words = windowrange.flatMap(x=>x.split(" ")).map(x=>(x,1))
val wordcount = words.reduceByKey(_+_)
val sort = wordcount.transform(rdd=>rdd.sort(x._2,false)
sort.print(3)
ssc.start()
```
2. 某网络平台根据如下公式计算网页的热度，其中u表示用户等级，x代表用户从进入网站到离开网站这段时间内对该网页的访问次数，y代表停留时间，z表示是否点赞\
        $$f(u,x,y,z)=0.2u+0.8x+0.3y+z$$
各网页的数据信息会实时地传送到IP为172.23.15.28服务器的8080端口，数据信息包括网页ID，用户等级，访问次数，停留时间以及是否点赞，以“\t”分隔，信息如下例：

| 网页ID    | 用户等级 | 访问次数 | 停留时间 | 是否点赞 |
| --------- | -------- | -------- | -------- | -------- |
| 2020.html | 7        | 5        | 0.7      | 0        |
| 2019.html | 5        | 3        | 0.9      | 1        |
| …         |          |          |          |          |

编写Spark Streaming代码，要求以5秒为批处理时间间隔，每10秒钟计算一次60s内各网页的热度总和，并输出热度最高的前10个网页ID及热度值信息。
```Scala
val ssc = new StreamingContext(Seconds(5))
val data = ssc.soctokenTextStream("172.23.15.28",8080)
val network = data.window(Seconds(60),Seconds(10))
val hot = network.map(x=>{val w=split("\t");
(w(0),0.2*w(1).toInt+0.8*w(2).toInt+0.3*w(3).toInt+w(4).toInt)}).reduceByKey(_+_)
val shorthtml = hot.transform(rdd=>rdd.sort(x._2,false))
scc.start()
```
3. 每随机间隔5秒在目录/data/SparkStreaming/下新建一个文件，并写入若干行内容（每一行包含若干单词，单词之间以空格分隔）。现利用Spark Streaming分别完成如下单词统计： \
(1)实时统计每10s新出现的单词数量（每10s统计1次）； \
(2)实时统计最近1分钟内每个单词的出现次数（每10s统计1次）；\
(3)实时统计每个单词的累积出现次数，并将结果保存到本地文件（每10s统计1次）\

**(1)**
```Scala
val ssc = new StreamingContext(sc.Seconds(10))
val df = ssc.textFileStream("/data/SparkStreaming/")
val words = df.flatmap(_.split(" ")).map(x=>(x,1))
val wordcount = words.reduceByKey(_+_)
val totalwordcount = words.updateStateByKey((cv:Seq[Int],pv:Option[Int])=>{
                                val v=cv.sum
                                Some(v+pv.getOrElse(0))}
)
val new = wordcount.union(totalwordcount).reduceByKey(_-_).filter(_._2==0)
val result = new.count()
```
**(2)**
```Scala
val ssc=new StreamingContext(sc.Seconds(10))
val df = sc.textFileStream("/data/SparkStreaming/")
val win = df.window(Seconds(60),Seconds(10))
val words = win.flatMap(_.split(" ")).reduceByKey(_+_)
```
**(3)**
```Scala
val ssc = new StreamingContext(sc.Seconds(10))
val df = ssc.textFileStream("/data/SparkStreaming/")
val words = df.flatmap(_.split(" ")).map(x=>(x,1))
val wordcount = words.reduceByKey(_+_)
val totalwordcount = words.updateStateByKey((cv:Seq[Int],pv:Option[Int])=>{
                                val v=cv.sum
                                Some(v+pv.getOrElse(0))}
)
totalwordcount.saveAsTextFiles("test","txt")
ssc.start()
```
