# Docsify 搭建文档网站

## 1 参考网站
1. [docsify官方文档](https://docsify.js.org/#/?id=docsify)
2. [docsify github地址](https://github.com/docsifyjs/docsify/)
3. [Github+docsify零成本轻松打造在线文档网站](https://cloud.tencent.com/developer/news/675461)


## 2 docsify 使用背景

一个好的开源软件必须要有一个完善的文档才容易被推广，那么我们在如何简单、高效、低成本的搭建一个文档网站呢？今天我们使用Github+docsify来零成本轻松打造一个在线文档系统！

不同于 GitBook、Hexo 的地方是它不会生成静态的 .html 文件，所有转换工作都是在运行时。只需要创建一个 index.html 就可以开始编写文档并直接部署在 GitHub Pages。

## 3 前提条件
### 3.1 安装node 和 npm 
[安装node](https://www.cnblogs.com/xilifeng/p/5538711.html)

### 3.2 npm 安装 docsify
```bash
npm i docsify-cli -g
```

### 3.3 注册github 

![image-20220817185010827](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171850942.png)

![image-20220817185120225](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171851328.png)

![image-20220817185320118](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171853261.png)

### 3.4 安装git 

### 3.5 github 配置 ssh



## 4 初始化项目

### 4.1 创建github 项目

![image-20220817190154714](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171901794.png)

![image-20220817190328272](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171903362.png)

### 4.2 克隆项目到本地

![image-20220817190613351](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171906439.png)

> 在cmd 中下载仓库
>
> 命令：`git clone`

![image-20220817191920454](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171919510.png)

### 4.3 初始化docsify 项目

> 进入项目目录中

```bash
docsify init ./docs
```

![image-20220817192200272](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171922349.png)

运行成功后，在docs 目录下会自动生成三个文件：

- index.html 入口文件 

- README.md 会做为主页内容渲染 

- .nojekyll 用于阻止 GitHub Pages 会忽略掉下划线开头的文件

## 5 运行项目

```bash
docsify serve docs
```

![image-20220817192321299](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171923379.png)

> 本地访问http://localhost:3000即可看到文档：

![image-20220817192356684](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171923757.png)

## 6 配置 docsify 项目

> docsify 的配置都在`index.html`中的`window.$docsify`函数中配置。

### 6.1 加载对话框

1. 修改 html 代码

```html
  <!-- 注释：默认首页
     <div id="app"></div>
   -->

  <!-- 添加：加载对话框1：https://docsify.js.org/#/quickstart?id=loading-dialog -->
  <div data-app id="main">Please wait...</div>
```

2. 修改`window.$docsify`代码

```javascript
// 为 el 指定 html 标签
window.$docsify = {
  el: '#main',
};
```

### 6.2  设置github地址

```javascript
window.$docsify = {
  repo: 'docsifyjs/docsify',
  // or
  repo: 'https://github.com/docsifyjs/docsify/',
};
```

### 6.3 设置最大目录级别

```javascript
window.$docsify = {
  maxLevel: 4,
};
```

### 6.4 加载导航栏

1. 创建`_navbarm.md`文件

   如果没有指定文件，则默认加载`_navbar.md` 文件

2. 设置index.html文件

   ```javascript
   window.$docsify = {
     // load from _navbar.md
     loadNavbar: true,
   
     // load from nav.md
     loadNavbar: 'nav.md',
   };
   ```

### 6.5 加载侧边栏

1. 创建`_sidebar.md`文件

   如果没有指定侧边栏文件，则默认加载`_sidebar.md` 文件

2. 设置index.html文件

   ```javascript
   window.$docsify = {
     // load from _sidebar.md
     loadSidebar: true,
   
     // load from summary.md
     loadSidebar: 'summary.md',
   };
   ```

### 6.6 完全隐藏侧边栏

此选项将完全隐藏您的侧边栏，并且不会在侧面呈现任何内容。

```javascript
window.$docsify = {
  hideSidebar: true,
};
```

### 6.7 设置侧边栏显示的目录级别

```javascript
window.$docsify = {
  subMaxLevel: 2,
};
```

### 6.8 设置首页文件

如果没有指定文件，默认使用`README.md`作为网站的主页。

```javascript
window.$docsify = {
  // Change to /home.md
  homepage: 'home.md',

  // Or use the readme in your repo
  homepage:
    'https://raw.githubusercontent.com/docsifyjs/docsify/master/README.md',
};
```

### 6.9 设置封面

1. 创建`_coverpage.md`文件

   如果没有指定封面文件，则默认加载`_coverpage.md` 作为封面文件

2. 修改`index.html`文件

   ```javascript
   window.$docsify = {
     coverpage: true,
   
     // Custom file name
     coverpage: 'cover.md',
   
     // multiple covers
     coverpage: ['/', '/zh-cn/'],
   
     // multiple covers and custom file name
     coverpage: {
       '/': 'cover.md',
       '/zh-cn/': 'cover.md',
     },
       
     // 设置访问主页时候仅加载封面
       onlyCover: false,
   };
   ```

### 6.10 设置logo

```java
window.$docsify = {
  logo: '/_media/icon.svg',
};
```

### 6.11 设置网站名称

```javascript
window.$docsify = {
  name: 'docsify',
};
```

name 字段还可以包含自定义 HTML，以便于自定义：

```javascript
window.$docsify = {
  name: '<span>docsify</span>',
};
```

### 6.12 设置名称链接

```javascript
window.$docsify = {
  nameLink: '/',

  // For each route
  nameLink: {
    '/zh-cn/': '#/zh-cn/',
    '/': '#/',
  },
};
```

### 6.13 设置主题颜色

```javascript
window.$docsify = {
  themeColor: '#3F51B5',
};
```



## 7 部署项目

### 7.1 github 部署

1. 提交代码

   ```bash
   git add .
   
   git commit -m "docsify init"
   
   git branch -M main
   
   git push -u origin main
   ```

2. 设置Github Pages

   Setting –> Pages –> 修改Branch：main，docs –> Save

   ![image-20220817203202270](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208172032367.png)

3. 访问网页

   > 默认采用 `README.md` 作为首页

	![](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208172033630.png)
	
	