---
title: "Gorm连接数据库"
date: 2021-09-13T10:04:07+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

## gorm连接mysql数据库

gorm支持多种数据库，这里主要介绍mysql,连接mysql主要有两个步骤:

1. 配置DSN (Data Source Name)
2. 使用gorm.Open连接数据库

### 1. 配置DSN (Data Source Name)

gorm库使用dsn作为连接数据库的参数，dsn翻译过来就叫数据源名称，用来描述数据库连接信息。一般都包含数据库连接地址，账号，密码之类的信息。

**DSN格式：**

```go
[username[:password]@][protocol[(address)]]/dbname[?param1=value1&...&paramN=valueN]
```

mysql连接dsn例子：

```go
//mysql dsn格式
//涉及参数:
//username   数据库账号
//password   数据库密码
//host       数据库连接地址，可以是Ip或者域名
//port       数据库端口
//Dbname     数据库名
username:password@tcp(host:port)/Dbname?charset=utf8&parseTime=True&loc=Local

//填上参数后的例子
//username = root
//password = 123456
//host     = localhost
//port     = 3306
//Dbname   = tizi365
//后面K/V键值对参数含义为：
//  charset=utf8 客户端字符集为utf8
//  parseTime=true 支持把数据库datetime和date类型转换为golang的time.Time类型
//  loc=Local 使用系统本地时区
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local

//gorm 设置mysql连接超时参数
//开发的时候经常需要设置数据库连接超时参数，gorm是通过dsn的timeout参数配置
//例如，设置10秒后连接超时，timeout=10s
//下面是完成的例子
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local&timeout=10s

//设置读写超时时间
// readTimeout - 读超时时间，0代表不限制
// writeTimeout - 写超时时间，0代表不限制
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local&timeout=10s&readTimeout=30s&writeTimeout=60s
```

### 2. 使用gorm.Open连接数据库

有了上面配置的dsn参数，就可以使用gorm连接数据库，下面是连接数据库的例子

```go
package main

import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

func main()  {
    //配置MySQL连接参数
	username := "root"  //账号
	password := "123456" //密码
	host := "127.0.0.1" //数据库地址，可以是Ip或者域名
	port := 3306 //数据库端口
	Dbname := "tizi365" //数据库名
	timeout := "10s" //连接超时，10秒
	
	//拼接下dsn参数, dsn格式可以参考上面的语法，这里使用Sprintf动态拼接dsn参数，因为一般数据库连接参数，我们都是保存在配置文件里面，需要从配置文件加载参数，然后拼接dsn。
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8&parseTime=True&loc=Local&timeout=%s", username, password, host, port, Dbname, timeout)
	//连接MYSQL, 获得DB类型实例，用于后面的数据库读写操作。
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic("连接数据库失败, error=" + err.Error())
	}
	//延时关闭数据库连接
	defer db.Close()
}
```

### 3. gorm调试模式

为了方便调试，了解gorm操作到底执行了怎么样的sql语句，**开发的时候**需要打开调试日志，这样gorm会打印出执行的每一条sql语句。

使用Debug函数执行查询即可
例子：

```go
result := db.Debug().Where("username = ?", "tizi365").First(&u)
```

## 二.gorm连接池

在高并发实践中，为了提高数据库连接的使用率，避免重复建立数据库连接带来的性能消耗，会经常使用数据库连接池技术来维护数据库连接。
gorm自带了数据库连接池使用非常简单只要设置下数据库连接池参数即可。

数据库连接池使用例子：
定义tools包，负责数据库初始化工作

