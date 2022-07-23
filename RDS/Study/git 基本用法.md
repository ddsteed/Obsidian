---
title: git基本用法
date: 2022-02-18 14:45:21
tags: 技术
toc: true
mathjax: false
categories: 技术
---
# 基本原理
---
# 子仓库
---
## 原理
---
如果一个仓库里面，需要使用第三方的独立仓库，则可以将该仓库作为原来仓库的子仓库，包含进项目内。这样，子仓库可以独立开发，独立升级，不影响父仓库项目。

如果只是把子仓库简单地拷贝进父仓库的目录内，git 会警告。这时可以使用 git 内置功能 submodule。

## submodule 使用
---
### 新建 submodule
在父仓库下执行：`git submodule add <仓库地址> <本地地址>`
添加成功后，会新增文件夹 submodule （或者指定的本地地址名称）。同时，还新增了文件 .gitmodules，包含 submodule 的一些描述，类似于
```
[submodule "third_party/pybind11"]
    ignore = dirty
    path = third_party/pybind11
    url = https://github.com/pybind/pybind11.git
```
执行命令 `cat .git/config` 可以发现配置中也新增了 submodule 段
```
[submodule "third_party/pybind11"]
  active = true
  url = https://github.com/pybind/pybind11.git
```

### 克隆 submodule
```
# 用来初始化本地配置文件
git submodule init
# 从该项目中抓取所有数据并检出父项目中列出的合适的提交(指定的提交)。
git submodule update

(以上两条命令也可以合并成一条组合命令：git submodule update --init --recursive)
------------------更好的方式---------------------
# clone 父仓库的时候加上 --recursive，会自动初始化并更新仓库中的每一个子模块
git clone --recursive https://gitee.com/xiaomumaozi/SubModule_Test.git
```

### 更新 submodule
`git submodule update --remote`

# 概念澄清
---
## bare repository vs. non-bare repository
---
建立 git 仓库时，有两种方式：bare 和 non-bare。命令分别是：`git init --bare` 和 `git init`。

bare 和 non-bare 仓库运行机理稍微有点差别，从目录结构来看，non-bare 仓库和本地的是一样的，但是 bare 仓库就相当于把  .git/ 目录下的所有文件（子目录）都移出来，而把实际的工作文件都去除了。如果只想建立一个远端仓库，就采用bare的模式。`push` 和 `pull` 都可以按照 `git` 的一般方式工作。

但是，有时候我们需要在远端仓库也改动文件（或者直接执行文件）---- 这相当于把远端仓库当作了本地仓库的备份，non-bare 仓库就发挥用处了。为了让 non-bare 仓库也能正常 `push` 和 `pull`，得先处理一下。
1. 在远端仓库运行命令：
   ```bash
   git config receive.denyCurrentBranch updateInstead
   ```
   这相当于告诉远端仓库，允许其他地方的文件进行推送；
   
2. 每次在本地进行 `push` 之后，在远端仓库中执行命令：`git reset --hard`，否则只是 .git 目录进行了同步，而实际的 working tree 下的所有文件并不更新。如果觉得这样很麻烦，可以在.git/hooks目录下建立一个文件 **post-receive**，内容如下:
   ```bash
   #!/bin/sh
   cd .. 
   env -i git reset --hard
   ```

表示每次 `push` 之后，自动执行 `reset` 命令。

参考: https://caiguanhao.wordpress.com/2013/01/10/git-push-non-bare-to-non-bare-repository/
	
## `fetch` vs. `pull`
---

# FAQ
## 远端仓库和本地代码冲突，无法提交
1. `git stash`
   先将本地代码存入临时缓存。可用 `git stash list` 查看缓存代码片段
2. `git pull`
   更新代码
3. `git stash pop stash@{0}`
   合并代码。其中 `stash@{0}` 是 `git stash` 时的一个标记，也可以使用 `git stash save XXX` 来定义一个标记，方便查询管理。
4. 打开冲突代码，手动解决

## 使用git pull命令出现如下警告
```
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint:
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```
解决办法：
若无特殊需求，执行命令
`git config pull.rebase false`
然后执行：
`git pull`

## 提交代码时用错了分支
1. `git checkout master` 切换到需要提交的分支上
2. `git cherry-pick change-id` 这里的 change-id 为在其他分支提交代码时的 id
3. `git push`

# continued
