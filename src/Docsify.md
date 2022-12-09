# Docsify

Docsify 可以快速帮你生成文档网站。不同于 GitBook、Hexo 的地方是它不会生成静态的 `.html` 文件，所有转换工作都是在运行时。

**注：由于Docsify不是生成静态站点，因此不支持导出PDF、ePub等。**

* [官方文档](https://docsify.js.org/#/zh-cn/)
* [docsify-cli命令行手册](https://github.com/docsifyjs/docsify-cli)：也可以用--help命令
* [案例项目](https://docsify.js.org/#/zh-cn/awesome)

# 安装

安装Node环境：[NodeJS官网](https://nodejs.org/en/download/)下载压缩包，解压，配置环境变量即可

```shell
# 下载Docisfy工具
npm i docsify-cli -g
# 初始化目录
docsify init ./docs
# 本地运行
docsify serve docs
```

# 配置



# 多级目录管理



# 问题记录

## 一级标题不会显示在子目录中

假如文章中标题如下

```
# 1
## 1-1
# 2
# 3
```

侧边栏目录会变成如下结构，首个一级标题不会显示在子目录中，导致1-1和2、3同级

* 1
  * 1-1
  * 2
  * 3

## 自动生成的sidebar只会有两级

假如文件夹结构如下

```
src
├── Android
│   ├── Android基础
│   │   ├── Activity.md
│   ├── Android性能
│   │   ├── Bitmap.md
├── Java
│   ├── ArrayList.md
```

使用`docsify generate src --sidebar _sidebar.md`生成目录结构，无法解析出第三级文件夹，会将三级文件夹展开再合并。

结果如下

* Android
  * Activity.md
  * Bitmap.md
* Java
  * ArrayList.md

## 文件夹嵌套目录

假如文件夹结构如下

```
src
├── Android
│   ├── _sidebar.md
│   ├── Android基础
│   │   ├── Activity.md
│   ├── Android性能
│   │   ├── Bitmap.md
├── Java
│   ├── ArrayList.md
```

文件夹嵌套时，`Android/_sidebar.md`中配置目录需要包含完整文件夹路径。

例如`[Activity](Android/Android基础/Activity.md)`，不能使用`_sidebar`的相对路径，`[Activity](Android基础/Activity.md)`。并且使用完整路径，会导致编辑器中无法ctrl打开文件，

配置使用相对路径：`relativePath: true,`，则可以改为`[Activity](Android基础/Activity.md)`，但是这个相对路径不是相对`_sidebar.md`文件，而是相对于浏览器url。

也就是说当前浏览器url为`<domain>/Android/README`时，点击目录链接，相对于`/Android/`路径，因此会跳转到`<domain>/Android/Android基础/Activity`，但是再点击目录时，相对的路径是`/Android/Android基础/`，因此会跳转到`<domain>/Android/Android基础/Android基础/Activity`

## 配置路由别名时key值不能为中文

配置路由别名，将`/Android性能/`映射为`/Android/Android性能/`，不能使用中文，需要改为unicode编码

```js
alias: {
	// key值左边不能为中文，需要使用unicode编码，或者将文件夹名称改为英文
	// '/Android%E6%80%A7%E8%83%BD/(.*)': '/Android/Android性能/$1', 
	'/AndroidBasic/(.*)': '/Android/AndroidBasic/$1',
},
```

## 别名配置对图片引用不生效

配置别名如下

```js
alias: {
	'/zh-cn/(.*)': './$1',
},
```

当访问`/zh-cn/hello.md`路径时，实际上会访问`./hello.md`

然而当文章引用了图片，例如`![](logo.png)`，访问`/zh-cn/logo.png`图片路径时，并不会替换为`./logo.png`，因此会找不到图片

## 图片引用

假如图片位于`/src/Android/Android基础/Activity/Activity生命周期.png`，在`/src/Android/Android基础/Activity.md`中引用图片时。

* 使用`![](Activity/Activity生命周期.png)`相对路径引用图片，会自动补全路径。
* 使用`<img src="src/Android/Android基础/Activity/Activity生命周期.png" />`引用图片时，需要使用完整文件夹路径，不会自动补全。（并且在编辑器中无法预览该图片）

如果要缩放图片

* 通用方式：`<img src="src/Android/Android基础/Activity/Activity生命周期.png" style="zoom: 50%"/>`，由于使用完整文件路径，无法在编辑器中预览图片。
* 使用Docsify方式缩放：`![](Activity/Activity生命周期.png 'size: 50%')`，无法在编辑器中预览缩放效果，需要运行Docsify才能解析