```go
//定义一个工具包，用来管理gorm数据库连接池的初始化工作。
package tools

//定义全局的db对象，我们执行数据库操作主要通过他实现。
var _db *gorm.DB

//包初始化函数，golang特性，每个包初始化的时候会自动执行init函数，这里用来初始化gorm。
func init() {
    ...忽略dsn配置，请参考上面例子...
    
    // 声明err变量，下面不能使用:=赋值运算符，否则_db变量会当成局部变量，导致外部无法访问_db变量
    var err error
    _db, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
		panic("连接数据库失败, error=" + err.Error())
	}
     
    sqlDB, _ := db.DB()

    //设置数据库连接池参数
    sqlDB.SetMaxOpenConns(100)   //设置数据库连接池最大连接数
    sqlDB.SetMaxIdleConns(20)   //连接池最大允许的空闲连接数，如果没有sql任务需要执行的连接数大于20，超过的连接会被连接池关闭。
    _db = db
}

//获取gorm db对象，其他包需要执行数据库查询的时候，只要通过tools.getDB()获取db对象即可。
//不用担心协程并发使用同样的db对象会共用同一个连接，db对象在调用他的方法的时候会从数据库连接池中获取新的连接
func GetDB() *gorm.DB {
	return _db
}
```

使用例子：

```go
package main
//导入tools包
import tools

func main() {
   db := tools.GetDB()
	//定义一个用户，并初始化数据
	u := User{
		Username:   "瘦子",
		Password:   "123456",
		CreateTime: time.Now().Unix(),
	}

	db.AutoMigrate(&u)
	//一般项目中我们会类似下面的写法，通过Error对象检测，插入数据有没有成功，如果没有错误那就是数据写入成功了。
	if err := db.Create(&u).Error; err != nil {
		fmt.Println("插入失败", err)
		return
	}
}
```

> 注意：使用连接池技术后，千万不要使用完db后调用db.Close关闭数据库连接，这样会导致整个数据库连接池关闭，导致连接池没有可用的连接。





## 二、使用gorm链式操作函数查询数据

gorm查询主要由以下几个部分的函数组成，这些函数可以串起来组合sql语句，使用起来类似编写sql语句的习惯。

### 1.query

执行查询的函数，gorm提供下面几个查询函数：

- **Take**
  查询一条记录

```go
例子：

//定义接收查询结果的结构体变量
User := User{}

//等价于：SELECT * FROM `Users`   LIMIT 1  
db.Take(&User)
```

- **First**
  查询一条记录，根据主键ID排序(**正序**)，返回第一条记录

```go
例子：

//等价于：SELECT * FROM `Users`   ORDER BY `Users`.`id` ASC LIMIT 1    
db.First(&User)
```

- **Last**
  查询一条记录, 根据主键ID排序(**倒序**)，返回第一条记录

```go
//等价于：SELECT * FROM `Users`   ORDER BY `Users`.`id` DESC LIMIT 1   
//语义上相当于返回最后一条记录
db.Last(&User)
```

- **Find**
  查询多条记录，Find函数返回的是一个数组

```go
//因为Find返回的是数组，所以定义一个商品数组用来接收结果
var Users []User

//等价于：SELECT * FROM `Users`
db.Find(&Users)
```

- **Pluck**
  查询一列值

```go
//商品标题数组
var titles []string

//返回所有商品标题
//等价于：SELECT title FROM `Users`
//Pluck提取了title字段，保存到titles变量
//这里Model函数是为了绑定一个模型实例，可以从里面提取表名。
db.Model(&User{}).Pluck("title", &titles)
```

#### 查询错误处理

users

```go
例子：
if err := db.Take(&User).Error; err != nil {
    fmt.Println("查询失败", err)
}
```

**错误特例：**

当 First、Last、Take 方法找不到记录时，GORM 会返回 ErrRecordNotFound 错误。

在实际开发中查询不到数据，我们不一定会当成错误处理, gorm库通过下面办法检测Error是不是查询不到数据.

```go
例子:
err := db.Take(&User).Error
if errors.Is(err, gorm.ErrRecordNotFound) {
    fmt.Println("查询不到数据")
} else if err != nil {
//如果err不等于record not found错误，又不等于nil，那说明sql执行失败了。
	fmt.Println("查询失败", err)
}
```

### 2.where

上面的例子都没有指定where条件，这里介绍下如何设置where条件，主要通过db.Where函数设置条件.
函数说明：
*db.Where(query interface{}, args ...interface{})*

参数说明:

