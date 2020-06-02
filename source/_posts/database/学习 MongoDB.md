---
title:  学习 MongoDB
date: 
categories:
- 数据库
tags:
- MongoDB
---

# mongodb

```javascript
db.accounts.insertOne(
... 	{
  ...     _id: "account1",
  ...     name: "alice",
  ...     balance: 100
  ... }
... );
```

查询集合

```javascript
show collections
```





```javascript
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

```javascript
db.accounts.insertMany(
	[
		{name: 'charlie', balance: 500},
		{name: 'david', balance: 200}
	]
)
```





```javascript
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





```javascript
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

```javascript
db.collection.find(<query>, <projection>)
```



```javascript
db.accounts.find().pretty()
```





```javascript
db.accounts.find({name: 'alice'})
```





```javascript
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

```javascript
db.accounts.find({name: {$eq: "alice"}})
```





```javascript
db.accounts.find({name: {$in: ['alice', 'charles']}})
```



### 逻辑操作符

* $not
* $and 权成立
* $or 至少一个
* $nor



```javascript
{
  <field>: {
  	$not:{
			<operrator-expression>
		}
  }
}
```





```javascript
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





```javascript
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

```javascript
db.accounts.find({
	balance: { $gt: 100},
	name: {$gt: 'bob'}
})
```





```javascript
db.accounts.find({
	balance: {
		$gt: 100,
		$lt: 500
	}
})
```
