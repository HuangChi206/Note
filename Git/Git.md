## 1. Git配置

### 1.1 配置优先级

Git 提供了一个叫做 `git config` 的工具，专门用来配置或读取相应的工作环境变量。

这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

* `/etc/gitconfig` 文件：系统中对所有用户都普遍适用的配置。若使用 `git config` 时用 `--system` 选项，读写的就是这个文件。

- `~/.gitconfig` 文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用 `--global` 选项，读写的就是这个文件。
- 当前项目的 Git 目录中的配置文件（也就是工作目录中的 `.git/config` 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 `.git/config` 里的配置会覆盖 `/etc/gitconfig` 中的同名变量。

优先级：`--local > --global > --system`

### 1.2 用户信息

配置个人的用户名称和电子邮件地址：

```bash
# 查看用户
git config --global user.name
# 设置用户
git config --global user.name '姓名'
# 查看邮箱
git config --global user.email
# 设置邮箱
git config --global user.email '邮箱'
```

如果用了 **--global** 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里

### 1.3 文本编辑器

```bash
# 查看git默认使用的文本编辑器
git config --global core.editor
```

### 1.4 差异分析工具

```bash
# 查看git默认使用的差异分析工具
git config --global merge.tool
```

### 1.5 查看配置信息

```bash
# 查看local配置信息
git config --local -l
# 查看global配置信息
git config --global -l
# 查看system配置信息
git config --system -l
```

## 2. Git工作区、暂存区和版本库

Git 工作区、暂存区和版本库概念：

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫 stage 或 index。一般存放在 **.git** 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录 **.git**，这个不算工作区，而是 Git 的版本库。

![1352126739_7909](Git.assets\2023-12-20-01.jpg)

- 图中左侧是工作区、右侧是版本库。在版本库中标记为“index”的区域是暂存区（stage/index），标记为“master”的是master分支所代表的目录树。
- 图中此时“HEAD”实际是指向master分支的一个“游标”。所以图示的命令中出现HEAD的地方我们用master来替换。
- 图中的objects标识的区域为Git的对象库，实际位于“./git/objects”目录下，里面包含了创建的各种对象及内容。
- 当对工作区修改（或新增）的文件执行`git add`命令式，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。
- 当执行提交操作`git commit`时暂存区的目录树写到版本库(对象库)中,master分支会做相应的更新。即master指向的目录树就是提交时暂存区的目录树。
- 当执行`git reset HEAD`时，暂存区的目录树会被重写，被master分支指向的目录树所替换，但是工作区不受影响。
- 当执行`git rm --cached <file>`时，会直接从暂存区删除文件，工作区则不做出改变。
- 当执行`git checkout .`或者`git checkout -- <file>`时，会用暂存区全部或指定的文件替换工作区的文件。这个操作会清除工作区中未添加到暂存区中的改动。
- 当执行`git checkout HEAD .`或者`git checkout HEAD <file>`时，会用HEAD指向master分支中的全部或者部分文件替换暂存区和工作区中的文件。这个操作不但会删除工作区中未提交的改动，也会清除暂存区的未提交的改动。

## 3. Git基本操作

Git 的工作就是创建和保存你项目的快照及与之后的快照进行对比。

![git-command](Git.assets\2023-12-20-02.jpg)

**说明：**

- workspace：工作区
- staging area：暂存区/缓存区
- local repository：版本库或本地仓库
- remote repository：远程仓库

### 3.1 创建和获取项目

| 命令        | 说明               |
| ----------- | ------------------ |
| `git init`  | 初始化仓库。       |
| `git clone` | 拷贝一份远程仓库。 |

### 3.2 提交与修改

| 命令           | 说明                                     |
| -------------- | ---------------------------------------- |
| `git add`      | 添加文件到暂存区。                       |
| `git status`   | 查看仓库当前的状态，显示有变更的文件。   |
| `git diff`     | 比较文件的不同，即暂存区和工作区的差异。 |
| `git commit`   | 提交暂存区到本地仓库。                   |
| `git reset`    | 回退版本。                               |
| `git rm`       | 将文件从暂存区和工作区删除。             |
| `git mv`       | 移动或重命名工作区文件。                 |
| `git checkout` | 分支切换。                               |
| `git switch`   | 更清晰的切换分支。                       |
| `git restore`  | 恢复或撤销文件的修改。                   |

