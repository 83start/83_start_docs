

# 1 简介
Git 是分布式版本控制系统，没有中央控制器，每个人的电脑就可以拥有一个完整的版本库，工作的时候不需要联网。协同的方法：A和B 都修改了文件 hello.java，只需要将各自的修改推送给对方，就可以互相看到对方的修改。Git 可以直接看到更新了哪些代码和文件。

**Git是目前世界上最先进的分布式版本控制系统。**

# 2 Git 历史

Linux和Git之父李纳斯·托沃兹（Linus Benedic Torvalds）1969、芬兰。

Git是免费、开源的，最初Git是为辅助 Linux 内核开发的，来替代 BitKeeper！

# 3 安装Git

## 3.1 下载

国外：

[https://git-scm.com/downloads](https://git-scm.com/downloads)

淘宝镜像：

[http://npm.taobao.org/mirrors/git-for-windows/](http://npm.taobao.org/mirrors/git-for-windows/)

## 3.2 卸载

1. 卸载软件
![image_4.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_4_1642699989368.png)

![image_5.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_5_1642699989422.png)

2. 清理path
![image_6.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_6_1642700001892.png)

![image_7.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_7_1642700001935.png)

![image_8.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_8_1642700001892.png)

## 3.3 安装

## 3.4 启动Git

安装成功后在开始菜单中会有Git项，菜单下有3个程序：任意文件夹下右键也可以看到对应的程序！![image_10.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_10_1642700035955.png)

![image_9.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_9_1642700023197.png)

**Git Bash ：**Unix与Linux风格的命令行，**使用最多，推荐最多**
**Git CMD ：**Windows风格的命令行
**Git GUI：**图形界面的Git，不建议初学者使用，尽量先熟悉常用命令

# 4 常用的Linux 命令（补充）

```bash
cd : 改变目录。
cd .. : 回退到上一个目录，直接cd进入默认目录
pwd : 显示当前所在的目录路径。
ls(ll):  都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细。
touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件。
rm:  删除一个文件, rm index.js 就会把index.js文件删除。
mkdir:  新建一个目录,就是新建一个文件夹。
rm -r :  删除一个文件夹, rm -r src 删除src目录
mv : 移动文件, mv index.html src index.html 是我们要移动的文件, src 是目标文件夹,当然, 这样写,必须保证文件和目标文件夹在同一目录下。
reset : 重新初始化终端/清屏。
clear : 清屏。
history : 查看命令历史。
help : 帮助。
exit : 退出。
#表示注释
```

# 5 Git 配置文件
## 5.1查看配置文件信息
**查看所有的配置文件：**

```YAML
git config -l
```

![image_10.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_10_1642700043948.png)

**查看系统配置文件：（筛选）**

```YAML
git config --system --list
```

![image_11.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_11_1642700050074.png)

查看当前用户（global）配置：

```YAML
git config --global  --list
```

![image_12.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_12_1642700056789.png)

## 5.2 查看配置文件位置

系统配置文件：

`Git\etc\gitconfig ` ：Git 安装目录下的 gitconfig

全局（用户）配置文件：

`C:\Users\[Administrator]\ .gitconfig`    只适用于当前登录用户的配置 

这里可以直接编辑配置文件，通过命令设置后会响应到这里。

## 5.2设置用户配置文件

**当你安装Git后首先要做的事情是设置你的用户名称和e-mail地址。这是非常重要的，因为每次Git提交都会使用该信息。**它被永远的嵌入到了你的提交中。

只需要做一次这个设置，如果你传递了--global 选项，因为Git将总是会使用该信息来处理你在系统中所做的一切操作。如果你希望在一个特定的项目中使用不同的名称或e-mail地址，你可以在该项目中运行该命令而不要--global选项。总之--global为全局配置，不加为某个项目的特定配置。

```YAML
git config --global user.name "caojun"  #名称
git config --global user.email 1308129550@qq.com   #邮箱
```

# 6 Git 理论（重要）
## 6.1 工作区域

**Git本地有三个工作区域：**

`工作目录（Working Directory）：`平时存放项目代码的地方。

`暂存区(Stage/Index)：`用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息。

`资源库(Repository或Git Directory)：`仓库区（或本地仓库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本。

**Git远程的区域：**

`仓库(Remote Directory)：`托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换。

**文件在这四个区域之间的转换关系如下：**

![image_13.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_13_1642700068958.png)

## 6.2 工作流程

1. 在工作目录中添加、修改文件；
2. 将需要进行版本管理的文件放入暂存区域；
3. 将暂存区域的文件提交到git仓库。

**git管理的文件有三种状态：已修改（modified）,已暂存（staged）,已提交(committed)**

![image_14.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_14_1642700076549.png)

## 6.3 常用指令

![image_15.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_15_1642700083032.png)

# 7 Git项目搭建

## 7.1 创建工作目录

工作目录（WorkSpace)一般就是你希望Git帮助你管理的文件夹，可以是你项目的目录，也可以是一个空目录，建议不要有中文。

## 7.2 本地搭建项目
1. 创建全新的仓库，需要用GIT管理的项目的根目录执行：

   ```YAML
   # 在当前目录新建一个Git代码库
       
   $ git init
   ```

2. 执行后可以看到，仅仅在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面。

## 7.3 拷贝远程仓库

1. 克隆远程目录，由于是将远程服务器上的仓库完全镜像一份至本地！

   ```YAML
   # 克隆一个项目和它的整个代码历史(版本信息)
   
   $ git clone [url]  # https://gitee.com/kuangstudy/openclass.git
   ```

2. 项目目录中出现了完整的克隆项目。

# 8 Git 的文件操作

## 8.1 文件的四种状态

版本控制就是对文件的版本控制，要对文件进行修改、提交等操作，首先要知道文件当前在什么状态，不然可能会提交了现在还不想提交的文件，或者要提交的文件没提交上。

- Untracked: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.
- Unmodify: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified. 如果使用git rm移出版本库, 则成为Untracked文件
- Modified: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过, 返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改 !
- Staged: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存, 文件状态为Modified

## 8.2 查看文件的状态

上面说文件有4种状态，通过如下命令可以查看到文件的状态：

```PowerShell
#查看指定文件状态
git status [filename]

#查看所有文件状态
git status
```

## 8.3 忽略文件

有些时候我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等

在主目录下建立".gitignore"文件，此文件有如下规则：

1. 忽略文件中的空行或以井号（#）开始的行将会被忽略。
2. 可以使用Linux通配符。例如：星号（*）代表任意多个字符，问号（？）代表一个字符，方括号（[abc]）代表可选字符范围，大括号（{string1,string2,...}）代表可选的字符串等。
3. 如果名称的最前面有一个感叹号（!），表示例外规则，将不被忽略。
4. 如果名称的最前面是一个路径分隔符（/），表示要忽略的文件在此目录下，而子目录中的文件不忽略。
5. 如果名称的最后面是一个路径分隔符（/），表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）。

```PowerShell
#为注释
*.txt        #忽略所有 .txt结尾的文件,这样的话上传就不会被选中！

!lib.txt     #但lib.txt除外

/temp        #仅忽略项目根目录下的TODO文件,不包括其它目录temp

build/       #忽略build/目录下的所有文件

doc/*.txt    #会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

```JavaScript
# 通用
*.class
*.log
*.lock
temp/

# Package Files
*.jar
*.war
*.ear

HELP.md
target/
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**/target/
!**/src/test/**/target/

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea/
*.iws
*.iml
*.ipr
*velocity.log*
.idea
.classpath
.project
.settings/
bin/

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/
build/
!**/src/main/**/build/
!**/src/test/**/build/

### VS Code ###
.vscode/

# rebel
*rebel.xml*
```

![](/upload/2022/01/-dcfc66984ae64bc79ff340f8a89f33b7.gitignore)

# 9 使用远程仓库

## 9.1 生成.ssh

[https://gitee.com/help/articles/4181#article-header0](https://gitee.com/help/articles/4181#article-header0)

**生成sshkey**

```JavaScript
ssh-keygen -t ed25519 -C "1308129550@qq.com"  
```


> 注意：这里的 `xxxxx@xxxxx.com` 只是生成的 sshkey 的名称，并不约束或要求具体命名为某个邮箱。
> 现网的大部分教程均讲解的使用邮箱生成，其一开始的初衷仅仅是为了便于辨识所以使用了邮箱。

![image_16.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_16_1642700131246.png)

**查看sshkey**

![image_17.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_17_1642700139154.png)

![image_18.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_18_1642700139118.png)

## 9.2 Gitee添加公钥

**添加公钥**
![image_19.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_19_1642700157665.png)

![image_20.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_20_1642700157758.png)

![image_21.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_21_1642700157720.png)

**测试代码**

添加后，在终端（Terminal）中输入

```JavaScript
ssh -T git@gitee.com
```
![image_22.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_22_1642700191432.png)

## 9.3 Github添加公钥

**添加公钥**

![image_23.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_23_1642700200561.png)

![image_24.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_24_1642700200684.png)

![image_25.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_25_1642700200828.png)

**测试代码**

```JavaScript
ssh -T git@github.com
```
![image_26.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_26_1642700223700.png)

# 10 分支

## 10.1 概述

分支在GIT中相对较难，分支就是科幻电影里面的平行宇宙，如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，我们就需要处理一些问题了！

![image_27.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_27_1642700232461.png)

![image_28.png](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/image_28_1642700232449.png)

## 10.2 命令

```JavaScript
# 列出所有本地分支
git branch
 
# 列出所有远程分支
git branch -r
 
# 新建一个分支，但依然停留在当前分支
git branch [branch-name]
 
# 新建一个分支，并切换到该分支
git checkout -b [branch]
 
# 合并指定分支到当前分支
$ git merge [branch]
 
# 删除分支
$ git branch -d [branch-name]
 
# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```


如果同一个文件在合并分支时都被修改了则会引起冲突：解决的办法是我们可以修改冲突文件后重新提交！选择要保留他的代码还是你的代码！

master主分支应该非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下在新建的dev分支上工作，工作完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来。