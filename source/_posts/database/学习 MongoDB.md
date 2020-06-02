---
title:  学习 MongoDB
date: 
categories:
- 数据库
tags:
- MongoDB
---

# mongodb

```
db.accounts.insertOne(
... 	{
  ...     _id: "account1",
  ...     name: "alice",
  ...     balance: 100
  ... }
... );
```

查询集合

```
show collections
```





```
db.<collection>.insertMany(
	[
		<document1>,
		<document2>
	],
	{
		writeConcern: <写入安全级别>,
		ordered: <boolean,不写 默认 true>
	}
)
```

```
db.accounts.insertMany(
	[
		{name: 'charlie', balance: 500},
		{name: 'david', balance: 200}
	]
)
```





```
db.<collection>.insert(
	[
		document or array of documents
	],
	{
		writeConcern: <写入安全级别>,
		ordered: <boolean,不写 默认 true>
	}
)
```





```
db.<collection>.insert(
	<document>,
	{
		writeConcern: <写入安全级别>
	}
)
```



## 读取文档

* db.collection.find()
* 匹配查询
* 查询操作符
* 返回的是游标
* 投射
  * 只返回部分字段
  * 内嵌文档的投射
  * 数组的投射

```
db.collection.find(<query>, <projection>)
```



```
db.accounts.find().pretty()
```





```
db.accounts.find({name: 'alice'})
```





```
 db.accounts.find({"_id.type": "savings"})
```



{<field>: {$<operator>: <value>}}

* $eq =
* $ne !=
* $gt >
* $gte >=
* $lt < 
* $lte <=
* $in
* $nin

```
db.accounts.find({name: {$eq: "alice"}})
```





```
db.accounts.find({name: {$in: ['alice', 'charles']}})
```



### 逻辑操作符

* $not
* $and 权成立
* $or 至少一个
* $nor



```
{
  <field>: {
  	$not:{
			<operrator-expression>
		}
  }
}
```





```
{
  <field>: {
  	$and: [
  		{
        <operrator-expression>
      },
      {
        <operrator-expression>
      },
      {
        <operrator-expression>
      }
  	]
  	
  }
}
```





```
db.accounts.find({
	$and : [
		{
			balance: {
				$gt: 100
			}
		},
		{
			name: {
				$gt: 'bob'
			}
		}
	]
})
```



简写

```
db.accounts.find({
	balance: { $gt: 100},
	name: {$gt: 'bob'}
})
```





```
db.accounts.find({
	balance: {
		$gt: 100,
		$lt: 500
	}
})
```





```

```





```

```





```

```





```

```





```

```





```

```





```

```





```

```





```

```



