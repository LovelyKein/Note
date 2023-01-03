## Git ?

> `Git`是一个开源的**分布式版本控制系统**；
>
> 用于敏捷高效地处理任何或小或大的项目；
>
> 不需要服务器端软件支持；



#### 版本控制

> 记录文件的内容变化，以便将来查阅待定版本修订情况；



###### 集中式

> `SVN`；
>
> 每次历史版本存储的是**版本之间的差异**，版本回溯时间较长；
>
> [^优点]:代码存放在单一的服务器上，便于项目的管理；
> [^缺点]:中央服务器宕机或损毁，整个项目的历史记录存在丢失风险；



###### 分布式

> `Git`；
>
> 存储的是**项目的完整快照**，需要的硬盘空间会较大；
>
> 版本回溯时间很快；





## 安装( Install )

> 在 Mac 中使用`brew`命令安装 Git；
>
> ```shell
> brew install git
> ```





## 配置( Config )

> 对 Git 做一些自定义配置；



#### 全局( Global )

> 用于 Git  的全局配置；



###### http.postBuffer

> 由于源代码比较庞大，clone 远程仓库 `RPC failure`报错；
>
> ```shell
> # 将提交文件大小的上限设置大，默认是 1M ；
> git config http.postBuffer 524288000
> # 或全局设置
> git config --global http.postBuffer 4M
> ```



#### 初始化( Initialize )

> 在新的系统上，需要先配置一下自己的 Git 的工作环境；
>
> 配置工作只需一次，之后也可以修改；
>
> `git config`；
>
> Git 提供了一个叫做`git config`的命令；
>
> 专门用来配置或读取相应的工作环境变量；



###### 用户信息

> 配置个人的用户名称和电子邮件地址；
>
> ```shell
> # 配置用户名；
> git config --global user.name "LovelyKein"
> # 配置用户邮箱地址；
> git config --global user.email 2312197273@qq.com
> 
> # 删除配置的用户信息；
> git config --global --unset user.name
> ```

[^Focus]:如果用了`--global`选项，该**用户**下的所有的项目都会默认使用这里配置的用户信息；
[^Focus]:如果用了`--system`选项，**系统**下的所有项目都会默认使用这里配置的用户信息；



###### 查看配置

> 要检查已有的配置信息；
>
> ```shell
> git config --list
> ```



###### 查看版本

> 查看安装的 Git 的版本；
>
> ```shell
> git --version
> ```



###### 初始化仓库

> 创建一个要被 Git 管理文件历史记录的仓库；
>
> ```shell
> git init
> ```





## Linux

> 基本的 Linux 系统命令；



#### 清屏

> 清除掉控制台显示的信息；
>
> ```shell
> clear
> ```



#### 创建文件

> 在**当前目录**下创建一个文件；
>
> ```shell
> # 创建一个 readme.md 文件，里面默认的内容是 message ；
> echo 'message' > readme.md
> ```



#### 查看文件

> 查看**目标目录**下的所有目录和文件；
>
> ```shell
> # find 关键词 要查看的目录路径；
> # ./ 就是当前目录；
> find ./
> 
> # 只查看文件，不看目录；
> find ./ -type f
> ```



#### 删除文件

> 删除**当前目录**下的某个文件；
>
> ```shell
> # rm 关键词 要删除的文件；
> rm readme.md
> ```



#### 文件重命名

> 对**当前目录**下的某个文件进行重命名；
>
> ```shell
> # mv 关键词 源文件 重命名的文件
> mv readme.md README.md
> ```



#### 查看内容

> 查看**指定文件**文件的内容；
>
> ```shell
> # cat 关键词 要查看的文件
> cat README.md
> ```



#### 修改文件

> 修改**指定文件**的内容；
>
> ```shell
> # vim 关键词 要修改的文件
> vim README.md
> 
> # 进入修改模式
> i
> 
> # 执行修改
> esc
> 
> # 保存并退出
> :wq
> 
> # 不保存退出
> :q!
> 
> # 设置行号
> :set nu
> ```





## 概念( Concept )

> Git 的**概念**和**底层原理**；



#### 区域

> 在 Git 中有 **3** 个区域的概念；



###### 工作区

> **“沙箱环境”**，就是本地的代码；可以任意的增删改差；
>
> Git 不会管理该区域的内容；

[^Focus]:**工作区**下的文件存在`已跟踪`和`未跟踪`两种状态；



###### 暂存区

> 存放确定项目版本之前的一系列修改的区域；

[^Focus]:**暂存区**的记录存在`已暂存`、`已修改`和`已提交`三种状态；



###### 版本库

> 存放确定修改的版本的区域；



#### 对象

> Git 工作流程中的 **3** 个对象；



###### Git 对象

