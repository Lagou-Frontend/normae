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
