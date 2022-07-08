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

# 概念澄清
---
## bare repository vs. non-bare repository
---
建立git仓库时，有两种方式：bare 和 non-bare。命令分别是：`git init --bare` 和 `git init`。

bare和non-bare仓库运行机理稍微有点差别，从目录结构来看，non-bare仓库和本地的是一样的，但是bare仓库就相当于把 .git/ 目录下的所有文件（子目录）都移出来，而把实际的工作文件都去除了。如果只想建立一个远端仓库，就采用bare的模式。`push`和`pull`都可以按照`git`的一般方式工作。

但是，有时候我们需要在远端仓库也改动文件（或者直接执行文件）---- 这相当于把远端仓库当作了本地仓库的备份，non-bare仓库就发挥用处了。为了让non-bare仓库也能正常`push`和`pull`，得先处理一下。
1. 在远端仓库运行命令：
   ```bash
   git config receive.denyCurrentBranch updateInstead
   ```
   这相当于告诉远端仓库，允许其他地方的文件进行推送；
   
2. 每次在本地进行`push`之后，在远端仓库中执行命令：`git reset --hard`，否则只是.git目录进行了同步，而实际的working tree下的所有文件并不更新。如果觉得这样很麻烦，可以在.git/hooks目录下建立一个文件 **post-receive**，内容如下:
   ```bash
   #!/bin/sh
   cd .. 
   env -i git reset --hard
   ```

表示每次`push`之后，自动执行`reset`命令。

参考: https://caiguanhao.wordpress.com/2013/01/10/git-push-non-bare-to-non-bare-repository/
	
## `fetch` vs. `pull`
---

# FAQ
## 远端仓库和本地代码冲突
1. `git stash`
   先将本地代码存入临时缓存。可用`git stash list`查看缓存代码片段
2. `git pull`
   更新代码
3. `git stash pop stash@{0}`
   合并代码。其中`stash@{0}`是`git stash`时的一个标记，也可以使用`git stash save XXX`来定义一个标记，方便查询管理。
4. 打开冲突代码，手动解决

### continued
