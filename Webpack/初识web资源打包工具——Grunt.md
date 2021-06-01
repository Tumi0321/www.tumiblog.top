---
title: 初识 web 资源打包工具——Grunt
date: 2021-6-2 00:37:16
tags:
  - Grunt
categories:
  - Webpack
---

[Grunt](https://www.gruntjs.net/) 是一个基于 Node.js 的自动化构建工具，是一个 JavaScript 任务运行器。配合一些插件可以对需要反复重复的任务，例如压缩（minification）、编译、单元测试、linting 等，实现自动化，简化你的工作。当你在 [Gruntfile](https://www.gruntjs.net/sample-gruntfile) 文件正确配置好了任务，任务运行器就会自动帮完成大部分无聊的工作。



## 快速开始



Grunt和 Grunt 插件是通过 [npm](https://www.npmjs.org/) 安装并管理的，npm是 [Node.js](https://nodejs.org/) 的包管理器。



Grunt 0.4.x 必须配合 Node.js `>= 0.8.0` 版本使用。



检查是否安装了 Node.js



```bash
$ node -v
```



将 Grunt 命令行（CLI）安装到全局环境中



```bash
$ npm install -g grunt-cli
```



Grunt-cli 只是一个命令行工具，用来执行一些接口的，而不是 Grunt 这个工具本身。



生成 package.json 文件



```bash
$ npm init
```



安装 Grunt 到开发依赖中



```bash
$ npm install grunt --save-dev
```



在项目根目录中添加 Gruntfile.js 或 Gruntfile.coffee 文件，用来配置或定义任务（task）并加载 Grunt 插件。



```js
module.exports = function(grunt) {

  // 初始化配置
  grunt.initConfig({
    // 相当于一个变量，读取的是 package.json 文件的内容
    pkg: grunt.file.readJSON('package.json'),
    // 对插件的配置
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // grunt 任务执行的时候去加载对应的插件
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 注册 grunt 的默认任务
  grunt.registerTask('default', ['uglify']);

};
```



## Gruntfile 文件



Gruntfile 由以下几部分构成：



- "wrapper" 函数
- 项目与任务配置
- 加载 grunt 插件和任务
- 自定义任务



```js
module.exports = function(grunt) {

  // 项目配置文件
  grunt.initConfig({
    // 读取 package.json 文件
    pkg: grunt.file.readJSON('package.json'),
    // 任务名
    uglify: {
      // 插件的选项，用来配置插件的
      options: {
        // 动态生成一个文件头注释
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        // 指定被压缩的文件路径
        src: 'src/<%= pkg.name %>.js',
        // 指定压缩后的文件输出路径
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // 加载包含 "uglify" 任务的插件。
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认被执行的任务列表。
  grunt.registerTask('default', ['uglify']);

};
```



在上面列出的这个 `Gruntfile` 中，`package.json`文件中的项目元数据（metadata）被导入到 Grunt 配置中， [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify) 插件中的 `uglify` 任务（task）被配置为压缩（minify）源码文件并依据上述元数据动态生成一个文件头注释。当在命令行中执行 `grunt` 命令时，`uglify` 任务将被默认执行。



## 常用插件



插件分为两类，grunt 团队提供的插件，以 grunt 开头命名；第三方提供的插件，也可以自己写。



- [grunt-contrib-clean](https://github.com/gruntjs/grunt-contrib-clean)——清除文件（打包处理生成的）
- [grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat)——合并多个文件的代码到一个文件中
- [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify)——压缩 js 文件
- [grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint)——JavaScript 语法错误检查
- [grunt-contrib-cssmin](https://github.com/gruntjs/grunt-contrib-cssmin)——压缩/合并 css 文件
- [grunt-contrib-htmlmin](https://github.com/gruntjs/grunt-contrib-htmlmin)——压缩 html 文件
- [grunt-contrib-imagemin](https://github.com/gruntjs/grunt-contrib-imagemin)——压缩图片文件（无损）
- [grunt-contrib-copy](https://github.com/gruntjs/grunt-contrib-copy)——复制文件、文件夹
- [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)——实时监控文件变化、调用相应的任务进行处理



以下举例个别插件的基本使用，其余都大同小异。详细使用参照官网。



## 合并文件



grunt-contrib-concat 用于合并多个文件。



安装



```bash
$ npm install grunt-contrib-concat --save-dev
```



启用



```js
grunt.loadNpmTasks('grunt-contrib-concat');
```



配置



```js
grunt.initConfig({
  concat: {
    options: {
      // 指定分隔符
      separator: ';',
    },
    dist: {
      // 指定需要合并的文件，也可以指定 *.js
      src: ['src/intro.js', 'src/project.js', 'src/outro.js'],
      // 合并后输出位置
      dest: 'dist/built.js',
    },
  },
});
```



执行



```bash
$ grunt concat
```



## 压缩 JS



grunt-contrib-uglify 用来压缩 js 文件。



安装



```bash
$ npm install grunt-contrib-uglify --save-dev
```



启用



```js
grunt.loadNpmTasks('grunt-contrib-uglify');
```



配置



```js
grunt.initConfig({
  uglify: {
    my_target: {
      files: {
        // 键为压缩后的位置，值为需要压缩的文件位置
        'dest/output.min.js': ['src/input1.js', 'src/input2.js']
      }
    }
  }
});
```



执行



```bash
$ grunt uglify
```



插件不单单是反文件压缩为一行。当你在文件中写了无用代码时，例如声名了无用变量，插件在压缩时会自动删除该变量。



## 语法检验



grunt-contrib-jshint 是对 js 文件做一个语法检验。



安装



```bash
$ npm install grunt-contrib-jshint --save-dev
```



启用



```js
grunt.loadNpmTasks('grunt-contrib-jshint');
```



配置



```js
grunt.initConfig({
  jshint: {
    // 需要检验的文件
    all: ['Gruntfile.js', 'lib/**/*.js', 'test/**/*.js']
  }
});
```



创建一个 `.jshintrc` 文件 



```json
{
  "curly": true,
  "eqnull": true,
  "eqeqeq": true,
  "expr": true,
  "immed": true,
  "newcap": true,
  "noempty": true,
  "noarg": true,
  "regexp": true,
  "browser": true,
  "devel": true,
  "node": true,
  "boos": false,
  // 不能使用未定义的变量
  "undef": true,
  // 语句后面必须有分号
  "asi": false,
  // 预定义不检查的全局变量
  "predef": ["define", "BMap", "angular", "BMAP_STATUS_SUCCESS"],
  "globals": {
    "jQuery": true
  }
}
```



执行



```bash
$ grunt jshint
```



## 文件监听



由于项目中引入的是打包好的文件，当你对源文件修改时，页面是不是发生变化的。



如果要对文件实时监听修改并重新打包，需要用到 grunt-contrib-watch 插件。



安装



```bash
$ npm install grunt-contrib-watch --save-dev
```



启用



```js
grunt.loadNpmTasks('grunt-contrib-watch');
```



配置



```js
grunt.initConfig({
  watch: {
    scripts: {
      // 需要监听的文件
        files: ['**/*.js'],
      // 源文件发生了改变会自动调用 tasks 中的任务
        tasks: ['jshint'],
        options: {
        // fase: 变量更新，文件改变了执行对应的 tasks
        // true: 全量更新，文件改变了执行全部的 tasks
        spawn: false,
        }
    }
    }
});
```



执行



```bash
$ grunt watch
```



## 自定义任务



每个任务都需要手动执行是非常麻烦的，当两个任务有所关联时就可能出错。这时候就需要使用一个任务来控制多个任务的执行。



`grunt.registerTask` 就是实现这样一个作用。



```js
grunt.registerTask('default', ['concat', 'uglify', 'jshint', 'watch']);
```



启动任务



```bash
$ grunt default
```



此时数组中的任务会按照顺序执行， grunt 默认执行 default 任务，所以可以省略直接使用 `grunt`。



如果项目中使用了 watch 插件，最好是将该任务与 default 任务分离。



```js
grunt.registerTask('default', ['concat', 'uglify', 'jshint']);
grunt.registerTask('myWatch', ['default', 'watch']);
```



因为当项目打包上线时，是用不到 watch 插件的。