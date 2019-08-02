# mysql的profiling

参考地址：https://blog.csdn.net/xusheng__zhang/article/details/78943121



## 使用profiling分析语句
需要每次都打开，在SQL语句层：

```
set profiling = on ;

.... 所要执行的sql 

# 显示出所有被记录的操作
show profiles;  

# 具体查看一个queryID
show profile for query1;

```




状态 |	描述|
------------- | -------------
System lock	| 确认是由于哪个锁引起的，通常是因为MySQL或InnoDB内核级的锁引起的建议：如果耗时较大再关注即可，一般情况下都还好
Sending data	| 从server端发送数据到客户端，也有可能是接收存储引擎层返回的数据，再发送给客户端，数据量很大时尤其经常能看见，备注：Sending Data不是网络发送，是从硬盘读取，发送到网络是Writing to net。建议：通过索引或加上LIMIT，减少需要扫描并且发送给客户端的数据量
Sorting result| 	正在对结果进行排序，类似Creating sort index，不过是正常表，而不是在内存表中进行排序建议：创建适当的索引
Table lock	| 表级锁，没什么好说的，要么是因为MyISAM引擎表级锁，要么是其他情况显式锁表
create sort index	|当前的SELECT中需要用到临时表在进行ORDER BY排序。建议：创建适当的索引
checking query cache for querychecking privileges on cachedsending cached result to clienstoring result in query cache	|和query cache相关的状态，已经多次强烈建议关闭

