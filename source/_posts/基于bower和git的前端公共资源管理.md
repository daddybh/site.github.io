title: 基于bower和git的前端公共资源管理
date: 2015-06-27 10:53:55
tags: [bower, git]
categories: git
---

## 现有问题 ##

* 现有代码基于项目，即使公用也无法共享

* 继续抽象出来的公用模块，也需要复制到其他目录

* 公用模块有多份代码，需维护多份代码，容易导致代码不一致

* 更新困难，当公用模块修改一个bug时，需要搜索所有项目进行修改

* 模块间的依赖关系混乱


## 使用bower及git解决方案 ##

#### 公用模块抽取 ####

* 公用代码抽取出来作为一个独立的git项目

* 初始化该项目为一个bower项目

	该目录下执行 `bower init`

* 添加公用代码，并把代码提交到gitlab


#### 模块安装 ####

* 首先需要执行`bower init` 初始化调用目录
	
	初始化为bower项目的目的是为了保存该项目所引用的模块

* 执行`bower install + git地址 --save-dev`

	例如
	```
		bower install http://fedgit.teiron.com/zhangbh/bower-test.git --save-dev
	```
* 代码会下载到bower_components目录下，页面可直接应用bower_components下的文件

	例如
	```
		<script src="bower_components/bower-test/index.js"></script>
	```

#### 模块更新 ####

* 更新模块时，需要更新bower.json文件的版本号，bower通过该版本号来判断，所以记得更新公用项目时，更新版本号

* 调用方通过执行`bower update + 模块名` 来下载最新代码

#### 注意  ####

* 抽取公用代码，保存在git服务器，公用模块必须有一定可复用性，不能依赖具体项目

* bower_components不能提交源代码管理（git/svn）

* 用户下载代码后，执行`bower install` 安装项目依赖模块

#### 问题解决 ####

* 公用模块只存一份代码

* 调用方无痛更新模块

* 模块依赖关系由bower解决 

* 提高代码复用率