| 参数名 | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| query  | sql语句的where子句, where子句中使用问号(?)代替参数值，则表示通过args参数绑定参数 |
| args   | where子句绑定的参数，可以绑定多个参数                        |

```go
例子1:
//等价于: SELECT * FROM `Users`  WHERE (id = '10') LIMIT 1
//这里问号(?), 在执行的时候会被10替代
db.Where("id = ?", 10).Take(&User)

//例子2:
// in 语句 
//等价于: SELECT * FROM `Users`  WHERE (id in ('1','2','5','6')) LIMIT 1 
//args参数传递的是数组
db.Where("id in (?)", []int{1,2,5,6}).Take(&User)

//例子3:
//等价于: SELECT * FROM `Users`  WHERE (create_time >= '2018-11-06 00:00:00' and create_time <= '2018-11-06 23:59:59')
//这里使用了两个问号(?)占位符，后面传递了两个参数替换两个问号。
db.Where("create_time >= ? and create_time <= ?", "2018-11-06 00:00:00", "2018-11-06 23:59:59").Find(&Users)

//例子4:
//like语句
//等价于: SELECT * FROM `Users`  WHERE (title like '%可乐%')
db.Where("title like ?", "%可乐%").Find(&Users)
```

### 3.select

设置select子句, 指定返回的字段

```go
//例子1:
//等价于: SELECT id,title FROM `Users`  WHERE `Users`.`id` = '1' AND ((id = '1')) LIMIT 1  
db.Select("id,title").Where("id = ?", 1).Take(&User)

//这种写法是直接往Select函数传递数组，数组元素代表需要选择的字段名
db.Select([]string{"id", "title"}).Where("id = ?", 1).Take(&User)


//例子2:
//可以直接书写聚合语句
//等价于: SELECT count(*) as total FROM `Users`
total := []int{}

//Model函数，用于指定绑定的模型，这里生成了一个User{}变量。目的是从模型变量里面提取表名，Pluck函数我们没有直接传递绑定表名的结构体变量，gorm库不知道表名是什么，所以这里需要指定表名
//Pluck函数，主要用于查询一列值
db.Model(&User{}).Select("count(*) as total").Pluck("total", &total)

fmt.Println(total[0])
```

### 4.order

设置排序语句，order by子句

```go
//例子:
//等价于: SELECT * FROM `Users`  WHERE (create_time >= '2018-11-06 00:00:00') ORDER BY create_time desc
db.Where("create_time >= ?", "2018-11-06 00:00:00").Order("create_time desc").Find(&Users)
```

### 5.limit & Offset

设置limit和Offset子句，分页的时候常用语句。

```go
//等价于: SELECT * FROM `Users` ORDER BY create_time desc LIMIT 10 OFFSET 0 
db.Order("create_time desc").Limit(10).Offset(0).Find(&Users)
```

### 6.count

Count函数，直接返回查询匹配的行数。

```go
//例子:
var total int64 = 0
//等价于: SELECT count(*) FROM `Users` 
//这里也需要通过model设置模型，让gorm可以提取模型对应的表名
db.Model(User{}).Count(&total)
fmt.Println(total)
```

### 7.分组

设置group by子句

```go
//例子:
//统计每个商品分类下面有多少个商品
//定一个Result结构体类型，用来保存查询结果
type Result struct {
    Type  int
    Total int
}

var results []Result
//等价于: SELECT type, count(*) as  total FROM `Users` GROUP BY type HAVING (total > 0)
db.Model(User{}).Select("type, count(*) as  total").Group("type").Having("total > 0").Scan(&results)

//scan类似Find都是用于执行查询语句，然后把查询结果赋值给结构体变量，区别在于scan不会从传递进来的结构体变量提取表名.
//这里因为我们重新定义了一个结构体用于保存结果，但是这个结构体并没有绑定Users表，所以这里只能使用scan查询函数。
```

> 提示：Group函数必须搭配Select函数一起使用

## 三、直接执行sql语句

对于复杂的查询，例如多表连接查询，我们可以直接编写sql语句，然后执行sql语句。
gorm通过db.Raw设置sql语句，通过Scan执行查询。

