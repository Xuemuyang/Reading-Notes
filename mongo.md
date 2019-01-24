# mongodb

## 安装

[macOS安装](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

```sh
brew install mongodb
```

## 命令

```sh
show dbs // 显示所有数据的列表
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

SQL|MongoDB|说明
---|---|---
database|database|数据库
table|collection|表/集合
row|document|
column|field|

有三个保留的数据库名`admin`、`local`和`config`

## API

### 删除数据库

```js
db.dropDatabase() // 删除当前数据库
db.collection.drop() // 删除集合
```

### 删除集合

```js
db.createCollection(name, options) // options可选
```

### 文档的CURD

```js
db.collection.insert()
db.collection.remove()
db.collection.find()
db.collection.update()
```

## mongoose
