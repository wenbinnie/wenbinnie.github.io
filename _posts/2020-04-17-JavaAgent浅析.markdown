---
layout: post
title:  "JavaAgent 浅析"
---

### Java Agent 是啥？有什么用？



* 先说说有什么用，应用场景有：热部署工具JRebel、线上诊断工具btrace、greys、arthas，混沌实验注入工具chaosblade，覆盖率统计工具Jacoco

* 我的理解，它是JVM提供给用户修改字节码的接口与规范。核心包 java.lang.instrument，两个关键接口

  * Instrumentation 

    这个接口提供了修改字节码的相关服务，获取它的实例有两种方法：

    1. 当启动 JVM时指定了某agent类时，JVM会给agent 类的premain方法传递一个实例。jacoco就是使用这种方式工作的。属于静态加载agent类。
    2. 在JVM启动之后，使用某些方法启动agent类时，如java 的com.sun.tools.attach api，JVM会给agent类的agentmain方法传递一个实例。java bin下的工具、chaosblade和arthas都是使用这种方式。属于动态加载agent类，通过attach api 动态修改字节码
  * ClassFileTransformer

    此接口的实现类负责修改指定类的字节码。修改动作是在一个类被***defined***之前执行的。
    
    有了这些JVM提供的接口，我们就可以使用一些字节码增强类库，在ClassFileTransformer.transform方法中写增强逻辑，然后将此转化器注册到Instrumentation的实例中即可。

### ClassLoader

1. JVM加载一个类的过程是什么？理解【加载、链接（验证、准备、解析）、初始化】 

2. 转换器是在类***defined***之前执行转换动作的，你知道defined 对应上面哪个阶段么？





## 字节码增强类库

### Javassist

- 官方使用教程 http://www.javassist.org/tutorial/tutorial.html

- 类库使用特点：用户将要增强的代码用将代码文本插入方法的指定位置，这些位置可以是方法的开头、结尾，甚至可以指定在某行插入代码片段。在文本里写的代码与平时idea里不尽相同，类引用时需要写全限定类名，否则保持no such class:ArrayList
```java
ArrayList list = new java.util.ArrayList();
```

- 特殊字符

  javassist给用户提供了获取方法参数类型、参数值的方法，在你的代码文本片段中使用以下特殊字符，在方法实际运行时，可以被代替为相应的值。

  `$0` 代表 this  `$1`代表第一个参数 `$2`代表第二个参数 ...   

  $args 代表方法参数数组，类型是 Object[]`. $args[0]=$1
  
  $sig  是java.lang.Class数组， 代表方法中参数的类型
  
- 增强代码demo以及增强后的class（使用arthas jad）

  <img src="/Users/wenbinnie/Library/Application Support/typora-user-images/image-20200406210022074.png" alt="image-20200406210022074" style="zoom:50%;" />

<img src="/Users/wenbinnie/Library/Application Support/typora-user-images/image-20200406210755516.png" alt="image-20200406210755516" style="zoom:50%;" />



### ByteBuddy

* tutorial : https://bytebuddy.net/#/

















