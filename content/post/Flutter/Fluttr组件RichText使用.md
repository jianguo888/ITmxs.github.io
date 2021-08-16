---
title: "Fluttr组件RichText使用"
date: 2021-08-16T08:48:19+08:00
draft: true
---





```dart

Widget frItem(String title, String number, Function onTap) {
  return InkWell(
    onTap: () {
      onTap();
    },
    child: RichText(
      text: TextSpan(children: [
        TextSpan(
            text: title,
            style: TextStyle(color: Color(0xFF8A8D99), fontSize: 24.w)),

            
        TextSpan(
            text: number,
            style: TextStyle(
              fontSize: 28.w,
              fontWeight: FontWeight.bold,
              color: Color(0xFF2E2F33),
            ))
      ]),
    ),
  );
}

```

