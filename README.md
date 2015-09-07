# normae
normae是基于[fis3](http://fis.baidu.com/fis3/index.html)的拉勾网前端解决方案
* 后端为Java＋Velocity
* 采用less作为css处理器
* 采用amd模块化开发方案
* 采用bower管理第三方依赖

## 目录规范
```javascript
site //能独立提供服务，具有单独二级域名的产品线
 ├ common //通用子系统
 | ├ component //组件，业务逻辑无关
 | | ├ popoup 
 | | | | ├ popoup.js 
 | | | | └ popoup.less
 | ├ static //非组件模块资源目录，包括模板页面引用的静态资源（favicon.ico）
 | ├ widget //模块，业务逻辑相关
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
 ├ module //子系统，具有较清晰业务逻辑关系的功能业务集合
 | ├ page //具有独立URL的输出内容
 | | ├ index 
 | | | | ├ index.html //velocity模版
 | | | | ├ index.html.js //页面对应的mock数据
 | | | | ├ index.less
 | | | | └ index.js
 | ├ static
 | ├ widget
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
#### 全局安装bower
```shell
$ npm install bower -g
```
#### 全局安装normae
```shell
$ npm install normae -g
```
#### normae内置了3种release模式
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
#### 本地调试
```shell
$ normae server start --rewrite
```
###### 模拟转发url
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

######  编译velocity模版
借助fis3-parser-vm插件，实现在开发阶段编译velocity模版。如果page文件夹下存在独立页面的velocity模版index.html，则需要在同级目录添加mock数据文件index.html.js。<br />
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
另外可以添加/mock/common/common.js文件，用来放置一些如模拟velocity tool的方法， 作为所有velocity模版的公共mock数据文件，在编译velocity模版时，会将该文件中的mock数据合并到模版对应的mock数据文件中。
