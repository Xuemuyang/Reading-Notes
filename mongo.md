# mongodb

## 数据库相关

NoSQL(Not Only SQL)

RDBMS(Relational Database Management System) --- 关系数据库管理系统

## 安装

[macOS 安装](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

```sh
brew install mongodb
```

## 命令

```sh
show dbs // 显示数据库
show collections // 显示已存在的集合
db // 显示当前数据库对象或集合
use xxx // 连接到指定的数据库，如果数据库不存在则创建
```

## 启动

```sh
mongod
mongo
```

## 概念

| SQL      | MongoDB    | 说明    |
| -------- | ---------- | ------- |
| database | database   | 数据库  |
| table    | collection | 表/集合 |
| row      | document   |
| column   | field      |

有三个保留的数据库名`admin`、`local`和`config`

## API

collection API

- collection.insert(doc)——插入一个或多个文档；
- collection.find(query)——找到跟查询匹配的文档；
- collection.remove(query)——移除跟查询匹配的文档；
- collection.drop()——移除 collection；
- collection.update(query)——更新跟查询匹配的文档；
- collection.count(query)——对跟查询匹配的文档计数。

操作一个或者多个文档

- collection.insertOne(doc)——插入单个文档；
- collection.insertMany([doc1,doc2])——插入多个文档；
- collection.findOne(query)——找到一个跟查询匹配的文档；
- collection.updateMany(query)——更新所有跟查询匹配的文档。

### update

```js
db.collection.update(
  <query>,
  <update>,
  {
    upsert: <boolean>,
    multi: <boolean>,
    writeConcern: <document>
  }
)
```

update 中传入对象直接重写，使用`$set`覆盖

```js
db.products.update(
  { _id: 100 },
  {
    $set: {
      quantity: 500,
      details: { model: "14Q3", make: "xyz" },
      tags: ["coats", "outerwear", "clothing"]
    }
  }
);
```

[更多操作间  见文档](https://docs.mongodb.com/manual/reference/operator/update/)

一些实例

```js
// 只更新第一条记录
db.col.update({ count: { $gt: 1 } }, { $set: { test2: "OK" } });
// 全部更新
db.col.update({ count: { $gt: 3 } }, { $set: { test2: "OK" } }, false, true);
// 只添加第一条
db.col.update({ count: { $gt: 4 } }, { $set: { test5: "OK" } }, true, false);
// 全部添加进去
db.col.update({ count: { $gt: 5 } }, { $set: { test5: "OK" } }, true, true);
// 全部更新
db.col.update({ count: { $gt: 15 } }, { $inc: { count: 1 } }, false, true);
// 只更新第一条记录
db.col.update({ count: { $gt: 10 } }, { $inc: { count: 1 } }, false, false);
```

### find

`pretty()`方法以格式化的方式来显示所有文档

AND

```js
db.col.find({ key1: value1, key2: value2 });
```

OR

```js
db.col.find({
  $or: [{ key1: value1 }, { key2: value2 }]
});
```

AND 与 OR 联合使用

```js
db.col.find({
  likes: { $gt: 50 },
  $or: [{ by: "菜鸟教程" }, { title: "MongoDB 教程" }]
});
```

$type操作符根据BSON类型检索集合中匹配的数据类型

`limit()`可以给返回的记录指定条数,`skip()`用来跳过某些记录

