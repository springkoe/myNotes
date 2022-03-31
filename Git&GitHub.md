 

## 1. 版本控制

* 更多参见：https://www.bilibili.com/video/BV1pW411A7a5?p=1
* 协同修改：多人并行修改服务端同一个文件
* 数据备份：不仅保存目录和文件当前状态，还能够保存每一个提交过的历史状态
* 版本管理：在保存每一个版本文件信息时候要做到不保存重复数据，节约存储空间。SVN采用增量管理，Git采用文件快照
* 权限控制：对团队开发人员进行权限控制，对团队外发开贡献者的代码进行审核 --Git独有
* 历史记录：查看修改人、修改时间、修改内容、日志信息，将本地文件恢复到某个历史状态
* 分支管理：允许开发团队工作过程中多条生产线同时推
* 集中式：SVN
* 分布式：Git
* Git优势：

> 1. 大部分操作在本地完成，无需联网
> 2. 完整性保证（hash）
> 3. 尽可能添加数据而不是删除或修改数据
> 4. 分支操作快捷流畅（快照）
> 5. 与Linux命令全面兼容

## 2. Git结构

```
Git = 工作区（写代码） + 暂存区（临时存储） + 本地库（历史版本）
```

* GitHub：一个代码托管中心
* Gitee：国内的代码托管中心（马云）
* 团队内协作

```
1. 管理员创建本地库，push到远程库
2. 团队成员clone远程库到本地库（自动初始化仓库）
3. 团队成员push本地仓库代码到远程库（需要先被管理员邀请并加入团队）
4. 管理员pull远程仓库到本地库
```

* 跨团队写作

```
1. 管理员在原来远程库基础上fork出贡献者远程库（内容一致，但属于贡献者，贡献者点击管理员项目主页的fork）
2. 贡献者clone自己的远程库到本地，完成后push到自己远程库
3. 贡献者向管理员发送pull request请求，管理员进行审核
4. 审核通过后完成merge合并（合并管理员的远程库和贡献者的远程库）
```

## 3. 命令行操作

### 3.1 安装和配置

* 安装git：

```shell
$ sudo pacman -S git
```

* 配置个人信息：

```shell
$ git config --global user.name 用户名 # 配置用户名
$ git config --global user.email 邮箱 # 配置用户邮箱
$ git config --list # 查看当前配置信息

# 个人信息仅作区分开发者用户作用，作为签名信息 --global表示使用系统级别，通常来说系统级别就好
# 系统级别配置文件在 ~/.gitconfig
```

* 初始化仓库：

```shell
$ git init 

# 需要提前进入到工作去目录 如~/workspace/test_git
# 该命令直接效果是在当前初始化的目录下创建一个.git文件夹，包含配置信息，历史记录等全部信息
```

* 查看git状态

```shell
$ git status
# 说明: 初始化仓库后默认工作在master分支，当工作区与仓库区不一致时会有提示。
```

### 3.2 工作与暂存区交互

* 工作区内容添加到暂存区

```shell
$ git add 文件名
$ git add *	# 添加全部

# 删除文件时 必须使用 git add 文件名
```

* 删除暂存区内容

```shell
$ git rm --cached 文件名	# 工作区不变 仅仅删除暂存区内容
```

* 从暂存区恢复内容到工作区

```shell
$ git checkout 文件名		# 工作区误删了 可以从暂存区找回 | 工作区写着写着不对 可以从暂存区恢复
```

* 查看暂存区所有内容

```shell
$ git ls-files
```

* 查看工作区和暂存区文件差异

```shell
$ git diff 文件名 			# 将工作区和暂存区文件进行比较 以行为单位
```

* add 过程中 设置自动忽略内容

```shell
$ vim .ignore

# .gitignore忽略规则简单说明


# file            表示忽略file文件
# *.a             表示忽略所有 .a 结尾的文件
# !lib.a          表示但lib.a除外
# build/          表示忽略 build/目录下的所有文件，过滤整个build文件夹；
```

### 3.4 暂存区与本地仓交互

* 暂存区提交到本地库

```shell
$ git commit [文件名] -m "提交说明"
```

* HEAD 指针

> 1. HEAD指针通常指向当前分支（如第一次commit后，master指向根提交，HEAD指向master）
> 2. 切换分支时，HEAD指向切换后的分支（如hot_fix分支）

### 3.4 工作区和本地仓交互

* 比较文件差异（工作区文件和本地仓历史文件）

```shell
$ git diff HEAD^ 文件名 		# 将上一个版本的文件和工作区文件进行比较
```

### 3.5 工作区、暂存区、本地仓交互

* 查看历史版本

```shell
$ git log # 最完整格式 但是会退后看不到回退到时间点后的记录
$ git reflog # 增加跨越版本需要的步数 且可以看到所有记录
```

「版本跨越」

```shell
$ git reset --hard 历史版本的哈希值前7位
$ git reset --hard HEAD~数字 # 后退数字个版本

# 重置暂存区，重置工作区，在本地仓库移动HEAD指针（三区同步）
# 可以用来回溯版本，找回删除数据等操作
```

