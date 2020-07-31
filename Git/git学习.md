#### git学习

https://www.cnblogs.com/sdcs/p/8270029.html

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
#关联远程
#$ git remote add origin https://github.com/shenshuqin/models.git
#然后就可以进行改动了
#改动之后添加改动
$ git add .
#提交改动到本地仓库
$ git commit . -m 克隆后第一次改动
#提交改动到远程仓库
$ git push origin master
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
#方法一
# 首先切换到分支
git checkout ssq
# 使用git pull 把分支代码pull下来
git pull
# 切换到主分支
git checkout master
# 将分支代码merge到主分支
git merge ssq
#推送上去
git push

#方法二
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