```gherkin
例子:
sql := "SELECT type, count(*) as  total FROM `Users` where create_time > ? GROUP BY type HAVING (total > 0)"
//因为sql语句使用了一个问号(?)作为绑定参数, 所以需要传递一个绑定参数(Raw第二个参数).
//Raw函数支持绑定多个参数
db.Raw(sql, "2018-11-06 00:00:00").Scan(&results)
fmt.Println(results)
```

# GORM更新数据

## 二、gorm更新记录常用方法

### 1. Save

用于保存模型变量的值。

> 提示: 相当于根据主键id，更新所有模型字段值。

```go
user := User{}
//先查询一条记录, 保存在模型变量user
//等价于: SELECT * FROM `users`  WHERE (id = '2') LIMIT 1
db.Where("id = ?", 2).Take(&user)

//修改user模型的值
user.Price = 100

//等价于: UPDATE `users` SET `title` = '可乐', `type` = '0', `price` = '100', `stock` = '26', `create_time` = '2018-11-06 11:12:04'  WHERE `users`.`id` = '2'
db.Save(&user)
```

### 2. Update

更新单个字段值

```go
//例子1:
//更新user模型对应的表记录
//等价于: UPDATE `users` SET `price` = '25'  WHERE `users`.`id` = '2'
db.Model(&user).Update("price", 25)
//通过user模型的主键id的值作为where条件，更新price字段值。


//例子2:
//上面的例子只是更新一条记录，如果我们要更全部记录怎么办？
//等价于: UPDATE `users` SET `price` = '25'
db.Model(&User{}).Update("price", 25)
//注意这里的Model参数，使用的是User{}，新生成一个空白的模型变量，没有绑定任何记录。
//因为User{}的id为空，gorm库就不会以id作为条件，where语句就是空的

//例子3:
//根据自定义条件更新记录，而不是根据主键id
//等价于: UPDATE `users` SET `price` = '25'  WHERE (create_time > '2018-11-06 20:00:00') 
db.Model(&User{}).Where("create_time > ?", "2018-11-06 20:00:00").Update("price", 25)
```

### 3. Updates

更新多个字段值

```go
//例子1：
//通过结构体变量设置更新字段
updataUser := User{
		Price:120,
		Title:"柠檬雪碧",
	}

//根据user模型更新数据库记录
//等价于: UPDATE `users` SET `price` = '120', `title` = '柠檬雪碧'  WHERE `users`.`id` = '2'
//Updates会忽略掉updataUser结构体变量的零值字段, 所以生成的sql语句只有price和title字段。
db.Model(&user).Updates(&updataUser)

//例子2:
//根据自定义条件更新记录，而不是根据模型id
updataUser := User{
		Stock:120,
		Title:"柠檬雪碧",
	}
	
//设置Where条件，Model参数绑定一个空的模型变量
//等价于: UPDATE `users` SET `stock` = '120', `title` = '柠檬雪碧'  WHERE (price > '10') 
db.Model(&User{}).Where("price > ?", 10).Updates(&updataUser)

//例子3:
//如果想更新所有字段值，包括零值，就是不想忽略掉空值字段怎么办？
//使用map类型，替代上面的结构体变量

//定义map类型，key为字符串，value为interface{}类型，方便保存任意值
data := make(map[string]interface{})
data["stock"] = 0 //零值字段
data["price"] = 35

//等价于: UPDATE `users` SET `price` = '35', `stock` = '0'  WHERE (id = '2')
db.Model(&User{}).Where("id = ?", 2).Updates(data)
```

> 提示： 通过结构体变量更新字段值, gorm库会忽略零值字段。就是字段值等于0, nil, "", false这些值会被忽略掉，不会更新。如果想更新零值，可以使用map类型替代结构体。

### 4. 更新表达式

*UPDATE `users` SET `stock` = `stock` + 1 WHERE id = '2'*
这样的带计算表达式的更新语句gorm怎么写？

gorm提供了Expr函数用于设置表达式

