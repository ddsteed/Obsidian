---
title: MAC下的brew和conda
date: 2022-02-07 15:38:21
tags: 技术
toc: true
mathjax: false
categories: 技术
---
## 基本概念
### mac
mac 指代一种 unix-like 的操作系统，以前常称之为 mac OSX，现在 Apple 公司为了统一旗下的操作系统，将电脑上的改为 macos。macos 的内核是借鉴 FreeBSD 以及 Mach 这两种系统的优点，混合而成的。在其之上，Apple 又增加了很多图形层以及其他应用层，形成了独特的 mac os。

### brew
虽然 macos 是 unix-like 的，但是很多 unix 的应用程序，尤其是 Linux 上的程序，并没有包含在 mac 里。而且，mac 的 unix-like 程序升级和管理都比较麻烦，不像 Ubuntu/Debian/Fedora 等，有自己的软件包管理系统。所以，有人设计了 brew 系统，模仿 Ubuntu 下的 apt 管理包，用以管理 mac 下的各种软件。

### conda
conda 是另一种软件包的管理方式，其来源于 python 下软件管理系统。随着 python 语言的流行，很多软件利用 python 语言来开发。因此 python 下为了管理这些软件，发展出了独特的 pip 和 conda 两套模式。这两种本来是独立的，但经常也混着用。因为 pip 出现得较早一点，前期用得较多。conda 设计更合理一些，但由于出现得较晚，有些软件包还没有纳入 conda 管理。所以，在某些情况下，安装某个大型软件，一会用 conda 安装，一会又用 pip 辅助一下。当然，最本质的区别还是：pip 仅仅用于 python 语言写的软件；conda 已经扩展到可以管理其他的软件包了。

### Conclusion
macos 是一个独立的操作系统，但是对于很多 unix-like 软件，没有较好的软件包管理。因此两种不同的软件包管理系统：brew 和 conda 就成为了 mac 上流行管理软件。

这三种生态下，都可以安装软件。因此，很多软件都是重复的。比如 brew 下，默认就安装了 xzmore 命令。在conda 下，这个命令也被默认安装。甚至还可以直接从 source code 直接安装 xzmore 命令在 mac 下。这样，xzmore 就在系统里出现了三次。一个参考管理的方式是：

1. 凡是与 *python* 相关的软件，用`conda`来管理；
2. 凡是 _non-python_ 的图形化软件，用`brew`来管理；
3. 其他的软件，看心情 :-)

Linux 的哲学是，百花齐放。mac 的基本思考是，apple 帮你做好一切事，不要到处找不同的软件安装在一个系统里，相互干扰。这两种方式，各有优缺点。造成了 mac 下一堆软件管理方式。如果头脑不清楚，很容易混淆。幸好 unix-like 都可以指定运行路径，稍微减轻一点软件名称的混杂。但有时候还是会出现版本不统一，无法正常运行的结果。

## brew
### 常用命令
brew 下最常用的命令无非几个：

Task | command
-------|-------
Install a package |  `brew install package_name`
Uninstall a package | `brew uninstall package_name`
Update |  `brew update && brew upgrade`
Diagnose | `brew doctor`
List all packages | `brew list`

## conda
### 常用命令
conda 下最常用的命令如下：

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


### 虚拟环境
conda 下安装软件，会变得越来越复杂，最后简直成了一个操作系统。为了安装一个软件，有时不得不安装一大堆依赖的其他软件包和库。可是，这些库和软件有可能会与系统已有的软件冲突。为了避免这样的事情发生，conda 设计了一种方法，在它之下生成一个新的环境，激活这个环境之后，所有的软件安装和执行都在这个环境之下。如果退出这个环境，在它之下安装的所有软件都不会被认识。相当于独立地安装软件和依赖包。当然，conda 默认的环境是 base，如果不特别制定，就处于 base 环境下。

创建和使用/退出环境的命令如下：

Task | command
-------|-------
Create an environment | `conda create --name environ_name python=x.x` [^pythonopt]
Activate an environment | `conda activate environ_name`
Deactivate an environment | `conda deactivate`
List all environments | `conda info --envs`
Remove an environment | `conda env remove -n env_name`

注意：**虚拟环境没有层级概念，除了基本的base环境外，所有的虚拟环境都是平级的，不能上下继承。**

### mamba
conda 安装软件有个缺点：太慢。因为它是用 python 写的。因此有人用 C++ 写了个新的安装包：mamba。它采用多线程方式下载和安装软件，速度大大提高。

可以用命令：`conda install mamba -n base -c conda-forge` 来安装 mamba。此后，很多软件包的安装就用 mamba 来代替 conda。例如：

`mamba install python=3.8 juyter -c conda-forge`

### pip
pip 也是 python 的一种包管理方式，很多软件同时支持 conda 和 pip。但有些软件就只有 pip 安装支持，可以直接用`pip install package_name`来安装。当然，conda 和 pip 最大的区别就是：pip 只负责 python 语言写的软件，conda 其实是不依赖于特定语言的。

#### pip 虚拟环境
pip 本身也可以创建虚拟环境。其思想与 conda 的虚拟环境类似，就是在处理某一特定功能时，需要安装一些 python 功能包，但又不想把这些功能包与其它的系统混淆起来。因此，我们创建一个虚拟的 pip 环境，将这些功能包只装在该环境/目录中，包装起来。
命令为：`python3 -m venv $envname`
使用时先初始化该环境，`source $envname/bin/activate`。当然，第一次最好把 pip 升级一下：`$rnvname/bin/python3 -m pip install --upgrade pip`。接下来所有的 pip 安装命令（`pip3 install -U $appname`），就自动装到该虚拟环境/目录下了。

[^pythonopt]: 如果没有*python*这个关键词，则新创建的环境就是全空的。加上*python*之后，就会按照基本的python常用工具，比如*pip*、*xz*等；如果设置*python=x.x*，则表示按照特定的*python*版本。