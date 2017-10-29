---
title: gradle深入理解
date: 2017-02-24 14:41:42
tags: [android studio,gradle,module依赖]
---
自 android studio 推出，引入new build system --- gradle，这一利器，是对 android 开发者的福音。虽然目前互联网上相关资料不胜其数，但是门槛还是不低，能熟练使用的少数。本文总结 gradle 一些常用的应用，水平有限，如有不正，欢迎大家拍砖斧正！
<!-- more -->

## gradle 构建流程

## gradle 自动化

## 依赖远程开源库
在项目中使用 github 上的一些开源库，这些库可能没有打包上传 gradle 中央仓库，以致不能直接在 gradle 中引用。我们首先想到的可能是先下载 zip 或 clone 本地，然后  import module。这种方式繁琐不说，依赖库一旦有更新、修复，咱们又要重复操作一遍。

那有没有其他办法，直接依赖远程库，并且可以提交合并更新等操作？

答案肯定是有的.

### 1. 调整项目结构
普遍情况下，我们使用 android studio 都指定一个 workspace，然后在此目录里面新建 project，或git clone [remote poroject]，结构如下：
```shell
workspace
├── localA
├────── build.gradle
├────── setting.gradle
├── remote1
├────── lib
├───────── build.gradle
├────── build.gradle
└────── setting.gradle

```
假设localA 和 localB 都依赖 remote1/lib，按上面的 import 方式后，localA 和 localB里都有一个副本，且和remote1没有关联。

setting.gradle 中 include 使用 “:” 来表示“/”，且相对于 project 的根路径，如：
``` gradle
include ':lib',':app',':ext:other'

```

以前 Eclipse 中依赖其他 project，只是一个 add 一个 path，并不会复制副本。按这思路，还未找到 module 绝对路径的使用方法，有知道的望告知！

讲到这里，关于 android studio 的 project 、 module的概念需要补充下，可以理解为对应 Eclipse 中的 Set、project。android studio 中 同一个 project 下的 module 是可以直接依赖的。

这样的话，要是把 workspace 当做一个 project，其他 project 降到 module，是不就解决这问题了？

需要的仅仅是修改 setting.gradle 而已！上面的结构调整后如下：
```shell
workspace
├── localA
├────── build.gradle
├── remote1
├────── lib
├───────── build.gradle
├────── build.gradle
├────── setting.gradle
├── build.gradle
└── setting.gradle

```
以后新建项目，只要在 workspace 下新建 module ；依赖远程库时，当前目录下 git clone，再修改 workspace下的 setting.grale。

当然需要注意：如果 workspace module 过多，第一次打开 android studio 需要一点时间。不过要完美的体验，换 mac 吧，16G + ssd 无压力，打开一次，几乎不再退出，就是这么不闹心！


### 2. git submodule
android 源码就是多个子模块单独维护，使用 repo 脚本工具统一管理。老罗有一篇分析 repo 的文章，[戳这里](#Android源代码仓库及其管理工具Repo分析)。 repo 其实就是对 git 做了一层封装，管理子模块依赖关系的还是 [git submodule](#gitsubmodule完全使用教程)。

这里列出 git submodule，是对上面解决思路的补充，可以很方便更新、提交或修改等操作远程库！

有关 git submodule 使用，请参考上面的链接。

## gradle 插件
### TODO


## 参考阅读
- [Gradle脚本基础全攻略](http://blog.csdn.net/yanbober/article/details/49314255)
- [多模块构建管理](http://yamlee.me/2016/04/20/2016-04-20-GradleForAndroid%E7%B3%BB%E5%88%975/)
- [Android源代码仓库及其管理工具Repo分析](http://blog.csdn.net/luoshengyang/article/details/18195205)
- [git submodule 完全使用教程](http://www.kafeitu.me/git/2012/03/27/git-submodule.html)