```go
//等价于: UPDATE `users` SET `stock` = stock + 1  WHERE `users`.`id` = '2'
db.Model(&user).Update("stock", gorm.Expr("stock + 1"))
```

# GORM删除数据

## 1. 删除模型数据

删除模型数据一般用于删除之前查询出来的模型变量绑定的记录。
*用法：db.Delete(模型变量)*

```go
//例子：
user := user{}
//先查询一条记录, 保存在模型变量user
//等价于: SELECT * FROM `users`  WHERE (id = '2') LIMIT 1
db.Where("id = ?", 2).Take(&user)

//删除user对应的记录，通过主键Id标识记录
//等价于： DELETE from `users` where id=2;
db.Delete(&user)
```

## 2. 根据Where条件删除数据

*用法：db.Where(条件表达式).Delete(空模型变量指针)*

```go
//等价于：DELETE from `users` where (`type` = 5);
db.Where("type = ?", 5).Delete(&user{})
```

> 提示：这里Delete函数需要传递一个空的模型变量指针，主要用于获取模型变量绑定的表名。 不能传递一个非空的模型变量，否则就变成删除指定的模型数据，自动在where语句加上类似id = 2这样的主键约束条件。

# GORM事务处理



## 自动事务

通过db.Transaction函数实现事务，如果闭包函数返回错误，则回滚事务。

```
db.Transaction(func(tx *gorm.DB) error {
  // 在事务中执行一些 db 操作（从这里开始，您应该使用 'tx' 而不是 'db'）
  if err := tx.Create(&Animal{Name: "Giraffe"}).Error; err != nil {
    // 返回任何错误都会回滚事务
    return err
  }

  if err := tx.Create(&Animal{Name: "Lion"}).Error; err != nil {
    return err
  }

  // 返回 nil 提交事务
  return nil
})
```

## 手动事务

在开发中经常需要数据库事务来保证多个数据库写操作的原子性。例如电商系统中的扣减库存和保存订单。
gorm事务用法：

```
// 开启事务
tx := db.Begin()

//在事务中执行数据库操作，使用的是tx变量，不是db。

//库存减一
//等价于: UPDATE `users` SET `stock` = stock - 1  WHERE `users`.`id` = '2' and stock > 0
//RowsAffected用于返回sql执行后影响的行数
rowsAffected := tx.Model(&user).Where("stock > 0").Update("stock", gorm.Expr("stock - 1")).RowsAffected
if rowsAffected == 0 {
    //如果更新库存操作，返回影响行数为0，说明没有库存了，结束下单流程
    //这里回滚作用不大，因为前面没成功执行什么数据库更新操作，也没什么数据需要回滚。
    //这里就是举个例子，事务中可以执行多个sql语句，错误了可以回滚事务
    tx.Rollback()
    return
}
err := tx.Create(保存订单).Error

//保存订单失败，则回滚事务
if err != nil {
    tx.Rollback()
} else {
    tx.Commit()
}
```

# GORM 关联查询-属于



GORM的关联查询（又叫连表查询）中的**属于**关系是**一对一**关联关系的一种，通常用于描述一个Model属于另外一个Model。

**例子**

存在一个users表和profiles表：

- users - 用户表
- profiles - 用户个性化信息表

他们之间存在一对一关系，每一个用户都有自己的个性化数据，那么可以说每一条profiles记录都**属于**某个用户。

```
// 用户表 - 下面使用go struct表示表结构
type User struct {
  // 继承gorm的基础Model,里面默认定义了ID、CreatedAt、UpdatedAt、DeletedAt 4个字段
  gorm.Model
  Name string
}

// 个性化信息表
type Profile struct {
  gorm.Model
  UserID uint // 外键
  // 定义user属性关联users表，默认情况使用 类型名 + ID 组成外键名，在这里UserID属性就是外键
  User   User
  Name   string
}
```

## 外键

在关联查询中必须包含外键，默认gorm使用（关联属性类型 + 主键）组成外键名，如上面的例子User + ID 组成UserID，UserID就作为Profile的外键。

也可以通过下面方式修改外键

