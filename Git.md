# Git

自学资源：廖雪峰的Git教程

网站：https://www.liaoxuefeng.com/wiki/896043488029600

---

# Git


## 注意事项

---

* 不要使用Windows自带的记事本编辑任何文本文件，因为每个文件开头自动附带了0xefbbbf的字符，会产生一些错误，推荐使用VScode进行编辑


## 添加

---

#### 基础知识

* 工作区：电脑中的目录
* 版本库： .git 文件夹中保存了历史信息，不建议改动。

  * 其中最重要的是stage的**暂存区**，其中有各个分支，例如自动创建的master和它的一个指针HEAD
* Git 记录每次修改的信息，而不是整个文件，同时用指针指向版本，因此存储空间极其小。
* Git 遵循Unix的哲学“没有消息就是好消息”，有些指令不会有反馈消息，表示成功。


#### git init

将当前目录初始化为一个版本库


#### git add

将文件提交到缓冲区

```git
$ git add <file>
```


#### git commit

将缓冲区的文件提交到版本库中，更新版本至当前状态

```git
$ git commit

// 附带备注信息
$ git commit -m "message"

```


## 版本管理

---

#### 基础知识

* 指针HEAD 表示当前版本；HEAD^ 表示上一个版本； HEAD^^ 表示上上个版本； HEAD~100 表示向前回溯100次的版本
* 每个版本都有一个commit id（版本号），使用SHA1计算出来的十六进制数字，可以用前面若干位表示一个版本号，Git会自动寻找匹配
* 版本回溯时，实际是将HEAD指针指向某个版本，因此速度很快
*



#### git staus

查看仓库当前状态，可以知道哪些文件被修改了，哪些修改还没有提交

```git
$ git status
```

当没有需要提交的修改且工作目录无修改文件时，会提示：

```git
On branch master
nothing to commit, working tree clean
```


#### git diff

查看一个文件具体被修改的内容

```git
$ git diff <file>

// 查看工作区和版本库里最新版本的区别
$ git diff HEAD -- <file>
```


#### git log

查看最近3次<u>提交</u>的历史记录

```git
$ git log

// 每条记录仅用一行显示
$ git log --pretty=oneline
```


#### git reflog

显示<u>命令</u>的历史记录

```git
$ git reflog
// 前面的字符串是版本号的前几位
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```


#### git reset

* 回溯版本至某个版本号（也可以是未来的版本号），实际上是将HEAD指针指向一个版本
* 将**暂存区**的修改回退到工作区中

```git
// 回溯版本
$ git reset --hard <commit id>

// 撤销暂存区的修改
$ git reset HEAD <file>
```


#### git checkout

*撤销操作新版的Git 使用git restore代替*

* 将文件在**工作区**的修改（包括删除）全部撤销（回到最近一次 git commit/git add 时的状态）：

  1. 文件修改后并没有提交到暂存区：撤销**工作区**的文件修改，回到和版本库一模一样
  2. 文件修改后已经提交到暂存区，然后又进行了修改：撤销**工作区**的文件修改，回到添加到暂存区后的状态


*切换操作新版的Git 使用git switch代替*

* 工作区切换到其他分支

```git
// 撤销文件修改
$ git checkout -- <file>

// 切换到分支
$ git checkout <branch>

// 创建并切换到新的分支
$ git checkout -b <branch>
```


#### git rm

删除工作区中的文件，并把这次删除操作提交到暂存区

* 相当于 rm <file> + git add <file>
* 需要在这之后使用git commit

```git
$ git rm <file>
```



## 远程仓库

---

#### 基础知识

**创建远程仓库，****以GitHub举例：**

本地Git仓库和GitHub仓库之间的传输是通过SSH加密的。

1. 创建一个GitHub账号
2. 在用户主目录（C:/User/用户名）查看是否有.ssh目录，这个目录下有无 id_rsa 和 id_rsa.pub 两个文件

    * 其中 id_rsa 是私钥（不可泄漏），id_rsa.pub 是公钥
    * 如果没有：打开Git Bash，创建SSH Key：
    * ```git
      $ ssh-keygen -t rsa -C “youremail@example.com”
      ```
3. 登录GitHub，在 Account settings - SSH Keys - Add SSH Key 文本框中，粘贴 id_rsa.pub 的文本内容


**SSH警告**

第一次使用 clone 和 push 命令连接到GitHub时会得到一个警告：

```git
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```

这是因为SSH连接第一次验证GitHub服务器的Key时，需要确认GitHub的Key的指纹信息是否真的来自GitHub服务器，输入 yes 回车即可。

这时候Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

```git
Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
```



#### 添加远程库

1. 在GitHub上创建一个Git仓库

    * 登录GitHub - Create a new repo（创建一个新的仓库） - 输入Repository name
2. 关联本地仓库(需要已经初始化)和GitHub仓库

    * 在本地的仓库下运行命令 (shortname是远程库的别名，origin是Git的默认叫法)
    * ```git
      $ git remote add <shortname> git@github.com:<Username>/<Repository>.git
      ```

