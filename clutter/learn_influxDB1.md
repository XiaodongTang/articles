# 时序数据库InfluxDB

## timestamp

既然是时间序列数据库，influxdb的数据都有一列名为time的列，里面存储UTC时间戳。

## field key，field value，field set
field value可以为string，float，integer或boolean类型。field value通常都是与时间关联的。
在influxdb中，字段必须存在。注意，字段是没有索引的。如果使用字段作为查询条件，会扫描符合查询条件的所有字段值，性能不及tag。类比一下，fields相当于SQL的没有索引的列。

## tag key，tag value，tag set
tags是可选的，但是强烈建议你用上它，因为tag是有索引的，tags相当于SQL中的有索引的列。tag value只能是string类型 

## measurement
measurement是fields，tags以及time列的容器，measurement的名字用于描述存储在其中的字段数据，类似mysql的表名

## retention policy
retention policy指数据保留策略，示例数据中的retention policy为默认的autogen。它表示数据一直保留永不过期，副本数量为1。你也可以指定数据的保留时间，如30天。

## series
series是共享同一个retention policy，measurement以及tag set的数据集合。

## point
point则是同一个series中具有相同时间的field set，points相当于SQL中的数据行

## database
influxdb不是一个完整的CRUD数据库，它更像是一个CR-ud数据库。它优先考虑的是增加和读取数据而不是更新和删除数据的性能，而且它阻止了某些更新和删除行为使得创建和读取数据更加高效。

## 基本用法：

```
#创建数据库  
create database "db_name"  
  
#显示所有的数据库  
show databases  
  
#删除数据库  
drop database "db_name"  
  
#使用数据库  
use db_name  
  
#显示该数据库中所有的表  
show measurements  
  
#创建表，直接在插入数据的时候指定表名  
insert test,host=127.0.0.1,monitor_name=test count=1  
  
#删除表  
drop measurement "measurement_name"  

#写入数据
curl -i -XPOST 'http://127.0.0.1:8086/write?db=metrics' --data-binary 'test,host=127.0.0.1,monitor_name=test count=1' 

use metrics   
insert test,host=127.0.0.1,monitor_name=test count=1 

#查询数据
curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=metrics" --data-urlencode "q=select * from test order by time desc"  

use metrics  
select * from test order by time desc  

#数据保存策略
show retention policies on "db_name"  

#创建新策略
create retention policy "rp_name" on "db_name" duration 3w replication 1 default

#修改策略
alter retention policy "rp_name" on "db_name" duration 30d default 

#删除策略
drop retention policy "rp_name"  
```

## 函数



