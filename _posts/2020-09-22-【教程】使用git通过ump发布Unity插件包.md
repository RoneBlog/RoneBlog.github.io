---
layout:     post
title:      【教程】使用git通过ump发布Unity插件包（PackageManager）
subtitle:  邱国鹭
date:       2020-09-22
author:     Rone
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
     - unity
     -插件包
---

# 前言

最近做的一件事是开发tpns的通用模块，并基于git工程发布到Unity的PackageManager。
俗话说，会者不难，难者不会,因为事先没有文档的存在，因此在发布阶段花费了大概一天的时间（其实半个小时就差不多了），所以接着这个机会系统的了解一下，发布，以加深印象。同时也希望这篇文章可以帮助更多人的少踩一些坑。

# 先谈一谈插件包的规范

因为开发的时候，并不清楚发布package的流程，这就导致在发布package的时候不得不做出调整，修改文件目录，同时还要修改代码。每一次调整都可能会带来人为错误，修改，测试，修改……，最直观的影响就是开发时间上去了，继而模块整体开发效率。人为错误是无法避免的，是否能通过一些规范约定来尽可能避免那些问题呢？
我根据自己的经历，总结了以下4点，相信能解决大部分问题。
## 1.代码统一设置命名空间
模块内所有的代码逻辑，都应该在一个或者多个自定义的命名空间下。  
开发package的目的是为多个项目服务，不可避免的会出现相同的命名，如Main，Manager。package的开发不应对使用它的项目造成影响。所以说，引入命名空间是一个非常好的策略。

## 2.在项目中业务只能有一个相关根目录