> **Git 的核心部分是一个简单的`键值对数据库`**；
>
> 可以向数据库插入任意类型的内容，会返回一个`键值`；
>
> 通过该`键值`可以再次检索对应的内容；

[^Focus]:Git 对象的操作都是在对本地数据库进行操作，不涉及**暂存区**；
[^Focus]:Git 对象，又叫**数据对象**，在 Git 中只负责储存内容；



> 向数据库写入内容，并返回对应的键值；
>
> ```shell
> echo 'message' | git hash-object -w --stdin
> # 返回 hash 键值：87c01c71c438d680787f5942d2a3dc439b2b7300；
> 
> # 此时如果用 Linux 的 cat 命令查看文件内容，会显示乱码，因为 Git 会压缩储存内容；
> ```
>
> [^-w]:指示 `hash-object`储存写入的内容，若不指定，则只返回对应的键值；
> [^stdin]:`standard input`，指示从**标准输入读取内容**，若不指定，则在命令结尾需加**待储存文件的路径**；

> 将一个文件储存在 Git 对象数据库；
>
> ```shell
> git hash-object -w ./README.md
> # 返回对应该文件的 hash 键值
> # 839f6536e10ea0cb17487bc426d6cc99c22412fa
> ```

> 根据`键值`查看内容；
>
> ```shell
> git cat-file -p 87c01c71c438d680787f5942d2a3dc439b2b7300
> # 返回内容： 'message'；
> 
> # 87c01c71c438d680787f5942d2a3dc439b2b7300
> # 对应 'message' 的 hash 键值；
> ```
>
> [^-p]:指示命令自动判断内容的类型，并显示格式友好的内容；



###### 树对象

> 在 Git 中，文件名不会被保存，只会保存文件的内容；
>
> `数对象`，**解决文件名保存的问题**，也允许将多个文件组织到一起；
>
> Git 会以一种类似`UNIX `文件系统的方式储存内容；
>
> **树对象**相当于**项目的版本快照**；

[^Focus]:**暂存区**中，相同**文件名**的记录会被覆盖；

> 构建树对象；
>
> 将文件添加进**暂存区**；
>
> ```shell
> # update-index 命令为 file.txt 文件的首个版本创建一个暂存区；
> git update-index --add --cacheinfo 100644 b1a738526f9c813e3df48cc3e92e95bf270a4903 file.txt
> 
> # git update-index --add --cacheinfo 文件模式 内容‘hash’键值 暂存区文件名；
> ```
>
> [^--add]:此文件之前并不存在于**暂存区**，首次添加进要加该选项；
> [^--cacheinfo]:因为要添加的文件在 Git 对象数据库中，而不是当前目录，要添加该选项；
>
> 文件模式：
>
> > [^100644]:表示这是一个普通文件；
> > [^100755]:表示这是一个可执行文件；
> > [^120000]:表示一个符号链接；

> 查看**暂存区**的记录；
>
> ```shell
> git ls-files -s
> ```

> 给**暂存区**的记录做一个快照，生成一个**树对象**，放入`版本库`中；
>
> ```shell
> git write-tree
> # 返回 31e7ba8fc8e9d17ac940b456bd6d2c7fad090374 ,代表树对象的键值；
> ```

[^Question-1]:**树对象**保存了项目的快照，但是查看内容依然要对应的 hash 键值；
[^Question-2]:不知道不同的快照之间存在什么**变化**，且**什么时候**保存了新的快照；



###### 提交对象

> **提交对象**会对**树对象**进行包裹，可以对**树对象**做一个解释和注释；
>
> **提交对象**相当于一个完整的项目版本；
>
> 历史回溯只需要对应版本的 hash 键值；



> 通过`commit-tree`命令创建一个**提交对象**；
>
> ```shell
> # 需要指定待提交对象的 hash 键值；
> echo 'first-commit' | git commit-tree 31e7ba8fc8e9d17ac940b456bd6d2c7fad090374
> # 'first' 是提交对象的注释；
> # 返回输出：
> # 764afcb06311af4135ed3322fc3d1b2318ac1996
> 
> # 查看提交对象；
> git cat-file -p 764afcb06311af4135ed3322fc3d1b2318ac1996
> # 返回输出：
> # tree 70b14473557da808aa3cd147f588271ba62462c8
> # 顶层的树对象，代表当前的项目快照；
> # author LovelyKein <2312197273@qq.com> 1644321656 +0800
> # committer LovelyKein <2312197273@qq.com> 1644321656 +0800
> # 作者/提交者信息；
> 
> # first
> # 提交的注释；
> ```

> 第二次之后的提交，指定之前的**提交对象**为父提交对象，形成链式关系；
>
> ```shell
> # echo '提交注释' | git commit-tree '待提交对象' -p '父提交对象';
> echo 'second-commit' | git commit-tree 0f83f30a -p 764afcb0
> ```





