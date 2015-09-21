# normae
normae是基于[fis3](http://fis.baidu.com/fis3/index.html)的拉勾网前端解决方案

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
 | ├ modules //模块，业务逻辑相关
 | | ├ header 
 | | | | ├ header.html 
 | | | | ├ header.js
 | | | | └ header.less
 ├ dep //第三方依赖
 | ├ jquery
 | | └ jquery.js
 | ├ esl
 | | └ esl.js
 ├ mock //模拟数据
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
 ├ .bowerrc //bower配置文件
 ├ bower.json //bower package文件
 ├ fis-conf.js //fis配置文件
 ├ server.conf //本地url模拟转发配置文件
```


## 使用

### 全局安装normae

```shell
$ npm install normae -g
```

### 全局安装bower

```shell
$ npm install bower -g
```

### normae内置了3种release模式
* 默认的dev模式，用于本地调试，会编译velocity，模拟转发url。

	```shell
	$ normae release
	```
* qa模式，用于测试环境测试，会添加md5戳，打包，合成精灵图。

	```shell
	$ normae release qa
	```
* prod模式，用于生产环境上线，会添加md5戳，打包，合成精灵图，压缩。

	```shell
	$ normae release prod
	```
具体配置请参考[这里](https://github.com/vicerwang/normae/blob/master/index.js)。<br />
normae的三种release方式只提供了一些基本的匹配处理规则，如果想添加适合本项目的特殊规则，如具体的打包规则等，请在项目根目录下的fis-conf.js文件中添加规则来扩展或覆盖基本的规则。

### 本地调试
```shell
$ normae server start --rewrite
```
其他命令以及参数请参考[fis相关文档](http://fis.baidu.com/fis3/docs/beginning/debug.html#%E5%90%AF%E5%8A%A8)。

* #### 模拟转发url

	normae使用fis内置server默认的node server，同时开启模拟转发url功能，需要在项目根目录添加server.conf文件。<br />
	文件内容参考如下：
	
	```shell
	rewrite ^\/$ /template/sub/page/index/index.html
	rewrite /test.json /mock/ajax/test.json
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
	
	```html
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
	
	```javascript
	module.exports = {
		foo: "bar",
		math: {
			floor: function(num) {
				return Math.floor(num);
			}
		}
	}
	```
	另外可以添加/mock/common/common.js文件，用来放置一些如模拟velocity tool的方法， 作为所有velocity模版的公共mock数据文件，在编译velocity模版时，会将该文件中的mock数据合并到模版对应的mock数据中。

## 开发

normae采用的是纯前端的解决方案，所以并没有对velocity模版进行扩展，因此不能直接引入独立的组件模块，只能或者分别引入组件模块的html文件，js文件，less文件，或者将js文件，less文件的依赖关系定义到html里，只引入html文件。<br />
以之前定义的目录为例，如果/sub/page/index/index.html想引入模块/common/modules/header:

* 引入html文件

	```html
	<link rel="import" href="../../../common/widget/header/header.html?__inline" />
	```
	我们并没有使用velocity模板自带的#parse语法，因为这种引入模板片段的方法只有在用户访问页面时，服务器才会将模板片段合并成一个完整的文件并编译输出，但是我们采用的是纯前端的解决方案，并没有扩展velocity模板的语法，是无法处理这种运行时的问题的，比如模板片段中引用资源的路径问题，以及无法将模板对css，js文件的引用链接最后统一放置在完整页面的合适位置，因此我们转而将合并模板片段的时间提前到发布期，借助fis的内容嵌入能力，实现了模板片段的开发分离，发布合并，事实上最后放到服务器上的是包括所有模板片段的完整的velocity模版页面。
	
* 引入less文件

	当然可以直接使用下面的方式引入less文件：
	
	```html
	<link rel="stylesheet" href="../../../common/widget/header/header.less" />
	```
	但是当页面中需要引入很多的模块对应的less文件时，那么你还可以在每一个html文件对应的入口less文件中像下面这样使用：
	
	index.html
	
	```html
	<link rel="stylesheet" href="./index.less" />
	```
	
	index.less

	```less
	/**
	 * index.css
	 * @require ../../../common/widget/header/header.less
	 */
	```
 　 
	这里可能也有同学会问为什么在less文件中没有使用less自带的import语法来引入各模块的less文件，那是因为如果使用import的方式，那么最后只能打包成一个文件，这是一种非常粗暴的打包方式，而我们想要的方案是可以根据我们在fis-conf.js的配置来随心所欲地打包，比如将header.less和footer.less打包成commonModules.css，那么所有的页面都可以享受commonModules.css缓存的好处了，而不是每一个页面的css文件都包含有header.less和footer.less的部分，因此我们采用的是fis中在css文件声明依赖css文件的方式，意思就是说，想要加载index.less，需要首先加载header.less。
	
* 引入js文件

	同样的可以使用下面的方式直接引入js文件：
	
	```html
	<script src="../../../dep/esl/src/esl.js"></script>
	<script src="../../../common/widget/header/header.js"></script>
	<script>
		require(["/common/widget/header/header"]);
	</script>
	```
	也可以像less文件那样，在js的入口文件中声明依赖来引入js文件：
	
	index.html
	
	```html
	<script type="text/javascript" src="../../../dep/esl/src/esl.js"></script>
	<script type="text/javascript">
		require(["index"]);
	</script>
	```
	index.js
	
	```javascript
	define(function (require, exports, module) {
		require("../../../common/widget/header/header");
	});
	```
	normae采用的是amd的模块化开发方案，amd中require([])为异步加载的用法，而且作为加载入口文件时也只能使用这种用法，但是目前出于打包等方面的考虑，最后发布时的js加载方案使用的是把js放在body底部的同步加载方案，主要是通过设置fis-hook-amd插件的globalAsyncAsSync属性为true实现的，这样异步加载的js模块都会默认改为同步加载，那如果确实需要异步加载一个js模块怎么办，很简单：

	```javascript
	// fis async
	require(['./module/a'])
	```
加了这段注释的异步用法就是真正的异步用法了。

* 前端模版使用

	拉勾之前的前端解决方案idt中，前端模版会通过html2js这样的node组件编译成js文件，再通过amd loader加载进来，在fis中提供了一种更简单的方式，将前端模版内容直接插入到js文件中，具体是通过fis内容嵌入能力实现的：
	
	```javascript
	var template = require("dep/artTemplate/dist/template");
	var tpl = __inline("./tpl/content.tpl");
	var html = template.compile(tpl)({features: result.features});
	$(".container").append(html);
	```
	__inline为编译期函数，在编译期间会把前端模版内容直接替换掉它。
	
* 打包方式
 
	在qa和prod的release模式中，自带了allInOne的打包方式，就是页面的所有css文件打包成一个文件，所有js文件打包成一个文件，就像之前说的并不建议这种粗暴的打包方式，其实我们可以自己配置打包的方式，其他的零散资源再使用allInOne的方式打包成一个文件。<br />
	打包可以参考如下的方式：
	
	```javascript
	fis.media('qa').match('common/modules/**.less', {
	    packTo : "/pkg/commonModules.css"
	});
	fis.media('qa').match('common/modules/**.js', {
	    packTo : "/pkg/commonModules.js"
	});
	```
