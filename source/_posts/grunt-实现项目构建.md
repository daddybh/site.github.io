title: grunt 实现项目构建
date: 2015-06-27 10:48:40
tags: [grunt, 工程化, 构建]
categories: 工程化
---
##背景
随着模块化进展，项目js文件越来越多，下面是越狱应用与正版应用的js引用， 真正发布产品过程，不可能直接将该文件丢到生产环境。
因此，发布前需要进行以下操作，`压缩，合并js/css文件`，`编译模板`，`生成雪碧图`, `文件进行缓存处理(filerev)`
下面代码片段是越狱应用的js引用列表：

```
    <script src="/common/js/jquery-1.7.2.min.js"></script>
    <script src="/common/js/json3.min.js"></script>
    <script src="/common/js/jqplugin.hashchange.min.js"></script>
    <script src="/common/js/tr.jqplugin.slide.min.js"></script>

    ... 此处省去20个引用...
        
    <script src="/common/js/iosV2/topicDetailController.js"></script>
    <script src="/common/js/iosV2/searchController.js"></script>
    <script src="/common/js/iosV2/main.js"></script>
```

本文档将介绍如何通过`useminPrepare` 和 `usemin`来构建一个`build`流程。

##构建过程
构建的最终结果是需要一个能正常运行的，可发布的程序包，同时，我们要保留源代码的结构以及原来的样子。
因此我们需要把程序构建（build）到子目录(dist)，这时，一个构建过程大概需要以下步骤：

* 清空dist目录，清空以前的构建代码，使之后编译到该文件夹的文件都是新的

* 文件复制（copy），由于我们要保留源文件，因此我们把文件复制到指定目录，再进行后续处理

* 合并js文件(concat)

* 压缩合并后的js(uglify)

* 计算压缩后js/css的md5或sha1值，替换原来的引用

实现以上步骤，则需要一系列的grunt任务

##难点

其实构建过程很简单，无非是压缩、合并，其中最麻烦的是，如何通过gruntjs，自动的去替换原来的20几个应用

##useminPrepare 与 usemin

整个构建流程主要通过`grunt-contrib-useminPrepare` 和`grunt-usemin`实现
useminPrepare原理是通过类似 ` <!-- build:js /common/js/xxx.js--> <!-- endbuild -->`的注释块包含需要替换的js或css文件列表
动态生成concat，uglify，cssmin的任务，然后替换原来的引用
有了它，我们不需要在Gruntfile.js中的concat，uglify，cssmin添加一堆文件名
只需在最终build task中，添加concat，uglify，cssmin任务就可以
下面build任务可以很清楚看出构建流程
```
    grunt.registerTask('build', ['clean',
                                'copy:build',
                                'useminPrepare',
                                'concat',
                                'cssmin',
                                'uglify',
                                'filerev',
                                'usemin']);
```

useminPrepare和usemin的配置也非常简单
> 注意：
> * useminPrepare需要添加 options:{ root :"."} , 否则无法正常生成文件
> * usemin 需要制定assetsDirs 为dist/, 后续使用filerev时，才能正常找到对应的文件
> * 坑爹的目录关系！！

```
    useminPrepare: {
            html: ['dist/jb_app/index.html','dist/zb_pp_v2/index.html'],
            options:{
                root :"."
            }
        },
        usemin:{
            html: ['dist/jb_app/index.html','dist/zb_pp_v2/index.html'],
            options: {
                assetsDirs: ['dist/'] //指定改目录，让usemin寻找用来替换revved文件
            }
        }
```

##grunt-filerev插件

以前对于静态文件的处理方法，在文件后面加上?v=10001 或者加上修改时间?update=20140203，这么做的问题虽然解决缓存问题，但是：
1. 需要手工修改静态文件
2. 如果文件没做出任何修改时，有时候会导致客户端重新加载文件

该插件按照一定的算法（md5，sha1等）生成一个hash值，然后取hash值的前几位作为文件名的一部分, 这样做的好处是，对于没修改过的文件，我们可以永久缓存到客户端



##最后
依赖的grunt插件有 `grunt-contrib-copy`, `grunt-contrib-clean`, `grunt-contrib-useminPrepare`, `grunt-contrib-concat`, `grunt-contrib-cssmin`, `grunt-contrib-uglify`, `grunt-usemin`,`grunt-filerev`
