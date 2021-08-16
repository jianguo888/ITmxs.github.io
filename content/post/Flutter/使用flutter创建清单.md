---
title: "使用flutter创建清单"
date: 2021-08-16T23:32:17+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';

List<String> listitems = [
  'Buy Apples',
  'Study Physics',
  'Push one Rank in Apex Legends',
  'Finish the Home Work in Java',
  'Subscribe Am I Worthy Youtube channel',
  'Make sure to dislike the unworthy contents',
];

List<Color> color = [
  Colors.green,
  Colors.red,
  Colors.purple,
  Colors.orange,
  Colors.blue,
  Colors.pink,
];

class StrikeOutPage extends StatefulWidget {
  StrikeOutPage({Key key}) : super(key: key);

  @override
  _StrikeOutPageState createState() => _StrikeOutPageState();
}

class _StrikeOutPageState extends State<StrikeOutPage> {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
          backgroundColor: Colors.white,
          body: Center(
              child: Container(
            height: 550.0,
            width: 440.0,
            decoration: BoxDecoration(
              color: Colors.white,
              borderRadius: BorderRadius.circular(8.0),
              boxShadow: [
                BoxShadow(
                  color: Colors.black12,
                  blurRadius: 20.0,
                ),
              ],
            ),
            child: Column(
              children: [
                Container(
                  height: 120.0,
                  width: double.infinity,
                  margin: EdgeInsets.only(bottom: 15.0),
                  padding: EdgeInsets.all(25.0),
                  decoration: BoxDecoration(
                    color: Colors.grey[850],
                    borderRadius: BorderRadius.vertical(
                      top: Radius.circular(8.0),
                    ),
                  ),
                  alignment: Alignment(-1.0, 0.0),
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        'To Do',
                        style: GoogleFonts.montserrat(
                          color: Colors.white,
                          fontSize: 35.0,
                          fontWeight: FontWeight.w600,
                        ),
                      ),
                      Text(
                        'A List for Today!',
                        style: GoogleFonts.montserrat(
                          color: Colors.white60,
                          fontSize: 16.0,
                          fontWeight: FontWeight.w400,
                        ),
                      ),
                    ],
                  ),
                ),
                Column(
                  children: listitems
                      .map(
                        (text) => CustomCheckList(
                          text,
                          color: color[listitems.indexOf(text)],
                        ),
                      )
                      .toList(),
                ),
              ],
            ),
          ))),
    );
  }
}

class CustomCheckList extends StatefulWidget {
  final String text;
  final Color color;
  CustomCheckList(this.text, {@required this.color});
  @override
  _CustomCheckListState createState() => _CustomCheckListState();
}

class _CustomCheckListState extends State<CustomCheckList> {
  bool isDone = false;

  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Material(
      color: Colors.transparent,
      child: InkWell(
        splashColor: widget.color.withOpacity(0.3),
        hoverColor: widget.color.withOpacity(0.1),
        highlightColor: widget.color.withOpacity(0.2),
        onTap: () {
          setState(() {
            isDone = !isDone;
          });
        },
        child: Container(
          height: 65.0,
          padding: EdgeInsets.symmetric(vertical: 20.0, horizontal: 30.0),
          child: Row(
            children: [
              AnimatedContainer(
                duration: Duration(milliseconds: 375),
                curve: Curves.easeInOutCubic,
                margin: EdgeInsets.only(right: 20.0),
                height: isDone ? 22.0 : 24.0,
                width: isDone ? 22.0 : 24.0,
                decoration: BoxDecoration(
                  borderRadius: BorderRadius.circular(3.0),
                  border: Border.all(
                    color:
                        isDone ? widget.color.withOpacity(0.2) : widget.color,
                    width: 1.5,
                  ),
                ),
                child: AnimatedOpacity(
                  curve: Curves.easeInOutCubic,
                  opacity: isDone ? 1.0 : 0.0,
                  duration: Duration(milliseconds: 375),
                  child: Icon(
                    Icons.check,
                    size: 17.0,
                    color: widget.color,
                  ),
                ),
              ),
              Stack(
                children: [
                  Align(
                    alignment: Alignment(0.0, 0.0),
                    child: AnimatedContainer(
                      duration: Duration(milliseconds: 375),
                      height: 1.5,
                      decoration: BoxDecoration(
                        borderRadius: BorderRadius.circular(10.0),
                        color: widget.color,
                      ),
                      width: isDone ? widget.text.length * 7.5 : 0.0,
                    ),
                  ),
                  Align(
                    alignment: Alignment(0.0, 0.0),
                    child: AnimatedPadding(
                      duration: Duration(milliseconds: 375),
                      padding: EdgeInsets.only(left: isDone ? 0.0 : 10.0),
                      curve: Curves.easeInOutCubic,
                      child: Text(
                        widget.text,
                        style: GoogleFonts.quicksand(
                          fontSize: 14.0,
                          fontWeight: FontWeight.w500,
                          letterSpacing: 0.2,
                          color: isDone ? Colors.black26 : widget.color,
                        ),
                      ),
                    ),
                  ),
                ],
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

