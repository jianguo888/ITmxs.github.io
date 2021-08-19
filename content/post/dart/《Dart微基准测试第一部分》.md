---
title: "《Dart微基准测试第一部分》"
subtitle: ""
date: 2021-06-04T11:29:53+08:00
lastmod: 2021-06-04T11:29:53+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"

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



> 本文主要介绍

<!--more-->



在过去的几个月里，我开始收到越来越多关于某些特定 Dart 操作性能的问题。以下是[Romain Rastel](https://twitter.com/lets4r)在他关于[提高](https://github.com/flutter/flutter/issues/71900) Flutter[中 ChangeNotifier 性能的](https://github.com/flutter/flutter/issues/71900)工作的背景下提出的此类问题的示例。

> 原文链接：https://mrale.ph/dartvm/
>
> PS：内容比较繁杂，请酌情观看
>
> 
>
> 看起来像创建一个包含少量项目的固定长度列表，有时比创建一个可增长的列表性能要低得多。[pic.twitter.com/B5opjZkmrX](https://t.co/B5opjZkmrX)
>
> — Romain Rastel 💙 (@lets4r) 
>
> 2020 年 11 月 30 日

鉴于我的经验，我第一眼就知道这个特定的基准测试*出了*什么问题……但是为了讲故事，让我假装我没有。那我将如何处理这个问题？

我通常会首先尝试重复报告的数字。在这种特殊情况下，我将首先创建一个空的 Flutter 应用程序

```
$ flutter create ubench
$ cd ubench
```

然后在`lib/benchmark.dart`我把下面的代码这段代码取自 Romain 的[要点](https://gist.github.com/letsar/ebc99e00540e60d3ec99636108e52e6a)并做了一个小修正：在原始版本中，基准名称被意外交换，因此`fixed-length`基准正在分配可增长的列表，反之亦然。

```
// ubench/lib/benchmark.dart
import 'package:benchmark_harness/benchmark_harness.dart';

abstract class Benchmark extends BenchmarkBase {
  const Benchmark(String name) : super(name);

  @override
  void exercise() {
    for (int i = 0; i < 100000; i++) {
      run();
    }
  }
}

class GrowableListBenchmark extends Benchmark {
  const GrowableListBenchmark(this.length) : super('growable[$length]');

  final int length;

  @override
  void run() {
    List<int>()..length = length;
  }
}

class FixedLengthListBenchmark extends Benchmark {
  const FixedLengthListBenchmark(this.length) : super('fixed-length[$length]');

  final int length;

  @override
  void run() {
    List(length);
  }
}

void main() {
  const GrowableListBenchmark(32).report();
  const FixedLengthListBenchmark(32).report();
}
```

最后我会在*发布*模式下运行它

```
$ flutter run --release -t lib/benchmark.dart
...
I/flutter (18126): growable[32](RunTime): 31464.890625 us.
I/flutter (18126): fixed-length[32](RunTime): 713.8279800142756 us
```

结果似乎显示固定长度列表的分配速度比可增长列表快 43 倍。我们是否应该就此搁笔，然后重构我们的代码以使用尽可能多的固定长度列表？

绝对不会……或者至少不会期望我们的代码变得快 43 倍。它*实际上*是有意义的可增长超过名单，其中固定长度的列表是天作之合喜欢固定长度的列表。它们的内存占用略小，分配速度更快，访问元素的间接访问更少。但是，您应该基于对事物如何运作的清晰理解，而不是基于微基准测试的未经解释的原始结果，故意做出这种选择。

在没有任何批判性分析的情况下从原始微基准数据中得出结论是与微基准测试相关的常见陷阱，我们应该尽力避免落入其中。不幸的`package:benchmark_harness`是，它并没有让避免此类陷阱变得更容易：它为开发人员提供了一种编写微基准测试的方法，但没有为他们提供有关如何验证基准测试和解释其结果的工具或指导。更糟糕的`package:benchmark_harness`是，甚至没有尝试让编写准确的微基准测试变得非常简单。

例如，考虑我可以按以下方式编写此列表基准测试，而无需覆盖`exercise`重复`run` `100000`次数：

```
// ubench/lib/benchmark-without-exercise.dart
import 'package:benchmark_harness/benchmark_harness.dart';

// Just using BenchmarkBase directly. Rest is the same.
class GrowableListBenchmark extends BenchmarkBase {
  // ...
}

// Just using BenchmarkBase directly. Rest is the same.
class FixedLengthListBenchmark extends BenchmarkBase {
  // ...
}
```

运行此变体将显示可增长列表仅比固定长度列表慢 6 倍

```
$ flutter run --release -t lib/benchmark-without-exercise.dart
I/flutter (14407): growable[32](RunTime): 1.8629797056305768 us.
I/flutter (14407): fixed-length[32](RunTime): 0.3052065645659146 us.
```

我应该相信哪个基准测试结果？**他们两个都没有！**我应该深入了解并尝试了解到底发生了什么。

Flutter 和 Dart 已经为开发人员提供了足够的工具来弄清楚为什么基准数据会这样。不幸的是，其中一些工具有些晦涩难懂且难以发现。

例如，众所周知，您可以使用`flutter run --profile`Observatory 来分析您的应用程序，但您还可以使用本机分析器（例如`simpleperf` 在 Android 上或在 iOS 上的 Instruments）来分析发布版本，这一点并不为人所知。同样，不知道（很可能在一组从事 VM 的工程师之外根本不知道）您可以通过执行以下操作从 AOT 构建中转储特定方法的带注释的反汇编

```
$ flutter build apk --extra-gen-snapshot-options=--print-flow-graph,\
    --disassemble,\
    --print-flow-graph-filter=FixedLengthListBenchmark.run
```

我可以用这篇文章的其余部分来解释如何使用这些工具来了解这些列表基准测试中究竟发生了什么，但相反，我想尝试想象如何从提供的原语中构建用于基准测试的集成工具通过 Dart 和 Flutter。该工具不仅应该运行基准测试，而且还应该自动为开发人员提供足够的洞察力，以发现他们在基准测试过程中犯的错误并帮助他们解释结果。

## 初步设置

我已经在 GitHub 上分叉了`benchmark_harness`包[`mraleph/benchmark_harness`](https://github.com/mraleph/benchmark_harness)。我所有的*原型*代码都将存在于[`experimental-cli`](https://github.com/mraleph/benchmark_harness/tree/experimental-cli)fork 的一个新分支中。

从这里开始，我将记录这个实验性基准 CLI 的演变。我想强调这个工具的高度实验性质：你会注意到它的一些功能最终将取决于 Dart 和 Flutter SDK 内部的补丁。这些补丁可能需要数周或数月才能发布，并且可以将我的更改合并到工具的上游版本中。

我首先添加了一个简单的`bin/benchmark_harness.dart`脚本，它将作为我们新的基准测试工具的入口点。

```
$ git clone git@github.com:mraleph/benchmark_harness.git
$ cd benchmark_harness
$ cat > bin/benchmark_harness.dart
void main() { print('Running benchmarks...'); }
^D
```

最后我改变`pubspec.yaml`了`ubench`项目（记住它是我们创建的一个空的 Flutter 项目来托管我们的基准测试）以对我的版本有路径依赖`benchmark_harness`

```
# ubench/pubspec.yaml

dependencies:
  # ...
  benchmark_harness:
    path: ../benchmark_harness
  # ...
```

这允许我`benchmark_harness`在`ubench`项目目录中运行脚本

```
$ flutter pub get
$ flutter pub run benchmark_harness
Running benchmarks...
```

## 生成基准

你有没有看过`benchmark_harness`你的基准测试是如何运行的？

事实证明，这个包正在做一些相当简单的事情（并且在某种程度上很天真）：它启动 a `Stopwatch`，然后`exercise` 根据秒表重复调用直到 2 秒过去。经过的时间除以`exercise`被调用的次数是报告的基准分数。自己看看：

```
// benchmark_harness/lib/src/benchmark_base.dart
abstract class BenchmarkBase {
  // Measures the score for the benchmark and returns it.
  double measure() {
    // ...
    // Run the benchmark for at least 2000ms.
    var result = measureFor(exercise, 2000);
    // ...
  }

  // Exercises the benchmark. By default invokes [run] 10 times.
  void exercise() {
    for (var i = 0; i < 10; i++) {
      run();
    }
  }

  // Measures the score for this benchmark by executing it repeatedly until
  // time minimum has been reached.
  static double measureFor(Function f, int minimumMillis) {
    var minimumMicros = minimumMillis * 1000;
    var iter = 0;
    var watch = Stopwatch();
    watch.start();
    var elapsed = 0;
    while (elapsed < minimumMicros) {
      f();
      elapsed = watch.elapsedMicroseconds;
      iter++;
    }
    return elapsed / iter;
  }
}
```

不幸的是，这段代码有一个问题，使它不适合微基准测试：测量循环有一堆与`exercise`自身无关的开销 。最明显的是，它在每次迭代时从操作系统获取当前时间。还有一个开销与测量循环和`run`包含我们想要测量的实际操作的方法体之间的多级虚拟调度相关联 。有一个[公关](https://github.com/dart-lang/benchmark_harness/pull/38)反对`benchmark_harness`，它试图解决调用`Stopwatch.elapsedMilliseconds`过于频繁的问题，但尽管获得了批准，它还是以某种方式陷入了困境。

避免这些开销的最好方法是为每个基准测试有一个单独的测量循环。

这就是它的样子。用户通过编写带有`@benchmark`注释标记的顶级函数来声明微基准测试。

```
// ubench/lib/main.dart
import 'package:benchmark_harness/benchmark_harness.dart';

const N = 32;

@benchmark
void allocateFixedArray() {
  List.filled(N, null, growable: false);
}

@benchmark
void allocateGrowableArray() {
  List.filled(N, null, growable: true);
}
```

然后基准测试工具会生成一个辅助源文件，其中包含每个基准测试的测量循环，以及一些代码来选择哪些基准测试应该在编译时运行：

```
// ubench/lib/main.benchmark.dart
import 'package:benchmark_harness/benchmark_harness.dart' as benchmark_harness;

import 'package:ubench/main.dart' as lib;

// ...

void _$measuredLoop$allocateFixedArray(int numIterations) {
  while (numIterations-- > 0) {
    lib.allocateFixedArray();
  }
}

// ...

const _targetBenchmark =
    String.fromEnvironment('targetBenchmark', defaultValue: 'all');
const _shouldMeasureAll = _targetBenchmark == 'all';

const _shouldMeasure$allocateFixedArray =
    _shouldMeasureAll || _targetBenchmark == 'allocateFixedArray';

// ...

void main() {
  benchmark_runner.runBenchmarks(const {
    // ...
    if (_shouldMeasure$allocateFixedArray)
      'allocateFixedArray': _$measuredLoop$allocateFixedArray,
    // ...
  });
}
```

实际测量将发生在一个简单的`measure`辅助函数中：

```
// benchmark_harness/lib/benchmark_runner.dart

/// Runs the given measured [loop] function with an exponentially increasing
/// parameter values until it finds one that causes [loop] to run for at
/// least [thresholdMilliseconds] and returns [BenchmarkResult] describing
/// that run.
BenchmarkResult measure(void Function(int) loop,
    {required String name, int thresholdMilliseconds = 5000}) {
  var n = 2;
  final sw = Stopwatch();
  do {
    n *= 2;
    sw.reset();
    sw.start();
    loop(n);
    sw.stop();
  } while (sw.elapsedMilliseconds < thresholdMilliseconds);

  return BenchmarkResult(
    name: name,
    elapsedMilliseconds: sw.elapsedMilliseconds,
    numIterations: n,
  );
}
```

我们从一个非常简单的实现开始，但它应该能满足我们最初的微基准测试需求。然而对于更复杂的情况，我们可能想要做一些更严格的事情：例如，一旦`numIterations`发现足够大， 我们可以重复`loop(numIterations)`多次并评估观察到的运行时间的统计特性。

### 使用 [`source_gen`](https://pub.dev/packages/source_gen)

要生成，`main.benchmark.dart`我们需要解析`main.dart`并找到所有带有`@benchmark`注解的函数。幸运的是，Dart 有许多用于代码生成的规范工具，这使得这非常容易。

我所要做的就是依赖`package:source_gen`并定义一个子类[`GeneratorForAnnotation`](https://pub.dev/documentation/source_gen/latest/source_gen/GeneratorForAnnotation-class.html)：

```
// benchmark_harness/lib/src/benchmark_generator.dart

class BenchmarkGenerator extends GeneratorForAnnotation<Benchmark> {
  // ...
  @override
  String generateForAnnotatedElement(
      Element element, ConstantReader annotation, BuildStep buildStep) {
    final name = element.name;
    return '''
void ${_\$measuredLoop\$$name}(int numIterations) {
  while (numIterations-- > 0) {
    lib.${name}();
  }
}
''';
  }
}
```

然后我把它包裹在一个 `Builder`

```
// benchmark_harness/lib/builder.dart

Builder benchmarkLibraryBuilder(BuilderOptions options) =>
    LibraryBuilder(BenchmarkGenerator(), generatedExtension: '.benchmark.dart');
```

最后`Builder`通过曝光这家工厂`build.yaml`

```
# benchmark_harness/build.yaml

builders:
  benchmark:
    import: "package:benchmark_harness/builder.dart"
    builder_factories: ["benchmarkLibraryBuilder"]
    build_extensions: {".dart": [".benchmark.dart"]}
    auto_apply: dependents
    build_to: source
```

基本上就是这样。现在，每当我运行时`build_runner build`，`ubench`我都会`lib/main.benchmark.dart`为以下定义的基准生成`lib/main.dart`：

```
ubench $ flutter pub run build_runner build
ubench $ ls lib/*.benchmark.dart
lib/main.benchmark.dart
```

您可以在此[提交中](https://github.com/mraleph/benchmark_harness/commit/474de50c574339b4ed11267d5885bad37060306b)查看代码生成器的完整源代码。

### 运行基准

我们可以通过将适当的传递`--dart-define` 给来执行单个基准测试`flutter run`，例如：

```
$ flutter run --release --dart-define targetBenchmark=allocateFixedArray -t lib/main.benchmark.dart
Launching lib/main.benchmark.dart on Pixel 3a in release mode...
Running Gradle task 'assembleRelease'...
Running Gradle task 'assembleRelease'... Done                       4.9s
✓ Built build/app/outputs/flutter-apk/app-release.apk (4.9MB).
Installing build/app/outputs/flutter-apk/app.apk...              1,268ms

Flutter run key commands.
h Repeat this help message.
c Clear the screen
q Quit (terminate the application on the device).
I/flutter (12463): benchmark_harness[{"event":"benchmark.running"}]
I/flutter (12463): benchmark_harness[{"event":"benchmark.result","params":{...}}]
I/flutter (12463): benchmark_harness[{"event":"benchmark.done"}]

Application finished.
```

但是手动执行此操作并不是我的目标。相反，我要改变`bin/benchmark_harness.dart`脚本既构建基准，然后运行所有生成的文件，收集的基准测试结果（全代码中看到[这个](https://github.com/mraleph/benchmark_harness/commit/13941c5e0d58c94bf78d694e619dacb4bfc3ba3c)承诺）。

```
// benchmark_harness/bin/benchmark_harness.dart

void main() async {
  // ...
  // Generate benchmark wrapper scripts.
  print(red('Generating benchmark wrappers'));
  'flutter pub run build_runner build'.start(progress: Progress.devNull());

  // Run all generated benchmarks.
  final resultsByFile = <String, Map<String, BenchmarkResult>>{};
  for (var file in find('*.benchmark.dart').toList().map(p.relative)) {
    resultsByFile[file] = await runBenchmarksIn(file);
  }

  // Report results.
  // ...
}

/// Runs all benchmarks in `.benchmark.dart` [file] one by one and collects
/// their results.
Future<Map<String, BenchmarkResult>> runBenchmarksIn(String file) async {
  // ...
}
```

这样的帮助脚本使运行基准测试变得非常简单：

```
$ flutter pub run benchmark_harness
Generating benchmark wrappers
Found 2 benchmarks in lib/main.benchmark.dart
  measuring allocateFixedArray
    benchmark is running
      done
  measuring allocateGrowableArray
    benchmark is running
      done

--------------------------------------------------------------------------------

Results for lib/main.benchmark.dart
allocateFixedArray: 0.0000030226074159145355 ms/iteration (fastest)
allocateGrowableArray: 0.00018900632858276367 ms/iteration (62.5 times as slow)
```

## 了解结果

现在我们有了一个运行微基准测试的工具，让我们扩展它，支持在运行时分析基准测试。这将帮助我们了解基准测试在哪里花费时间并确认它正在测量我们想要它测量的内容。

Flutter 的*发布*版本不包括 Dart 的内置分析器，因此我们将不得不使用原生分析器，例如`simpleperf`在 Android 上。

Android有全面[的文档](https://android.googlesource.com/platform/system/extras/+/master/simpleperf/doc/android_application_profiling.md)使用`simpleperf`，这我不打算在这里重复。`simpleperf`还带有调用的 C++（和 Java）代码[`app_api`](https://android.googlesource.com/platform/system/extras/+/master/simpleperf/app_api/cpp/simpleperf.cpp)，这些代码可以链接到应用程序中以允许以编程方式访问分析器。

实际上`app_api`并没有做任何过于花哨的事情：它只是`simpleperf`使用正确的命令行选项运行 二进制文件。这就是我决定将相关部分移植`app_api`到纯 Dart 的原因。我们也可以绑定到`app_api`使用 Dart FFI 的C++ 版本，但这需要将这个 C++ 打包成一个*Flutter 插件*，这使事情变得复杂，因为它`benchmark_harness`是一个纯 Dart 包，它不能依赖于 Flutter 插件包。

```
// benchmark_harness/lib/src/simpleperf/profiling_session.dart

class ProfilingSession {
  Future<void> start(
      {RecordingOptions options = const RecordingOptions()}) async {
    // ...
    await _startSimpleperfProcess(options);
  }

  Future<void> _startSimpleperfProcess(RecordingOptions options) async {
    final simpleperfBinary = await _findSimplePerf();
    _simpleperf = await Process.start(
      simpleperfBinary,
      [
        'record',
        '--log-to-android-buffer',
        '--log',
        'debug',
        '--stdio-controls-profiling',
        '--in-app',
        '--tracepoint-events',
        '/data/local/tmp/tracepoint_events',
        '-o',
        options.outputFilename ?? _makeOutputFilename(),
        '-e',
        options.event,
        '-f',
        options.frequency.toString(),
        '-p',
        _getpid().toString(),
        ..._callgraphFlagsFrom(options),
      ],
      workingDirectory: simpleperfDataDir,
    );
    // ...
  }
}
```

然后我调整`benchmark_runner.dart`运行基准测试它刚刚在分析器下测量并将配置`perf-$benchmarkName.data` 文件保存到文件中。此文件将在应用程序的数据目录中创建：

```
Future<void> runBenchmarks(Map<String, void Function(int)> benchmarks) async {
  _event('benchmark.running');
  final profiler = Platform.isAndroid ? ProfilingSession() : null;
  for (var entry in benchmarks.entries) {
    final result = measure(entry.value, name: entry.key);
    _event('benchmark.result', result);

    if (profiler != null) {
      // Run benchmark for the same amount of iterations and profile it.
      await profiler.start(
          options: RecordingOptions(outputFilename: 'perf-${entry.key}.data'));
      entry.value(result.numIterations);
      await profiler.stop();
    }
  }
  _event('benchmark.done');
}
```

NDK 还附带了一个辅助脚本`api_profiler.py`，它实现了两个命令：

- `api_profiler.py prepare` 配置您的设备以进行分析 - 我们将在运行基准测试之前调用它；
- `api_profiler.py collect`从设备中提取收集的配置文件 - 我们将在所有基准测试完成运行后调用它以提取`perf-*.data`从设备生成的所有文件 。

### 显示收集的分析数据

NDK 的`simpleperf`二进制文件同时支持`record`和`report`命令，就像 Linux 一样`perf`。在 NDK 中环顾四周，我还发现了一堆用 Python 编写的帮助脚本（例如`report_html.py`，可以生成 HTML 报告）。深入了解这些脚本，我发现它们利用了`libsimpleperf_report.so`处理收集到的配置文件的解析和符号化的库。此库的 API 定义[`simpleperf/report_lib_interface.cpp`](https://android.googlesource.com/platform/system/extras/+/refs/heads/master/simpleperf/report_lib_interface.cpp#34)在`simpleperf`源文件 的顶部。

使用[`ffigen`](https://pub.dev/packages/ffigen)我`dart:ffi` 为此库生成的基于绑定，允许我从`benchmark_harness` 脚本中使用它来处理收集的分析样本：

```
final reportLib = report_bindings.NativeLibrary(
    ffi.DynamicLibrary.open(ndk.simpleperfReportLib));

Future<void> _printProfile(String profileData) async {
  final session = reportLib.CreateReportLib();
  reportLib.SetRecordFile(session, Utf8.toUtf8(profileData).cast());

  // Iterate over all collected samples.
  for (;;) {
    final sample = reportLib.GetNextSample(session);
    if (sample == ffi.nullptr) {
      break;
    }
    final period = sample.ref.period;
    final symbol = reportLib.GetSymbolOfCurrentSample(session);

    final dsoName = Utf8.fromUtf8(symbol.ref.dso_name.cast());
    final symbolName = Utf8.fromUtf8(symbol.ref.symbol_name.cast());

    // Process sample for the symbol [symbolName] in dso [dsoName] and collect
    // aggregate statistics (samples per symbol, total sampling period, etc).
    // ...
  }

  // Report top N hottest symbols
}
```

当我第一次运行它时，我发现`simpleperf`无论 for `libapp.so`（包含 AOT 编译的 Dart 代码）还是 for `libflutter.so` （包含 Flutter 引擎代码）都不能真正将大多数样本归因于一个有意义的符号 。这是我收到的第一份报告：

```
Hot methods when running allocateGrowableArray:
 88.24% _kDartIsolateSnapshotInstructions (libapp.so)
  4.04% unknown (libflutter.so)
  3.15% unknown ([kernel.kallsyms])
  1.44% pthread_mutex_lock (libc.so)
  1.30% pthread_mutex_unlock (libc.so)
  ...
```

这并不奇怪：这两个库都被剥离了，并且不包含任何有用的符号信息以`simpleperf`供使用。

幸运的是，`libflutter.so`符号可以从 Cloud Storage 中获取，其中构建基础架构正在归档它们，例如，提交时 Flutter 引擎的 ARM64 Android 版本构建的符号`e115066d...` 驻留在 `gs://flutter_infra/flutter/e115066d.../android-arm64-release/symbols.zip`. 就在几个月前，我编写了一些 Dart 代码，用于下载和缓存基于提交哈希的 Flutter 引擎符号 for [`@flutter-symbolizer-bot`](https://github.com/flutter-symbolizer-bot)，所以我可以在这里重用完全相同的代码。

获取符号`libapp.so`是一个更有趣的问题。Dart VM AOT 编译器能够在 ELF 二进制文件中生成 DWARF 调试部分。然而，仅仅通过`--extra-gen-snapshot-options=--dwarf-stack-traces`并不能让我们到达那里：`libapp.so`仍然会缺少符号。对 Flutter CLI 源代码的检查表明，它总是简单地[指示 AOT](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/lib/src/base/build.dart#L143-L157)编译器生成剥离的输出。熟悉高级 Flutter 工具选项的用户可能知道`--split-debug-info=...`flag，它指示 AOT 编译器生成一个单独的 ELF 文件，该文件只包含 DWARF 部分，但不包含快照有效负载本身。这个文件足以进行符号化，但不幸的是不能很好地与`llvm-objdump`. GNU`objdump`支持通过 发现的单独调试信息`.gnu_debuglink`，但混合源代码和反汇编等某些功能在此模式下似乎不起作用。

出于原型设计的目的，我选择修补`flutter`CLI 以允许用户通过`--no-strip`在`--extra-gen-snapshot-options`.

现在，当我们构建基准测试时，我们可以保留 DWARF 调试信息 `libapp.so`，然后将其用于符号化，使我们的配置文件更具信息性：

```
Hot methods when running allocateGrowableArray:
 54.17% Precompiled_Stub__iso_stub_AllocateArrayStub (libapp.so)
 14.29% Precompiled______measuredLoop_allocateGrowableArray_21146765_1230 (libapp.so)
  8.01% Precompiled__GrowableList_0150898__GrowableList_0150898__withData_0150898_136 (libapp.so)
  7.63% Precompiled__GrowableList_0150898__GrowableList_0150898__164 (libapp.so)
  4.96% Precompiled__GrowableList_0150898__allocateData_0150898_161 (libapp.so)
  3.66% unknown ([kernel.kallsyms])
```

然后我更进一步，并使用 DWARF 中可用的信息来漂亮地打印符号名称（这`simpleperf`似乎并不行）。幸运的是，我实际上不必手动解析 DWARF，`package:native_stack_traces` 已经拥有所有必要的工具来为我们处理 ELF/DWARF 解析：

```
String _userFriendlyName(Dwarf dwarf, elf_lib.Elf elf, String symbol) {
  final elfSymbol = elf.staticSymbolFor(symbol);
  if (elfSymbol != null) {
    final callInfo = dwarf.callInfoFor(elfSymbol.value);
    if (callInfo != null && callInfo.isNotEmpty) {
      final lastInfo = callInfo.last;
      if (lastInfo is DartCallInfo) {
        return lastInfo.function
            .replaceFirst(r'_$measuredLoop$', 'measured loop for ');
      }
      return lastInfo.toString();
    }
  }
  return symbol.replaceFirst('Precompiled_Stub__iso_stub_', 'Stub::');
}
```

这会产生更具可读性的输出：

```
$ flutter run benchmark_harness report
Results for lib/main.benchmark.dart
allocateFixedArray: 0.000006573274731636047 ms/iteration (fastest)
allocateGrowableArray: 0.00020492076873779297 ms/iteration (31.2 times as slow)

Hot methods when running allocateFixedArray:
 99.64% measured loop for allocateFixedArray (libapp.so)

Hot methods when running allocateGrowableArray:
 54.17% Stub::AllocateArrayStub (libapp.so)
 14.29% measured loop for allocateGrowableArray (libapp.so)
  8.01% new _GrowableList._withData (libapp.so)
  7.63% new _GrowableList (libapp.so)
  4.96% _GrowableList._allocateData (libapp.so)
  3.66% unknown ([kernel.kallsyms])
```

我们现在可以看到基准测试的行为似乎略有不同：`allocateFixedArray`将所有时间都花在其测量循环中，而 `allocateGrowableArray`将时间花在与数组分配相关的各种方法上。

为了更好地理解这一点，我们需要查看为这两个测量循环生成的本机代码。

有多种可能的方法来实现这一点，例如我可以选择将此任务委托给`perf annotate`或类似的东西。然而`perf` ，在搜索符号时对文件夹结构相当挑剔，而且似乎没有一个 NDK 脚本符合要求，所以我选择只使用 `llvm-objdump`分析信息并注释结果。

通过一些后期处理，使用 Dart VM 特定名称作为保留寄存器`thr`（为当前`Thread`指针保留）和对符号名称（通过`_userFriendlyName`帮助程序）进行解构，我们得到如下输出：

```
Hot methods when running allocateFixedArray:
 99.64% measured loop for allocateFixedArray (libapp.so)
                      0: stp	fp, lr, [sp, #-16]!
                      4: mov	fp, sp
                      8: ldr	x2, [fp, #16]
           7.87%      c: ldr	x16, [thr, #64]
          18.40%     10: cmp	sp, x16
           9.30%     14: b.ls	->60
           8.88%     18: cmp	x2, null
                     1c: b.eq	->68
           9.65%     20: asr	x3, x2, #1
                     24: tbz	w2, #0, ->2c
                     28: ldur	x3, [x2, #7]
           9.45%     2c: sub	x2, x3, #1
                     30: cmp	x3, #0
           9.85%     34: b.le	->50
           9.72%     38: adds	x0, x2, x2
                     3c: b.vc	->48
                     40: bl	Stub::AllocateMintSharedWithoutFPURegsStub
                     44: stur	x2, [x0, #7]
           8.90%     48: mov	x2, x0
           7.60%     4c: b.al	->c
                     50: mov	x0, null
                     54: mov	sp, fp
                     58: ldp	fp, lr, [sp], #16
                     5c: ret
                     60: bl	Stub::StackOverflowSharedWithoutFPURegsStub
                     64: b.al	->18
                     68: bl	Stub::NullErrorSharedWithoutFPURegsStub
```

熟悉 ARM 汇编语言的开发人员可能能够仅从这个输出中发现我们的基准测试的问题（是的，显然存在问题）。

但是我决定花更多的时间在上面，并在程序集之上覆盖有关 Dart VM 编译器的中间语言的信息，使其更容易理解。

在内部，我们的编译器支持使用人类可读的注释来注释它生成的机器代码，其中包括描述生成特定机器代码片段的 IL。

我已经尝试了两种不同的路径，我考虑过从 AOT 编译器中获取这些信息：

- 添加一个标志`--write-code-comments-to=output.json`，告诉编译器将偏移量到注释的映射转储到 JSON 文件中，然后在我们的基准测试 CLI 中摄取此 JSON。
- 添加一个标志`--write-code-comments-as-synthetic-source-to=comments.txt`，告诉编译器从所有代码注释中合成一个巨大的文件，并将偏移量到注释的映射作为 DWARF 行程序写入`.debug_line` 部分。

最后我决定第二种方法更好，因为它使*任何*能够理解`.debug_line`在反汇编的同时显示代码注释的工具成为可能（例如，`gdb`也将开始显示它）。

这是[我](https://github.com/mraleph/sdk/commit/6612e52dc01cc843f04a205b2dc56c2d0d69d852#diff-c21c72c7a0fbe730891dd0c86bd141f23c33fddc11b512025ac7381fbda7563a)针对 Dart SDK 实现必要管道的[补丁](https://github.com/mraleph/sdk/commit/6612e52dc01cc843f04a205b2dc56c2d0d69d852#diff-c21c72c7a0fbe730891dd0c86bd141f23c33fddc11b512025ac7381fbda7563a)。有了这个，我唯一要做的改变就是添加`-S`到`llvm-objdump` 调用中，它处理其余的：

```
Hot methods when running allocateFixedArray:
 99.64% measured loop for allocateFixedArray (libapp.so)
                         ;; Enter frame
                      0: stp	fp, lr, [sp, #-16]!
                      4: mov	fp, sp
                         ;; ParallelMove r2 <- S+2
                      8: ldr	x2, [fp, #16]
                         ;; CheckStackOverflow:30(stack=0, loop=1)
           7.87%      c: ldr	x16, [thr, #64]
          18.40%     10: cmp	sp, x16
           9.30%     14: b.ls	->60
                         ;; CheckNull:10(v3, NoSuchMethodError) T{int}
           8.88%     18: cmp	x2, null
                     1c: b.eq	->68
                         ;; v26 <- UnboxInt64(v3 T{int}) T{int}
           9.65%     20: asr	x3, x2, #1
                     24: tbz	w2, #0, ->2c
                     28: ldur	x3, [x2, #7]
                         ;; v6 <- BinaryInt64Op(- [tr], v26 T{int}, v32) T{int}
           9.45%     2c: sub	x2, x3, #1
                         ;; Branch if RelationalOp(>, v26 T{int}, v34) T{bool} goto (3, 5)
                     30: cmp	x3, #0
           9.85%     34: b.le	->50
                         ;; v30 <- BoxInt64(v6) T{int}
           9.72%     38: adds	x0, x2, x2
                     3c: b.vc	->48
                     40: bl	Stub::AllocateMintSharedWithoutFPURegsStub
                     44: stur	x2, [x0, #7]
                         ;; ParallelMove r2 <- r0 goto:28 B4
           8.90%     48: mov	x2, x0
           7.60%     4c: b.al	->c
                         ;; ParallelMove r0 <- C
                     50: mov	x0, null
                         ;; Return:36(v0)
                     54: mov	sp, fp
                     58: ldp	fp, lr, [sp], #16
                     5c: ret
                         ;; CheckStackOverflowSlowPath
                     60: bl	Stub::StackOverflowSharedWithoutFPURegsStub
                     64: b.al	->18
                         ;; slow path check null (nsm) operation
                     68: bl	Stub::NullErrorSharedWithoutFPURegsStub

Hot methods when running allocateGrowableArray:
 54.17% Stub::AllocateArrayStub (libapp.so)
 14.29% measured loop for allocateGrowableArray (libapp.so)
                         ;; Enter frame
                      0: stp	fp, lr, [sp, #-16]!
                      4: mov	fp, sp
                      8: sub	sp, sp, #16
                         ;; CheckStackOverflow:8(stack=0, loop=0)
                      c: ldr	x16, [thr, #64]
                     10: cmp	sp, x16
                     14: b.ls	->94
                         ;; v36 <- UnboxedConstant:32(#32) [32, 32] T{_Smi}
                     18: mov	x0, #32
                         ;; ParallelMove r1 <- S+2, S-2 <- r0
                     1c: ldr	x1, [fp, #16]
                     20: stur	x0, [fp, #-16]
                         ;; CheckStackOverflow:30(stack=0, loop=1)
                     24: ldr	x16, [thr, #64]
           0.73%     28: cmp	sp, x16
                     2c: b.ls	->9c
                         ;; CheckNull:10(v3, NoSuchMethodError) T{int}
           0.33%     30: cmp	x1, null
                     34: b.eq	->a4
                         ;; v26 <- UnboxInt64(v3 T{int}) T{int}
           0.30%     38: asr	x2, x1, #1
                     3c: tbz	w1, #0, ->44
                     40: ldur	x2, [x1, #7]
                         ;; v6 <- BinaryInt64Op(- [tr], v26 T{int}, v32) T{int}
           0.35%     44: sub	x1, x2, #1
                         ;; ParallelMove S-1 <- r1
                     48: stur	x1, [fp, #-8]
                         ;; Branch if RelationalOp(>, v26 T{int}, v34) T{bool} goto (3, 5)
          10.41%     4c: cmp	x2, #0
                     50: b.le	->84
                         ;; PushArgument(v18)
           0.29%     54: ldr	x16, [pp, #5160]
           0.35%     58: stp	x0, x16, [sp, #-16]!
                         ;; StaticCall:10( _GrowableList@0150898.<0> v18, v36, result_type = T{_GrowableList})
                     5c: bl	new _GrowableList
           0.33%     60: add	sp, sp, #16
                         ;; ParallelMove r2 <- S-1
                     64: ldur	x2, [fp, #-8]
                         ;; v30 <- BoxInt64(v6) T{int}
           0.53%     68: adds	x0, x2, x2
                     6c: b.vc	->78
                     70: bl	Stub::AllocateMintSharedWithoutFPURegsStub
                     74: stur	x2, [x0, #7]
                         ;; ParallelMove r1 <- r0, r0 <- S-2 goto:28 B4
           0.29%     78: mov	x1, x0
                     7c: ldur	x0, [fp, #-16]
           0.38%     80: b.al	->24
                         ;; ParallelMove r0 <- C
                     84: mov	x0, null
                         ;; Return:36(v0)
                     88: mov	sp, fp
                     8c: ldp	fp, lr, [sp], #16
                     90: ret
                         ;; CheckStackOverflowSlowPath
                     94: bl	Stub::StackOverflowSharedWithoutFPURegsStub
                     98: b.al	->18
                         ;; CheckStackOverflowSlowPath
                     9c: bl	Stub::StackOverflowSharedWithoutFPURegsStub
                     a0: b.al	->30
                         ;; slow path check null (nsm) operation
                     a4: bl	Stub::NullErrorSharedWithoutFPURegsStub
  8.01% new _GrowableList._withData (libapp.so)
  7.63% new _GrowableList (libapp.so)
  4.96% _GrowableList._allocateData (libapp.so)
  3.66% unknown ([kernel.kallsyms])
  ..(run with -v to disassemble all hot methods in libapp.so)..
```

现在它应该在`allocateFixedArray`实际上不包含*任何数组分配*的输出中可见，它被编译器消除（沉没），因此我们的基准测试似乎将空`while (N-- > 0);`循环的性能与实际执行一些数组分配的循环的性能进行比较 .

然而，空循环远非微不足道。它本身似乎包含两个开销来源：

- 变量

  ```plaintext
  numIterations
  ```

  似乎在每次循环迭代时都被拆箱和重新装箱，从以下实现的指令序列中可以明显看出

  ```plaintext
  numIterations--
  ```

  ：

  ```
  ;; CheckNull:10(v3, NoSuchMethodError) T{int}
  18: cmp x2, null
  1c: b.eq ->68
    ;; v26 <- UnboxInt64(v3 T{int}) T{int}
  20: asr x3, x2, #1
  24: tbz w2, #0, ->2c
  28: ldur x3, [x2, #7]
    ;; v6 <- BinaryInt64Op(- [tr], v26 T{int}, v32) T{int}
  2c: sub x2, x3, #1
    <...>
    ;; v30 <- BoxInt64(v6) T{int}
  38: adds x0, x2, x2
  3c: b.vc ->48
  40: bl Stub::AllocateMintSharedWithoutFPURegsStub
  44: stur x2, [x0, #7]
  ```

- 循环头包含

  ```plaintext
  CheckStackOverflow
  ```

  执行内存加载和每次循环迭代比较的指令。

  ```
  ;; CheckStackOverflow:30(stack=0, loop=1)
  24: ldr x16, [thr, #64]
  28: cmp sp, x16
  2c: b.ls ->9c
  ```

对于准确的微基准测试，我们当然应该努力将测量循环的开销减少到最低限度。

### 防止 `numIterations`

```
void _$measuredLoop$allocateGrowableArray(int numIterations) {
  while (numIterations-- > 0) {
    lib.allocateGrowableArray();
  }
}
```

为什么要被`numIterations`编译器装箱？这里有多种因素在起作用。最重要的是，编译器根本无法证明它`numIterations`不在`null`函数的入口处，这是因为我们使用了一个可测量的循环方法来调用它。*TFA*（*类型流分析*，我们的全局类型传播算法）不会尝试推断闭包参数的精确类型信息。如果我们的代码是使用类构建的，例如：如果我们在默认情况下以声音不可为空 (NNBD) 模式运行，那么编译器也将能够依赖于`numIterations`永远不能为`null`. 这说明了 NNBD 的好处之一：为编译器提供更多信息以供使用。

```
abstract class MeasuredLoop {
  void run(int numIterations);
}

class Loop$allocateGrowableArray extends MeasuredLoop {
  void run(int numIterations) =>
      _$measuredLoop$allocateGrowableArray(numIterations);
}

void main() {
  await benchmark_runner.runBenchmarks(const {
    // Use class instead of tearing off _$measuredLoop$allocateGrowableArray
    'allocateGrowableArray': Loop$allocateGrowableArray(),
  });
}
```

然后编译器将能够推断出它`numIterations`永远不能为空，并且不仅`numIterations`在内部本地拆箱`run`，`_$measuredLoop$...`而且甚至更改这两个函数的调用约定以在没有装箱的情况下传递参数。

这适用于方法而不适用于闭包的原因是因为静态类型的方法调用比静态类型的闭包调用为编译器提供了更多关于潜在被调用者的信息。

```
class Loop$allocateGrowableArray extends MeasuredLoop {
  void run(int numIterations) {
    // Can only be reached from a call-site where
    // static type of a receiver is either [dynamic] or
    // related to [Loop$allocateGrowableArray].
  }
}

MeasuredLoop loop;
loop.run(...);  // Can invoke only overrides of [MeasuredLoop.run].

// ---------------------------------------------------------

var g = (int numIterations) {
  // Can be reached from any closure call-site with a compatible
  // function type and invocation signature.
};

void Function(int) f;
f(...);  // Can reach any compatible closure in the program.
```

现在 TFA 做了一个非常保守的近似，假设任何闭包都可以从其他任何地方调用。这当然可以改进：例如，TFA 可以使用静态类型信息来缩小每个闭包的潜在调用点。这可以更进一步——TFA 可以尝试通过程序跟踪关闭流程并尝试确定它可以到达哪些调用站点，但这是一个更复杂的分析。

鉴于 TFA 不能推断`numIterations`为从不包含，`null`我们可能希望通过在测量循环包装器的开头添加显式检查来帮助它。

```
void _$measuredLoop$allocateGrowableArray(int numIterations) {
  if (numIterations == null) throw 'Unexpected null!';
  while (numIterations-- > 0) {
    lib.allocateGrowableArray();
  }
}
```

不幸的是，这无济于事：即使编译器推断出`numIterations`它永远不会`null`在循环内，它仍然不会对其进行拆箱，因为它的拆箱算法对于`int`类型变量过于保守。如果所有到达值都是装箱操作或常量，则当前的启发式集仅对整数变量进行拆箱。这可能是一个需要修复的错误，但同时我们可以简单地满足这个要求：在这里谈论拆箱时，我允许自己有点不准确。编译器实际上并不拆箱*变量*，因为它在 SSA 形式上运行，该形式不再包含任何变量操作。谈论拆箱*phis*会更准确，但我不想让没有编译器背景的读者过多混淆。

```
void _$measuredLoop$allocateGrowableArray(int numIterations) {
  // n will be unboxed because it is either
  //     Box(Unbox(numIterations) + 0) or Box(Unbox(n) - 1)
  var n = numIterations + 0;
  while (n-- > 0) {
    lib.allocateGrowableArray();
  }
}
```

### 消除 `CheckStackOverflow`

`CheckStackOverflow`编译器插入的指令有双重目的：它们检查堆栈溢出（顾名思义），但也用作中断点，允许 VM 干净地中断执行 Dart 代码的线程。例如，GC 可以使用此机制将 mutator 线程停放在安全点。`CheckStackOverflow`的速度相当快：它们由通常会访问 CPU 缓存的内存负载和几乎从不使用的比较和分支组成。然而，它们在非常紧凑的循环中仍然具有可见的成本。

作为一项实验，我更改了 VM 以完全消除`CheckStackOverflow`标有`@pragma('vm:no-interrupts')`.

每次迭代有`CheckStackOverflow`指令时，空循环需要执行`3.56 ns/iteration`，没有指令时，空循环的执行速度`1.78 ns/iteration`基本上是原来的两倍。

空测量回路的最终形状是：

```
    ;; v9 <- BinaryInt64Op(- [tr], v6 T{int}, v36) T{int}
24: sub    x0, x1, #1
    ;; Branch if RelationalOp(>, v6 T{int}, v34) T{bool} goto (3, 5)
28: cmp    x1, #0
2c: b.le   ->38
    ;; ParallelMove r1 <- r0 goto:32 B4
30: mov    x1, x0
34: b.al   ->24
```

### 防止编译器优化计算

最后一步是修复基准以防止编译器优化我们的计算。核心思想是改变每个基准以返回它产生的值并使测量循环消耗返回值：

```
// ubench/lib/main.dart

@benchmark
Object allocateFixedArray() {
  return List.filled(N, null, growable: false);
}

// ubench/lib/main.benchmark.dart
@pragma('vm:never-inline')
@pragma('vm:no-interrupts')
void _$measuredLoop$allocateFixedArray(int numIterations) {
  // ...
  while (n-- > 0) {
    final result = lib.allocateFixedArray();
    // Call a special intrinsic from dart:_internal which keeps
    // the value alive and prevents it from being optimized out.
    // However does not result in any actual calls being produced.
    benchmark_runner.reachabilityFence(result);
  }
}
```

## 最终基准测试结果

使用基准工具*和*基准本身固定以防止编译器优化列表分配，我们得到以下结果

```
Results for lib/main.benchmark.dart
allocateFixedArray: 131.607 (±0.845) ns/iteration (fastest)
allocateGrowableArray: 177.443 (±1.603) ns/iteration (1.3 times as slow)

Hot methods when running allocateFixedArray:
 77.98% Stub::AllocateArrayStub (libapp.so)
  6.19% measured loop for allocateFixedArray (libapp.so)
  5.21% unknown ([kernel.kallsyms])
  1.16% pthread_mutex_lock (libc.so)
  1.16% pthread_mutex_unlock (libc.so)

Hot methods when running allocateGrowableArray:
 53.13% Stub::AllocateArrayStub (libapp.so)
 11.53% new _GrowableList._withData (libapp.so)
  9.35% new _GrowableList (libapp.so)
  8.15% measured loop for allocateGrowableArray (libapp.so)
  6.23% _GrowableList._allocateData (libapp.so)
  3.92% unknown ([kernel.kallsyms])
```

因此，与分配具有 32 个元素的固定长度列表相比，分配具有 32 个元素的可增长列表需要多出约 30% 的时间，这比我们使用初始基准获得的结果要合理得多。

这也是有道理的：可增长数组只是一个包含在固定长度数组中的对象，因此分配可增长数组比分配固定长度数组要慢一些，因为您需要分配和初始化更多内存，并且需要跳过更多圈套到达那里：`allocateFixedArray` 大部分时间都花在分配数组的存根（一段手写机器代码）上，在完成`allocateGrowableArray`这项工作时涉及多个功能。

## 关于基准测试的最后评论

总的来说，我选择在这篇文章中忽略一些与微基准测试相关的最困难的问题：例如，我决定完全忽略 JIT，专门关注 AOT。对 JIT 进行基准测试真的很难，因为它们的行为就像活的有机体，即使经过长时间的预热，性能也会出现剧烈波动（参见例如[虚拟机预热吹冷热](https://soft-dev.org/pubs/html/barrett_bolz-tereick_killick_mount_tratt__virtual_machine_warmup_blows_hot_and_cold_v6/)论文）。我还决定专注于简单化的*平均运营成本*指标，这可能会隐藏一些重要但不经常出现的开销。数组分配实际上是一个很好的例子：它通常可能相当便宜，但它可能经常触发 GC——其成本将与实时数据量成正比，在微基准测试中可能接近 0。平均数以千计的操作将完全消除 GC 的成本，但是在实际应用程序中，像这样的 GC 可能很重要，因为它们可能会导致丢失帧。

## 下一部分

在本系列的第 2 部分中，我将使用基准测试工具 CLI，我刚刚实施来回答我在 Twitter 上遇到的更多性能问题，例如：

- 将字段初始值设定项从 更改为`[]`如何`List.filled(0, null)`可以提高代码的性能；
- `async`语法糖的性能特点；
- 当您通过动态类型变量调用闭包时会发生什么；
