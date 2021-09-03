---
title: "《Flutter获取通讯录好友02》"
subtitle: ""
date: 2021-05-20T10:45:15+08:00
lastmod: 2021-05-20T10:45:15+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","通讯录"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment:
  enable: true
mapbox:
  accessToken: ""
---



> 本文主要介绍Flutter如何获取通讯录好友



<!--more-->



```dart
 import 'package:easy_contact_picker/easy_contact_picker.dart';
 
 List<Contact> _list = new List();
  final EasyContactPicker _contactPicker = new EasyContactPicker();
//获取通讯录好友
  _openAddressBook() async {
    // 申请权限
    Map<Permission, PermissionStatus> permissions =
        await [Permission.contacts].request();

    if (permissions[Permission.contacts] == PermissionStatus.granted) {
      return _getContactData();
    } else {
      return Fluttertoast.showToast(msg: "获取手机通讯录失败");
    }
  }

  _getContactData() async {
    List<Contact> list = await _contactPicker.selectContacts();
    setState(() {
      _list = list;
      print("--------${_list.length}");
    });
  }
  
  
  
  Widget _getContectItem(Contact contact) {
    return Container(
      height: 80,
      padding: EdgeInsets.only(left: 13),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: <Widget>[
          Text(contact.fullName),
          Text(
            contact.phoneNumber,
            style: TextStyle(
              color: Colors.grey,
            ),
          ),
          // Text(contact.firstLetter),
        ],
      ),
    );
  }
```


