title: grunt应用
date: 2015-06-27 10:52:30
tags: [grunt, 工程化, 构建]
categories: 工程化
---
##介绍
gruntjs是一个基于nodejs的自动构建化工具

[Gruntjs官网](http://gruntjs.com/)

[Getting Start](http://gruntjs.com/getting-started)

####[nodejs](http://nodejs.org/)####

```
Node.js is a platform built on Chrome's JavaScript runtime for
easily building fast, scalable network applications. Node.js uses an event-driven,
non-blocking I/O model that makes it lightweight and efficient,
perfect for data-intensive real-time applications that run across distributed devices.
```

* 基于Chrome v8引擎
* 事件驱动
* 非阻塞io模型
* 适合高并发，实时应用

####Gruntjs可以做什么####

* 文件压缩
* 代码合并
* 图片处理（雪碧图合并，图片压缩等）
* 自动化测试
* 代码预编译（模板，sass等）
* ...

####Why Gruntjs####

* 提高开发效率 (文件监听watch，自动刷新，自动构建等)
* 提高性能 （js、css合并压缩，图片压缩，雪碧图等）
* 质量保证（jslint， jshint）


##安装
* 安装nodejs

  nodejs安装包
  [32-bit](http://nodejs.org/dist/v0.10.28/node-v0.10.28-x86.msi)
  [64-bit](http://nodejs.org/dist/v0.10.28/x64/node-v0.10.28-x64.msi)

  nodejs 安装包自带[npm(Node Packaged Modules)](https://www.npmjs.org/)管理工具

  所有grunt插件都部署在npm的镜像上，用户可以通过
    > npm config set registry 镜像地址

  国内镜像地址有：
  > https://npm.taobao.org/

  > http://registry.cnpmjs.org/


* 安装grunt-cli（grunt命令行）
    >npm install -g grunt-cli

* 定位到你项目目录
    > cd d:\www\branches\applepc\ios_v2

* 初始化，根据提示输入项目信息，初始化完成后，会自动生成package.json文件
    > npm init

* 项目根目录，添加Gruntfile.js（文件名`区分`大小写）, Gruntfile.js内容基本结构如下：
    ```
    module.exports = function(grunt) {
        grunt.initConfig({

        });

        grunt.loadNpmTasks('grunt-contrib-uglify');

        grunt.registerTask('default', ['uglify']);

    }
    ```


* 通过npm install 安装你需要的插件. `注意:` 这里加上`--save-dev`参数，
安装完成后，会将该插件添加到package.json `devDependencies`字段

    >npm install grunt-contrib-copy --save-dev

    >npm install grunt-contrib-watch --save-dev

* 回到Gruntfile.js中，为新安装的插件添加任务
```
    module.exports = function(grunt) {
        grunt.initConfig({
           copy:{//配置copy任务，详细配置信息，请参考grunt-contrib-copy github页
                main:{
                    files:[
                        {
                            cwd: 'common/tpl/appDetails/compiled/',
                            src: 'template.js',
                            dest: 'common/js/bcvy6gt37&[8fgt4!3$8gfg82799/',
                            expand: true,
                            rename : function(dest, src){
                                return dest + "appDetailsTpl.js";
                            }
                        }
                    ]
                }
            }
        });

        grunt.loadNpmTasks('grunt-contrib-uglify');
        grunt.loadNpmTasks('grunt-contrib-copy');//加载任务

        grunt.registerTask('default', ['uglify']);
        grunt.registerTask('copy', ['copy']);//注册复制任务

    }
```

* 运行`grunt copy`就可以执行复制任务

* 自定义任务, grunt支持自定义任务，以腾讯tmod为例，请参考下面代码片段, 运行`grunt ct`可执行simple ArtTemaplte模板预编译
```
    module.exports = function(grunt) {
        grunt.initConfig({
           copy:{//配置copy任务，详细配置信息，请参考grunt-contrib-copy github页
                main:{
                    files:[
                        {
                            cwd: 'common/tpl/appDetails/compiled/',
                            src: 'template.js',
                            dest: 'common/js/bcvy6gt37&[8fgt4!3$8gfg82799/',
                            expand: true,
                            rename : function(dest, src){
                                return dest + "appDetailsTpl.js";
                            }
                        }
                    ]
                }
            }
        });

        grunt.registerTask('ct', 'complie template', function() {
            var TmodJS = require("tmodjs");
            var path = './common/tpl/appDetails/';
            var options = {
                output: 'compiled',
                helpers:'helpers.js',
                charset: 'utf-8',
                debug: false // 此字段不会保存在配置中
            }
            TmodJS.init(path, options);
            TmodJS.compile([], true);
        });

        grunt.loadNpmTasks('grunt-contrib-uglify');
        grunt.loadNpmTasks('grunt-contrib-copy');//加载任务

        grunt.registerTask('default', ['uglify']);
        grunt.registerTask('copy', ['copy']);//注册复制任务

    }
```

####目录结构及源代码管理####

* node_modules目录(svn ignores)
* package.json 及Gruntfile.js (svn)
* 执行npm install

##常见插件

* `grunt-contrib-uglify` 文件压缩工具 （[github](https://github.com/gruntjs/grunt-contrib-uglify)）
* `grunt-contrib-copy` 文件复制  （[github](https://github.com/gruntjs/grunt-contrib-copy)）
* `grunt-css-sprite` 从css文件中提取图片，生成雪碧图 ([github](https://github.com/laoshu133/grunt-css-sprite))
* `grunt-contrib-watch` 检测文件变化，然后执行相应的任务 ([github](https://github.com/gruntjs/grunt-contrib-copy))
* 更多插件，请参考官网([Grunt.js官网插件页](http://gruntjs.com/plugins))


##Gruntjs vs (gulp)[http://gulpjs.com/]

* Gruntjs配置难度高
* 任务配置不同，Gruntjs基于配置，gulp基于代码
* gulp接口更优雅
* gulp生态系统不如Grunt，日常使用应该足够
* Grunt的I/O操作是瓶颈，适用小项目


##reference
[gulpjs官网](http://gulpjs.com/)

[presentation build wars gulp vs grunt](http://markdalgleish.github.io/presentation-build-wars-gulp-vs-grunt/)

[Gruntjs vs gulpjs](http://jaysoo.ca/2014/01/27/gruntjs-vs-gulpjs/)