3. 将本地库的所有内容推送到远程库上

    * ```git
      // -u参数 会让本地分支和远程分支关联起来，之后的推送或拉取可以简化命令
      $ git push -u <shortname> <branch>
      ```


#### 删除远程库

先使用命令查看远程库信息

```git
$ git remote -v
```


解除本地库和远程库的绑定关系，并没有物理上删除远程库，远程库本身没有任何改动

想要真正删除远程库，需要在GitHub的后台页面删除

```git
$ git remote rm <shortname>
```


#### 克隆远程库

克隆已有的远程库到本地

```git
$ git clone git@github.com:<Username>/<Repository>.git
```




## 分支管理

---

#### 基础知识

* Git里主分支一般为master，HEAD并不是严格指向提交，而是指向master，master指向新的提交
* 当创建新的分支dev时，HEAD指向dev，此时dev和master指向同一个提交
* 在dev上进行提交，dev“向前移动”指向新的提交，而HEAD会跟进指向dev，然而master指针并没有发生变化，仍然指向旧的提交
* 当把dev分支合并到master上时，master指向dev当前指向的提交，HEAD重新指向master。由于只是改变指针，所以合并速度非常快


#### 创建分支

```git
// -b参数 表示创建并切换，相当于后面两条指令
$ git checkout -b <NewBranch>

$ git branch <branch>
$ git checkout <branch>

// 更加科学的创建命令, -c参数 表示创建并切换
$ git switch -c <NewBranch>
```


#### 查看分支

其中显示*号的表示当前所在分支

```git
$ git branch
```


#### 切换分支

会让工作区转换到其他分支的状态

```git
$ git checkout <branch>

$ git switch <branch>
```


#### 合并分支

将分支合并到主分支上

```git
$ git merge <AssociateBranch>
```


#### 删除分支

```git
$ git branch -d <branch>
```


#### 解决冲突

* 当两个分支各自有新的提交时，有时候会形成冲突，无法合并（Git不知道你想保留那一边的冲突部分），这个时候需要手动解决冲突再合并
* 先使用 git status 查看冲突的文件
* 然后打开冲突的文件，进行手动修改

  * Git会使用 === 《《《  》》》标记出不同分支的内容，这些标记也需要手动删除
* 修改完毕后再次进行提交即可，之后可以删除副分支
* 还可以使用带参数的git log 查看分支合并情况

  * ```git
    $ git log --graph --pretty=oneline --abbrev-commit 
    ```


#### 分支管理策略

* 合并分支时Git默认使用 Fast forward 模式，这种模式下删除分支后会丢失分支信息
* 禁用 Fast forward 模式时，Git会在merge时生成一个新的commit，这样就可以在分支历史上看到分支信息

  * ```git
    $ git merge --no-ff -m "message" <AssociateBranch>
    ```
* 合并后可以使用 git log 查看分支历史



#### 储藏分支

暂存当前未完成的分支，以此切换到其他分支进行别的工作

```git
$ git stash
```


查看暂存的分支

```git
$ git stash list
```


恢复分支

```git
// 恢复并删除暂存的分支
$ git stash pop [id]

// 相当于以下两个命令
$ git stash apply [id]
$ git stash drop [id]
```


复制特定提交到当前分支

```git
$ git cherry-pick [id]
```


#### 强制丢弃分支

强制丢弃一个没有被合并过的分支

```git
$ git branch -D <branch>
```



#### 抓取分支

一般情况下，克隆一个远程库只有master分支。

使用以下指令将一个远程分支抓取到本地

```git
$ git checkout -b <branch> <shortname>/<RemoteBranch>
```


#### 推送分支

在本地分支修改完毕后可以推送修改到远程分支

```git
$ git push <shortname> <branch>
```

如果推送时有冲突，则需要手动将远程分支抓取到本地，在本地解决冲突并合并，然后再推送

```git
// 设置本地分支与远程分支的链接
$ git branch --set-upstream-to=<shortname>/<RemoteBranch> <branch> 

// 拉取远程分支
$ git pull
```


#### rebase

将分查的提交历史“整理”成一条直线，看上去更加直观。

原理是把本地的修改（从分叉点开始）移动到分歧分支最新提交之后

缺点是本地的分叉提交已经被修改过了

```git
$ git rebase
```



## 标签管理

---

#### 基础知识

标签（tag）可以理解为 commit id 的别名


#### 创建标签

首先切换到一个分支上，然后使用命令：

```git
$ git tag <tagname>

// 对一个commit打标签
$ git tag <tagname> [commit id]

// -a参数 创建带有说明的标签
$ git tag -a <tagname> -m "message" [commit id]
```


查看所有标签

```git
$ git tag
```


查看标签信息

```git
$ git show <tagname>
```


#### 操作标签

删除标签

```git
// 删除本地标签
$ git tag -d <tagname>

// 删除远程标签
$ git push <shortname> :refs/tags/<tagname>
```


推送标签

```git
// 推送一个标签
$ git push <shortname> <tagname>

// 推送本地所有未推送的标签
$ git push <shortname> --tags
```
