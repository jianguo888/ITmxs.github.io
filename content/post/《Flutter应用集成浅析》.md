---
title: "《Flutter应用集成浅析》"
subtitle: ""
date: 2021-07-31T21:18:50+08:00
lastmod: 2021-07-31T21:18:50+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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



> 本文主要简单分析下 Flutter 如何集成到现有 Android 应用中。

<!--more-->





# 前言

截止目前 Flutter (1.12.13+hotfix.5)，集成到 Android 应用已经非常简单了。

> Flutter can be embedded into your existing Android application piecemeal, as a source code Gradle subproject or as AARs.

无非两种集成方式：源码集成或 AAR 产物集成。，这里不再赘述，仅做简单总结。

- Android Studio 3.6 + Flutter IntelliJ plugin (version 42及以上) 可以方便快速地自动集成 Flutter 模块
- Flutter 的 Android 引擎使用 Java 8 特性，所以要记得开启 1.8 兼容，否则会提示 “default interface methods” 问题
- [源码集成](https://flutter.dev/docs/development/add-to-app/android/project-setup#option-b---depend-on-the-modules-source-code) 时注意 Android 工程和 Flutter 工程在同级目录下
- 注意编译模式及CPU架构，不匹配的话会出现找不到 `libflutter.so` 的问题
- Flutter 的 AOT(ahead of time) 编译产物只支持 `armeabi-v7a` 和 `arm64-v8a`，x86 下可进行 debug (Just-In-Time, JIT 模式)，但不能安装 release 包

# 产物集成

产物集成相比源码集成更简单。主要步骤如下，具体过程可参考[官方文档](https://flutter.dev/docs/development/add-to-app/android/project-setup#create-a-flutter-module)。

第一步，生成 AAR。

```
cd flutter_project
flutter build aar
```

缺省编译所有模式下的产物，包括 debug, profile 和 release。不想编译 profile 模式产物的话，加上 `--no-profile` 即可。

另外注意编译 AAR 有限制，仅能为 plugin 或 module 工程编译 AAR 产物，否则提示如下错误。

![-w541](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

第二步，添加产物仓库及依赖。

```
repositories {
  // AAR 产物本地仓库
  maven {
      url '/Users/user/wd/xyz/build/host/outputs/repo'
  }
  // Flutter 框架官方仓库
  maven {
      url 'http://download.flutter.io'
  }
}
dependencies {
  debugImplementation 'com.tencent.xyz:flutter_debug:1.0
  profileImplementation 'com.tencent.xyz:flutter_profile:1.0
  releaseImplementation 'com.tencent.xyz:flutter_release:1.0
}
```

![-w700](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

# 源码集成

第一步，创建 Flutter 项目。在 host app 的同级目录下创建 `my_flutter` 项目：

```
$ cd some/path/
$ flutter create -t module --org com.example my_flutter
```

第二步，引入 Flutter 项目并作为模块。在 host app 的 `settings.gradle` 文件中添加如下配置：

```
...
setBinding(new Binding([gradle: this]))                                 // new
evaluate(new File(                                                      // new
  settingsDir.parentFile,                                               // new
  'my_flutter/.android/include_flutter.groovy'                          // new
)) 
include ':my_flutter'
project(':my_flutter').projectDir = new File('../my_flutter')
```

这段配置的作用不妨视作黑魔法，其作用如下：

- 将 `my_flutter` **Flutter Project** 作为名为 `:flutter` 的 **Android Library Module**，引入到当前 **Android Project**
- 查找并保存 `my_flutter` 依赖的 Flutter 插件

第三步，添加对 `:flutter` module 的依赖。在 host app 的 `build.gradle` 中加上以下配置：

```
dependencies {
  implementation project(':flutter')
}
```

以上是手工操作步骤。在 Android 3.6 中可以自动操作，同样也会产生跟上面相同的配置。

Android Studio 3.6 中打开 host app，并新建一个 Flutter Module：

![-w633](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

创建完成后生成的配置如下：

![-w1185](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

可见，无论手工操作还是自动操作，源码集成的关键在于这几个脚本：

- ~~my_flutter/.android/include_flutter.groovy~~ - 黑魔法，用于在 Android 工程引入 Flutter 工程，我们略过
- `my_flutter/.android/Flutter/build.gradle` - 这个脚本决定 Flutter 工程如何构建，它引入 Flutter SDK 中的 `flutter.gradle` 脚本
- `<Flutter SDK>/packages/flutter_tools/gradle/flutter.gradle` - 集成 Flutter 工程的核心

接下来我将分析 `build.gradle` 和 `flutter.gradle` 两个脚本是如何将 Flutter 集成到 Android 应用的。

# build.gradle

先来分析 `my_flutter/.android/Flutter/build.gradle`。

第一步，加载并解析 `.android` 目录下的 `local.properties` 文件。

```
// 加载 .android 目录下的 local.properties 文件
def localProperties = new Properties()
def localPropertiesFile = new File(buildscript.sourceFile.parentFile.parentFile, 'local.properties')
if (localPropertiesFile.exists()) {
    localPropertiesFile.withReader('UTF-8') { reader ->
        localProperties.load(reader)
    }
}

// 解析 flutter.sdk(必选)
def flutterRoot = localProperties.getProperty('flutter.sdk')
if (flutterRoot == null) {
    throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
}

// 解析 flutter.versionCode(可选)
def flutterVersionCode = localProperties.getProperty('flutter.versionCode')
if (flutterVersionCode == null) {
    flutterVersionCode = '1'
}

// 解析 flutter.versionName(可选)
def flutterVersionName = localProperties.getProperty('flutter.versionName')
if (flutterVersionName == null) {
    flutterVersionName = '1.0'
}
```

第二步，引入 `flutter.gradle` 脚本并通过 `flutter` 插件指定 **Flutter Project** 源码位置。`flutter` 插件来自 `flutter.gradle` 脚本中的 `FlutterPlugin`。

```
apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"

...

// 指定的 Flutter 源码路径
flutter {
    source '../..'
}

...
```

# [flutter.gradle](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/gradle/flutter.gradle)

再来看 `<Flutter SDK>/packages/flutter_tools/gradle/flutter.gradle`。`flutter` 插件的具体实现在 `apply()` 方法。

```
// 应用 FlutterPlugin
apply plugin: FlutterPlugin

// 定义名为 flutter 的 FlutterPlugin 插件
class FlutterPlugin implements Plugin<Project> {
    private Project project

    @Override
    void apply(Project project) {
        this.project = project
        // 创建名为 flutter 的 FlutterExtension
        project.extensions.create("flutter", FlutterExtension) 
        // 添加 Flutter Task
        project.afterEvaluate this.&addFlutterTasks
        // 配置 APK。修改 project.android (android插件)的配置
        ...
        // 获取 Flutter 相关信息
        // 添加 Flutter Dependency
        project.android.buildTypes.each this.&addFlutterDependencies
        project.android.buildTypes.whenObjectAdded this.&addFlutterDependencies
    }
}
```

`apply()` 主要流程概括如下：

- 创建 FlutterExtension。这个插件很简单，只包括
  - `source`(源码路径)
  - `target`(Dart入口，通常是 `lib/main.dart`)
- **添加 Flutter Task**
- 配置 APK
  - 针对 Target Platform 生成 multiple APK 或 fat APK
  - 配置 build type，例如是否压缩资源
- 获取 Flutter 相关信息
  - 本地 Flutter SDK 路径
  - `flutter` 命令
  - 引擎版本
  - 引擎路径 (来自 `gradle.properties` 文件的 `local-engine-out` 属性 )
- **添加 Flutter Dependency**

接下来我们挑重点说，

- 添加 Flutter Task
- 添加 Flutter Dependency

简单来说，添加 Flutter Task 是添加一些 Task 用于处理三类 Flutter 相关的资源，库、资源、插件：

- 库 - 库是编译过程中生成jar文件和so文件。库文件应正确地打包到 AAR 或 APK
- 资源 - 资源是 `pubspec.yaml` 文件添加指定的各类资源，如图片、字体等。资源文件应正确地打包到 AAR 或 APK
- 插件 - 插件是 `pubspec.yaml` 文件添加的各种 Dart 库。插件的处理比较麻烦，一是某些插件包含原生Java或OC代码，二是插件之间有依赖关系

而添加 Flutter Dependency 则是将 Flutter 框架(包括引擎)添加为 Android 工程的依赖，具体包括：

- `flutter_embedding.jar` - Flutter Framework，即 `io.flutter.embedding.android.FlutterActivity` 所在的 Java 库
- `libflutter.so` - Flutter 引擎

------

跟以上流程相关的几个辅助方法：

- `useLocalEngine()` - 判断是否使用本地 Flutter 引擎，来自 `gradle.properties` 文件的 `local-engine-repo` 属性
- `getTargetPlatforms` - 获取 Target Platform，来自 `gradle.properties` 文件的 `target-platform` 属性
- `shouldSplitPerAbi()` - 判断是否生成 multiple APK (即针对每种架构生成一个 APK，与之对应的是 fat APK)，来自 `gradle.properties` 文件的 `split-per-abi` 属性，缺省为 `false`
- `getPluginList()` - 解析 `.flutter-plugins` 文件获取插件列表
- `getPluginDependencies()` - 解析 `.flutter-plugins-dependencies` 文件获取插件依赖

------

## 添加 Flutter Task

`addFlutterTasks()` 是最复杂的方法。精简后的代码如下：

```
private void addFlutterTasks(Project project) {
    // 1. 参数检查
    ...
    // 2. 从 `gradle.properties` 获取各种参数
    ...
    // 3. 定义 addFlutterDeps 匿名方法
    def addFlutterDeps = { variant -> 
    
    }
    ...
    // 4. 为所有 applicationVariants 或 libraryVariants 添加 Flutter 依赖
    if (project.android.hasProperty("applicationVariants")) {
        project.android.applicationVariants.all addFlutterDeps
    } else {
        project.android.libraryVariants.all addFlutterDeps
    }
    // 5. 配置插件依赖
    configurePlugins()
}
```

- 第3步和第4步处理库和资源
- 第5步处理插件

### addFlutterDeps

首先看第3步 `addFlutterDeps` 的创建。

```
// 1. 创建 compileTask。FlutterTask 实际是对 flutter build 命令的包装
FlutterTask compileTask = project.tasks.create(name: taskName, type: FlutterTask) {}

// 2. 创建 packFlutterAppAotTask。将第1步中的编译结果打包成 libs.jar 文件
Task packFlutterAppAotTask = project.tasks.create(name: "packLibs${FLUTTER_BUILD_PREFIX}${variant.name.capitalize()}", type: Jar) {

// 3. 将第2步生成的 libs.jar 文件添加为依赖 
addApiDependencies(project, variant.name, project.files {
    packFlutterAppAotTask
}

// 4. 创建 copyFlutterAssetsTask。
Task copyFlutterAssetsTask = project.tasks.create(
    name: "copyFlutterAssets${variant.name.capitalize()}",
    type: Copy,
) { ... }

// Flutter 项目可能作为插件编译或子项目编译(分别对应AAR产物集成和源码集成)
//  a) 当作为插件编译时，编译产物为 AAR
//  b) 当作为子项目编译时，编译产物为 APK

// 作为插件编译和作为子项目编译时对 assets 的处理是不一样的
// 第5步和第6步分别对这两种情况进行处理
boolean isUsedAsSubproject = ...

// 5. 处理作为插件编译时的 assets 拷贝
if (!isUsedAsSubproject) {
    return
}

// 6. 处理作为子项目编译时的 assets 拷贝
// Flutter module included as a subproject in add to app.
Project appProject = project.rootProject.findProject(':app')
appProject.afterEvaluate { ... }
```

`FlutterTask` 继承自 `BaseFlutterTask`。`BaseFlutterTask` 实际是对 `flutter build` 命令的包装，具体包装过程可以参数 [BaseFlutterTask.buildBundle()](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/gradle/flutter.gradle#L819)。

### configurePlugins

再来看 `configurePlugins()` 如何配置插件依赖。仍然分两种情况处理：源码集成和产物集成。代码如下：

```
/**
 * Configures the Flutter plugin dependencies.
 *
 * The plugins are added to pubspec.yaml. Then, upon running `flutter pub get`,
 * the tool generates a `.flutter-plugins` file, which contains a 1:1 map to each plugin location.
 * Finally, the project's `settings.gradle` loads each plugin's android directory as a subproject.
 */
private void configurePlugins() {
    // 第1种情况，源码集成
    // 配置源码集成时的插件
    if (!buildPluginAsAar()) {
        // 1. 将 plugin 工程添加为 Android 工程的依赖
        getPluginList().each this.&configurePluginProject
        // 2. 将 plugin 工程的依赖添加为 Android 工程的依赖
        getPluginDependencies().each this.&configurePluginDependencies
        return
    }
    // 第2种情况，产物集成
    // 配置产物集成时的插件
    
    // 1. 将 plugin 工程的编译输出目录添加为 Android 工程的 maven 库
    project.repositories {
        maven {
            url "${getPluginBuildDir()}/outputs/repo"
        }
    }
    // 2. 将 plugin AAR 产物添加为 Android 工程的依赖
    getPluginList().each { pluginName, pluginPath ->
        configurePluginAar(pluginName, pluginPath, project)
    }
}
```

另外，注释中提到了几个很重要的信息：

> 插件在 pubspec.yaml 中添加。当运行 `flutter pub get` 命令时，工具会生成 `.flutter-plugins` 和 `.flutter-plugins-dependencies` 文件。`.flutter-plugins` 包含每个插件的位置，`.flutter-plugins-dependencies` 包含每个插件的依赖项
>
> Android 项目的 `settings.gradle` 文件会加载每个插件为子工程

以我们的项目为例。`.flutter-plugins` 文件内容如下：

```
# This is a generated file; do not edit or check into version control.
flutter_integration=/Users/abc/flutter/.pub-cache/hosted/pub.dartlang.org/flutter_integration-0.0.1/
flutter_mmkv_cache=/Users/abc/flutter/.pub-cache/hosted/pub.dartlang.org/flutter_mmkv_cache-0.0.2/
path_provider=/Users/abc/flutter/.pub-cache/hosted/pub.dartlang.org/path_provider-1.3.0/
sensors=/Users/abc/flutter/.pub-cache/hosted/pub.dartlang.org/sensors-0.4.1+8/
sqflite=/Users/abc/flutter/.pub-cache/hosted/pub.dartlang.org/sqflite-1.1.7+1/
```

该项目在 Android Studio 中看到的工程结构如下：

![-w249](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

## 添加 Flutter Dependency

`apply()` 的另一个要点是添加 Flutter 依赖，由 `addFlutterDependencies()` 实现。相比添加 Flutter Task，添加 Flutter 依赖则简单得多。

```
class FlutterPlugin implements Plugin<Project> {
    @Override
    void apply(Project project) {
        ...   
        project.android.buildTypes.each this.&addFlutterDependencies
        project.android.buildTypes.whenObjectAdded this.&addFlutterDependencies
    }


    /**
     * addFlutterDependencies() 方法为 Flutter 工程添加 embedding 和 libflutter.so 依赖 
     *
     * Adds the dependencies required by the Flutter project.
     * This includes:
     *    1. The embedding
     *    2. libflutter.so
     */
    void addFlutterDependencies(buildType) {
        String flutterBuildMode = buildModeFor(buildType)
        if (!supportsBuildMode(flutterBuildMode)) {
            return
        }
        // 1. 添加 Flutter 引擎仓库，默认使用 MAVEN_REPO 
        // MAVEN_REPO 仓库地址 http://download.flutter.io
        String repository = useLocalEngine()
            ? project.property('local-engine-repo')
            : MAVEN_REPO

        project.rootProject.allprojects {
            repositories {
                maven {
                    url repository
                }
            }
        }
        // 2. 添加 embedding 依赖
        // Add the embedding dependency.
        addApiDependencies(project, buildType.name,
                "io.flutter:flutter_embedding_$flutterBuildMode:$engineVersion")

        ...
        platforms.each { platform ->
            String arch = PLATFORM_ARCH_MAP[platform].replace("-", "_")
            // 3. 添加 libflutter.so 依赖
            // Add the `libflutter.so` dependency.
            addApiDependencies(project, buildType.name,
                    "io.flutter:${arch}_$flutterBuildMode:$engineVersion")
        }
    }
}
```

# 实践

通过看 `buid.gradle` 和 `flutter.gradle` 源码，我们对 Flutter 如何集成到 Android 项目中有一定的了解了。现在结合两个实例来加深理解，这里以一个编译失败问题和 so 加载失败问题为例。

## 编译失败 Cause: assert appProject != null

Flutter 1.12.13+hotfix.5 有一个编译失败的 [Issue #42214](https://github.com/flutter/flutter/issues/42214)，错误日志如下：

![企业微信截图_d5221a9a-5eea-4c5d-9c8b-8f99449e06](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15843459718378.jpg)

问题来源：这个问题实际上来自 `flutter.gradle` 脚本中的一处 bug， 见[Pull #41333](https://github.com/flutter/flutter/pull/41333/files)
问题分析：`addFlutterTasks()` 方法中第4步存在硬编码问题，[默认所有的 app module 名为 `app`](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/gradle/flutter.gradle#L724)。实际项目中 app module 名很可能不是 `app`，所以断言失败，导致集成 Flutter 后编译出错

解决办法：要么将 app module 改名为 `app`，要么给本地的 `flutter.gradle` 打上如下补丁。注意要将 `IGame` 替换成实际项目名。

![-w824](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15837916714301.jpg)

## 找不到 `libflutter.so` 或 `libapp.so`

一些年代比较久远的 Android 项目中，so 往往放在 `lib/armeabi` 目录。

![-w322](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15837916714301.jpg)

而 Flutter 的 AOT 产物只支持 `x86_64`、 `armeabi-v7a` 和 `arm64-v8a` 三种架构。另外，**Flutter 的构建流程默认将会将 so 文件打包到对应的目录中**，

![-w323](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15837916714301.jpg)
所以会出现找不到 `libflutter.so` 的问题。一种简单而粗暴的解决方案见 [Flutter原理与实践 - 美团技术团队](https://tech.meituan.com/2018/08/09/waimai-flutter-practice.html)。

Flutter 构建流程中 `packFlutterAppAotTask` 会将生成的 `app.so` 移动并重命名为 `lib/<abi>/libapp.so`。

```
Task packFlutterAppAotTask = project.tasks.create(
  name: "packLibs${FLUTTER_BUILD_PREFIX}${variant.name.capitalize()}", type: Jar) {
    destinationDir libJar.parentFile
    archiveName libJar.name
    dependsOn compileTask
    targetPlatforms.each { targetPlatform ->
        String abi = PLATFORM_ARCH_MAP[targetPlatform]
        from("${compileTask.intermediateDir}/${abi}") {
            include "*.so"
            // Move `app.so` to `lib/<abi>/libapp.so`
            rename { String filename ->
                return "lib/${abi}/lib${filename}"
            }
        }
    }
}
```

修改一：注意这里的 `<abi>` 只支持上述提到的三种架构，并不包括 `armeabi`。我们可以修改 `packFlutterAppAotTask`，修改后将 `app.so` 移动并重命名为 `lib/armeabi/libapp.so` 的目的。

修改二：修改原始的 embedding jar 包(`libflutter.so` 从原始的 `lib/armeabi-v7a` 移到 `lib/armeabi` 目录)，并在 `gradle.properties` 中提供 `local-engine-repo`，将其指向修改后的 embedding jar 包。具体见 [addFlutterDependencies()](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/gradle/flutter.gradle#L198)

```
/**
 * Adds the dependencies required by the Flutter project.
 * This includes:
 *    1. The embedding
 *    2. libflutter.so
 */
void addFlutterDependencies(buildType) {
    String flutterBuildMode = buildModeFor(buildType)
    if (!supportsBuildMode(flutterBuildMode)) {
        return
    }
    String repository = useLocalEngine()
        ? project.property('local-engine-repo')
        : MAVEN_REPO

    project.rootProject.allprojects {
        repositories {
            maven {
                url repository
            }
        }
    }
}
```

# 参考

[Integrate a Flutter module into your Android project - Flutter](https://flutter.dev/docs/development/add-to-app/android/project-setup)

[有赞 Flutter 混编方案](https://tech.youzan.com/you-zan-flutter-hun-bian-fang-an/)