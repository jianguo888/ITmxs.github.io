---
title: "《Flutter引擎奔溃分析方法简介》"
subtitle: ""
date: 2021-07-31T18:33:42+08:00
lastmod: 2021-07-31T18:33:42+08:00
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



> 本文主要介绍

<!--more-->

Android 项目信息 Flutter 后上线，偶尔会遇到一些 Flutter 引擎的崩溃。简单记录下如何还原引擎崩溃日志，方便在源码中定位问题。



# Flutter Engine Crash 堆栈还原

首先，在 `bin/internal/engine.version` 找到 flutter engine revision

```
b851c718295a896918dc93cb1ff14f2f895a1b90
```

第二步，下载带符号表的 `libflutter.so` 文件

```
https://console.cloud.google.com/storage/browser/flutter_infra/flutter/b851c718295a896918dc93cb1ff14f2f895a1b90
```

第三步，利用 `ndk-stack` 还原堆栈信息

```
/path/to/android-ndk-r16b/ndk-stack -sym /path/to/symbols/armeabi-v7a -dump /path/to/stacktrace.txt
```

如果是将 crash 上报到 bugly 平台，则不必使用 `ndk-stack`，直接在 bugly 上配置符号表即可。

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/04/15938295883428.jpg)

配置成功后就能在 bugly 平台上看到 native crash 堆栈信息。

原始堆栈信息
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/04/15938297243231.jpg)

还原后的堆栈信息
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/04/15938297398367.jpg)

拿到还原后的堆栈信息，就可以对着源码分析问题了。

# 一个疑问

官方提到每个 `libflutter.so` 文件中有一个 build id，native crash 堆栈中见到的 `BuildId` 要跟 `libflutter.so` 的匹配，否则没法还原 native crash 堆栈信息。原文如下：

> The build system sets a build id for each libflutter.so file. In the tombstones, you would see the ID like so:

> ```
> > #00 pc 000000000062d6e0  /data/app/com.app-tARy3eLH2Y-QN8J0d0WFog==/lib/arm64/libflutter.so!libflutter.so (offset 0x270000) (BuildId: 34ad5bdf0830d77a)
> >
> ```

> This equals to a build id of 34ad5bdf0830d77a. The libflutter.so debug files downloaded as shown above could be verified using the file command:

> ```
> > % file ~/Downloads/libflutter.so
> > /Users/user/Downloads/libflutter.so: ELF 64-bit LSB shared object, ARM aarch64, version 1 (SYSV), dynamically linked, BuildID[xxHash]=34ad5bdf0830d77a, with debug_info, not stripped
> >
> ```

> Ensure the build IDs match, else you will not be able to symbolicate.

不过我往 bugly 上传了一个非严格匹配的符号表(上报 crash 的 app 使用的是自己编译的 `libflutter.so`，符号表是官方提供的 `libflutter.so`)，也能还原出来。让人很是疑惑。

观察了下，bugly 上看到的 native crash 日志中并没有找到类似 `(BuildId: 34ad5bdf0830d77a)` 这样的信息。*推测*由此导致匹配变得不严格，所以也能还原出来。

# 实例

某个 native crash 还原后得到如下堆栈信息。

