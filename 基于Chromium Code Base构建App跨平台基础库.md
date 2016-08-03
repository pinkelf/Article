## 背景
&emsp;&emsp;Chromium的代码结构清晰，质量非常高，其中base目录下面包括了智能指针、字符串处理、Message Loop、Timer、线程管理、系统路径管理、文件操作、内存管理、JNI接口生成(Android)等基础模块的跨平台实现。学习和了解这些基础模块代码，可以学习到许多基础知识，也更便于阅读Chromium code base，同时这些基础库可以直接应用到任何大型C++工程中。

## JNI Generator
&emsp;&emsp;写过Android JNI层代码的都知道，JNI接口定义和实现是非常繁琐和痛苦的事情，JNI/Java/C/C++函数签名的定义、数据类型的对应及其用起出错，另外JNI类型数据的分配和回收还得格外小心，否则很容易导致内存泄漏和Crash。
<br/>&emsp;&emsp;Google的工程师写了一个Python脚本自动生成JNI接口，目前Chromium Code base中大约有57个JNI文件9000余行的JNI代码是用这个工具生成的。这个脚本可以生成Java -> Native 和 Native -> Java 双向JNI接口。对于Java暴露给Native的JNI接口，只需要在对于的方法上面加上"@CalledByNative"标注；Native暴露给Java的JNI接口，需要在Java code中加上诸如"private native int nativeFoobar(String zoo)"的声明。

## 线程

## 参考资料
- [Chrome学习笔记（二）：UI组件，皮肤引擎 —— 基础设施篇](http://bigasp.com/archives/520 )
- [Chromium 基础库使用说明](https://www.zybuluo.com/rogeryi/note/56894)
- [JNI on Chromium for Android](https://www.chromium.org/developers/design-documents/android-jni)
- [Cross-platform develeopment with Chromium](http://geekluo.com/contents/2014/05/21/19-cross-platform-development-with-chromium.html)
- [iOS Run Loops in Chromium](http://geekluo.com/contents/2014/04/22/11-ios-run-loops-in-chromium.html)
- [Chrome是新的C运行时](http://www.labazhou.net/2014/01/chrome-is-the-new-c-runtime/)
- [Chromium网络协议栈架构浅析](https://blog.finaltheory.me/research/Chromium-Network-Stack.html)
- [chromium base 目录概述](https://docs.google.com/document/d/1kGTXjfBfOZxGjmifT-LJqnAaa04VVTtpNHtqo_P67o4/edit?pref=2&pli=1#heading=h.fhc0z5y5dc6u)
