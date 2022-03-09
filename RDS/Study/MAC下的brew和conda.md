---
title: MAC下的brew和conda
date: 2022-02-07 15:38:21
tags: 技术
toc: true
mathjax: false
categories: 技术
---
# 基本概念
## mac
mac指代一种unix-like的操作系统，以前常称之为mac OSX，现在apple公司为了统一旗下的操作系统，将电脑上的改为macos。macos的内核是借鉴FreeBSD以及Mach这两种系统的优点，混合而成的。在其之上，apple又增加了很多图形层以及其他应用层，形成了独特的mac os。

## brew
虽然macos是unix-like的，但是很多unix的应用程序，尤其是Linux上的程序，并没有包含在mac里。而且，mac的unix-like程序升级和管理都比较麻烦，不像Ubuntu/Debian/Fedora等，有自己的软件包管理系统。所以，有人设计了brew系统，模仿Linux下的apt管理包，用以管理mac下的各种软件。

## conda
conda是另一种软件包的管理方式，其来源于python下软件管理系统。随着python语言的流行，很多软件利用python语言来开发。因此python下为了管理这些软件，发展出了独特的pip和conda两套模式。这两种本来是独立的，但经常也混着用。因为pip出现得较早一点，前期用得较多。conda设计更合理一些，但由于出现得较晚，有些软件包还没有纳入conda管理。所以，在某些情况下，安装某个大型软件，一会用conda安装，一会又用pip辅助一下。

## 总结
macos是一个独立的操作系统，但是对于很多unix-like软件，没有较好的软件包管理。因此两种不同的软件包管理系统：brew和conda就成为了mac上流行管理软件。

这三种生态下，都可以安装软件。因此，很多软件都是重复的。比如brew下，默认就安装了xzmore命令。在conda下，这个命令也被默认安装。甚至还可以直接从source code直接安装xzmore命令在mac下。这样，xzmore就在系统里出现了三次。一个参考管理的方式是：
1. 凡是与*python*相关的软件，用`conda`来管理；
2. 凡是_non-python_的图形化软件，用`brew`来管理；
3. 其他的软件，看心情 :-)

Linux的哲学是，百花齐放。mac的基本思考是，apple帮你做好一切事，不要到处找不同的软件安装在一个系统里，相互干扰。这两种方式，各有优缺点。造成了mac下一堆软件管理方式。如果头脑不清楚，很容易混淆。幸好unix-like都可以指定运行路径，稍微减轻一点软件名称的混杂。但有时候还是会出现版本不统一，无法正常运行的结果。

# brew
## 常用命令
brew下最常用的命令无非几个：

Task | command
-------|-------
Install a package |  `brew install package_name`
Uninstall a package | `brew uninstall package_name`
Update |  `brew update && brew upgrade`
Diagnose | `brew doctor`
List all packages | `brew list`

# conda
## 常用命令
conda下最常用的命令如下：

Task | command
-------|-------
Install a package | `conda install package_name`
Update | `conda update --name environ_name package_name`
Update package manager | `conda update conda`
Uninstall a package | `conda remove package_name`
Search available packages | `conda search search_term`
Install package from specific source | `conda install --channel $URL package_name`
List installed packages | `conda list --name environ_name`
Create requirements file | `conda list --export`
Install other package manager | `conda install pip`
Install Python | `conda install python=x.x`
Update Python | `conda update python`

`-c channel_name`选项很有用，其中`-c conda-forge`里面包含了比默认channel更多的软件。

详细的conda command sheet 可以查阅：https://docs.conda.io/projects/conda/en/latest/_downloads/843d9e0198f2a193a3484886fa28163c/conda-cheatsheet.pdf 和 https://docs.conda.io/projects/conda/en/latest/commands.html


## 虚拟环境
conda下安装软件，会变得越来越复杂，最后简直成了一个操作系统。为了安装一个软件，有时不得不安装一大堆依赖的其他软件包和库。可是，这些库和软件有可能会与系统已有的软件冲突。为了避免这样的事情发生，conda设计了一种方法，在它之下生成一个新的环境，激活这个环境之后，所有的软件安装和执行都在这个环境之下。如果退出这个环境，在它之下安装的所有软件都不会被认识。相当于独立地安装软件和依赖包。当然，conda默认的环境是base，如果不特别制定，就处于base环境下。

创建和使用/退出环境的命令如下：

Task | command
-------|-------
Create an environment | `conda create --name environ_name python=x.x` [^pythonopt]
Activate an environment | `conda activate environ_name`
Deactivate an environment | `conda deactivate`
List all environments | `conda info --envs`
Remove an environment | `conda env remove -n env_name`

注意：**虚拟环境没有层级概念，除了基本的base环境外，所有的虚拟环境都是平级的，不能上下继承。**

## mamba
conda安装软件有个缺点：太慢。因为它是用python写的。因此有人用C++写了个新的安装包：mamba。它采用多线程方式下载和安装软件，速度大大提高。

可以用命令：`conda install mamba -n base -c conda-forge` 来安装mamba。此后，很多软件包的安装就用mamba来代替conda。例如：

`mamba install python=3.8 juyter -c conda-forge`

## pip
pip也是python的一种包管理方式，很多软件同时支持conda和pip。但有些软件就只有pip安装支持，可以直接用`pip install package_name`来安装。当然，conda和pip最大的区别就是：pip只负责python语言写的软件，conda其实是不依赖于特定语言的。

### pip 虚拟环境
pip本身也可以创建虚拟环境。其思想与conda的虚拟环境类似，就是在处理某一特定功能时，需要安装一些python功能包，但又不想把这些功能包与其它的系统混淆起来。因此，我们创建一个虚拟的pip环境，将这些功能包只装在该环境/目录中，包装起来。
命令为：`python3 -m venv $envname`
使用时先初始化该环境，`source $envname/bin/activate`。当然，第一次最好把pip升级一下：`$rnvname/bin/python3 -m pip install --upgrade pip`。接下来所有的pip安装命令（`pip3 install -U $appname`），就自动装到该虚拟环境/目录下了。

[^pythonopt]: 如果没有*python*这个关键词，则新创建的环境就是全空的。加上*python*之后，就会按照基本的python常用工具，比如*pip*、*xz*等；如果设置*python=x.x*，则表示按照特定的*python*版本。