---
title: "《在Flutter中制作金融应用》"
subtitle: ""
date: 2021-07-16T20:52:16+08:00
lastmod: 2021-07-16T20:52:16+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
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
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍金融应用程序的 UI

<!--more-->



在本教程中，我将向您展示如何使用 Flutter 和 android studio





```dart
class CreditCard extends StatelessWidget {


  @override
  Widget build(BuildContext context) {
    final bottom = MediaQuery.of(context).viewInsets.bottom;
    return Scaffold(
      resizeToAvoidBottomPadding: false,
      resizeToAvoidBottomInset: false,
      body: SingleChildScrollView(
        padding: EdgeInsets.only(bottom: bottom),
        reverse: true,
        child: SafeArea(
          child: Padding(
            padding: const EdgeInsets.fromLTRB(12.0,12.0,12.0,0),
            child: Column(
              children: <Widget>[
               Card(
                 child: Stack(
                   children: <Widget>[
                     Image.asset("assets/credit_card.png",fit: BoxFit.fill,),
                     Padding(
                       padding: const EdgeInsets.all(18.0),
                       child: Column(
                         mainAxisAlignment: MainAxisAlignment.center,
                         crossAxisAlignment: CrossAxisAlignment.stretch,
                         children: <Widget>[
                           Padding(
                             padding: const EdgeInsets.symmetric(vertical:16.0, horizontal: 8.0),
                             child: Text(
                                 "**** **** **** 6543",
                               style: TextStyle(
                                 fontSize: 28.0,
                                 color: Colors.white
                               ),
                             ),
                           ),
                           SizedBox(
                             height: 20.0,
                           ),
                           Row(
                             children: <Widget>[
                               Column(
                                 children: <Widget>[
                                   Text(
                                     "Expiry",
                                     style: TextStyle(
                                       color: Colors.white,
                                       fontSize: 22.0
                                     ),
                                   ),
                                   SizedBox(
                                     height: 10.0,
                                   ),
                                   Text(
                                     "MM/YY",
                                     style: TextStyle(
                                       color: Colors.white,
                                       fontSize: 18.0,
                                     ),
                                   )
                                 ],
                               ),
                               SizedBox(
                                 width: 50.0,
                               ),
                               Column(
                                 children: <Widget>[
                                   Text(
                                     "CVV",
                                     style: TextStyle(
                                         color: Colors.white,
                                         fontSize: 22.0
                                     ),
                                   ),
                                   SizedBox(
                                     height: 10.0,
                                   ),
                                   Text(
                                     "****",
                                     style: TextStyle(
                                       color: Colors.white,
                                       fontSize: 18.0,
                                     ),
                                   )
                                 ],
                               )
                             ],
                           ),
                           SizedBox(
                             height: 8.0,
                           ),
                           Text(
                             "Card Holder",
                             style: TextStyle(
                               color: Colors.white,
                               fontSize: 28.0
                             ),
                             textAlign: TextAlign.end,

                           ),

                         ],
                       ),
                     ),
                   ],
                 ),
                 elevation: 5.0,
                 clipBehavior: Clip.antiAliasWithSaveLayer,
                 semanticContainer: true,
                 shape: RoundedRectangleBorder(
                   borderRadius: BorderRadius.circular(12.0)
                 ),
               ),
                SizedBox(
                  height: 20.0,
                ),
                Padding(
                  padding: const EdgeInsets.all(8.0),
                  child:Column(
                    children: <Widget>[
                      TextFormField(
                        decoration: InputDecoration(
                          border: OutlineInputBorder(
                            borderRadius: BorderRadius.circular(8.0),
                            borderSide: BorderSide(),
                          ),
                          labelText: "Credit Card Number",
                          labelStyle: TextStyle(
                            fontSize: 18.0
                          )
                        ),
                      ),
                      SizedBox(
                        height: 20.0,
                      ),
                      TextFormField(
                        decoration: InputDecoration(
                            border: OutlineInputBorder(
                              borderRadius: BorderRadius.circular(8.0),
                              borderSide: BorderSide(),
                            ),
                            labelText: "Credit Card Expiry Date",
                            labelStyle: TextStyle(
                                fontSize: 18.0
                            )
                        ),
                      ),
                      SizedBox(
                        height: 20.0,
                      ),
                      TextFormField(
                        decoration: InputDecoration(
                            border: OutlineInputBorder(
                              borderRadius: BorderRadius.circular(8.0),
                              borderSide: BorderSide(),
                            ),
                            labelText: "Credit Card Code",
                            labelStyle: TextStyle(
                                fontSize: 18.0
                            )
                        ),
                      ),
                      SizedBox(
                        height: 20.0,
                      ),
                      TextFormField(
                        decoration: InputDecoration(
                            border: OutlineInputBorder(
                              borderRadius: BorderRadius.circular(8.0),
                              borderSide: BorderSide(),
                            ),
                            labelText: "Credit Card Hodler Name",
                            labelStyle: TextStyle(
                                fontSize: 18.0
                            )
                        ),
                      ),
                      SizedBox(
                        height: 20.0,
                      ),
                      FlatButton(
                        color: Colors.pink,
                        onPressed: (){},
                        shape: StadiumBorder(),
                        child: Padding(
                          padding: const EdgeInsets.symmetric(horizontal: 50.0, vertical: 12.0),
                          child: Text(
                              "Pay",
                            style: TextStyle(
                              color: Colors.white,
                              fontSize: 22.0
                            ),

                          ),
                        ),
                      )
                    ],
                  )
                )
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

