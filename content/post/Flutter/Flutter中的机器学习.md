---
title: "Flutter中的机器学习"
date: 2021-08-15T20:18:16+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

对机器学习充满热情？如何在 Flutter 中创建一个使用机器学习模型的应用程序❤

![img](https://miro.medium.com/max/1400/0*m-iT7Pov_v6OhgQF)

我们都知道机器学习是 IT 中最受欢迎的领域之一。因此，让我们将 TensorFlow 与 Flutter 集成，并为我们的应用程序添加机器学习。



为此，我们将使用[pub.dev](https://pub.dev/packages/tflite)上提供的`tflite`包， 我们还将使用`image_picker`从设备获取图像并传递给我们的图像分类器模型。你可以从[pub.dev](https://pub.dev/packages/image_picker)获取这个包。

现在，第一件事情就是你需要的是一个**图像分类模型。**您可以从 google 获取一个或请您的一些朋友为您创建一个。如果您了解机器学习，请随意创建一个模型并以 tflite 的形式导出。为方便[**起见**](https://teachablemachine.withgoogle.com/)，您还可以使用[**Teachable Machine**](https://teachablemachine.withgoogle.com/)**。**在可教机器中，您可以通过上传图像并为每个类提供标签来训练自己的图像分类器模型。训练完成后，需要以**tflite**的形式**导出。**将此模型和标签文件添加到项目的资产文件夹中。

在资产文件夹中添加文件并在 pubspec.yaml 中添加所需的包后，让我们从我们的主要代码开始！

## 第 1 步：加载模型

第一步是在 flutter 应用程序中加载我们的模型。不要忘记在 pubspec.yaml 文件中添加资产。然后，导入 tflite 包并在 initState 方法中加载模型。让我们创建一个方法来加载我们的模型。

```dart
loadModel() async {
    await Tflite.loadModel(
      model: "assets/model_unquant.tflite",
      labels: "assets/labels.txt",
    );
  }
```

该模型需要在我们页面的 initState() 方法中加载。

```
@override
  void initState() {
    super.initState();
    loadModel().then((value) {
      setState((){});
    });
  }
```

## 第 2 步：创建 2 个变量，一个用于图像，另一个用于输出。

```
List _outputs;  
File _image;
```

## 第 3 步：打开图库或相机以使用 `image_picker`

打开相机：

```
Future openCamera() async {
    var image = await ImagePicker.pickImage(
      source: ImageSource.camera,
    );
    setState(() {
      _image = image;
    });
    classifyImage(image);
  }
```

打开图库：

```
Future openGallery() async {
    var image;
    image = await ImagePicker.pickImage(
      source: ImageSource.gallery,
    );
    setState(() {
      _image = image;
    });
    classifyImage(image);
  }
```

您可以看到我们使用了一个被调用的函数`classifyImage`，它获取我们捕获/选择的图像。所以现在让我们定义这个函数。

## 第 4 步：将图像传递给我们的分类器模型

```
classifyImage(File image) async {
    var output = await Tflite.runModelOnImage(
       path: filepath,   // required
       imageMean: 0.0,   // defaults to 117.0
       imageStd: 255.0,  // defaults to 1.0
       numResults: 2,    // defaults to 5
       threshold: 0.2,   // defaults to 0.1
       asynch: true      // defaults to true
    );
    setState(() {
      _outputs = output;
    });
  }
```

## 第五步：打印类名！

一旦模型对图像进行分类，您就可以直接打印类名！

```
Text('${_outputs[0]["label"]}')
```

## 第六步：最后需要在dispose()方法中关闭TFlite模型

```
@override
  void dispose() async {
    await Tflite.close();
    super.dispose();
  }
}
```

呜呜！！恭喜！现在你是 ML & Flutter 专家 😉

希望你喜欢这篇文章！