```shell
$ git reset --soft 历史版本的哈希值前7位

# 不会碰工作区和暂存区，仅仅在本地库移动HEAD指针（本地库往后一版本，工作区和暂存区貌似"向前"一版本=>git 所以status有变化）
```

```shell
$ git reset --mixed 历史版本的哈希值前7位

# 重置暂存区，在本地库移动HEAD指针
```

* 标签管理：记录|备份大版本

```shell
$ git tag -a 标签名 -m "标签说明"		# 打标签
$ git push origin 标签名			   # 将标签推送到远程仓库
$ git tag -d 标签名				   # 删除本地标签
$ git push origin --delete tag 标签名 # 删除远程标签
```

```shell
$ git tag 标签名 版本7位哈希id -m "标签说明" # 添加标签
$ git reset --hard 标签名 # 移动到标签所在版本
```

## 4. 分支管理

![](https://backlog.com/git-tutorial/cn/img/post/stepup/capture_stepup1_5_6.png)

* 特点：

  > 1. 同时推进多个任务（如同时开发多个新功能）
  > 2. 各个分支间互不影响（开发新功能不会影响到正在运行的主服务，主服务出问题可以马上开启热修复分支，不会停下服务）

* 查看当前分支

```shell
$ git branch     # 结果中带*开头的是当前所在分支
```

* 创建分支

```shell
$ git branch 分支名
```

* 切换分支

```shell
$ git checkout 分支名
```

* 将分支推送到远程

```shell
$ git push -u origin 分支名		# add commit push 后 远程将会有个新的分支
```

* 切换回原来分支

```shell
$ git checkout main
```

* 将新分支合并到主分支

```shell
$ git merge 分支名
```

* 将主分支推送到远程

```shell
$ git push			# 这样远程主分主就有了合并新分支的内容了 别人pull下来内容也会包含新内容
```

* 删除本地分支

```shell
$ git branch -d 分支名
```

* 删除远程分支

```shell
$ git push origin --delete 分支名
```

* 合并分支细节

```shell
# 第一步：先切换到被合并的分支
$ git branch 被合并分支
# 第二步：合并分支
$ git merge 合并进来的分支
```

* 合并冲突

  > 1. 冲突原因是不同分支修改了同一行内容
  > 2. 合并后出现冲突提示后，文件会处于待手动合并状态
  > 3. 编辑冲突文件，删除特殊字符，人为修改到满意程度后，保存退出
  > 4. git add 文件名
  > 5. git commit -m "日志信息"
  > 5. 养成良好的操作习惯,先`pull`在修改,修改完立即`commit`和`push`
  > 7. 一定要确保自己正在修改的文件是最新版本的
  > 8. 各自开发各自的模块
  > 9. 如果要修改公共文件,一定要先确认有没有人正在修改
  > 10. 下班前一定要提交代码,上班第一件事拉取最新代码
  > 11. 一定不要擅自修改同事的代码

```yacas
<<<<<<< HEAD
edit by master	# 当前分支内容
=======
edit by hot_fix	# 另一个分支内容
>>>>>>> hot_fix
```

* 减少冲突

  > 1. 降低项目耦合度，不同分支只编写自己的内容，尽量不要修改父分支内容
  > 2. 如果要修改父分支内容，提前做好分工，不要多个分支同时修改同一个父分支内容

* 删除分支

```shell
$ git branch -d 分支名
```

## 5. GitHub使用

「远程的本地仓」

> 1. 创建GitHub帐号
> 2. 创建远程仓库
> 3. 选择SSH连接方式，添加自己的公钥
> 4. 添加远程仓库

* 添加远程仓库

```shell
$ git remote add 使用SSH的远程仓库地址
```

* 查看当前远程仓库

```shell
$ git remote -v
```

* 断开远程仓库连接

```shell
$ git remote rm origin
```

* 将本地分支推送到远程仓库

```shell
$ git push origin 分支名 # 例如团队成员clone代码并添加内容后，执行此条命令将修改后内容推送到远程仓库，前提是被管理员邀请

$ git push -u origin 分支名 # 第一次推送分支使用-u表示与远程对应分支	建立自动关联 分支名如master
```

* 本地库修改时 推送到远程仓库

```shell
$ git push # 写远程库
```

* 克隆远程仓库

  > 1. 完整的把远程仓库下载到本地（自动完成）
  > 2. 创建origin远程地址别名（自动完成）
  > 3. 初始化本地仓库（自动完成）

```shell
$ git clone ... # 读远程库 新建本地库
```

* 允许指定团队成员可以推送

```
在GitHub主页的collaborators中添加对方的GitHub帐号
```

* 拉取远程仓库内容

```shell
$ git fetch origin master # 不会修改本地文件
```

* 查看拉取的内容

```shell
$ git checkout origin/master # 现在可以看到刚刚拉取的内容了,先fetch再merge意义在于可以查看并确认修改的内容
```

* 将远程仓库合并到本地仓库

```shell
$ git checkout master
$ git merge origin/master
```

* 拉取并合并

```shell
$ git pull # git pull = fetch + merge
```