## 命令( Command )

> Git 中的高层命令，对底层概念进行了合并封装；



#### git init

> 对当前目录进行初始化，将该目录下的文件交给 Git 管理，创建仓库；
>
> ```shell
> git init
> ```



#### git status

> 查看文件处于什么状态；
>
> ```shell
> git status
> ```



#### git diff

> 查看哪些**已修改**文件未添加到**暂存区**；
>
> 可以查看到文件具体有哪些修改处；
>
> ```shell
> git diff
> 
> # 查看哪些更新已放入暂存区，但是还未提交；
> git diff --cached
> # 或者（git 版本在 1.6.1 以上）；
> git diff --staged
> ```



#### git add ./

> 将工作区产生修改的文件生成**数量对应**的 Git 对象；
>
> 再将 Git 对象，添加进**暂存区**；
>
> ```shell
> git add ./
> ```

[^./]:`./`表示当前工作区目录下的所有文件；



#### git commit -m 'info'

> 给**暂存区**的记录做一个版本快照，生成一个**树对象**；
>
> 再使**树对象**去提交，包裹生成一个**提交对象**；
>
> ```shell
> git commit -m '提交对象的注释信息'
> ```



#### git commit -a

> 跳过**暂存区**，直接将**已修改**状态的**已被跟踪**的文件一并提交；
>
> ```shell
> git commit -a
> ```



#### git rm

> 删除**工作区**中对应的文件，再将修改添加到**暂存区**；
>
> ```shell
> git rm 'file.txt'
> ```



#### git mv

> 重命名**工作区**的文件，再将修改添加到**暂存区**；
>
> ```shell
> # git mv 原文件名 修改后文件名；
> git mv file.txt file-modify.txt
> 
> # 相当于运行了下面的命令；
> mv file.txt file-modify.txt
> git rm file.txt
> git add file-modify.txt
> ```



#### git log

> 查看**已提交**的历史记录日志；
>
> ```shell
> git log
> 
> # 同条日志记录友好显示在一行；
> git log --pretty=oneline
> 
> # 简写 hash 键值显示；
> git log --oneline
> ```





## 分支( Branch )

> [^Focus]:将工作从项目开发主线分离出来，避免对主线产生影响；
>
> Git 中的分支模型极其地高效轻量；
>
> **分支的本质就是一个提交对象，默认的名字是`master`**；



#### 创建分支

> `git branch`创建一个**可以移动的新指针**，创建一个新的**分支**；
>
> ```shell
> # 创建一个 testing 分支；
> git branch testing
> 
> # 该命令不加分支参数时，显示所有的分支列表；
> git branch
> ```

[^Focus]:创建新的分支后，并不会自动切换到**新分支**中去；



#### 切换分支

> 切换到目标分支，则之后的提交都在目标分支中；
>
> ```shell
> # 切换回到 master 主分支；
> git checkout master
> 
> # 创建并切换到新分支中（建议使用）；
> git checkout -b testing
> ```

[^Focus]:在每次切换分支之前，一定要将当前分支的修改进行提交，否则会污染其他分支；
[^Focus]:因为切换分支会改变**工作区的文件**、**暂存区的记录**、**`HEAD`所指向的分支**；



#### 删除分支

> 删除掉一个分支；
>
> ```shell
> # 删除掉 testing 分支；
> git branch -d testing
> 
> # 强制删除掉待销毁分支中的修改提交；
> git branch -D testing
> ```

[^Focus]:当前所在的分支不能不删除，需切换到另外的分支；



#### 合并分支

> 在当前分支上，合并另外一个分支；
>
> ```shell
> # 切换回主分支；
> git checkout msater
> # master 分支合并 testing 分支；
> git merge testing
> ```

[^Focus]:分清楚主次关系，一定是主要分支去合并其他分支；
[^Question]:当在不同的分支中，对同一个文件的同一部分进行了不同的修改，合并会产生冲突；
[^Answer]:解决完有冲突的部分，再暂存修改，Git 就会标记为冲突已解决；



#### 查看分支

> 查看当前分支的**最后一次提交**信息；
>
> ```shell
> git branch -v
> 
> # 查看项目的所有分支记录；
> git log --oneline --decorate --graph --all
> 
> # 查看那些分支已经被合并到当前分支；
> git branch --merged
> ```



#### 版本回溯

> 新建一个分支，并使该分支指向目标**提交对象**；
>
> ```shell
> # git branch '新分支名称' '目标提交对象的 hsah 键值'
> git branch history 764afcb0
> ```





## 存储( Stash )

