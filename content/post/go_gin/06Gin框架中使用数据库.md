---
title: "Gin框架中使用数据库"
date: 2021-09-05T23:10:11+08:00
draft: false
tags: ["go","gin"]
categories: ["go","gin"]
 
---

# Gin框架中使用数据库

在使用gin框架进行开发项目时，会使用到数据库进行数据存储。本节课我们来学习如何使用gin框架链接和操作数据库。

### MySQL数据库的安装

在使用MySQL数据库之前，首先要安装MySQL数据库。

MySQL官方下载链接：https://dev.mysql.com/downloads/mysql/

国内镜像下载链接：http://mirrors.sohu.com/在国内镜像列表中，有windows，macOS，linux全部的版本。可以自行选择下载。

### Gin链接和使用MySQL数据库

- 1、安装MySQL驱动
  想要在Gin中操作MySQL，首先要有MySQL驱动程序。该驱动程序需要下载：

  ```go
  go get "github.com/go-sql-driver/mysql"
  ```

- 2、创建数据库
  在已经安装好的MySQL数据库中，创建一个新的数据库，如：ginsql。
  在终端下登录mysql数据库，并创建ginsql数据库，具体命令如下:

  ```go
  mysql -uroot -p
  create database ginsql;
  ```

  ![image-20200812095007657](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200812095007657.png)
  

使用第一条命令进行登录，需要输入密码。
  使用create database ginsql即可创建一个名字为ginsql的新的数据库。

- 3、在gin中使用编程语言进行连接数据库
  在gin中连接和操作mysql与在其他框架中连接操作mysql没有什么区别，分为主要的几个步骤：

  - a、引入mysql驱动程序：使用import将mysql驱动默认引入，具体语法如下：

    ```go
    import _ "github.com/go-sql-driver/mysql"
    ```

  - b、拼接链接字符：在程序中链接mysql，需要按照一定的规则进行用户名，密码等信息的组合。

    ```go
    connStr := "root:12345678@tcp(127.0.0.1:3306)/ginsql"
    ```

  - c、使用sql.Open创建数据库连接

    ```go
    db, err := sql.Open("mysql", connStr)
        if err != nil {
            log.Fatal(err.Error())
            return
        }
    ```

- 4、操作数据库
  正确创建了数据库链接后，可以执行db对象支持的相关数据库操作,比如：创建表结构，查询数据，插入数据，删除操作等。举例如下：

  - a、 创建数据库表

    ```go
      _, err = db.Exec("create table person(" +
    "id int auto_increment primary key," +
    "name varchar(12) not null," +
    "age int default 1" +
    ");")
    if err != nil {
    log.Fatal(err.Error())
    return
    }
    ```

    ![image-20200812100258990](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200812100258990.png)

  - b、向数据库中插入数据
  
    ```go
      _, err = db.Exec("insert into person(name,age) "+
    "values(?,?);", "Lily", 15)
    if err != nil {
    log.Fatal(err.Error())
    return
    } else {
    fmt.Println("数据插入成功")
  }
    ```

  - c、查询数据库记录
  
    ![image-20200812103258674](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200812103258674.png)
    
    ```go
    rows, err := db.Query("select id,name,age from person")
    if err != nil {
    log.Fatal(err.Error())
    return
    }
    scan:
    if rows.Next() {
    //columns, err := rows.Columns()
    //fmt.Println(columns)
    person := new(Person)
    err = rows.Scan(&person.Id, &person.Name, &person.Age)
    if err != nil {
    log.Fatal(err.Error())
    return
    }
    fmt.Println(person.Id, person.Name, person.Age)
    goto scan
    }
    ```

全部源码 

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"log"
)
func main() {
	//1.链接数据库
	connStr := "root:123456@tcp(127.0.0.1:3306)/ginsql"
	db, err := sql.Open("mysql", connStr)
	if err != nil {
		log.Fatal(err.Error())
		return
	}
	//创建数据表
	_, err = db.Exec("create table person(" +
		"id int auto_increment primary key," +
		"name varchar(12) not null," +
		"age int default 1" +
		");")
	if err != nil {
		log.Fatal(err.Error())
		return
	}else{
		fmt.Printf("数据库表创建成功")
	}
	//插入数据到 数据库中
	_, err = db.Exec("insert into person(name,age) "+
		"values(?,?);", "itmxs", 18)
	if err != nil {
		log.Fatal(err.Error())
		return
	} else {
		fmt.Println("数据插入成功")
	}
	//查询数据库
	rows, err := db.Query("select id,name,age from person")
	if err != nil {
		log.Fatal(err.Error())
		return
	}
scan:
	if rows.Next() {
		//columns, err := rows.Columns()
		//fmt.Println(columns)
		person := new(Person)
		err = rows.Scan(&person.Id, &person.Name, &person.Age)
		if err != nil {
			log.Fatal(err.Error())
			return
		}
		fmt.Println(person.Id, person.Name, person.Age)
		goto scan
	}
}
type Person struct {
	Id int
	Name string
	Age int

}
```

