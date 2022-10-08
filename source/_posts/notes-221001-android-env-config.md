---
title: Android 开发环境
tags: Android
categories: 默认
date: 2022-10-01 12:00:00
---

### 记录安装开发环境的配置

其实如果一个项目被Android Studio打开.是不需要配置任何环境变量都能被正确编译的

但是我们需要的不仅仅是这样

下载SDK与开发工具的位置

```
D:\Android\Android Studio
D:\Android\SDK
D:\Android\Home
D:\Android\Flutter
D:\Android\Gradle
```

### Java环境配置

这个时候我们可以使用Android Studio自带的JDK与JRE

``` properties
JAVA_HOME=D:\Android\Android Studio\jre
JRE_HOME=D:\Android\Android Studio\jre\jre
CLASSPATH=.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
```

### Android环境配置

配置好Android SDK的位置.

> 配置GRADLE_USER_HOME的原因是因为我想控制gradle的缓存位置

``` properties
Android_SDK_HOME=D:\Android\Home
ANDROID_HOME=D:\Android\SDK
ANDROID_SDK_ROOT=%ANDROID_HOME%
GRADLE_USER_HOME=D:\Android\Gradle
```

### Flutter环境变量

若要配置Flutter

``` properties
FLUTTER_HOME=D:\Android\Flutter
FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
PUB_HOSTED_URL=https://pub.flutter-io.cn
```


### PATH

加入到PATH,方便命令行可以调用

```
%JAVA_HOME%\bin
%JRE_HOME%\bin
%ANDROID_HOME%\platform-tools
%FLUTTER_HOME%\bin
```

### Gradle 下载代理

> D:\Android\Gradle\gradle.properties

``` properties
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=1080
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=1080
```

### 完成

经过以上配置.任意一个Android项目下载到本地,不需要Android Studio打开.

可以直接通过gradlew assembelDebug直接进行编译.在只想运行一下别人项目的时候十分便捷.