> 将未完成的修改保存起来，等待下次继续应用，而不会污染其他分支；
>
> ```shell
> # 保存修改到一个栈中；
> git stash
> 
> # 查看栈中已存储的记录；
> git stash list
> 
> # 应用一个存储记录，但不会在栈中将它删除；
> # 如果不指定应用哪个存储记录，则默认选择栈顶的那个；
> git stash apply stash@{0}
> 
> # 移除栈中的一个存储记录；
> git stash drop stash@{1}
> 
> # 应用该存储记录后立即在栈中删除掉该记录；
> git stash pop
> ```





## 撤销/重置



#### 撤销( Backout )

> 撤销在工作区、暂存区、版本库中的操作与提交；
>
> ```shell
> # 撤销在 工作目录 中的修改；
> git checkout --file.txt
> # 撤销在 暂存区 中的记录；
> git reset HEAD file.txt
> # 撤销上一次的提交，重新再提交一次；
> git commit --amend
> ```
>



#### 重置( Reset )

> 重置的三步骤；
>
> ```shell
> # 第一步：重置了 HEAD，带着分支一起变化；
> git reset --soft HEAD~
> # 第二步：重置 HEAD、暂存区；
> git reset --mixed HEAD~
> # 第三步：重置 HEAD、暂存区、工作区；
> git reset --hard HEAD~
> ```

[^Alert]:`--hard`标记是 reset 命令里唯一危险的用法，是 Git 中会**真正销毁数据**的操作之一；因为它会强制覆盖**工作区**中的文件，若此前存在新增且从未加入暂存区并提交的文件，此操作会导致文件无法恢复；





## 数据恢复( Recover )

> 在强制删除了正在工作的分支，恢复该分支的数据；
>
> ```shell
> # 查看 Git 的工作日志；
> git reflog
> git log -g
> ```
>
> 怎么恢复数据？
>
> 找到目标分支的 hash 键值，直接创建新的分支；





## 标签( Tag )

> Git 可以给历史的某一个提交打上标签，表示重要；
>
> 常用来标记发布节点（版本号）；



#### 标签列表

> 展示出已有的标签；
>
> ```shell
> # 展示所有的标签；
> git tag
> # 按规则匹配标签：展示 v1.8.5 之后的标签；
> git tag -l 'v1.8.5*'
> ```



#### 创建标签

> 给一次提交打上标签；
>
> ```shell
> # 轻量标签
> git tag v1.0
> 
> # 给指定的提交对象打上标签；
> # git tag 标签名称 提交对象的'hash'键值
> git tag v1.1 b76fd8f
> ```



#### 查看标签

> 查看目标标签的一些内容和信息；
>
> ```shell
> # git show 标签名称
> git show v1.0
> ```



#### 删除标签

> 删除本地仓库上的一个标签；
>
> ```shell
> # git tag -d 标签名称
> git tag -d v1.0
> ```



#### 检出标签

> 查看某个标签所指向的文件版本；
>
> ```shell
> # git checkout 标签名称
> git checkout v1.2
> ```





## 远程协作( Team )

> 在 GitHub 上进行团队远程协作；



#### 基本流程

> 创建远程仓库；
>
> ```shell
> # 在代码管理平台上创建一个新仓库；
> # 仓库地址；
> https://github.com/LovelyKein/Test_GitHub.git
> ```
>
> 初始化本地仓库；
>
> ```shell
> git init
> ```
>
> 为远程仓库配置别名&用户信息；
>
> ```shell
> # git remote add 仓库别名 仓库地址
> git remote add test_GitHub https://github.com/LovelyKein/Test_GitHub.git
> 
> # 显示远程仓库使用的别名&地址 URL；
> git remote -v
> 
> # 查看目标远程仓库的信息；
> # git remote show 仓库别名
> git remote show test_GitHub
> 
> # 远程仓库别名的重命名；
> # git remote rename 旧名 新名
> git remote rename test_GitHub Test_GitHub
> ```
>
> 推送本地项目到远程仓库；
>
> ```shell
> # git push 仓库别名 项目分支
> git push Test_GitHub master
> # 每次只会推送一个分支；
> # 生成主分支的 远程跟踪分支 ；
> ```
>
> 克隆远程仓库到本地；
>
> ```shell
> git clone https://github.com/LovelyKein/Test_GitHub.git
> # 默认会创建本地仓库；
> # 默认会给该远程仓库配置 origin 别名；
> # 默认生成主分支的 远程跟踪分支 ；
> ```
>
> 更新提交内容，将内容获取到本地仓库；
>
> ```shell
> # git fetch 远程仓库别名
> git fetch Test_GitHub
> # 会将数据获取到本地仓库，但并不会合并或修改当前的工作区，获取的内容会在“远程跟踪分支”中，需要手动合并；
> git merge Test_GitHub/master
> ```



#### 远程跟踪分支

> 在`push`和`clone`操作之后，会生成当前分支的**远程跟踪分支**；
>
> 作为**远程仓库**与**本地仓库**的的媒介；