```
type Profile struct {
  gorm.Model
  Name      string
  User      User `gorm:"foreignkey:UserRefer"` //使用 UserRefer 作为外键
  UserRefer uint // 外键
}
```

## 关联外键

在连表操作中，除了外键，还需要一个关联外键组成一对才能完成连表，例如上面的例子，Profile中UserID属性作为外键，它和User中的ID进行关联，这里User的ID就是关联外键。

默认GORM使用主键作为关联外键，所以上面的User使用ID作为关联外键。

也可以自定义关联外键

```
type User struct {
  gorm.Model
  Refer string // 关联外键
  Name string
}

type Profile struct {
  gorm.Model
  Name      string
  User      User `gorm:"references:Refer"` // 使用 Refer 作为关联外键
  UserRefer string
}
```

## 属于关联查询例子

```
profile := Profile{}
// 查询用户个性数据
//自动生成sql： SELECT * FROM `profiles` WHERE id = 1 AND `profiles`.`deleted_at` IS NULL LIMIT 1
db.Where("id = ?", 1).Take(&profile)
fmt.Println(profile)

user := User{}
// 通过Profile关联查询user数据, 查询结果保存到user变量
db.Model(&profile).Association("User").Find(&user)
fmt.Println(user)
// 自动生成sql: SELECT * FROM `users` WHERE `users`.`id` = 1 // 1 就是user的 ID，已经自动关联
```

# GORM 关联查询 - 一对一关系（has one）

GORM的关联查询（又叫连表查询）中的Has One关系是**一对一**关联关系的一种，通常用于描述一个Model拥有另外一个Model。

> 提示：Has one很像属于（belongs to）关系，都是一对一关系，区别是Has One关系和属于关系，持有关联Model属性的关系是相反的，例如：A 关联 B，Has One关系通常是A 结构体持有B属性， belongs to关系则是B结构体持有A

**例子**

每一个用户都有一张信用卡，下面以Go Struct表示表结构

```
// 信用卡
type CreditCard struct {
  // 继承gorm的基础Model,里面默认定义了ID、CreatedAt、UpdatedAt、DeletedAt 4个字段
  gorm.Model
  Number   string
  UserID   uint // 外键
}

// 用户
type User struct {
  gorm.Model
  CreditCard   CreditCard // 持有信用卡属性（关联信用卡）
}
```

## 外键

关联查询必须包含外键，默认情况下Has One关系的外键由持有关联属性的**类型名 + 主键** 组成外键名，如上例，User关联CreditCard的外键就是User + ID = UserID。

通过下面方式可以自定义外键

```
type CreditCard struct {
  gorm.Model
  Number   string
  UserName string // 外键
}

type User struct {
  gorm.Model
  // 通过标签将外键定义为：UserName
  CreditCard CreditCard `gorm:"foreignkey:UserName"`
}
```

## 关联外键

默认情况下，保存User的时候，会自动将User的主键保存到外键UserID中，关联查询的时候，也会使用外键和关联外键进行关联进行查询，这里User的ID就是关联外键。

自定义关联外键的例子

```
type CreditCard struct {
  gorm.Model
  Number string
  UID    string
}

type User struct {
  gorm.Model
  Name       `sql:"index"` // 关联外键
  // 自定义关联外键为：name
  CreditCard CreditCard `gorm:"foreignkey:uid;references:name"`
}
```

## 关联查询例子

```
user := User{}
// 查询用户数据
//自动生成sql： SELECT * FROM `users`  WHERE (username = 'tizi365') LIMIT 1
db.Where("username = ?", "tizi365").First(&user)
fmt.Println(user)

var card CreditCard
////自动生成SQL： SELECT * FROM credit_cards WHERE user_id = 123; // 123 自动从user的ID读取
// 关联查询的结果会填充到card变量
db.Model(&user).Association("CreditCard").Find(&card)
```

# GORM 关联查询 - 一对多关系（Has Many）



GORM的关联查询（又叫连表查询）中的Has Many关系是**一对**多关联关系，通常用于描述一个Model拥有多个Model。

**例子**

一个**用户**拥有多张**信用卡**，下面以Go Struct表示表结构

