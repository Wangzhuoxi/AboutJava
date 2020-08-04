git:和SVN一样，版本管理工具 

SVN介绍：https://blog.csdn.net/yonghuliu/article/details/83476959

# 集中式与分布式

Git 属于分布式版本控制系统，而 SVN 属于集中式。

![image-20200707005013270](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707005013270.png)

![img](https://camo.githubusercontent.com/2d189eb8f288d1305057460db068eb7595e74137/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31666532646337372d396132642d343634332d393062332d6262663530663634396261632e706e67)

Git:

![image-20200707005627374](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707005627374.png)

即时没有共享版本库自己也可以在本地仓库提交删除代码。

![image-20200707005331710](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707005331710.png)

集中式版本控制只有中心服务器拥有一份代码，而分布式版本控制每个人的电脑上就有一份完整的代码。

集中式版本控制有安全性问题，当中心服务器挂了所有人都没办法工作了。

集中式版本控制需要连网才能工作，如果网速过慢，那么提交一个文件的会慢的无法让人忍受。而分布式版本控制不需要连网就能工作。

分布式版本控制新建分支、合并分支操作速度非常快，而集中式版本控制新建一个分支相当于复制一份完整代码。

![image-20200707010025223](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707010025223.png)

# 中心服务器

中心服务器用来交换每个用户的修改，没有中心服务器也能工作，但是中心服务器能够 24 小时保持开机状态，这样就能更方便的交换修改。

Github 就是一个中心服务器。

# 工作流

新建一个仓库之后，当前目录就成为了工作区，工作区下有一个隐藏目录 .git，它属于 Git 的版本库。

Git 的版本库有一个称为 Stage 的暂存区以及最后的 History 版本库，History 中存有所有分支，使用一个 HEAD 指针指向当前分支。

![img](https://camo.githubusercontent.com/cb4dca781752f5111aaa3d721abadd36e0853baa/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f37316239376135302d613439662d346631612d383164312d3438633333363464363162332e706e67)

- git add files 把文件的修改添加到暂存区
- git commit 把暂存区的修改提交到当前分支，提交之后暂存区就被清空了
- git reset -- files 使用当前分支上的修改覆盖暂存区，用来撤销最后一次 git add files
- git checkout -- files 使用暂存区的修改覆盖工作目录，用来撤销本地修改

![img](https://camo.githubusercontent.com/522598a98e1f3f18406c306abfdfec73ed28f601/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f37326565376539612d313934642d343265392d623464372d3239633233343137636131382e706e67)

可以跳过暂存区域直接从分支中取出修改，或者直接提交修改到分支中。

- git commit -a 直接把所有文件的修改添加到暂存区然后执行提交
- git checkout HEAD -- files 取出最后一次修改，可以用来进行回滚操作

![img](https://camo.githubusercontent.com/8031f29882992b21036ed11d90fd9ed16238dbbc/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f61346130613665362d333836622d346266612d623839392d6563333364333331306633652e706e67)

# 分支实现

系统自动创建一个master分支。

每次提交，git会把这些提交串成一条时间线。这条时间线就是一个分支。

新建分支是新建一个指针指向时间线的最后一个节点，并让 HEAD 指针指向新分支表示新分支成为当前分支。

![image-20200707095912671](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707095912671.png)



![image-20200707100310217](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707100310217.png)

每次提交只会让当前分支指针向前移动，而其它分支指针不会移动。

#### 创建新的分支

![image-20200707100153192](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707100153192.png)

![image-20200707100848871](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707100848871.png)

# 冲突

当两个分支都对同一个文件的同一行进行了修改，在分支合并时就会产生冲突。

![img](https://camo.githubusercontent.com/4b8e44767bbe7341f2954373e24c41995ad2de88/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f33326230356538312d343162332d343134612d383635362d3733366339363034653364362e706e67)

Git 会使用 <<<<<<< ，======= ，>>>>>>> 标记出不同分支的内容，只需要把不同分支中冲突部分修改成一样就能解决冲突。

```
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

# Fast forward

"快进式合并"（fast-farward merge），会**直接将 master 分支指向合并的分支**，这种模式下进行分支合并会丢失分支信息，也就不能在分支历史上看出分支信息。

可以在合并时加上 --no-ff 参数来禁用 Fast forward 模式，并且加上 -m 参数让合并时产生一个新的 commit。

```
$ git merge --no-ff -m "merge with no-ff" dev
```

![img](https://camo.githubusercontent.com/a955d2d919270b15c85e1d856d175903671593d0/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f39613531393737332d383462322d346338312d383163662d3465376464373339613937612e706e67)

# 分支管理策略

master 分支应该是非常稳定的，只用来发布新版本；

日常开发在开发分支 dev 上进行。

![img](https://camo.githubusercontent.com/9af9d856c7c8626b5a78a4922c33ed4ce297d874/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f32343566643266622d323039632d346164352d626335652d6562353636343936366130652e6a7067)

# 储藏（Stashing）

在一个分支上操作之后，如果还没有将修改提交到分支上，此时进行切换分支，那么另一个分支上也能看到新的修改。这是因为所有分支都共用一个工作区的缘故。

可以使用 git stash 将当前分支的修改储藏起来，此时当前工作区的所有修改都会被存到栈上，也就是说当前工作区是干净的，没有任何未提交的修改。此时就可以安全的切换到其它分支上了。

```
$ git stash
Saved working directory and index state \ "WIP on master: 049d078 added the index file"
HEAD is now at 049d078 added the index file (To restore them type "git stash apply")
```

该功能可以用于 bug 分支的实现。如果当前正在 dev 分支上进行开发，但是此时 master 上有个 bug 需要修复，但是 dev 分支上的开发还未完成，不想立即提交。在新建 bug 分支并切换到 bug 分支之前就需要使用 git stash 将 dev 分支的未提交修改储藏起来。

# SSH 传输设置

Git 仓库和 Github 中心仓库之间的传输是通过 SSH 加密。

![image-20200707110132477](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707110132477.png)

#### 首先在github上建立一个远程仓库

![image-20200707110253719](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707110253719.png)

#### 在本地仓库通过以下命令来创建 SSH Key：

```
$ ssh-keygen -t rsa -C "git@github.com:itcasrgitub/repo1.git"
```

此时在文件中就是产生两个文件：私钥文件id_rsa 公钥文件：id_rsa.pub

#### 然后把公钥 id_rsa.pub 的内容复制到 Github "Account settings" 的 SSH Keys 中,

如下图所示

![image-20200707110710046](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707110710046.png)

![image-20200707110724410](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707110724410.png)

![image-20200707110732223](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707110732223.png)

#### 本次仓库对远程仓库进行连接

**![image-20200707105739932](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200707105739932.png)**

# .gitignore 文件

忽略以下文件：

- 操作系统自动生成的文件，比如缩略图；
- 编译生成的中间文件，比如 Java 编译产生的 .class 文件；
- 自己的敏感信息，比如存放口令的配置文件。

# Git 命令一览

![img](https://camo.githubusercontent.com/1350aec4b4cbc749b773e29b2def7e6ee1de0f01/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f37613239616363652d663234332d343931342d396630302d6632393838633532383431322e6a7067)