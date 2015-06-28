title: 万能的git分支管理
date: 2015-06-27 09:58:23
categories: git
tags: [git,branch]
---

今天地铁上看了《精通git》中的分支管理一章，刚好今天遇到一个场景，马上联系起来，心情非常激动，哈哈，这屌丝心里:)，先来说说今天遇到的场景

##场景

其实问题早就存在，只是在遇到git分支管理之前，一直都使用svn的蛋疼解决办法

1. 需求新版应用详情页开发（基于项目svn进行）
2. 开发完成后，界面拥挤，等待产品反馈过程中，对越狱应用进行重构，应用页也作相应重构
3. 此时经理老唐过来了解情况问：现在能不能独立上线appDetails，我说问题不大，重构没有基于原有代码。
4. 经理想先看一下效果，问题出现了

##问题

1. 重构与appdetails修改都基于项目svn，导致经理想看appdetails效果时，必须将重构appdetails的代码删除

2. 根据产品反馈新版appdetails取消，此时需要回滚新版代码，但是保留重构代码

3. 在测试完成之前，appdetails及重构都不能提交到当前svn，开发过程，无法追踪修改历程，也即没有commit记录

4. 如果appdetails开发过程中，需要解决原有系统中的bug，需要revert部分代码，再做修改

5. 重构代码无法分享给其他同事，同事需要另外配一个环境或修改nginx的配置来看新代码效果

那么我们来看看svn是如何解决上述这些问题的

##svn解决办法

1. 第一个问题由于我的修改都是基于一个备份，原来则改名为index.bak.html, 此时就需要把index.bak.html`重命名`index.html, 如果新功能不是基于备份修改，则更加蛋疼。

2. 问题二需要`手工删除`新版appdetails代码，保留重构代码，然后小心翼翼的所有功能测试一遍

3. 代码commit到`另外一个`以我名字命名的`svn目录`。同时，也利用这个目录来跟踪我的代码修改历史，每天下班前commit一次代码--!!!

4. 问题四依然需要备份新修改，revert部分代码，然后修改再提交

5. 问题五在问题三中解决了，测试环境需要重新手工配置

综上，svn解决这些问题的时候，都是比较简单粗暴，`采用新建svn目录`，`备份文件`，`回滚`，`手工对比`。

##git分支的优雅解决方法

1. 在内嵌页创建一个appdetails分支，所有修改都可以随时commit到该分支
重构开始时，创建另外一个分支ios_refactoring，重构代码commit到该分支
此次，需要给经理看代码时，只需切好到appdetails分支即可
```
git checkout -b appdetails master
git checkout appdetails
```
2. 同样，需要回滚appdetails的时候，只需删除appdetails分支即可,此时并不影响重构分支
```
git branch -d appdetails
```

3. 此问题需要搭配git服务器，然后把本地重构分支push到服务器，同事就只需要clone到本地即可
```
git clone ios_refactoring
```

4. 与问题2一样的，只要重新切换回到主干，然后就可以马上进行修改，修改完成后，再切换回相应的分支即可，同时，可以将主干的修改也merge到分支中，或者等分支完成后，合并到主干时，再一起合并
```
git checkout master //切换回主干
vim index.html  //fix bug
git commit -a -m "fixed some bugs"
git checkout ios_refactoring //切换回重构分支
git merge master // 将master的修改合并到分支
```
5. 问题五解决办法依然是搭配git服务器，在同个目录下，进行分支切换

##总结

现在一开始的问题就变成了分支间的`无痛切换`了，无需手工修改代码！！
```
git checkout master //切换回主干，修复原有系统的bug
git checkout appdetails // 切换回appdetails分支，可以供经理看可先上线版本的appdetails
git checkout ios_refactoring //切换回这个重构分支，继续我的优化工作
```
是不是很优雅呢 ：）， 心动不如行动~~
```
git init //初始化你的项目目录
git add . // 添加所有文件到暂存区
git commit -m "first commit" //提交暂存区文件
git checkout -b appdetails  //新建appdetails分支
vim index.html // 在这个分支上修改
git commit -m "new version appdetails" // 提交修改到appdetails分支
git checkout -b ios_refactoring // 基于appdetails分支，再新建ios_refactoring分支
```