```
// 用户
type User struct {
  // 继承gorm的基础Model,里面默认定义了ID、CreatedAt、UpdatedAt、DeletedAt 4个字段
  gorm.Model
  CreditCards []CreditCard // 一对多关联属性，表示多张信用卡
}

// 信用卡
type CreditCard struct {
  gorm.Model
  Number   string // 卡号
  UserID  uint // 默认外键， 用户Id
}
```

## 外键

默认情况下，GORM使用持有关联属性的 **类型名 + 主键ID** 作为外键名。

如上例，User使用User + ID = UserID 作为外键名。

自定义外键

```
type User struct {
  gorm.Model
  // 通过标签，将外键定义为：UserRefer
  CreditCards []CreditCard `gorm:"foreignkey:UserRefer"`
}

type CreditCard struct {
  gorm.Model
  Number    string
  UserRefer uint // 新定义的外键名
}
```

## 关联外键

外键和关联外键都是成对出现的，默认情况GORM使用主键ID，作为关联外键。

主键ID，默认为ID，如上面的例子，使用User的ID作为关联外键

自定义关联外键

```
type User struct {
  gorm.Model
  MemberNumber string // 关联外键字段
  // 使用references定义关联外键名
  CreditCards  []CreditCard `gorm:"foreignkey:UserMemberNumber;references:MemberNumber"`
}

type CreditCard struct {
  gorm.Model
  Number           string
  UserMemberNumber string // 外键字段
}
```

## 一对多关联查询例子

```
user := User{}
// 查询用户数据
//自动生成sql： SELECT * FROM `users`  WHERE (username = 'tizi365') LIMIT 1
db.Where("username = ?", "tizi365").First(&user)
fmt.Println(user)

//自动生成SQL： SELECT * FROM emails WHERE user_id = 111; // 111 是user的主键ID值
// 关联查询的结果，保存到user.CreditCard属性
db.Model(&user).Association("CreditCard").Find(&user.CreditCard)
```

# GORM 关联查询预加载 Preloading



默认情况下GORM因为性能问题，不会自动加载关联属性的值，gorm通过Preload函数支持预加载（Eager loading）关联数据，下面介绍预加载关联数据的方法。

## 预加载例子

```
// 用户表
type User struct {
  gorm.Model
  Username string
  Orders []Orders // 关联订单，一对多关联关系
}
// 订单表
type Orders struct {
  gorm.Model
  UserID uint // 外键字段 
  Price float64
}

// 预加载Orders字段值，Orders字段是User的关联字段
db.Preload("Orders").Find(&users)
// 下面是自动生成的SQL，自动完成关联查询
//// SELECT * FROM users;
//// SELECT * FROM orders WHERE user_id IN (1,2,3,4);


// Preload第2，3个参数支持设置SQL语句条件和绑定参数
db.Preload("Orders", "state NOT IN (?)", "cancelled").Find(&users)
// 自动生成的SQL如下
//// SELECT * FROM users;
//// SELECT * FROM orders WHERE user_id IN (1,2,3,4) AND state NOT IN ('cancelled');

// 通过组合Where函数一起设置SQL条件
db.Where("state = ?", "active").Preload("Orders", "state NOT IN (?)", "cancelled").Find(&users)
// 自动生成的SQL如下
//// SELECT * FROM users WHERE state = 'active';
//// SELECT * FROM orders WHERE user_id IN (1,2) AND state NOT IN ('cancelled');

// 预加载Orders、Profile、Role多个关联属性
// ps: 预加载字段，必须是User的属性
db.Preload("Orders").Preload("Profile").Preload("Role").Find(&users)
//// SELECT * FROM users;
//// SELECT * FROM orders WHERE user_id IN (1,2,3,4); // has many
//// SELECT * FROM profiles WHERE user_id IN (1,2,3,4); // has one
//// SELECT * FROM roles WHERE id IN (4,5,6); // belongs to
```

## 自动预加载