在我开发的模块中，Assets下有这样的4个文件夹Demo,Document,Editor,Runtime。
**Runtime**:这里放置的模是能保证模块运行的内容。  
**Demo**：demo有关的资源，代码，配置…… 放置到这里。  
**Document**：不影响项目运行，具有说明性质的文档，放置到这里。  
**Editor**：所有编辑器代码都应该放到这下面，其他地方不应该出现编辑器相关的代码。  
![](https://roneasset.oss-cn-shanghai.aliyuncs.com/2020-09-22-143345.png)

## 3.demo和核心业务分离
这一点的目的，是为了可以让demo不用进入正式的项目中去，虽然一般情况下，demo并不大。我这个人有强迫症，总觉得这个demo跟项目没关系，就是不应该进包。

## 4.demo里和模块内不放置dll

同命名空间一样，也会面临着与正在开发的项目出现重复的情况。常见的解决方案是发布时不导入dll，由使用的项目自行导入。可以导入dll，也可以把dll包装成一个新的package发布，继而导入其package。


# 发布流程

## 1.创建package.json文件
在你发布的根目录下创建package.json文件，并设置你的工程的信息。这个本目录可以是你unity工程的Assets文件，也可以任意内容的文件夹。

```
{
"name": "com.rone.testpackage",
"displayName": "my testpackage",
"description": "this is my testpackage",
"version": "1.0.0",
"unity": "2018.3",
"license": "MIT",
"dependencies": {
 }
}
```


## 2.创建[.asmdef文件](hhttps://docs.unity3d.com/2018.3/Documentation/Manual/ScriptCompilationAssemblyDefinitionFiles.html)
根据实际情况创建.asmdef文件，Runtime和Editor要分别创建对应的.asmdef文件，前面说将所有editor脚本都放到Editor一个文件夹里，就是为了方便.asmdef文件的创建和管理。

通过 Assets->Create->Assembly Definition即可创建.asmdef文件，根据实际情况创建设置信息。  

![](https://roneasset.oss-cn-shanghai.aliyuncs.com/2020-09-22-142008.png)

下面的三张图是我之前开发tpns推送模块的目录结构，和设置信息，大家可以参考一下。  

![](https://roneasset.oss-cn-shanghai.aliyuncs.com/2020-09-22-143223.png)


**这一步很容易出现两个错误：**  
- .asmdef的平台设置出现错误
- Editor下的.asmdef文件没有引用Runtime下的.asmdef


## 3.上传git并发布

### 将要发布的模块上传并推送到远端
发布前需要将对应的内容上传并推送到远端，我的工程是上传到master分支，然后在发布。

### 开始发布
下面描述中开发模块的名字就用yourmodule来替代。  
因为发布的时候要执行4行指令，每行指令的参数相同又不相同，发布的时候不可避免的会出现上下参数不匹配的错误。这个错误直到导入package的时候，可能才会被发现，删除创建好的分支和标签，再重新发布，我想大部分人都不喜欢这些事情。所以我把发布指令写进了一个bat脚本，发布的时候改一改路径，名字和版本，双击一下bat脚本，很轻松的就发布了。  
下面是bat脚本的内容

ToolName：一般是yourmodule，但为了区分一般分支，所以有必要加上“upm-”前缀  
ToolVersion：这里采用yourmodule-version的模式，例如：发布版本1.0.1，ToolVersion可以是yourmodule-1.0.1  
ToolAssetPath：将要发布模块的Assets根目录或是有packge.json的目录

运行下面的命令，如果没有什么报错，就证明成功已经成功发布了。
```
::设置模块名字
SET ToolName=upm-yourmodule
::设置模块版本
SET ToolVersion=yourmodule-1.0.0
::设置模块源路径
SET ToolAssetPath=Plugin/RoneDir/Assets

::此命令会创建一个ToolName的分支，并同步ToolAssetPath下的内容
git subtree split -P %ToolAssetPath% --branch %ToolName%
:: 在ToolName分支设置标签ToolVersion节点
git tag %ToolVersion% %ToolName%

:: 推送到远端
git push origin %ToolName% %ToolVersion%
git push origin %ToolName%
pause
```

**Tips：如果远端已经有了当前版本的标签，会发布失败。这里有两个解决方案：**  
- 升版本。ToolVersion与package.json的版本信息相匹配。且要大于之前的版本。
- 删除远端的标签。
source的标签栏里可以预览现有的标签，要确定删除远端已经不存在你将要发布的标签。

# 导入已发布package的方式
在当前项目中找到Packages/manifest.json
添加你的要引用的模块信息
确认模块名称与版本信息与发布时在package.json里的信息是一致的。  
可参考下面的这段引用的设置：
```
"com.rone.testpackage":"http://192.168.51.222/pub/publicmodule.git#yourmodule-1.0.0",
```

# package-lock.json

**这里的内容为搬砖，点击[传送门](https://segmentfault.com/a/1190000017239545)，即可跳转至出处。我觉得这有有助于理解package-lock.json的用途。**  
在你了解 package-lock 甚至 package.json之前，你必须了解[语义版本控制](https://semver.org/)。 这是npm背后的天才，是什么使它更成功。简而言之，如果你正在构建与其他应用程序接口的应用程序，你应该告知你所做的更改将如何影响第三方与你的应用程序交互的能力。这是通过语义版本控制完成的，版本由三部分组成：X，Y，Z，分别是主要版本，次要版本和补丁版本。  
例如：1.2.3，主要版本1，次要版本2，补丁3。  
补丁中的更改表示不会破坏任何内容的错误修复。 次要版本的更改表示不会破坏任何内容的新功能。 主要版本的更改代表了一个破坏兼容性的大变化。 如果用户不适应主要版本更改，则内容将无法正常工作。

---
我简单看了一下，其实常规的业务逻辑只需要：name，version，lockfileVersion这三个字段就能满足版控需求。

```
{
	"name": "com.xxx.yourmodule",
	"version": "0.0.1",
	"lockfileVersion": 1
}

```


# 参考与引用
[【简书】开发Unity PackageManager 插件包](https://www.jianshu.com/p/153841d65846)  
[【Tutorial】Tutorial: How to develop a package for UnityPackageManager](https://www.patreon.com/posts/25070968)   
[【Unity Community Discussion】Package Manager : Git support on Package Manager](https://forum.unity.com/threads/git-support-on-package-manager.573673/)  
[【Unity Community Discussion】Package Manager : Setup for scoped registries (private registries)](https://forum.unity.com/threads/setup-for-scoped-registries-private-registries.573934/)  
[【Configuring npm】npm-package.json——Specifics of npm's package.json handling](https://docs.npmjs.com/files/package.json.html)  
[【unity Documention】Script compilation and assembly definition files](https://docs.unity3d.com/2018.3/Documentation/Manual/ScriptCompilationAssemblyDefinitionFiles.html)
