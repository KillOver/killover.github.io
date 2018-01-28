---
layout:     post
title:      Linux上部署OpenCV 3
subtitle:   
date:       2017-10-28
author:     一条小虫
header-img: img/Linux上部署OpenCV 3/cover.jpg
catalog: true
tags:
    - Linux
---

# 安装 java 和 python

centos3.x 需要 java1.8+ 和 python2.7.x 的版本。

# 安装依赖包

1. 安装以下依赖：

```
yum install cmake  
```

2. 安装ant

```
yum install ant
```

# 安装opencv

1. 去官网下载 opencv3.x 的源码包（sources）：

  [http://opencv.org/releases.html](http://opencv.org/releases.html)

2. 把源码包解压后放在 `/home`  目录下;
3. 在 opencv 源码目录下新建 `build` 目录，然后在该目录下编译源码，安装opencv;

```
cd /home/opencv-3.2.0  
```

慢慢等吧。完了就可以使用c++开发了。

> ⚠️ 在编译的过程中可能会遇到如下问题：
> ![](/img/Linux上部署OpenCV 3/opencv compile error.png)
> 解决办法：修改出问题的 jas_math.h 文件
> `vi /usr/include/jasper/jas_math.h`
> 在 `#include <stdint.h>` 后添加如下代码：
> `#if ! defined SIZE_MAX`


4. 生成 jar：

```
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -DBUILD_TESTS=OFF ..
```

5. 配置JVM运行参数：

进入到jetty目录下，编辑 `bin/jetty.sh` 。找到如下这行代码：

```
JAVA_OPTIONS+=("-Djetty.home=$JETTY_HOME" "-Djava.io.tmpdir=$TMPDIR"）
```

增加一个 `-Djava.library.path` 参数，其值为安装动态库的位置：

```
JAVA_OPTIONS+=("-Djetty.home=$JETTY_HOME" "-Djava.io.tmpdir=$TMPDIR" "-Djava.library.path=/usr/local/OpenCV/java")
```

如果上面配置的这个参数不行，可以使用opencv build目录下的bin目录：

```
JAVA_OPTIONS+=("-Djetty.home=$JETTY_HOME" "-Djava.io.tmpdir=$TMPDIR" "-Djava.library.path=/home/opencv-3.2.0/build/bin")
```


