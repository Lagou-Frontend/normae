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