```
type User struct {
  gorm.Model
  Name       string
  CompanyID  uint
  Company    Company `gorm:"PRELOAD:false"` // 通过标签属性关闭预加载
  Role       Role                           // 默认开启预加载特性
}
// 通过Set设置gorm:auto_preload属性，开启自动预加载，查询的时候才会自动完成关联查询
db.Set("gorm:auto_preload", true).Find(&users)
```

## 嵌套预加载

```
// 预加载User.Orders.OrderItems属性值，使用点连接嵌套属性即可
db.Preload("Orders.OrderItems").Find(&users)
db.Preload("Orders", "state = ?", "paid").Preload("Orders.OrderItems").Find(&users)
```

# GORM 自动建表（Migration特性）



GORM支持Migration特性，支持根据Go Struct结构自动生成对应的表结构。

> 注意：GORM 的AutoMigrate函数，仅支持建表，不支持修改字段和删除字段，避免意外导致丢失数据。

## 自动建表

通过AutoMigrate函数可以快速建表，如果表已经存在不会重复创建。

```
// 根据User结构体，自动创建表结构.
db.AutoMigrate(&User{})

// 一次创建User、Product、Order三个结构体对应的表结构
db.AutoMigrate(&User{}, &Product{}, &Order{})

// 可以通过Set设置附加参数，下面设置表的存储引擎为InnoDB
db.Set("gorm:table_options", "ENGINE=InnoDB").AutoMigrate(&User{})
```

## Schema方法

### 检测表是否存在

```
// 检测User结构体对应的表是否存在
db.Migrator().HasTable(&User{})

// 检测表名users是否存在
db.Migrator().HasTable("users")
```

### 建表

```
// 根据User结构体建表
db.Migrator().CreateTable(&User{})
```

### 删除表

```
// 删除User结构体对应的表
db.Migrator().DropTable(&User{})

// 删除表名为users的表
db.Migrator().DropTable("users")
```

### 删除字段

```
// 删除User结构体对应表中的description字段
db.Migrator().DropColumn(&User{}, "Name")
```

### 添加索引

```
type User struct {
  gorm.Model
  Name string `gorm:"size:255;index:idx_name,unique"`
}

// 为 Name 字段创建索引
db.Migrator().CreateIndex(&User{}, "Name")
db.Migrator().CreateIndex(&User{}, "idx_name")

// 为 Name 字段删除索引
db.Migrator().DropIndex(&User{}, "Name")
db.Migrator().DropIndex(&User{}, "idx_name")

// 检查索引是否存在
db.Migrator().HasIndex(&User{}, "Name")
db.Migrator().HasIndex(&User{}, "idx_name")

type User struct {
  gorm.Model
  Name  string `gorm:"size:255;index:idx_name,unique"`
  Name2 string `gorm:"size:255;index:idx_name_2,unique"`
}
// 修改索引名
db.Migrator().RenameIndex(&User{}, "Name", "Name2")
db.Migrator().RenameIndex(&User{}, "idx_name", "idx_name_2")
```

## 组合索引

两个字段使用同一个索引名，Migration将创建复合索引，例如：

```
type User struct {
    Name   string `gorm:"index:idx_member"`
    Number string `gorm:"index:idx_member"`
}
```

# GORM 错误处理

下面介绍GORM关于错误的处理方式

## 错误处理

如果在执行SQL查询的时候，出现错误，GORM 会将错误信息保存到 *gorm.DB 的Error字段，我们只要检测Error字段就可以知道是否存在错误。

```
if err := db.Where("name = ?", "tizi365").First(&user).Error; err != nil {
  // 错误处理
}
```

或者

```
if result := db.Where("name = ?", "jinzhu").First(&user); result.Error != nil {
  // 错误处理
}
```

## ErrRecordNotFound error

当 First、Last、Take 方法找不到记录时，GORM 会返回 ErrRecordNotFound 错误。如果发生了多个错误，你可以通过 errors.Is 判断错误是否为 ErrRecordNotFound，例如：

```
// 检查错误是否为 RecordNotFound
err := db.First(&user, 100).Error
errors.Is(err, gorm.ErrRecordNotFound)
```
