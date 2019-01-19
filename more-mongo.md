# MongoDB 高级？

-   1:1 (1对1)
-   1: N (1对多)
-   N: 1 (多对1)
-   N: N (多对多)

## 考虑使用嵌入式关系

## 数据库引用

### 使用 DBRefs

`{ $ref : , $id : , $db :  }`

-   $ref：集合名称
-   $id：引用的id
-   $db:数据库名称，可选参数

```sh
{
   "_id":ObjectId("53402597d852426020000002"),
   "address": {
   "$ref": "address_home",
   "$id": ObjectId("534009e4d852427820000002"),
   "$db": "runoob"},
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin"
}

>var user = db.users.findOne({"name":"Tom Benzamin"})
>var dbRef = user.address
>db[dbRef.$ref].findOne({"_id":(dbRef.$id)})

{
   "_id" : ObjectId("534009e4d852427820000002"),
   "building" : "22 A, Indiana Apt",
   "pincode" : 123456,
   "city" : "Los Angeles",
   "state" : "California"
}
```

## 覆盖索引查询

> 覆盖查询是以下的查询：

-   所有的查询字段是索引的一部分
-   所有的查询返回字段在同一个索引中

⚠️ 如果是以下的查询，不能使用覆盖索引查询：

-   所有索引字段是一个数组
-   所有索引字段是一个子文档

## 查询分析

### explain()

### hint()

## 原子操作

### findAndModify

-   $set 用来指定一个键并更新键值，若键不存在并创建。

    `{ $set : { field : value } }`

-   $unset 用来删除一个键。

    `{ $unset : { field : 1} }`

-   $inc $inc可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作。

    `{ $inc : { field : value } }`

-   $push 把 value 追加到 field 里面去，field 一定要是数组类型才行，如果 field 不存在，会新增一个数组类型加进去。

    `{ $push : { field : value } }`

-   $pushAll 同$push,只是一次可以追加多个值到一个数组字段内。

    `{ $pushAll : { field : value_array } }`

-   $pull

    从数组field内删除一个等于value值。

    `{ $pull : { field : _value } }`

-   $addToSet

    增加一个值到数组内，而且只有当这个值不在数组内才增加。

-   $pop 删除数组的第一个或最后一个元素

    `{ $pop : { field : 1 } }`

-   $rename 修改字段名称

    `{ $rename : { old_field_name : new_field_name } }`

-   $bit 位操作，integer类型

    `{$bit : { field : {and : 5}}}`

## MongoDB 高级索引

### 索引数组字段

### 索引子文档字段

## MongoDB 索引限制

⚠️ 每个索引占据一定的存储空间，在进行插入，更新和删除操作时也需要对索引进行操作。所以，如果你很少对集合进行读取操作，建议不使用索引。

⚠️ 由于索引是存储在内存(RAM)中,你应该确保该索引的大小不超过内存的限制。

⚠️ 如果索引的大小大于内存的限制，MongoDB会删除一些索引，这将导致性能下降。

### 查询限制

> 索引不能被以下的查询使用：

-   正则表达式及非操作符，如 $nin, $not, 等。
-   算术运算符，如 $mod, 等。
-   $where 子句

### 最大范围

-   集合中索引不能超过64个
-   索引名的长度不能超过128个字符
-   一个复合索引最多可以有31个字段

## MongoDB ObjectId

> ObjectId 是一个12字节 BSON 类型数据，有以下格式：

-   前4个字节表示时间戳
-   接下来的3个字节是机器标识码
-   紧接的两个字节由进程id组成（PID）
-   最后三个字节是随机数。

🎸 MongoDB采用ObjectId，而不是其他比较常规的做法（比如自动增加的主键）的主要原因，因为在多个 服务器上同步自动增加主键值既费力还费时。

### 创建新的ObjectId

`newID = ObjectId()`

### 获取时间戳

`newID.getTimestamp()`

### 转换为字符串

`newID.str`

## MongoDB Map Reduce

![](https://raw.githubusercontent.com/ronething/Image-Hosting/master/img/20190119142054.png)

http://www.runoob.com/mongodb/mongodb-map-reduce.html

## MongoDB 全文检索

```sh
{
   "post_text": "enjoy the mongodb articles on Runoob",
   "tags": [
      "mongodb",
      "runoob"
   ]
}

db.posts.ensureIndex({post_text:"text"})

db.posts.find({$text:{$search:"runoob"}})
```
## MongoDB 正则表达式

MongoDB 使用 $regex 操作符来设置匹配字符串的正则表达式。

MongoDB使用PCRE (Perl Compatible Regular Expression) 作为正则表达式语言。

http://www.runoob.com/mongodb/mongodb-regular-expression.html

### $regex 操作符

-   i 忽略大小写，{<field>{$regex/pattern/i}}，设置i选项后，模式中的字母会进行大小写不敏感匹配。
-   m 多行匹配模式，{<field>{$regex/pattern/,$options:'m'}，m选项会更改^和$元字符的默认行为，分别使用与行的开头和结尾匹配，而不是与输入字符串的开头和结尾匹配。
-   x 忽略非转义的空白字符，{<field>:{$regex:/pattern/,$options:'m'}，设置x选项后，正则表达式中的非转义的空白字符将被忽略，同时井号(#)被解释为注释的开头注，只能显式位于option选项中。
-   s 单行匹配模式{<field>:{$regex:/pattern/,$options:'s'}，设置s选项后，会改变模式中的点号(.)元字符的默认行为，它会匹配所有字符，包括换行符(\n)，只能显式位于option选项中。
-   使用$regex操作符时，需要注意下面几个问题:
-   
-   i，m，x，s可以组合使用，例如:{name:{$regex:/j*k/,$options:"si"}}
-   在设置索弓}的字段上进行正则匹配可以提高查询速度，而且当正则表达式使用的是前缀表达式时，查询速度会进一步提高，例如:{name:{$regex: /^joe/}

## GridFS

## MongoDB 固定集合（Capped Collections）

### 查询

⚠️ 固定集合文档按照插入顺序储存的,默认情况下查询就是按照插入顺序返回的,也可以使用$natural调整返回顺序。

`>db.cappedLogCollection.find().sort({$natural:-1})`

🎸 在32位机子上一个cappped collection的最大值约为482.5M,64位上只受系统文件大小的限制。

### 属性

-   属性1:对固定集合进行插入速度极快
-   属性2:按照插入顺序的查询输出速度极快
-   属性3:能够在插入最新数据时,淘汰最早的数据

### 用法

-   用法1:储存日志信息
-   用法2:缓存一些少量的文档

## MongoDB 自动增长

其实就是用 js 实现一个函数返回一个自动增加 1 的值，然后调用。

http://www.runoob.com/mongodb/mongodb-autoincrement-sequence.html

---

## 参考文档

-   [MongoDB 教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)