![img](https://www.sunmoonblog.com/images/15938315565456.jpg)

首先看到的是 `message_loop_impl.cc` 中的 `MessageLoopImpl::RunExpiredTasksNow`。这个方法会立即执行任务队列中的已过期任务。

```
void MessageLoopImpl::FlushTasks(FlushType type) {
  TRACE_EVENT0("fml", "MessageLoop::FlushTasks");
  std::vector<fml::closure> invocations;

  task_queue_->GetTasksToRunNow(queue_id_, type, invocations);

  for (const auto& invocation : invocations) {
    invocation();
    std::vector<fml::closure> observers =
        task_queue_->GetObserversToNotify(queue_id_);
    for (const auto& observer : observers) {
      observer();
    }
  }
}

void MessageLoopImpl::RunExpiredTasksNow() {
  FlushTasks(FlushType::kAll);
}
```

接下来看到的是 `platform_view_android_jni.cc` 中的 `FlutterViewUpdateSemantics`

```
void FlutterViewUpdateSemantics(JNIEnv* env,
                                jobject obj,
                                jobject buffer,
                                jobjectArray strings) {
  // 调用 g_update_semantics_method (Java 层方法)
  env->CallVoidMethod(obj, g_update_semantics_method, buffer, strings);
  // 检查 Java 代码是否发生异常，有异常则写入 fml 日志中
  FML_CHECK(CheckException(env));
}

bool RegisterApi(JNIEnv* env) {
  // g_update_semantics_method 对应的是 
  // Java 层中的 FlutterJNI.updateSemantics() 方法
  g_update_semantics_method =
      env->GetMethodID(g_flutter_jni_class->obj(), "updateSemantics",
                       "(Ljava/nio/ByteBuffer;[Ljava/lang/String;)V");
  ...
  return true;
}

// 检查 Java 代码是否发生异常
// 发生异常时返回 false，否则返回 true
bool CheckException(JNIEnv* env) {
  if (env->ExceptionCheck() == JNI_FALSE)
    return true;

  jthrowable exception = env->ExceptionOccurred();
  env->ExceptionClear();
  FML_LOG(ERROR) << fml::jni::GetJavaExceptionInfo(env, exception);
  env->DeleteLocalRef(exception);
  return false;
}
```

最后看到的是 `logging.cc` 第92行。

```
LogMessage::~LogMessage() {
  stream_ << std::endl;

#if defined(OS_ANDROID)
  android_LogPriority priority =
      (severity_ < 0) ? ANDROID_LOG_VERBOSE : ANDROID_LOG_UNKNOWN;
  switch (severity_) {
    case LOG_INFO:
      priority = ANDROID_LOG_INFO;
      break;
    case LOG_WARNING:
      priority = ANDROID_LOG_WARN;
      break;
    case LOG_ERROR:
      priority = ANDROID_LOG_ERROR;
      break;
    case LOG_FATAL:
      priority = ANDROID_LOG_FATAL;
      break;
  }
  __android_log_write(priority, "flutter", stream_.str().c_str());
#elif defined(OS_IOS)
  syslog(LOG_ALERT, "%s", stream_.str().c_str());
#else
  std::cerr << stream_.str();
  std::cerr.flush();
#endif

  if (severity_ >= LOG_FATAL) {
    // 最终的 crash 由这里产生
    abort();
  }
}
```

初看起来可能会有点不明白 crash 如何产生，梳理一下就渐渐明了：

- 调用 `RunExpiredTasksNow` 立即执行队伍中的过期任务
- 过期任务中的某一个任务调用了 `FlutterViewUpdateSemantics()` (C++层)
- `FlutterViewUpdateSemantics()` 调用 `FlutterJNI.updateSemantics()` (Java层)
- `FlutterViewUpdateSemantics()` 调用 `CheckException()` 检查 Java 代码执行后是否产生异常
- `FlutterViewUpdateSemantics()` 调用 `FML_CHECK()` 记录错误日志 (如有 Java 异常)

所以我们可以把怀疑目标锁定到 `FlutterJNI.updateSemantics()` 方法。剩下的工作就是进入 Java 层分析该方法的出错原因了。

```
@Keep
public class FlutterJNI {
  ...
  
  /**
   * Invoked by native to send semantics tree updates from Flutter to Android.
   *
   * The {@code buffer} and {@code strings} form a communication protocol that is implemented here:
   * https://github.com/flutter/engine/blob/master/shell/platform/android/platform_view_android.cc#L207
   */
  @SuppressWarnings("unused")
  @UiThread
  private void updateSemantics(@NonNull ByteBuffer buffer, @NonNull String[] strings) {
    ensureRunningOnMainThread();
    if (accessibilityDelegate != null) {
      accessibilityDelegate.updateSemantics(buffer, strings);
    }
    // TODO(mattcarroll): log dropped messages when in debug mode (https://github.com/flutter/flutter/issues/25391)
  }
  
  ...
}
```

# 参考

- [获取并还原 Flutter Engine Crash 堆栈 | 区长](https://fucknmb.com/2019/10/20/获取并还原Flutter-Engine-Crash堆栈/)
- [Crashes · flutter/flutter Wiki](https://github.com/flutter/flutter/wiki/Crashes)