# Docsify

Docsify 可以快速帮你生成文档网站。不同于 GitBook、Hexo 的地方是它不会生成静态的 `.html` 文件，所有转换工作都是在运行时。

**注：由于Docsify不是生成静态站点，因此不支持导出PDF、ePub等。且不支持文章内添加`<script>`脚本**

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



# 自定义插件

在封面`_coverpage.md`中添加每日一言、访问量统计等。

## 在封面添加每日一言

首先在`_coverpage.md`中添加html和css元素

```html
<style>
.poem-side {
    width: 50%;
    margin: 0px 25%;
}
.poem-side div#hitokoto {
    font-size: 20px;
    text-align: left;
}
.poem-side div#hitokotofrom {
    font-size: 16px;
    text-align: right;
}
</style>
<div class="poem-side">
  <div id="hitokoto">loading...</div>
  <div id="hitokotofrom"></div>
</div>
```

由于Docsify文章中不支持添加`<script>`脚本，文章渲染的时候不会执行JS脚本，因此需要在`index.html`中添加脚本。

在Docsify生命周期hook中添加脚本，文章渲染完之后调用，如下

> 直接在`<body />`中添加的话只有首次加载的时候才会执行，页面内路由跳转不会触发脚本。并且可能出现找不到`hitokoto`元素的问题

```js
window.$docsify = {
    // 自定义插件
    plugins: [function(hook, vm) {
        // 每次路由切换时数据全部加载完成后调用，没有参数。
        hook.doneEach(function() {
            // 判断cover元素是否显示，避免重复加载
            if (document.getElementsByClassName("cover show").length != 0) {
                fetch('https://v1.hitokoto.cn')
                  .then(response=>response.json())
                  .then(data=>{
                    hitokoto.innerHTML = data.hitokoto
                    if (data.from_who != null) {
                        hitokotofrom.innerHTML = '——' + data.from_who + ' 《' + data.from + '》'
                    } else {
                        hitokotofrom.innerHTML = '——《' + data.from + '》'
                    }
                }
                ).catch(console.error)
            }
        });
    }
    ]
}
```

## 添加访问量统计

首先在`_coverpage.md`中添加html和css元素

```html
<span id="busuanzi_container_site_pv" style="display: inline;">
    👁️本页总访问次数:<span id="busuanzi_value_site_pv"></span> 
</span>
<span id="busuanzi_container_site_uv" style="display: inline;"> 
    | 🧑总访客数: <span id="busuanzi_value_site_uv"></span>
</span>
```

接着在`<body />`中添加js脚本，这里只有首次打开页面的时候执行一次，页面内跳转不会执行。

```html
<body>
	<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
</body>
```

# 问题记录

## 首个一级标题不会显示在子目录中

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

## generate命令只能生成一级目录

假如文件夹结构如下

```shell
src # 当前目录
├── Android # 一级目录
│   ├── Android基础 # 二级目录
│   │   ├── Activity.md
│   ├── Android性能
│   │   ├── Bitmap.md
├── Java
│   ├── ArrayList.md
```

使用`docsify generate src --sidebar _sidebar.md`生成目录结构，无法解析出二级目录，会将二级目录展开再合并。

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

文件夹嵌套时，`Android/_sidebar.md`中配置目录需要包含完整文件夹路径，不能使用`_sidebar`的相对路径。

即`[Activity](Android基础/Activity.md)`，需要改为`[Activity](Android/Android基础/Activity.md)`。

但是使用完整路径，又会导致编辑器中无法ctrl打开文件。

配置使用相对路径：`relativePath: true,`，则可以改为`[Activity](Android基础/Activity.md)`。但是这个相对路径不是相对`_sidebar.md`文件，而是相对于浏览器url。

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
* 使用Docsify方式缩放：`![](Activity/Activity生命周期.png 'size: 50%')`，无法在编辑器中预览缩放效果，需要运行Docsify才能解析。