### 3.3 提交命令

| 命令               | 说明                                   |
| ------------------ | -------------------------------------- |
| `git log`          | 查看历史提交记录。                     |
| `git blame <file>` | 以列表形式查看指定文件的历史修改记录。 |

### 3.4 远程操作

| 命令         | 说明               |
| ------------ | ------------------ |
| `git remote` | 远程仓库操作。     |
| `git fetch`  | 从远程获取代码库   |
| `git pull`   | 下载远程代码并合并 |
| `git push`   | 上传远程代码并合并 |

## 4. Git分支管理

几乎每一种版本控制系统都以某种形式支持分支，一个分支代表一条独立的开发线。

使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。

![git-brance](Git.assets\2023-12-20-03.svg)

```bash
# 查看分支
git branch
# 创建分支
git branch 分支名
# 切换分支
git checkout 分支名
# 合并分支
git merge 分支名
# 创建分支并切换到新分支
git checkout -b 分支名
# 删除分支
git branch -d 分支名
```

## 5. Git提交历史

Git 提交历史一般常用两个命令：

- `git log` 查看历史提交记录。
- `git blame <file>` 以列表形式查看指定文件的历史修改记录。

#### 5.1 git log

```bash
git log [选项] [分支名/提交哈希]
```

常用的选项包括：

- `-p`：显示提交的补丁（具体更改内容）。
- `--oneline`：以简洁的一行格式显示提交信息。
- `--graph`：以图形化方式显示分支和合并历史。
- `--decorate`：显示分支和标签指向的提交。
- `--author=<作者>`：只显示特定作者的提交。
- `--since=<时间>`：只显示指定时间之后的提交。
- `--until=<时间>`：只显示指定时间之前的提交。
- `--grep=<模式>`：只显示包含指定模式的提交消息。
- `--no-merges`：不显示合并提交。
- `--stat`：显示简略统计信息，包括修改的文件和行数。
- `--abbrev-commit`：使用短提交哈希值。
- `--pretty=<格式>`：使用自定义的提交信息显示格式。

#### 5.2 git blame

`git blame` 命令用于逐行显示指定文件的每一行代码是由谁在什么时候引入或修改的。

```bash
git blame [选项] <文件路径>
```

常用的选项包括：

- `-L <起始行号>,<结束行号>`：只显示指定行号范围内的代码注释。
- `-C`：对于重命名或拷贝的代码行，也进行代码行溯源。
- `-M`：对于移动的代码行，也进行代码行溯源。
- `--show-stats`：显示包含每个作者的行数统计信息。

```bash
# 显示文件每一行的代码注释和相关信息
git blame <文件路径>
# 只显示指定行号范围内的代码注释
git blame -L <起始行号>,<结束行号> <文件路径>
# 对于重命名或拷贝的代码行进行溯源
git blame -C <文件路径>
# 对于移动的代码行进行溯源
git blame -M <文件路径>
# 显示行数统计信息
git blame --show-stats <文件路径>
```

## 6. Git标签

```bash
# 查看标签
git tag
# 设置标签
git tag -a 标签名 [提交哈希]
# 删除标签
git tag -d 标签名
```

## 7. Git远程仓库

### 7.1 添加远程仓库

```bash
# 添加远程仓库
git remote add 仓库名称 [url]
```

### 7.2 查看当前远程仓库

```bash
# 查看当前远程库
git remote
git remote -v
```

### 7.3 提取远程仓库

Git 有两个命令用来提取远程仓库的更新。

1、从远程仓库下载新分支与数据：

```bash
git fetch
```

该命令执行完后需要执行 git merge 远程分支到你所在的分支。

2、从远端仓库提取数据并尝试合并到当前分支：

```bash
git merge
```

该命令就是在执行 `git fetch` 之后紧接着执行 `git merge` 远程分支到你所在的任意分支。

![main-qimg-00a6b5a8ec82400657444504c4d4d1a7](Git.assets\2023-12-20-04.png)

### 7.4 推送到远程仓库

```bash
# 将本地的 [branch] 分支推送成为 [alias] 远程仓库上的 [branch] 分支
git push [alias] [branch]
```

### 7.5 删除远程仓库

```bash
git remote rm 仓库名称
```

