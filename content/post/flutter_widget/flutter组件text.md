---
title: "Flutter组件text"
date: 2021-08-19T13:02:25+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

https://gitee.com/itmxs/flutter_native



```dart
Text(
  "Hello :)",
  style: TextStyle(
      fontSize: 60.0,
      fontWeight: FontWeight.bold,
      color: Colors.blue,
      shadows: [
        Shadow(
            color: Colors.indigo,
            offset: Offset(0.0, 3.0),
            blurRadius: 3.0),
      ]),
);
```



```dart
Widget neumorphism(BuildContext context) {
  final elevation = 3.0;
  return Text(
    "Hello :)",
    style: TextStyle(
        fontSize: 60.0,
        fontWeight: FontWeight.bold,
        color: Colors.grey.shade50,
        shadows: [
          Shadow(
              color: Colors.grey.shade300,
              offset: Offset(3.0, 3.0),
              blurRadius: elevation),
          Shadow(
              color: Colors.white,
              offset: Offset(-3.0, 3.0),
              blurRadius: elevation),
        ]),
  );
}
```



```dart
return PhysicalModel(
  color: Colors.lightBlue,
  elevation: 3.5,
  shape: BoxShape.rectangle,
  child: SizedBox.fromSize(
    size: const Size.square(100.0),
  ),
);
```



```dart
return AnimatedPhysicalModel(
  child: GestureDetector(
    onTap: () => ..., // change elevation here
    child: SizedBox.fromSize(
      size: const Size.square(100.0),
    ),
  ),
  shape: BoxShape.rectangle,
  elevation: elevation,
  borderRadius: BorderRadius.circular(12.0),
  color: Colors.amber,
  shadowColor: Colors.deepOrange,
  duration: Duration(milliseconds: 500),
);
```



```dart
BoxDecoration(
  borderRadius: BorderRadius.circular(12.0),
  shape: BoxShape.rectangle,
  color: Colors.lightBlue,
  boxShadow: [
    BoxShadow(
        color: Colors.blue.shade700,
        spreadRadius: 1.0,
        blurRadius: 10.0,
        offset: Offset(3.0, 3.0))
  ],
);
```







```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  constraints: BoxConstraints.expand(width: 100.0, height: 100.0),
  decoration: decorationsList[index],
  child: Icon(Icons.play_arrow, color: Colors.white,),
)
```



```dart
BoxDecoration(
  borderRadius: BorderRadius.circular(6.0),
  color: Colors.grey.shade50,
  shape: BoxShape.rectangle,
  boxShadow: [
    BoxShadow(
        color: Colors.grey.shade300,
        spreadRadius: 0.0,
        blurRadius: elevation,
        offset: Offset(3.0, 3.0)),
    BoxShadow(
        color: Colors.grey.shade400,
        spreadRadius: 0.0,
        blurRadius: elevation / 2.0,
        offset: Offset(3.0, 3.0)),
    BoxShadow(
        color: Colors.white,
        spreadRadius: 2.0,
        blurRadius: elevation,
        offset: Offset(-3.0, -3.0)),
    BoxShadow(
        color: Colors.white,
        spreadRadius: 2.0,
        blurRadius: elevation / 2,
        offset: Offset(-3.0, -3.0)),
  ],
);
```



```dart
return PhysicalShape(
  clipper: StarClipper(),
  color: Colors.amber,
  elevation: 4.0,
  shadowColor: Colors.deepOrange,
  child: SizedBox.fromSize(size: Size.square(100.0),),
);
```



```dart
class StarClipper extends CustomClipper<Path> {

  @override
  bool shouldReclip(covariant CustomClipper oldClipper) => false;

  @override
  Path getClip(Size size) => ShapeUtils.createStar(size);

}
```



```dart
return CustomPaint(
  size: Size.square(100.0),
  willChange: false,
  isComplex: true,
  painter: ShadowedShapePainter(
      shape: ShapeUtils.createStar(Size.square(100.0)),
      shapeColor: Colors.amber,
      shadows: [
        Shadow(
          offset: Offset(2.5, 2.5),
          blurRadius: 6.0,
          color: Colors.deepOrange.withOpacity(0.5),
        )
      ]),
);
```



```dart
class ShadowedShapePainter extends CustomPainter {
  final Path shape;
  final Color shapeColor;
  final List<Shadow> shadows;
  final Paint _paint;

  ShadowedShapePainter({this.shape, this.shapeColor, this.shadows})
      : _paint = Paint()
          ..color = shapeColor
          ..style = PaintingStyle.fill
          ..isAntiAlias = true;

  @override
  void paint(Canvas canvas, Size size) {
    canvas.clipRect(Rect.fromLTWH(0.0, 0.0, size.width, size.height));
    shadows.forEach((s) {
      canvas.save();
      canvas.translate(s.offset.dx, s.offset.dy);
      canvas.drawShadow(shape, s.color, sqrt(s.blurRadius), false);
      canvas.restore();
    });
    canvas.drawPath(shape, _paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```



```dart
@override
void paint(Canvas canvas, Size size) {
  canvas.clipRect(Rect.fromLTWH(0.0, 0.0, size.width, size.height));
  shadows.forEach((s) {
    _shadowPaint
      ..color = s.color
      ..maskFilter = MaskFilter.blur(BlurStyle.normal, sqrt(s.blurRadius));
    canvas.save();
    canvas.translate(s.offset.dx, s.offset.dy);
    canvas.drawPath(shape, _shadowPaint);
    canvas.restore();
    // We want to avoid Canvas.drawShadow for neumorphism design,
    // as it draws a greyed shadow !
    //canvas.drawShadow(shape, s.color, sqrt(s.blurRadius), false);
  });
  canvas.drawPath(shape, _paint);
}
```

