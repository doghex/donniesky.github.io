---
layout:     post
title:      "Android混淆"
subtitle:   "Android混淆简单介绍"
date:       2016-04-15
author:     "donnieSky"
header-img: "img/post-bg-proguard.jpg"
catalog: true
tags:
    - Android
    - ProGuard
---

# Android混淆

> 最近项目打包用到混淆，查了很多资料，为了增强记忆，这次用博客记录一下！

----

## 启用ProGuard

* 在app文件夹下`build.gradle`中进行配置,将`minifyEnabled`改为`true`：
```java
buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
```
如上代码所示，在`release`打包是就会按照我们`proguard-rules.pro`的配置进行混淆，在debug时是不会进行混淆的。

## 混淆ProGuard常用语法

后面的文件名，类名，或者包名等都可以使用占位符代替，`？`标示一个字符可以匹配多个字符，但如果是一个类，不会匹配其前面的包名，`*`可以匹配多个字符，会匹配前面的包名。

* **输入输出选项：**
    1. `-include filename`   从给定的文件中读取配置参数
    2. `-injars class_path`   输入(即使用的)jar文件路径
    3. `-outjars class_path`   输出jar路径
    4. `-libraryjars class_path`   指定的jar将不被混淆
    5. `-skipnonpubliclibraryclasses`   跳过(不混淆)jars中的非public classes
    6. `-dontskipnonpubliclibraryclasses`   不跳过(混淆)jars中的非public classes默认选项
    7. `-dontskipnonpubliclibraryclassmenbers`   不跳过jars中的非public classes的members
    8. `-keepdirectories [directory_filter]`   指定目录keep在out jars中
* **保持不变的选项(混淆不进行处理的内容):**
    1. `-keep {Modifier} {class_specification}`   保护指定的类文件和类的成员
    2. `-keepclassmembers {modifier} {class_specification}`   保护指定类的成员，如果此类受到保护他们会保护的更好
    3. `-keepclasseswithmembers {class_specification}`   保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在
    4. `-keepnames {class_specification}`   保护指定的类和类的成员的名称（如果他们不会压缩步骤中删除）
    5. `-keepclassmembernames {class_specification}`   保护指定的类的成员的名称（如果他们不会压缩步骤中删除）
    6. `-keepclasseswithmembernames {class_specification}`   保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）
    7. `-printseeds {filename}`   列出类和类的成员-keep选项的清单，标准输出到给定的文件
* **压缩选项：**
    1. `-dontshrink`   不启用 shrink。shrink操作默认启用，主要的作用是将一些无效代码给移除，即没有被显示调用的代码。
    2. `-printusage {filename}`   打印被移除的代码，在标准输出
    3. `-whyareyoukeeping {class_specification}`   打印在shrink过程中为什么有些代码被keep
        
* 一般一个Android应用项目创建成功后，系统默认有一个空的写满注释的文件`proguard-rules.pro`,我们可以在安装`SDK`目录`Android_SDK\tools\proguard`下找着`proguard-android.txt`这个文件，里面有系统默认准备的一些配置:


```java

#This is a configuration file for ProGuard.
#http://proguard.sourceforge.net/index.html#manual/usage.html

-dontusemixedcaseclassnames  #不使用大小写混合
-dontskipnonpubliclibraryclasses  #不混淆第三方jar
-verbose  #混淆时记录日志

# Optimization is turned off by default. Dex does not like code run
# through the ProGuard optimize and preverify steps (and performs some
# of these optimizations on its own).
-dontoptimize  #不优化输入的类文件
-dontpreverify  #混淆时不做预校验
# Note that if you want to enable optimization, you cannot just
# include optimization flags in your own project configuration file;
# instead you will need to point to the
# "proguard-android-optimize.txt" file instead of this one from your
# project.properties file.

-keepattributes *Annotation*
-keep public class com.google.vending.licensing.ILicensingService
-keep public class com.android.vending.licensing.ILicensingService

# For native methods, see http://proguard.sourceforge.net/manual/examples.html#native
-keepclasseswithmembernames class * {
    native <methods>;
}

# keep setters in Views so that animations can still work.
# see http://proguard.sourceforge.net/manual/examples.html#beans
-keepclassmembers public class * extends android.view.View {
   void set*(***);
   *** get*();
}

# We want to keep methods in Activity that could be used in the XML attribute onClick
-keepclassmembers class * extends android.app.Activity {
   public void *(android.view.View);
}

# For enumeration classes, see http://proguard.sourceforge.net/manual/examples.html#enumerations
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

-keepclassmembers class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
    public static <fields>;
}

# The support library contains references to newer platform versions.
# Don't warn about those in case this app is linking against an older
# platform version.  We know about them, and they are safe.
-dontwarn android.support.**

# Understand the @Keep support annotation.
-keep class android.support.annotation.Keep

-keep @android.support.annotation.Keep class * {*;}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <methods>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <fields>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <init>(...);
}

```

----

> 时间有点晚了，后续明天继续补偿！

