#### git学习

https://www.cnblogs.com/sdcs/p/8270029.html

### GIt下载

下载地址:https://git-scm.com/dowloads

### Git本地工作区域

对于任何一个文件,在git内都只有三种区域:工作区,暂存区,本地仓库

+ 工作区:表示新增或修改了某个文件,但是还没有保存好
+ 暂存区:表示把已经新增或修改的文件,放在下次提交时要保存的清单中,
+ 本地仓库:文件已经被安全保存在本地仓库了

### 设置签名信息

作用:区分不同开发人员的身份信息

#### 项目级别:仅在当前目录的本地git仓库有效

```shell
git config user.name ssq
git config user.email ssq@qq.com
```

签名信息保存在:`./git/config文件中`

```shell
/*查看文件位置*/
pwd

git config user.name ssq
git config user.email ssq@qq.com

/*查看文件内容*/
cat .git/config
```

#### 系统用户级别:登录当前操作系统的用户范围

```shell
git config --global user.name ssq
git config --global user.email ssq@qq.com
```

### 生成秘钥

```shell
# 进入当前用户目录
$ cd ~
# 如果有.ssh目录,则删除.ssh目录
$ rm -rvf .ssh

# 命令生成.ssh密钥目录
$ ssh-keygen -t rsa -C ssq@qq.com
# 进入.ssh目录查看文件列表
$ cd .ssh
# 查看所有目录与文件
$ ls -lf
# 查看id_rsa.pub文件内容
$ cat id_rsa.pub
```



### Git 基本操作

+ 查看状态 `git status`

+ 创建文件(按i 插入内容,按:wq保存并退出,按!q不保存强制退出) `vim demo.txt`

+ 查看版本的历史记录 `git log` 每条记录只显示一行 `git log --pretty=oneline`

+ 显示回滚版本步数:`git reflog`

+ 前进后退版本版本

  + 基于索引值操作:`git reset --hard <索引值>` 
  + 举例: `git reset --hard 64d3d2a`

+ 后退版本

  + 使用^符号:只能后退 `git reset --hard HEAD^`
  + 使用~符号,只能后退:`git resrt --hard HEAD~n`

  + 对比文件差异:`git diff <文件名>`

### 第一次提交到git

+ 在github上创建一个仓库
+ 通过命令 git init 把文件夹变成Git可管理的仓库
```shell
$ git init
```
+ 然后我们通过git add .把刚才复制过来的项目全部添加到仓库上。
```shell
$ git add .
```
+ 用git commit -m "日志" 把项目提交到仓库。
```shell
$ git commit -m "备注"
```
+ Github上创建好Git仓库之后我们就可以和本地仓库进行关联了，根据创建好的Git仓库页面的提示，可以在本地testapp仓库的命令行输入：(==下次添加不需要此步骤==)
```shell
$ git remote add origin  https://github.com/shenshuqin/models.git
#其中origin是这个远程仓库链接的名字(自己命的)
```
+  关联好之后我们就可以把本地库的所有内容推送到远程仓库（也就是Github）上了，通过：$ git push -u origin master
```shell
$ git push -u origin master
#master是默认的主分支 -u是第一次push需要加的参数
```
### push到远程
```shell
$ git add .
#用git commit -m "日志" 把项目提交到本地仓库。
$ git commit -m "备注"
#push提交到origin远程仓库的master分支
$ git push -u origin master
```

### 克隆项目
```shell
$ git clone  https://github.com/shenshuqin/models.git newfoldername
```
例如 我需要改动别人写的一个仓库的代码
```shell
#先克隆这个项目
$ git clone  https://github.com/shenshuqin/models.git newfoldername 
#初始化本地仓库
$ git init
#然后就可以进行改动了
#改动之后添加改动
$ git add .
#提交改动到本地仓库
$ git commit . -m 克隆后第一次改动
#提交改动到远程仓库
$ git push origin master
```
### PULL拉取

pull拉取其实是操作两步: pull = fetch + merge

fetch 操作:只是把远程仓库下载到本地,但是没有改变本地工作区的文件

merge操作: 把远程仓库地址合并到本地代码中,git merge 远程仓库别名/远程分支名

```shell
git fetch 远程仓库名  远程分支名

# 演示

git fetch origin master
cat demo.txt //查看并没有改变本地工作区的文件
git checkout origin/master  //切换到远程分支对比内容
cat demo.txt  //内容不一样

git checkout master //切换本地仓库

 # 合并
 git merge origin/master
```

pull 操作

```shell
git pull 远程仓库别名  远程分支

# 演示

git checkout master

git pull origin master
```



### 新建分支

```shell
#切换到基础分支
git checkout master
#创建并切换到新分支
git checkout -b ssq
#可以看到已经在ssq分支上
git branch
#更新分支代码并提交
git add *
git commit -m "init ssq"
git push origin ssq
#在git代码管理界面经可以看到ssq分支了，成功~~
```

### 合并

```shell
#首先切换到分支
git checkout ssq
#拉取主分支,即可合并
git pull origin master
#若有冲突,解决冲突,需要重新提交,最后push即可
git add .
git commit -m 'resove conflict'
git push origin master

# 如果分支合并出错,可以撤销合并,重新再来
git reset --merge  
```



### pull 项目

若远程某一分支上代码版本与当前本地的版本不同,可以通过pull来拉取你们 之间的差异
```shell
$ git pull origin master

```

### 码云问题解决(push提交报错需要输入用户名和密码):原因是采用的是https方式提交代码如果采用的是ssh方式只需要在版本库中添加用户的sha的key就可以实现提交时无需输入用户名和密码。


```shell
git remote -v
git remote rm origin
git remote add origin git@gitee.com:username/repository.git
git push -u origin master

```
### git修改远程仓库地址
```shell
git remote origin set-url [url]
```
### 创建分支
创建dev分支,然后切换到dev分支
```shell
git checkout -b dev
```
git checkout 命令加上-b参数表示创建并切换,相当于下面这两条指令
```shell
 git branch dev
 git checkout dev
```
然后用`git branch`命令查看当前分支
```
 git branch
* dev
  master
```

### git两人合作

甲:
+ 首先将修改代码上传Git 
```shell
git add .
git commit -m ''
git push origin master
```
+ 切换到主分支,合并
```shell
git checkout master
git merge ssq(分支)
git push origin master
```
乙:
+ 将主分支的代码拉取下来
```shell
git checkout master
git pull origin master
git merge hujin(分支)
```
+ 如果有冲突,解决冲突,再上传到master
```shell
git add .
git commit -m ''
git push origin master
```
甲继续工作,需要从主分支拉取
```shell
git checkout master
git pull origin master
git checkout ssq
```

### 遇到重命名仓库后如何解决问题

1.首先删除以前的旧地址

```shell
//查看地址
git remote -v
//删除
git remote rm origin
```

2.修改github仓库名称和本地路径名称

```shell
git remote add origin git@github.com:.....//(新路径)
//重新push
git push -u origin master
```
### 上游仓库的操作
#### 1.首先fork
#### 2.将远程仓库clone下来
#### 3.将代码复制拷贝入本地仓库,然后push至远程仓库
#### 4.发起合并请求至上游仓库-分支
#### 5.拉取上游仓库
```shell
//关联仓库
git remote add upstream url(上游仓库地址)
git remote show
//切换
git switch master
//拉取(相应的分支)
git pull upstream master
```
#### 6.push到远程仓库


