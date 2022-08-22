# 安装Vue

## 参考资料
1. http://t.csdn.cn/AS2fx
2. http://t.csdn.cn/sQo7s

## 前提条件
1. 安装nodejs
2. 修改 npm 的镜像源

## html 引入 vue

## npm 管理 Vue
1. npm 安装 Vue
   ```bash
   npm install vue -g
   ```

2. 查看版本：
   ```bash
   npm info vue
   ```
## npm 管理 Vue/cli 2.x
1. npm 卸载 Vue/cil 2.x

    ```bash
    npm uninstall vue-cli -g
    ```

2. npm 安装 Vue/cli 2.x
   ```bash
   npm install vue-cli -g
   ```

3. Vue/cli 创建 Vue 项目
    ```bash
    vue init ${项目名}
    ```
    - Project name（工程名）:回车(含大写字母给我报错了，我给改了my-vue)
    - Project description（工程介绍）：回车
    - Author：作者名 ：回车
    - Vue build ==> （是否安装编译器）runtime-compiler、 runtime-only 都是打包方式，第二个效率更高；
    - Install vue-router ==> 是否要安装 vue-router，项目中肯定要使用到路由，所以Y 回车；
    - Use ESLint to lint your code ==> 是否需要ESLint检测代码，目前我们不需要所以 n 回车；
    - Set up unit tests ==> 是否安装 单元测试工具 目前我们不需要 所以 n 回车；
    - Setup e2e tests with Nightwatch ==>是否需要端到端测试工具目前我们不需要所以n回车；
    - Should we run npm install for you after the project has been created? (recommended) (Use arrow keys)==> 安装依赖npm install
    - 回车；


## npm 管理 Vue/cli 3.x
1. npm 安装 Vue/cli 3.x

    ```bash
    npm uninstall @vue/cli -g
    ```

2. npm 安装 Vue/cli 3.x
   ```bash
   npm install @vue/cli –g
   ```

3. 访问 UI 界面
    ```bash
    vue ui
    ```
4. cmd 创建 vue 项目
    ```bash
    vue create ${项目名}
    ```
    - Please pick a preset=》选择一个配置:default默认有babel、eslint，Manually
    select features 手动配置。
      选择手动配置，根据自己的需要选择，敲空格键配合方向键进行选择。
    - where do you prefer placing config for …=》配置放在哪里
    In dedicated config files =》 每项配置有单独的文件
    In package.json =》在package.json 文件中
    - Save this as a preset for future project? =>是否为保存配置习惯文件，存了后下次新建新项目选择配置时就会有此选项了
    - Save preset as; =>存个名字
    - Pick the package …=>运行选择npm或者yarn

5. cli3下拉取2.x模板
   依然可以新建2.x的项目 vue init webpack my-vue
   ```bash
   npm install -g @vue/cli-init
   ```
## 访问项目



## vue 的常用命令
```bash
# 查看 vue 版本
vue -V

# 查看 vue 功能列表
vue -h
```

## VSCode Vue插件
1. 1。Vetur —— 语法高亮、智能感知、Emmet等
   包含格式化功能， Alt+Shift+F （格式化全文），Ctrl+K Ctrl+F（格式化选中代码，两个Ctrl需要同时按着）
2. ![img](https://pic2.zhimg.com/80/v2-0e7b023abaf6dc9adbd12fedfef36ba1_720w.jpg)

## Vue 的项目结构
### 1 build：构建脚本目录
1）build.js ==> 生产环境构建脚本；
2）check-versions.js ==> 检查npm，node.js版本；
3）utils.js ==> 构建相关工具方法；
4）vue-loader.conf.js ==> 配置了css加载器以及编译css之后自动添加 前缀；
5）webpack.base.conf.js ==> webpack基本配置；
6）webpack.dev.conf.js ==> webpack开发环境配置；
7）webpack.prod.conf.js ==> webpack生产环境配置；
### 2 config：项目配置
1）dev.env.js ==> 开发环境变量；
2）index.js ==> 项目配置文件；
3）prod.env.js ==> 生产环境变量；
### 3 node_modules
npm 加载的项目依赖模块
### 4 src
这里是我们要开发的目录，基本上要做的事情都在这个目录里。里面包含了几个目录及文件：
1）assets：资源目录，放置一些图片或者公共js、公共css。但是因为它们属于代码目录下，所以可以用 webpack 来操作和处理。意思就是你可以使用一些预处理比如 Sass/SCSS 或者 Stylus。
2）components：用来存放自定义组件的目录，目前里面会有一个示例组件。
3）router：前端路由目录，我们需要配置的路由路径写在index.js里面；
4）App.vue：根组件；这是 Vue 应用的根节点组件，往下看可以了解更多关注 Vue 组件的信息。
5）main.js：应用的入口文件。主要是引入vue框架，根组件及路由设置，并且定义vue实例，即初始化 Vue 应用并且制定将应用挂载到index.html 文件中的哪个 HTML 元素上。通常还会做一些注册全局组件或者添额外的 Vue 库的操作。
### 5 static
静态资源目录，如图片、字体等。不会被webpack构建
### 6 index.html
首页入口文件，可以添加一些 meta 信息等。 这是应用的模板文件，Vue 应用会通过这个 HTML 页面来运行，也可以通过 lodash 这种模板语法在这个文件里插值。 注意：这个不是负责管理页面最终展示的模板，而是管理 Vue 应用之外的静态 HTML 文件，一般只有在用到一些高级功能的时候才会修改这个文件。
### 7 package.json
npm包配置文件，定义了项目的npm脚本，依赖包等信息
### 8 README.md
项目的说明文档，markdown 格式
### 9 .xxxx文件
这些是一些配置文件，包括语法配置，git配置等
1）.babelrc:babel编译参数
2）.editorconfig:编辑器相关的配置，代码格式
3）.eslintignore : 配置需要或略的路径，一般build、config、dist、test等目录都会配置忽略
4）.eslintrc.js : 配置代码格式风格检查规则
5）.gitignore:git上传需要忽略的文件配置
6）.postcssrc.js ：css转换工具
