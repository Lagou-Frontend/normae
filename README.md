# normae
normae![npm status](https://img.shields.io/npm/v/normae.svg)是基于[fis3](http://fis.baidu.com/fis3/index.html)的拉勾网前端解决方案

* 后端为Java＋Velocity
* 采用less作为css处理器
* 采用amd模块化开发方案
* 采用bower管理第三方依赖

阅读本文档前，建议先阅读[fis3的文档](http://fis.baidu.com/fis3/docs/beginning/intro.html)，很多涉及到fis3的内容都没有提及，请查看相应的文档。

## 目录规范

```
site //能独立提供服务，具有单独二级域名的产品线
 ├ common //通用子系统
 | ├ components //组件，业务逻辑无关
 | | ├ popoup
 | | | | ├ popoup.js
 | | | | └ popoup.less
 | ├ static //非组件模块资源目录，包括模板页面引用的静态资源（favicon.ico）
 | ├ widgets //模块，业务逻辑相关
 | | ├ header
 | | | | ├ header.html
 | | | | ├ header.js
 | | | | └ header.less
 ├ dep //第三方依赖
 | ├ jquery
 | | └ jquery.js
 | ├ esl
 | | └ esl.js
 ├ test //测试数据
 | ├ common //所有模版共用的模拟数据，主要放置velocity tool相关的方法
 | | └ common.js
 | ├ ajax //模拟ajax请求返回的数据
 | | └ test.json
 ├ sub //子系统，具有较清晰业务逻辑关系的功能业务集合
 | ├ page //具有独立URL的输出内容
 | | ├ index
 | | | | ├ index.html //velocity模版
 | | | | ├ index.html.js //页面对应的mock数据
 | | | | ├ index.less
 | | | | └ index.js
 | ├ static
 | ├ modules
 | | ├ content
 | | | | ├ img
 | | | | ├ tpl //放置模块的前端模版
 | | | | | └ content.tpl
 | | | | ├ content.html
 | | | | ├ content.js
 | | | | └ content.less
 | ├ fis-conf.js //子系统的fis配置文件
 | ├ server-conf.js //子系统的url模拟转发配置文件
 ├ .bowerrc //bower配置文件
 ├ bower.json //bower package文件
 ├ fis-conf.js //fis配置文件
 ├ server.conf //本地url模拟转发配置文件
```


## 使用

### 本地安装normae

``` shell
$ npm install normae
```

### 全局安装bower

``` shell
$ npm install bower -g
```

### normae内置了3种release模式
* 默认的dev模式，用于本地调试，会编译velocity，模拟转发url。

	``` shell
	$ normae release
	```
* qa模式，用于测试环境测试，会添加md5戳，打包，合成精灵图。

	``` shell
	$ normae release qa
	```
* prod模式，用于生产环境上线，会添加md5戳，打包，合成精灵图，压缩。

	``` shell
	$ normae release prod
	```
具体配置请参考[这里](https://github.com/vicerwang/normae/blob/master/index.js)。<br />
normae的三种release方式只提供了一些基本的匹配处理规则，如果想添加适合本项目的特殊规则，如具体的打包规则等，请在项目根目录下的fis-conf.js文件中添加规则来扩展或覆盖基本的规则。

### 按子系统release
随着子系统的增加，给我们的日常开发带来了以下两个问题

1. 整个系统releae，时间越来越长，自动刷新功能总是延迟很多
2. `fis-conf.js`，`server.conf`配置文件内容越来越多，变得难以维护

因此，normae在v0.1.0将release的粒度改到子系统的层级，相应地需要在子系统目录下添加fis-conf.js以及server.conf来配置release规则和url转发规则，而根目录下的fis-conf.js以及server.conf则放置一些common的配置。通过修改根目录下的fis-conf.js文件中的releaseMods变量，来release所有子系统或者指定的子系统：

``` javascript
var releaseMods = [ 'dashboard' ];
// var releaseMods = 'all';

var root = fis.project.getProjectPath();
var confs = fis.util.find(root, '/**/fis-conf.js');
var path = require('path');
var mods = [];
confs.forEach(function(conf) {
    var mod = path.dirname(path.relative(root, conf));
    mod !== '.' && mods.push(mod);
});
mods.forEach(function(mod) {
    if (releaseMods === 'all') {
        require('./' + mod + '/fis-conf');
        delete require.cache['./' + mod + '/fis-conf'];
    } else if (releaseMods.indexOf(mod) === -1) {
        fis.match('/' + mod + '/**/*', {
            release: false
        });
    } else {
        require('./' + mod + '/fis-conf');
        delete require.cache['./' + mod + '/fis-conf'];
    }
});
```
需要注意以下几点：

* 如果修改了子系统间共同依赖的资源，请release所有的子系统
* 通过`-wL`参数开启监听改动自动刷新功能后，即使修改了子系统下的fis-conf.js文件，也不会被监听到，请重新输入`normae release -wL`release


### 本地调试
``` shell
$ normae server start
```
其他命令以及参数请参考[fis相关文档](http://fis.baidu.com/fis3/docs/beginning/debug.html#%E5%90%AF%E5%8A%A8)。

* #### 模拟转发url

	normae使用fis内置server默认的node server，同时开启模拟转发url功能，需要在项目根目录添加server.conf文件。<br />
	文件内容参考如下：

	``` shell
	rewrite ^\/$ /template/sub/page/index/index.html
	rewrite /test.json /test/ajax/test.json
	```
	配置文件说明：

	* 配置文件每一行为一条规则。
	* 规则格式为：匹配类型 (空格) 匹配url正则或url (空格) 命中后的目的文件url。
	* 匹配类型包括rewrite和redirect。
	* rewrite：匹配规则后转发到一个文件，同时url修改为访问文件的url。
	* redirect：匹配规则后重定向到另一个url。

* #### 编译velocity模版

	借助fis-postprocessor-velocity插件，实现在开发阶段编译velocity模版。如果page文件夹下存在独立页面的velocity模版index.html，则需要在同级目录添加mock数据文件index.html.js。

	index.html

	``` html
	<!DOCTYPE html>
	<html>
	<head>
		<title></title>
	</head>
	<body>
		<div>$!{foo}</div>
		<div>$math.floor(2.5)</div>
	</body>
	</html>
	```

	index.html.js

	``` javascript
	module.exports = {
		foo: "bar",
		math: {
			floor: function(num) {
				return Math.floor(num);
			}
		}
	}
	```
	另外可以添加/test/common/common.js文件，用来放置一些如模拟velocity tool的方法， 作为所有velocity模版的公共mock数据文件，在编译velocity模版时，会将该文件中的mock数据合并到模版对应的mock数据中。

## 开发

### 引入html
``` html
<link rel="import" href="mod.html?__inline" />
```
我们并没有使用velocity模板自带的#parse语法，因为这种引入模板片段的方法只有在用户访问页面时，服务器才会将模板片段合并成一个完整的文件并编译输出，但是我们采用的是纯前端的解决方案，并没有扩展velocity模板的语法，是无法处理这种运行时的问题的，比如模板片段中引用资源的路径问题，以及无法将模板对css，js文件的引用链接最后统一放置在完整页面的合适位置，因此我们转而将合并模板片段的时间提前到发布期，借助fis的内容嵌入能力，实现了模板片段的开发分离，发布合并，事实上最后放到服务器上的是包括所有模板片段的完整的velocity模版页面。

### js加载方式
normae采用的是amd的模块化开发方案，amd中require([])为异步加载的用法，而且作为加载入口文件时也只能使用这种用法，但是目前出于打包等方面的考虑，最后发布时的js加载方案使用的是把js放在body底部的同步加载方案，主要是通过设置fis-hook-amd插件的globalAsyncAsSync属性为true实现的，这样异步加载的js模块都会默认改为同步加载，那如果确实需要异步加载一个js模块怎么办，很简单：

``` javascript
// fis async
require(['./module/a'])
```
加了这段注释的异步用法就是真正的异步用法了。

### 引入模块
normae采用的是纯前端的解决方案，所以并没有对velocity模版进行扩展，因此不能直接引入独立的组件模块。以下面的目录为例：

```
.
├── modules
│   └── sidebar
│       ├── main.html
│       ├── main.js
│       └── main.less
└── page
    └── index
        ├── main.html
        ├── main.js
        └── main.less
```
#### 过去的引入方式
index/main.html

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="main.less" />
</head>
<body>
    <link rel="import" href="../../modules/sidebar/main.html?__inline" />

    <script src="../../../dep/esl/src/esl.js"></script>
    <script>
        require(['main.js']);
    </script>
</body>
</html>
```
index/main.js

``` javascript
require('../../modules/sidebar/main.js');
```
index/main.less

``` less
/**
 * @require "../../modules/sidebar/main.less"
**/
```
这种模块组件引入方式延续了拉勾过去的前端集成开发方案idt的模式，为页面分别提供一个入口js文件和入口css文件，再在其中分别引入模块组件的js和css文件，但是这种模式将模块组件割裂开来，引入和删除都很不方便。

#### 推荐的引入方式
因此我们决定改为将js文件/css文件的依赖关系定义到html文件中，引入模块直接引入html文件即可（如果只有js文件/css文件，那么将css的依赖关系定义到js文件里，只需要引入js文件）。

index/main.html

``` html
<!DOCTYPE html>
<html lang="en">
<head>
</head>
<body>
    <link rel="import" href="../../modules/sidebar/main.html?__inline" />

    <script src="../../../dep/esl/src/esl.js"></script>
</body>
</html>
```
sidebar/main.html

``` html
<!--
    @require "main.less"
-->

<div>Sidebar</div>

<script>
    require(['main.js']);
</script>
```
### 前端模版使用
拉勾之前的前端解决方案idt中，前端模版会通过html2js这样的node组件编译成js文件，再通过amd loader加载进来，在fis中提供了一种更简单的方式，将前端模版内容直接插入到js文件中，具体是通过fis内容嵌入能力实现的：

``` javascript
var template = require("dep/artTemplate/dist/template");
var tpl = __inline("./tpl/content.tpl");
var html = template.compile(tpl)({features: result.features});
$(".container").append(html);
```
__inline为编译期函数，在编译期间会把前端模版内容直接替换掉它。

### ignore html代码
可以通过在html中添加注释来使部分html代码在指定的release模式下才能release，其他模式将被忽略。

source code

``` html
<div>all release</div>

<!-- fis-dev-start -->
<div>dev release</div>
<!-- fis-dev-end -->

<!-- fis-qa|prod-start -->
<div>qa release or prod release</div>
<!-- fis-qa|prod-end -->
```
release code

* ```normae release```

	``` html
	<div>all release</div>

	<!-- fis-dev-start -->
	<div>dev release</div>
	<!-- fis-dev-end -->
	```
* ```normae release qa``` or ```normae release prod```

	``` html
	<div>all release</div>

	<!-- fis-qa|prod-start -->
	<div>qa release or prod release</div>
	<!-- fis-qa|prod-end -->
	```

不支持嵌套，开始和结束注释标签中的release模式必须一致，支持多个release模式，用' | '连接。

### 打包方式
在qa和prod的release模式中，自带了allInOne的打包方式，就是页面的所有css文件打包成一个文件，所有js文件打包成一个文件，就像之前说的并不建议这种粗暴的打包方式，其实我们可以自己配置打包的方式，其他的零散资源再使用allInOne的方式打包成一个文件。<br />
打包可以参考如下的方式，数组中资源路径的顺序决定了最后在打包文件中资源的顺序。

``` javascript
fis.media('qa').match('::package', {
    packager: fis.plugin('map', {
        'pkg/vendor.js': [
            'dep/**/esl.js',
            'dep/**/jquery.js',
            'dep/**/jquery.cookie.js',
            'dep/**/jquery.colorbox.js',
            'dep/**/template.js'
        ]
    })
});
```

不再使用如下的packTo的配置打包方式

``` javascript
fis.media('qa').match("dep/**/{jquery,jquery.cookie,jquery.colorbox,template}.js",{
    packTo : "/pkg/vendor.js"
});
```
原因是无法控制各文件在打包后文件中的顺序，而且两者不能混用。
