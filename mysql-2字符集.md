# MySQL2.字符集

## 字符集简介
计算机中只能存储二进制数据，建立字符与二进制数据的映射关系来存储字符。

从两方面考虑：
	1.界定清楚字符范围，即哪些字符映射成二进制数据
	2.怎么映射
		将一个字符映射成一个二进制数据的过程叫做`编码`，将二进制数据映射到字符叫做`解码`。

人们抽象出一个`字符集`的概念来描述某个字符范围的编码规则。如用0001代表a字符

### 重要的字符集
* ASCII
	共128个字符，使用1个字节来编码
* ISO 8859-1 latin1
	256个字符
* GB2312 
	兼容ASCII，采用`变长编码方式`(表示一个字符需要的字节数可能不同)，2个字节
* GBK
	只是对GB2312作了扩充
* utf8
	收录地球上所有能想到的字符，采用变长编码，一个字符需要使用1~4个字节。
	
## MySQL中支持的字符集和排序规则
## utf8 utfmb4
在MySQL中字符集表示一个字符所用最大字节长度在某些方面会影响系统的存储和性能。

所以MySQL中定义了两个概念：

* utf8mb3:只使用1~3个字节表示字符，即utf8
* utf8mb4:使用1~4个字节表示字符 			
		
## 字符集和比较规则的应用
### 各个级别的字符集和比较规则
MySQL有4个级别的字符集和比较规则

* 服务器级别
* 数据库级别
* 表级别
* 列级别

服务器级别：可以在启动服务器程序时通过启动选项或者在服务器程序运行过程中修改如下配置，

```
character_set_server=gbk
collation_server=gbk_chinese_ci
```

数据库级别：在创建和修改数据库的时候可以指定字符集和比较规则。

```
CREATE DATABASE 数据库名
    [[DEFAULT] CHARACTER SET 字符集名称]
    [[DEFAULT] COLLATE 比较规则名称];

ALTER DATABASE 数据库名
    [[DEFAULT] CHARACTER SET 字符集名称]
    [[DEFAULT] COLLATE 比较规则名称];
    
SHOW VARIABLES LIKE 'character_set_database';    
```

表级别：在创建和修改表的时候指定。如果没有指定，则默认使用该表所在的库的字符集。

列级别：**对于存储字符串的列，同一个表中的不同列也可以有不同的字符集和比较规则**。
```
CREATE TABLE 表名(
    列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称],
    其他列...
);

ALTER TABLE 表名 MODIFY 列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称];
```


### 修改字符集或仅修改比较规则
字符集和比较规则是互相有联系的

* 只修改字符集，比较规则将变为修改后的字符集默认的比较规则
* 只修改比较规则，字符集将变为修改后的比较规则的字符集

### MySQL客户端和服务器通信中的字符集	
从MySQL客户端向服务器发送请求到返回结果这个过程中经过3个系统变量：

* character_set_client  请求时使用的字符集
* character_set_connection 服务器处理请求时会把请求字符串从character_set_client转为character_set_connection
* character_set_results 服务器向客户端返回数据时使用的字符集

服务器认为客户端发送过来的请求时用`character_set_client`；
服务器将把得到的结果集使用`character_set_results`返回给客户端；
`character_set_connection`只是服务器在将请求的字节串从`character_set_client`转换为`character_set_connection`时使用，要注意的是该字符集包含的字符范围一定涵盖请求中的字符。

为此，MySQL提供了**`set names 字符集名`**来统一这3个字符。
			
### 比较规则
作用通常体现在比较字符串大小的表达式以及某个字符串列进行**排序**,所以也称为排序规则。		