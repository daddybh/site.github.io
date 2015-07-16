title: a successful git branching model
date: 2015-07-13 11:17:43
tags: [git,branch,翻译]
---
A successful Git branching model（翻译）
======================================

原文链接：
http://nvie.com/posts/a-successful-git-branching-model/

*发现早早就有人翻译过了，囧rz*

```
http://www.oschina.net/translate/a-successful-git-branching-model
```

本文将介绍我在一年前，在所有项目（工作上和个人的）上引入一个开发模式，这个模式最终在实践上也很成功。
我之前一直想要写关于这方面的文章，但从没有时间去透彻的解析这个模式，直到现在。我不会在文中讨论关于我们项目
的细节，而只是介绍分支的策略和发布管理。

{% asset_img git-model@2x.png [git模型]%}

本文只关注于git作为一个源代码管理工具相关的实践。

## Why git?

关于git与其他中心化源代码工具的利弊对比，请看这个[链接](http://git.or.cz/gitwiki/GitSvnComparsion)。
网上也还有很多关于这方面的争论。作为一个开发者，我在所有工具中选择了git。Git真的改变开发者对于合并与分支的想法。
最为一个从传统CVS/Subversion过来人，合并/分支一直是有点可怕的东西（“小心合并冲突，他们会咬你”）而且很长时间才会做一次。

但是在git下，这些都是非常简单和廉价操作，也是日常工作流程中核心内容。例如，在CVS/Subversion书里面，分支与合并在最后一章
才会讲到（而且只针对高级用户），但是在每本git书里，基本上在第三章就会说到。

由于它的简单性和重复性，不用担心分支和合并的问题。相对其他事情，版本控制工具就理应更好的协助分支/合并工作。

关于工具内容就这样先，我们接着来看一下这个开发模型。这个模型实际上就是一个系列的步骤，每个团队成员都必须遵守，从而来实现一个
可管理的软件开发流程。

## 去中心化，但又中心化

仓库的设置和使用，也适用当前这个分支模型，也有一个中心的真正仓库。只是这个仓库被认为是中心节点（实际上git是一个分布式版本管理，
技术上是没有中心仓库这个概念）。我们把这个仓库称之为origin, 对于所有git用户对这个名字应该很熟悉。

{% asset_img centr-decentr@2x.png [git] %}

每个开发者都往orgin 拉取/推送修改。但除了与中心节点的push-pull关系外，每个开发者之间可以形成一个子team，相互拉取修改。
例如，当两个或多个开发同时在开发一个较大的新功能，但是推送到origin有点早，这时候这个功能非常有用。在上图中，有这些子team
Alice和Bob子team，Alice 和David 以及 Clair 和David。

实际技术上的实现就是，Alice建立一个git remote，命名为bob，然后指向Bob仓库，其它子team同样道理。

## 主要的分支

{% asset_img main-branches@2x.png [主要的分支] %}

该开发模型最核心的内容，是基于上面模型的启发。中心有两个永久存在的分支

* master
* develop

对于git用户应该很熟悉在origin仓库中的master分支，另外一个则称为develop.

我们认为 `origin/master` 源代码的HEAD指针，应该反应线上的真实状态的主要分支

我们认为 `origin/develop`源代码的HEAD指针，经常反应下一个版本更新的最新开发修改。有些人会叫做`集成分支`。nightly build也是这个分支。

当develop分支的源代码到达一个稳定的时候，而且可发布，所有的修改会合并回master，然后一个发布版本号打上标签。至于怎么做，接下来会讨论细节内容。

因此，每次当修改备合并到master时，实际意义就是一次全新的产品上线。对于这个操作我们有很严格的要求，所以理论上，我们会用一个Git钩子脚本，每次有commit到
master，执行自动化构建同时发布我们的软件到生产服务器。

## 其它支持的分支

除了`master`和`develop`分支外，我们的开发模型还有其他几个类型的分支，来辅助团队成员间的协同开发，更方便的追踪功能，产品发布准备以及快速修复线上问题。
与主分支不同的是，这些分支经常生命周期比较短，最终也都会被删除。

这些可能用到分支有以下几种类型：

* 功能分支 (feature branch)
* 发布分支 (release branch)
* 修复分支 (hotfix branch)

每种类型都有特定的意义，同时有严格的要求：应该从哪个分支从开始创建，一级应该最终合并到那个分支。一会我们会过一些这些细节。

这些“特殊”的分支，在技术上没有什么意义，这些分支是依据我们的使用方式进行分类，它们自然跟普通分支没什么区别。

#### 功能分支

{% asset_img fb@2x.png [feature branch] %}

由哪个分支checkout

    develop 
必须合并到

    develop
分支命名规则

    除了master，develop，release-* 和 hotfix-*之外

功能分支（有时也叫特性分支）主要用于开发一个即将或者很久以后才发布的版本，这个新功能将在那次发布中合并也是未知的。
基本上功能分支在开发过程中一直存在，最终会合并到develop分支（当确定要在即将发布中发布时）或者丢弃（万一体验非常糟糕时）。

功能分支基本上只存在开发者的仓库，不会出现在orgin

**创建一个功能分支**

当开始一个新功能开发，从develop分支checkout

```
    $ git checkout -b myfeature develop
    Switched to a new branch "myfeature"
```

**集成一个完成功能到develop**

已完成功能当确定要在接下来的发布一起发布时，会合并到develop分支

```
    $ git checkout develop
    Switched to branch 'develop'
    $ git merge --no-ff myfeature
    Updating ea1b82a..05e9557
    (Summary of changes)
    $ git branch -d myfeature
    Deleted branch myfeature (was 05e9557).
    $ git push origin develop
```

这里`--no-ff` 参数，merge时只会创建一个commit对象。就算是merge是`fast-forward`。这可以避免丢失功能分支的历史存在，
同时对所有的该功能的提交组合一起。对比：

{% asset_img merge-without-ff@2x.png [no-ff] %}

右边的merge方式，你无法从git历史记录中看出，那些commit是实现了某个功能，你必须通过读每条日志去分辨。
还原一个功能（比如一组commit），在第二种情况下是很头疼的一件事情，但加上`--no-ff`标识，则会很简单。

是的，你会创建一些（空）提交对象，但是好处比这个损失多。

不幸的是，目前还没有办法`git merge`时，将 `--no--ff`设置为默认，但真的需要带上这个标识。
 
#### 发布分支

由哪个分支checkout

    develop 
必须合并到

    develop和master
分支命名规则

    release-*
    
发布分支用于一个发布的准备工作，它运行最后一刻的细节修改。甚至，它允许一些细小的bug修复和准备发布用的meta-data（版本号，构建日期等）
这些工作都在发布分支中完成，`develop`分支被清出来用于下次大发布。

从develop分支切换一个发布的关键时刻应该是，当develop能够反映（几乎反应）下个发布的状况时。至少即将发布和构建的所有功能，都确定合并到
develop分支。其它后续发布的功能则不能涵盖在这个release中，它们必须等该分支切换出去后。

它是一个即将的发布，能够确切指定一个版本号的开始的时候-不是更早时候。直到那个时候，develop分支放映“下次发布”的修改，但它不清楚，这次发布
最终会变成`0.3` 还是`1.0`,知道最终发布分支开始。这个必须在release 分支开始的时候决定，根据项目的版本号跳跃规则。

**创建一个发布分支 **

发布分支从develop分支创建。例如，版本号1.1.5是当前线上的版本，我们有一个大版本即将发布。develop分支的已经准备好“下次发布”，同时，我们也决定
即将发布的版本称为1.2（而不是1.1.6 或2.0）. 所以我们可以从develop分支创建一个名字能够反应版本号的发布分支：

```
    $ git checkout -b release-1.2 develop
    Switched to a new branch "release-1.2"
    $ ./bump-version.sh 1.2
    Files modified successfully, version bumped to 1.2.
    $ git commit -a -m "Bumped version number to 1.2"
    [release-1.2 74d9424] Bumped version number to 1.2
    1 files changed, 1 insertions(+), 1 deletions(-)
```

创建一个新分支并切换到它后，我们更新版本号。这里，bump-version.sh是一个虚拟的shell脚本用于修改版本号。（当然也可以手工修改-这时候会有一些文件修改）
然后，版本修改会被提交。

这个新分支会存在一段时间，知道发布分支真正被发布出去。这段时间，bug的修复会合并到这个分支上（而不是在develop分支）。严格禁止添加大量的新功能。
它们必须合并到开发分支，等待下次发布。

**完成一个发布分支**

当发布分支到达一个可以真正发布的状态时，需要做几个事情。首先，发布分支合并到master（记住，master上的每个提交都是一次发布的定义）。
接着，master上的提交必须标签，以便将来回顾历史版本。最终，在release分支上的修改也必须合并会develop，从而将来的发布也会包含这些bug修复。

前两个git步骤如下：

```
    $ git checkout master
    Switched to branch 'master'
    $ git merge --no-ff release-1.2
    Merge made by recursive.
    (Summary of changes)
    $ git tag -a 1.2
```

这样发布就完成，然后添加tag用于将来引用

```
    Edit: 你也可以加上-s或者-u <key>标识来对你tag进行加密签名
```

为了保留发布分支的修改，我们需要合并到开发分支.

```
    $ git checkout develop
    Switched to branch 'develop'
    $ git merge --no-ff release-1.2
    Merge made by recursive.
    (Summary of changes)
```

这个步骤可能会导致合并冲突（很可能，因为我们改版了版本号）。如果这样，处理冲突并提交。

现在发布真正完成，发布分支可以删除，我们不再需要它。

```
    $ git branch -d release-1.2
    Deleted branch release-1.2 (was ff452fe).
```

#### 修复分支
 
{% asset_img hotfix-branches@2x.png [hotfix] %}

由哪个分支checkout

    master 
必须合并到

    develop和master
分支命名规则

    hotfix-*

Hotfix分支很想发布分支，因为它也是准备一个线上发布，虽然是计划之外的。它们方便于快速处理线上版本的问题。当一个线上版本出现一个致命错误，我们必须马上解决，
hotfix分支从master分支的标识服务器版本的tag中分支出来。

基本上，其它团队成员，还是继续在develop分支继续开发，但是另外一个成员准备一个快速的线上修复。

**创建hotfix分支**

hotfix分支基于master分支创建。例如，假设版本1.2是当前线上运行版本，因为一个严重的bug导致一些问题。但是在develop上的修改还不稳定。这是我们分出一个hotfix
分支来解决这个问题：

```
    $ git checkout -b hotfix-1.2.1 master
    Switched to a new branch "hotfix-1.2.1"
    $ ./bump-version.sh 1.2.1
    Files modified successfully, version bumped to 1.2.1.
    $ git commit -a -m "Bumped version number to 1.2.1"
    [hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
    1 files changed, 1 insertions(+), 1 deletions(-)
```

不要忘记在切出分支后，修改版本号

然后，修改bug并提交一次或多次commits:

```
    $ git commit -m "Fixed severe production problem"
    [hotfix-1.2.1 abbe5d6] Fixed severe production problem
    5 files changed, 32 insertions(+), 17 deletions(-)
```

**完成hotfix分支**

完成后，bug fix需要合并会master，同时也需要合并回develop，保证下次发布会包含这个bugfix。这流程跟发布分支的流程一模一样。

首先，更新master 和 为release添加tag

```
    $ git checkout master
    Switched to branch 'master'
    $ git merge --no-ff hotfix-1.2.1
    Merge made by recursive.
    (Summary of changes)
    $ git tag -a 1.2.1
```

Edit: 你也可以加上-s或者-u <key>标识来对你tag进行加密签名

接着，将bugfix也合并到develop

```
    $ git checkout develop
    Switched to branch 'develop'
    $ git merge --no-ff hotfix-1.2.1
    Merge made by recursive.
    (Summary of changes)
```

有一个特殊的情况是，当一个发布分支已经存在时，这个修复修改必须合并到发布分支，而不是develop。在release分支完成后，最终修改也自动同步到develop分支。
（如果develop分支等不及release分支完成，马上需要这个bugfix，你也可以合并这个bugfix到develop）

最后，删除这个临时分支

```
    $ git branch -d hotfix-1.2.1
    Deleted branch hotfix-1.2.1 (was abbe5d6).
```

## 总结

虽然这里没有很多全新的惊人的分支模型，大致的框架已经本文的开始那里，在我们的项目中超级有用。它让我们形成了一个优雅的思维模型，
一个容易实现，同时让我们团队成员，达成对分支与发布流程理解的共识。
















