---
layout: post
title:  "我所理解的NDK-JNI入门"
date:   2016-09-01 22:12:18 +0800
categories: Android
tags: JNI NDK
author: Lyon
---
* content
{:toc}
NDK开发主要是为了让项目可以调用一些底层C/C++的代码，应用的场景有两种，一个是这部分程序需要高效率，一个就是只能通过C/C++实现。我在学习阶段，暂时还没涉及到JNI-NDK开发，自己也不是梳理的很清楚，之所以写这篇博文，是发现网上关于NDK入门这块的文章，由于时间的关系，很多东西改变，已经不再正确了，为了不误导大家，我在这里记录下我开发第一个NDK程序过程。









# 软件环境

- NDK版本：NDKr12b
- AS版本   ：2.1.3
- Gradle   ：2.1.3

网上很多文章，不能说它错，只是因为过时了，所以我先注明我的软件版本，不保证高版本之后还是这样

---

# 配置工程

1. 根据自己系统下载NDK（翻墙请看前面文章） ，解压到任意目录

2. 新建一个Android 项目

3. 配置项目结构

   快捷键 Ctrl+Alt+Shfit+ S 打开ProjectStructure，填入NDK location

4. 在build.grade（Module）文件里加一句ndk{moduleName "任意"}

   ```java
       defaultConfig {
           applicationId "com.example.lyon.ndktest"
           minSdkVersion 15
           targetSdkVersion 24
           versionCode 1
           versionName "1.0"
           ndk{
               moduleName "JniLibName"
           }
       }
   ```

   “JniLibName”是等下生成so库的名字，可任意写。

5. gradle.properties文件加入

   ```java
   	android.useDeprecatedNdk=true
   ```


   以使用旧版本的Ndk，目前最新就是Ndkr12b，所以这句话暂时没用到

---

# 声明native方法

我在这里新建了一个NdkUtils类，里面声明了一个native方法。如下：

```java
package com.example.lyon.ndktest;

/**
 * Author: Lyon
 * Version: 1.0
 * Created: 2016/9/1 0001 21:45.
 */
public class NdkUtils {
    public static native String getStringFromC();

}
```

这里native是一定要写的，而且不能加{}，而static不是必须的，不要被网上有些文章误导了，如果不是static，就new一个对象在调用方法即可，这里写static是为了方便和规范。

---

# 生成C代码

网上很多文章用javah 先生成.h头文件，再新建jni目录，然后复制到jni目录，最后自己写C代码，这样也没有错误，但是AS提供了更方便的快捷键来实现一键生成C代码。选择刚刚那个native方法，点击Alt + Enter，AS会提示是否自动生成JNI方法，在方法里修改

```c
return (*env)->NewStringUTF(env, returnValue);
```

把returnValue改成任意字符串返回即可

---

# 使用

```java
//加载so库
static {
    System.loadLibrary("JniLibName");
}
//调用方法
Toast.makeText(MainActivity.this, NdkUtils.getStringFromC(), Toast.LENGTH_SHORT).show();
```

---

# 结束语

如果无法生成Jni方法，可以手动写，

- 如下：

  ```C
  #include <jni.h>

  JNIEXPORT jstring JNICALL
  Java_com_example_lyon_ndkdemo_NdkUtils_getStringFromC(JNIEnv *env, jclass type){

  return (*env)->NewStringUTF(env, "HelloNDK");
  }
  ```

- 更多配置见[官网](https://sites.google.com/a/android.com/tools/tech-docs/new-build-system/gradle-experimental)