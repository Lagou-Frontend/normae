## 0.1.1 / 2015-12-09

* 去掉对前端模版的压缩功能，如果artTemplate模版中含有除标签以外的`<`，一般是用做表示小于号，会导致使用fis-optimizer-html-minifier压缩时报错
* 更新fis-postprocessor-velocity的版本到0.0.5，将mock文件添加到对应的velocity模版的依赖缓存中，在开启`-wL`后，修改mock文件会自动增量release，刷新页面
* 解决修改子系统下server.conf中的路由还是使用缓存的文件没有变化的问题
* 在README中更新引入模块组件的方式以及自定义打包的方式

## 0.1.0 / 2015-11-25

支持按子系统release，每个模块下都有对应的fis-conf.js和server.conf来配置本模块的releae规则和url转发，通过修改根目录下的fis-conf.js文件中的releaseMods变量，
来release所有子系统或者指定的子系统：

``` javascript
var releaseMods = all;
// var releaseMods = [ 'dashboard' ];
```
## 0.0.12 / 2015-11-23

更新fis-postprocessor-velocity的版本到0.0.4，主要替换了解析velocity模版的引擎组件，旧的引擎会导致如`!${str.length()}`语法无法解析，并且mock数据中的标签默认会被自动转义

## 0.0.11 / 2015-11-03

* 不发布md为后缀的文档
* qa/prod模式不发布example project
* 暂时不再压缩html中的javascript代码，原因是如果javascript代码参杂velocity变量，会导致压缩报错

## 0.0.10 / 2015-10-16

* 修改目录规范，模拟数据的文件夹名由`mock`改为`test`，原因是fis内置的node server只能转发post的请求到根目录下的test文件夹中的模拟json文件
* `page`文件夹下的js文件也被认为是模块
* 在`qa/prod`的release模式中，为less文件添加了useHash

## 0.0.9 / 2015-09-25

添加逻辑：通过注释可以控制部分html代码只有在指定的release模式才能release，其他release模式将被删除。

## 0.0.8 / 2015-09-24

添加html静态代码检查插件normae-lint-html的依赖。

## 0.0.6 / 2015-09-19

* 修改目录规范，公共子系统下的模块文件夹名由widget改为widgets，独立子系统下的模块文件夹名由widget改为modules，公共子系统下的业务逻辑无关组件文件夹名由component改为components
* 只有components/widgets/modules文件夹下的js文件才被认为是模块化的js

## 0.0.4 / 2015-09-18

引入html片段的语法由```#parse()```的velocity语法替换为fis内置的内容嵌入语法
```<link rel="import" href="" />```，对应的开发阶段解析velocity的插件由
" fis3-parser-vm "替换为" fis-postprocessor-velocity "。

## 0.0.1 / 2015-09-07
