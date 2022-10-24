# Git入门笔记
## 一、Git简介
什么是“版本控制”？我为什么要关心它呢？ 版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。在 `GIT CODE` 中，我们对保存着软件源代码的文件作版本控制，但实际上，你可以对任何类型的文件进行版本控制。

如果你是位图形或网页设计师，可能会需要保存某一幅图片或页面布局文件的所有修订版本（这或许是你非常渴望拥有的功能），采用版本控制系统（`VCS`）是个明智的选择。 有了它你就可以将选定的文件回溯到之前的状态，甚至将整个项目都回退到过去某个时间点的状态，你可以比较文件的变化细节，查出最后是谁修改了哪个地方，从而找出导致怪异问题出现的原因，又是谁在何时报告了某个功能缺陷等等。 使用版本控制系统通常还意味着，就算你乱来一气把整个项目中的文件改的改删的删，你也照样可以轻松恢复到原先的样子。 但额外增加的工作量却微乎其微。

接下来，我们将要回顾版本控制系统的发展历史。版本控制系统发展可以分为三个阶段：
1. 本地版本控制系统
2. 集中式版本控制系统
3. 分布式版本控制系统

### 1.1 本地版本控制系统
许多人习惯用复制整个项目目录的方式来保存不同的版本，或许还会改名加上备份时间以示区别。 这么做唯一的好处就是简单，但是特别容易犯错。 有时候会混淆所在的工作目录，一不小心会写错文件或者覆盖意想外的文件。

为了解决这个问题，人们很久以前就开发了许多种本地版本控制系统，大多都是采用某种简单的数据库来记录文件的历次更新差异。
![RCS图示](https://img-blog.csdnimg.cn/0eaeab80e94a4bce99bb98c7a6f241a3.png "RCS图示") 
其中最流行的一种叫做 `RCS` ，现今许多计算机系统上都还看得到它的踪影。 `RCS` 的工作原理是在硬盘上保存补丁集（补丁是指文件修订前后的变化）；通过应用所有的补丁，可以重新计算出各个版本的文件内容。

### 1.2 集中式版本控制系统
接下来人们又遇到一个问题，如何让在不同系统上的开发者协同工作？ 于是，集中化的版本控制系统（`Centralized Version Control Systems`，简称 `CVCS`）应运而生。 这类系统，诸如 `CVS`、`Subversion` 以及 `Perforce` 等，都有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。多年以来，这已成为版本控制系统的标准做法。
![CVCS图示](https://img-blog.csdnimg.cn/3899fccca54d44009dcfb4414b00d2e8.png "CVCS图示")
这种做法带来了许多好处，特别是相较于老式的本地 `VCS`来说。 现在，每个人都可以在一定程度上看到项目中的其他人正在做些什么。 而管理员也可以轻松掌控每个开发者的权限，并且管理一个 `CVCS` 要远比在各个客户端上维护本地数据库来得轻松容易。

但这么做也有一个显而易见的缺点，那就是是中央服务器的单点故障。

如果宕机一小时，那么在这一小时内，谁都无法提交更新，也就无法协同工作
如果中心数据库所在的磁盘发生损坏，又没有做恰当备份，毫无疑问你将丢失所有数据——包括项目的整个变更历史，只剩下人们在各自机器上保留的单独快照。
本地版本控制系统也存在类似问题，只要整个项目的历史记录被保存在单一位置，就有丢失所有历史更新记录的风险。

### 1.3 分布式版本控制系统
于是分布式版本控制系统（`Distributed Version Control System`，简称 `DVCS`）面世了。 在这类系统中，像 `Git`、`Mercurial`、`Bazaar` 以及 `Darcs` 等，客户端并不只提取最新版本的文件快照， 而是把代码仓库完整地镜像下来，包括完整的历史记录。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。
![DVCS图示](https://img-blog.csdnimg.cn/c8a4a32ead834ee685f4ff014a94a37c.png "DVCS图示")
不仅如此，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。这样一来，你就可以在同一个项目中，分别和不同工作小组的人相互协作。 你可以根据需要设定不同的协作流程，比如层次模型式的工作流，而这在以前的集中式系统中是无法实现的。

### 1.4 Git 的诞生
#### 1.4.1 Git 诞生的背景

同生活中的许多伟大事物一样，`Git` 诞生于一个极富纷争大举创新的年代。

`Linus` 在`1991`年创建了开源的 `Linux`，从此，`Linux` 系统不断发展，已经成为最大的服务器系统软件了。在`1991`－`2002`年期间，世界各地的志愿者把源代码文件通过 `diff` 的方式发给 `Linus`，然后由 `Linus` 本人通过手工方式合并代码。

你也许会想，为什么 `Linus` 不把 `Linux` 代码放到版本控制系统里呢？不是有`CVS`、`SVN`这些免费的版本控制系统吗？因为`Linus` 坚定地反对`CVS`和`SVN`，这些集中式的版本控制系统不但速度慢，而且必须联网才能使用。有一些商用的版本控制系统，虽然比`CVS`、`SVN`好用，但那是付费的，和 `Linux` 的开源精神不符。

#### 1.4.2 Linus 两周完成 Git

到 `2002` 年，`Linux` 系统已经发展了十年了，代码库之大让 `Linus` 很难继续通过手工方式管理了，整个项目组开始启用一个专有的分布式版本控制系统 `BitKeeper` 来管理和维护代码，`BitKeeper` 的东家 `BitMover` 公司也免费授权 `Linux` 社区使用这个版本控制系统。后来 `BitMover` 公司发现社区有人试图破解 `BitKeeper` 的协议，于是 `BitMover` 公司收了回 `Linux` 社区的免费使用权。

这就迫使 `Linux` 开源社区（特别是 `Linux` 的缔造者 `Linus Torvalds`）基于使用 `BitKeeper` 时的经验教训，开发出自己的版本系统。 他们对新的系统制订了若干目标：
   - 速度
   - 简单的设计
   - 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）
   - 完全分布式
   - 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）
于是，`Linus` 花了两周时间自己用 `C` 写了一个分布式版本控制系统，这就是 `Git`！一个月之内，`Linux` 系统的源码已经由 `Git` 管理了！

#### 1.4.3 Git 的发展壮大

自 `2005` 年诞生以来，`Git` 日臻成熟完善，在高度易用的同时，仍然保留着初期设定的目标。它的速度飞快，极其适合管理大项目，有着令人难以置信的非线性分支管理系统。`Git` 迅速成为最流行的分布式版本控制系统，尤其是 `2008` 年，`GitHub` 网站上线了，它为开源项目免费提供 `Git` 存储，无数开源项目开始迁移至 `GitHub` ，包括`jQuery`，`PHP`，`Ruby`等等。

## 二、Git安装
在开始使用 `Git` 前，需要将它安装在你的计算机上。 即便已经安装，最好将它升级到最新的版本。 你可以通过软件包或者其它安装程序来安装，或者下载源码编译安装。

下面，将会介绍不同操作系统上 `Git` 的安装方法。
### 2.1 在 Windows 上安装
在 `Windows` 上安装 `Git` 的方法如下：

#### 2.1.1 使用官方版本安装

官方版本可以在 `Git` 官方网站下载。 打开 https://git-scm.com/download/win，然后选择相应的版本即可。

![Git下载页面](https://img-blog.csdnimg.cn/04ae0eba0734465691f84ce3aff70982.png "Git下载页面")
>要注意这是一个名为 `Git for Windows` 的项目（也叫做 `msysGit`），和 `Git` 是分别独立的项目；更多信息请访问 http://msysgit.github.io/。

下载完成后，按照提示进行安装即可。

#### 2.1.2 `Chocolatey` 自动安装

如果要进行自动安装，你可以使用 `Git Chocolatey` 包。 注意 `Chocolatey` 包是由社区维护的。

`Chocolatey` 官网：https://chocolatey.org

安装好 `Chocolatey` 后，执行如下命令即可：

    choco install git.install

### 2.2 在 macOS 上安装
在 `Mac` 上安装 `Git` 有多种方式。 最简单的方法是安装 `Xcode Command Line Tools`。 `Mavericks （10.9`） 或更高版本的系统中，在 `Terminal` 里尝试首次运行 `git` 命令即可。

    $ git --version
如果没有安装过命令行开发者工具，将会提示你安装。

如果你想安装更新的版本，可以使用二进制安装程序。 官方维护的 `macOS Git` 安装程序可以在 `Git` 官方网站下载，网址为 https://git-scm.com/download/mac。

### 2.3 在 Linux 上安装
在 `Linux` 上用二进制安装程序来安装基本的 `Git` 工具，可以使用发行版包含的基础软件包管理工具来安装。 以 `Fedora` 为例，如果你在使用它（或与之紧密相关的基于 `RPM` 的发行版，如 `RHEL` 或 `CentOS`），你可以使用 `dnf`：

    $ sudo dnf install git-all
如果你在基于 `Debian` 的发行版上，如 `Ubuntu`，请使用 `apt`：

    $ sudo apt install git-all
要了解更多选择，`Git` 官方网站上有在各种 `Unix` 发行版的系统上安装步骤，网址为 https://git-scm.com/download/linux。


### 2.4 Git环境配置
好了，当你当完成了 `Git` 的安装后，接下来我们就需要对 `Git` 进行一些必要的环境配置。

通常情况下，每台计算机上只需要配置一次 `Git`，当 `Git` 程序升级时会保留配置信息。 你可以在任何时候再次通过运行 `git config`命令来修改它们。

    git config
`Git` 自带一个 `git config` 的工具来帮助设置控制 `Git` 外观和行为的配置变量。

接下来，我们先学习如何通过 `git config` 命令来配置用户信息

#### 2.4.1 配置用户名和邮件地址
安装完 `Git` 之后，要做的第一件事就是设置你的用户名和邮件地址。 这一点很重要，因为每一个 `Git` 提交都会使用这些信息，它们会写入到你的每一次提交中，不可更改：

    $ git config --global user.name "李老师"
    $ git config --global user.email li@csdn.net
再次强调，如果使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， `Git` 都会使用那些信息。

>当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

#### 2.4.2 检查配置
如果想要检查你的配置，可以使用 `git config --list` 命令来列出所有 `Git` 当时能找到的配置。

    $ git config --list
    user.name= "李老师"
    user.email=li@csdn.net
    color.status=auto
    color.branch=auto
    color.interactive=auto
    color.diff=auto
    ...
你可能会看到重复的变量名，因为 `Git` 会从不同的文件中读取同一个配置（例如：`/etc/gitconfig` 与 `~/.gitconfig`）。 这种情况下，`Git` 会使用它找到的每一个变量的最后一个配置。

你可以通过输入 `git config <key>：` 来检查 `Git` 的某一项配置，例如：

    $ git config user.name
    李老师

#### 2.4.3 更多Git 配置
既然已经在系统上安装了 `Git`，你会想要做几件事来定制你的 `Git` 环境。 每台计算机上只需要配置一次，程序升级时会保留配置信息。 你可以在任何时候再次通过运行命令来修改它们。

`Git` 自带一个 `git config` 的工具来帮助设置控制 `Git` 外观和行为的配置变量。 这些变量存储在三个不同的位置：

- `/etc/gitconfig` 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 `git config` 时带上 `--system` 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）
- `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户。 你可以传递 `--global` 选项让 `Git` 读写此文件，这会对你系统上 所有 的仓库生效。
- 当前使用仓库的 `Git` 目录中的 `config` 文件（即 `.git/config`）：针对该仓库。 你可以传递 `--local` 选项让 `Git` 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 `Git` 仓库中才能让该选项生效。）

每一个级别会覆盖上一级别的配置，所以 `.git/config` 的配置变量会覆盖 `/etc/gitconfig` 中的配置变量。

在 `Windows` 系统中，`Git` 会查找 `$HOME` 目录下（一般情况下是 `C:\Users\$USER` ）的 `.gitconfig` 文件。 `Git` 同样也会寻找 `/etc/gitconfig` 文件，但只限于 `MSys` 的根目录下，即安装 `Git` 时所选的目标位置。 如果你在 `Windows` 上使用 `Git 2.x` 以后的版本，那么还有一个系统级的配置文件，`Windows XP` 上在 `C:\Documents and Settings\All Users\Application Data\Git\config` ，`Windows Vista` 及更新的版本在 `C:\ProgramData\Git\config` 。此文件只能以管理员权限通过 `git config -f <file>` 来修改。

你可以通过以下命令查看所有的配置以及它们所在的文件：

    $ git config --list --show-origin   

### 2.5 Git 颜色配置
到目前为止，我们已经配置了 `user.name` 和 `user.email` ，实际上，`Git` 还有很多可配置项。

比如，让 `Git` 显示颜色，会让命令输出看起来更醒目：

    $ git config --global color.ui true
#### Git 显示颜色
这样，Git 会适当地显示不同的颜色，比如使用`git status`命令后文件名就会标上颜色。
![](https://img-blog.csdnimg.cn/3caff6ef647041b187957666134c2c17.png)

### 2.6 Git忽略文件配置
有些时候，你必须把某些文件放到 `Git` 工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件等等，每次`git status`都会显示`Untracked files ...`，这种情况下，就可以实用忽略特殊文件 `.gitignore` 来很方便的解决这个问题。

首先我们在 `Git` 工作区的根目录下创建一个特殊的 `.gitignore`文件，然后把要忽略的文件名填进去，`Git` 在每次进行提交的时候就会自动忽略这些文件。

#### 2.6.1 忽略文件的规则

日常使用中，我们一般不需要从头开始编辑`.gitignore`文件，已经有各种现成的种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览 https://gitcode.net/codechina/gitignore

##### 忽略文件的原则是：
1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

让我们来看一个例子：

假设你在 `Windows` 下进行 `Python` 开发，`Windows` 会自动在有图片的目录下生成隐藏的缩略图文件，如果有自定义目录，目录下就会有`Desktop.ini`文件，因此你需要忽略 `Windows` 自动生成的垃圾文件：

然后，继续忽略Python编译产生的.pyc、.pyo、dist等文件或目录：

加上你自己定义的文件，最终得到一个完整的`.gitignore`文件，内容如下：

    # Windows:
    Thumbs.db
    ehthumbs.db
    Desktop.ini

    # Python:
    *.py[cod]
    *.so
    *.egg
    *.egg-info
    dist
    build

    # My configurations:
    db.ini
    deploy_key_rsa
最后一步就是把`.gitignore`也提交到 `Git`，就完成了！当然检验`.gitignore`的标准是`git status`命令是不是说`working directory clean`。

有些时候，你想添加一个文件到 `Git`，但发现添加不了，原因是这个文件被`.gitignore`忽略了：

#### 2.6.2 强制添加被忽略文件
    $ git add App.class
    The following paths are ignored by one of your .gitignore files:
    App.class
    Use -f if you really want to add them.
如果你确实想添加该文件，可以用`-f`强制添加到 `Git`：

    $ git add -f App.class

或者你发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

#### 2.6.3 检查忽略规则


    $ git check-ignore -v App.class
    .gitignore:3:*.class	App.class
`Git`会告诉我们，`.gitignore`的第`3`行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

还有些时候，当我们编写了规则排除了部分文件时：   

    # 排除所有.开头的隐藏文件:
    .*
    # 排除所有.class文件:
    *.class
但是我们发现`.*`这个规则把`.gitignore`也排除了，并且`App.class`需要被添加到版本库，但是被`*.class`规则排除了。

#### 2.6.4 添加例外规则
这个时候，虽然可以用`git add -f`强制添加进去，但我们建议你可以添加两条例外规则：

    # 排除所有.开头的隐藏文件:
    .*
    # 排除所有.class文件:
    *.class

    # 不排除.gitignore和App.class:
    !.gitignore
    !App.class
把指定文件排除在`.gitignore`规则外的写法就是`!+文件名`，所以，只需把例外文件添加进去即可。

### 2.7 Git 配置别名
除了通过 配置忽略文件 来提高`git commit` 时的便捷性外，`Git` 中还有一种可以让大家在敲入 `Git` 命令时偷懒的办法——那就是配置 `Git` 别名。

#### 配置 `git status`/commit/checkout/branch


比如在使用`git status`命令时，我们可以通过配置别名的方式将其配置为`git st`，这样在使用时是不是就比输入 `git status`简单方便很多呢？

我们只需要敲一行命令，告诉 `Git`，以后`st`就表示`status`：

    $ git config --global alias.st status
当然还有别的命令可以简写，很多人都用`co`表示`checkout`，`ci`表示`commit`，`br`表示`branch`：

#### 配置别名

    $ git config --global alias.co checkout
    $ git config --global alias.ci commit
    $ git config --global alias.br branch
配置完成以上别名后，以后提交就可以简写成：

    $ git ci -m "sth."
`--global`参数是全局参数，也就是这些命令在这台电脑的所有 `Git` 仓库下都可以使用。

### 2.8 配置 `git reset HEAD file`
再比如`git reset HEAD file命`令，他可以把暂存区的修改撤销掉（`unstage`），重新放回工作区。既然是一个`unstage`操作，就可以配置一个`unstage`别名：

    $ git config --global alias.unstage 'reset HEAD'
当你敲入命令：

    $ git unstage test.py
实际上 Git 执行的是：

    $ git reset HEAD test.py
### 2.9 配置 `git log` -1
配置一个`git last`，让其显示最后一次提交信息：

    $ git config --global alias.last 'log -1'
这样，用`git last`就能显示最近一次的提交：

    $ git last
    commit 4aac6c7ee018f24d2dabfd01a1e09c77612e1a4e (HEAD -> master)
    Author: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Tue Nov 17 11:14:15 2020 +0800

        branch test

### 2.10 配置 `git lg`
    git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
让我们来看看 `git lg` 配置好之后的效果：
![](https://img-blog.csdnimg.cn/c7d6ea843a7b4c349b10dfd6a5595da6.png)
以上这些就是 git 常用的一些配置了。

### 2.11 Git 配置文件
这些自定义的`Git`配置文件通常都存放在仓库的`.git/config`文件中：

#### 2.11.1 全局配置文件

    $ cat .git/config 
    [core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
    [remote "origin"]
        url = git@gitcode.net:codechina/learngit.git
        fetch = +refs/heads/*:refs/remotes/origin/*
    [branch "master"]
        remote = origin
        merge = refs/heads/master
    [alias]
        last = log -1
别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。

而当前用户的 `Git` 配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

#### 2.11.2 用户配置文件

    $ cat .gitconfig
    [alias]
        co = checkout
        ci = commit
        br = branch
        st = status
    [user]
        name = Your Name
        email = your@email.com
    [color]
        ui = true
配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

## 三、Git使用
在开始 `Git` 的基础命令学习之前，我们先来认识一下版本库——`Repository`，接下来我们所有提到的 `Git` 基础命令，都是基于版本库的。

那么什么是版本库呢？版本库又名仓库，英文名 `repository`，你可以简单理解成一个目录，这个目录里面的所有文件都可以被`Git`管理起来，每个文件的修改、删除，`Git`都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

接下来，我们首先看看 `Git` 版本库创建的相关命令。

### 3.1 在已存在目录中初始化仓库 —— `git init`
在已存在目录中创建一个版本库的过程非常简单：
首先，选择一个合适的地方，创建一个空目录：
#### 创建目录

    $ mkdir learning-git
    $ cd learning-git
    $ pwd
    /Users/xxm/learning-git
第二步，通过`git init`命令把这个目录变成 `Git` 可以管理的仓库：
#### 初始化仓库

    $ git init
    Initialized empty Git repository in /Users/xxm/learning-git/.git/
瞬间 `Git` 就把仓库建好了，而且告诉你是一个空的仓库（`empty Git repository`），同时在当前目录下多了一个`.git`的目录，这个目录是 `Git` 来跟踪管理版本库的，如果你没有看到 `.git` 目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看到了。

### 3.2 克隆现有的仓库 —— `git clone`
如果你想获得一份已经存在了的 `Git` 仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到 `git clone` 命令，`Git` 克隆的是该 `Git` 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要文件。
#### git clone
当你执行 `git clone` 命令的时候，默认配置下远程 `Git` 仓库中的每一个文件的每一个版本都将被拉取下来。

克隆仓库的命令是 `git clone <url>` 。 比如，要克隆 `Git` 的链接库 `libgit2`，可以用下面的命令：

    $ git clone https://gitcode.net/codechina/help-docs
这会在当前目录下创建一个名为 `help-docs` 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的 `help-docs` 文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。
#### 自定义本地仓库名称
当然如果你想在克隆远程仓库的时候，自定义本地仓库的名字也是可以的，你可以通过额外的参数指定新的目录名：

    $ git clone https://gitcode.net/codechina/help-docs mydocs
这会执行与上一条命令相同的操作，但目标目录名变为了 `mydocs`。

`Git` 支持多种数据传输协议。 上面的例子使用的是 `https://` 协议，不过你也可以使用 `git://` 协议或者使用 `SSH` 传输协议，比如 `user@server:path/to/repo.git` 。

### 3.3 编辑并添加文件
接下来，我们来尝试在已经准备好的 `Git` 仓库中编辑一个`readme.txt`文件，内容如下：

    Git is a version control system.
    Git is free software.
接下来，我们可以通过2个命令将刚创建好的`readme.txt`添加到`Git`仓库：

第一步，用命令`git add`告诉 `Git`，把文件添加到仓库：

    $ git add readme.txt
执行上面的命令，没有任何显示，说明添加成功。

### 3.4 提交变动到仓库
第二步，用命令`git commit`告诉 `Git`，把文件提交到仓库：

    $ git commit -m "wrote a readme file"
    [master (root-commit) 50ed06b] wrote a readme file
    1 file changed, 2 insertions(+)
    create mode 100644 readme.txt
这里简单解释一下`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

`git commit`命令执行成功后会告诉你：
- 1 file changed：1个文件被改动（我们新添加的readme.txt文件）
- 2 insertions：插入了两行内容（readme.txt有两行内容）   

为什么 `Git` 添加文件需要`add，commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件，比如：

    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files."

### 3.5 查看Git仓库当前状态变化
我们已经成功地添加并提交了一个`readme.txt`文件，接下来让我们继续修改`readme.txt`文件，改成如下内容：

    Git is a distributed version control system.
    Git is free software.
#### 查看 `git status` 结果
现在，运行`git status`命令看看结果：

    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")
`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.txt`被修改过了，但还没有准备提交的修改。

### 3.6 比较变动
虽然 `Git` 告诉我们`readme.txt`被修改了，但并没有告诉我们具体修改的内容是什么，假如刚好是上周修改的，等到周一来班时，已经记不清上次怎么修改的`readme.txt`，这个时候我们就需要用`git diff`这个命令查看相较于上一次暂存都修改了些什么内容了：
#### 运行 `git diff` 命令
    $ git diff readme.txt 
    diff --git a/readme.txt b/readme.txt
    index 46d49bf..9247db6 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
    -Git is a version control system.
    +Git is a distributed version control system.
    Git is free software.
    (END)
`git diff`顾名思义就是查看 `difference`，显示的格式正是 `Unix` 通用的 `diff` 格式，可以从上面的输出看到，我们在第一行添加了一个`distributed`单词。

### 3.7 综合操作
知道了对`readme.txt`作了什么修改后，再把它提交到仓库就放心多了，提交修改和提交新文件是一样的两步，第一步是`git add`：

    $ git add readme.txt
同样没有任何输出。在执行第二步`git commit`之前，我们再运行`git status`看看当前仓库的状态：

    $ git status
    On branch master
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        modified:   readme.txt
`git status`告诉我们，将要被提交的修改包括`readme.txt`，下一步，就可以放心地用`git commit`提交了：

    $ git commit -m "add distributed"
    [master e55063a] add distributed
    1 file changed, 1 insertion(+), 1 deletion(-)
提交后，我们再用`git status`命令看看仓库的当前状态：

    $ git status
    On branch master
    nothing to commit, working tree clean
`Git`告诉我们当前没有需要提交的修改，而且，工作目录是干净（`working tree clean`）的。

### 3.8 查看日志
如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

    $ git log --pretty=oneline
    e55063ad7f97dd979e4f94e12d2bc44a25a0fd55 (HEAD -> master) add distributed
    50ed06bd62fd34afbe501e6f2a4af73ccbe187f0 wrote a readme file
    (END)
每提交一个新版本，实际上 `Git` 就会把它们自动串成一条时间线。如果使用可视化工具或者之前在 `git` 自定义配置中介绍的 `git lg`命令，就可以更清楚地看到提交历史的时间线：

    $ git lg
    * e55063a - (HEAD -> master) add distributed (4 minutes ago) <Miykael_xxm>
    * 50ed06b - wrote a readme file (6 minutes ago) <Miykael_xxm>
    (END)
作为一个优秀的版本控制系统，`Git` 能够让我们查看每一次提交的记录。在日常的工作中，我们可以随时对 `Git` 仓库中的内容进行修改，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在 `Git`中 被称为`commit` / 提交。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个`commit`恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

在 `Git` 中，我们可以通过`git log`命令查看全部的`commit`记录：

    $ git log
    commit e55063ad7f97dd979e4f94e12d2bc44a25a0fd55 (HEAD -> master)
    Author: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Fri Nov 27 16:08:04 2020 +0800

        add distributed

    commit 50ed06bd62fd34afbe501e6f2a4af73ccbe187f0
    Author: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Fri Nov 27 16:06:11 2020 +0800

        wrote a readme file
    (END)
`git log`命令显示从最近到最远的提交日志，我们可以看到2次提交，最近的一次是`add distributed`，最早的一次是`wrote a readme file`。

### 3.9 Git 回退
这个时候，假设我们需要将 `readme.txt` 回退到上一个版本，也就是 `wrote a readme file` 的这个版本，我们需要怎么操作呢？

首先，`Git` 必须知道当前版本是哪个版本，在 `Git` 中，用`HEAD`表示当前版本，也就是最新的提交`e55063a`，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上`100`个版本写`100`个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本`add distributed`回退到上一个版本`wrote a readme file`，就可以使用`git reset`命令：

    $ git reset --hard HEAD^
    HEAD is now at 50ed06b wrote a readme file
现在让我们看看`readme.txt`的内容是不是版本`wrote a readme file`：

    $ cat readme.txt
    Git is a version control system.
    Git is free software.
果然还原到最初`wrote a readme file`这个版本了。

`Git` 的版本回退速度非常快，因为 `Git` 在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，`Git` 仅仅是把`HEAD`从指向`add distributed`：

#### `HEAD` 指针移动记录
    ┌────┐
    │HEAD│
    └────┘
    │
    └──> ○ add distributed
            │
            ○ wrote a readme file
改为指向`wrote a readme file`：

    ┌────┐
    │HEAD│
    └────┘
    │
    │    ○ add distributed
    │    │
    └──> ○ wrote a readme file
然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

### 3.10 Git 重置
现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

好在 `Git` 提供了一个命令`git reflog`用来记录你的每一次命令，当你用`git reset --hard HEAD^`回退到`wrote a readme file`版本时，再想恢复到`add distributed`，就可以通过`git reflog`命令找到`add distributed`的`commit id`。

    $ git reflog
    50ed06b (HEAD -> master) HEAD@{0}: reset: moving to HEAD~
    e55063a HEAD@{1}: reset: moving to HEAD
    e55063a HEAD@{2}: commit: add distributed
    50ed06b (HEAD -> master) HEAD@{3}: commit (initial): wrote a readme file
    ESC
从上面的输出可以看到，`add distributed`的`commit id`是`e55063a`，现在，我们就可以通过 `git reset --hard e55063a`切换到最新的版本上了。

### 3.11 工作区和暂存区
`Git` 和其他版本控制系统如 `SVN` 的一个不同之处就是有暂存区的概念。

#### 工作区（Working Directory）
就是你在电脑里能看到的目录，比如我的`learngit`文件夹就是一个工作区：
![](https://img-blog.csdnimg.cn/6f20c131183246d2a73ab3f501a0c034.png)
#### 版本库（Repository）
工作区有一个隐藏目录`.git`，这个不算工作区，而是 `Git` 的版本库。

`Git` 的版本库里存了很多东西，其中最重要的就是称为`stage`（或者叫`index`）的暂存区，还有 `Git` 为我们自动创建的第一个分支`master`，以及指向 `master` 的一个指针叫`HEAD`。
![](https://img-blog.csdnimg.cn/c3722fed6cd64a43a727649ad6e04eb9.png)
前面讲了我们把文件往 `Git` 版本库里添加的时候，是分两步执行的：

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。
因为我们创建 `Git` 版本库时，`Git`自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往 `master` 分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

现在，我们来试一下，先对`readme.txt`做个修改，比如加上一行内容：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
然后，在工作区新增一个`LICENSE`文本文件。

先用`git status`查看一下状态：

    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

    Untracked files:
    (use "git add <file>..." to include in what will be committed)

        LICENSE

    no changes added to commit (use "git add" and/or "git commit -a")
`Git`非常清楚地告诉我们，`readme.txt`被修改了，而`LICENSE`还从来没有被添加过，所以它的状态是`Untracked`。

现在，使用两次命令`git add`，把`readme.txt`和`LICENSE`都添加后，用`git status`再查看一下：

    $ git status
    On branch master
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        new file:   LICENSE
        modified:   readme.txt
现在，暂存区的状态就变成这样了：
![](https://img-blog.csdnimg.cn/8f8ef5d863d44a4d8ea577dd6d617758.png)
所以，`git add`命令实际上就是把要提交的所有修改放到暂存区（`Stage`），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

    $ git commit -m "understand how stage works"
    [master 599dbdb] understand how stage works
    2 files changed, 2 insertions(+)
    create mode 100644 LICENSE
一旦提交后，如果你又没有对工作区做任何修改，那么工作区就是“干净”的：

    $ git status
    On branch master
    nothing to commit, working tree clean
现在版本库变成了这样，暂存区就没有任何内容了：
![](https://img-blog.csdnimg.cn/4d0318ec49894887b20464c10e93da52.png)

### 3.12 小结
`git`仓库部分的常用基础的命令
- `git init`
- `git clone`
- `git add`
- `git commit`
- `git status`
- `git diff`
- `git log`
- `git reset`
- `git reflog`

## 四、版本管理
几乎所有的版本控制系统都以某种形式支持分支。 使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。 在很多版本控制系统中，这是一个略微低效的过程——常常需要完全创建一个源代码目录的副本。对于大项目来说，这样的过程会耗费很多时间。

有人把 `Git` 的分支模型称为它的“必杀技特性”，也正因为这一特性，使得 `Git` 从众多版本控制系统中脱颖而出。 为何 `Git` 的分支模型如此出众呢？ `Git` 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，并且在不同分支之间的切换操作也是一样便捷。 与许多其它版本控制系统不同，`Git` 鼓励在工作流程中频繁地使用分支与合并，哪怕一天之内进行许多次。 理解和精通这一特性，你便会意识到 `Git` 是如此的强大而又独特，并且从此真正改变你的开发方式。

### 4.1 撤消修改(git commit --amend)
在任何一个阶段，你都有可能想要撤消某些操作。接下来，我们将会学习几个撤消你所做修改的基本命令。 注意，有些撤消操作是不可逆的。 这是在使用 `Git` 的过程中，会因为操作失误而导致之前的工作丢失的少有的几个地方之一。

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令来重新提交：

    $ git commit --amend
这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令）， 那么快照会保持不变，而你所修改的只是提交信息。

文本编辑器启动后，可以看到之前的提交信息。 编辑后保存会覆盖原来的提交信息。

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

    $ git commit -m 'initial commit'
    $ git add forgotten_file
    $ git commit --amend
最终你只会有一个提交——第二次提交将代替第一次提交的结果。

>当你在修补最后的提交时，并不是通过用改进后的提交 原位替换 掉旧有提交的方式来修复的， 理解这一点非常重要。从效果上来说，就像是旧有的提交从未存在过一样，它并不会出现在仓库的历史中。
>
>修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或者 “小修补，修正笔误”这种提交信息弄乱你的仓库历史。

### 4.2 取消暂存的文件(git reset)
接下来我们看看如何操作暂存区和工作目录中已修改的文件。 这些命令在修改文件状态的同时，也会提示如何撤消操作。例如，你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入 `git add *` 暂存了它们两个。如何只取消暂存两个中的一个呢？ `git status` 命令提示了你：

    $ git add *
    $ git status
    On branch master
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        renamed:    LICENSE -> LICENSE.md
        modified:   readme.txt
在 `“Changes to be committed”` 文字正下方，提示使用 `git reset HEAD <file>...` 来取消暂存。 所以，我们可以这样来取消暂存 `readme.txt` 文件：
#### `git reset HEAD`

    $ git reset HEAD readme.txt
    Unstaged changes after reset:
    M	readme.txt

    $ git status
    On branch master
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        renamed:    LICENSE -> LICENSE.md

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt
这个命令有点儿奇怪，但是起作用了。 `readme.txt` 文件已经是修改未暂存的状态了。

>`git reset` 确实是个危险的命令，如果加上了 `--hard` 选项则更是如此。 然而在上述场景中，工作目录中的文件尚未修改，因此相对安全一些。

### 4.3 撤消对文件的修改(git --checkout)
如果你并不想保留对 `readme.txt` 文件的修改怎么办？ 你该如何方便地撤消修改——将它还原成上次提交时的样子（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）？ 幸运的是，`git status` 也告诉了你应该如何做。 在最后一个例子中，未暂存区域是这样：

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt
它非常清楚地告诉了你如何撤消之前所做的修改。 让我们来按照提示执行：
#### `git checkout – file`

    $ git checkout -- readme.txt
    $ git status
    On branch master
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        renamed:    LICENSE -> LICENSE.md
可以看到那些修改已经被撤消了。

>请务必记得 `git checkout -- <file>` 是一个危险的命令。 你对那个文件在本地的任何修改都会消失——`Git` 会用最近提交的版本覆盖掉它。 除非你确实清楚不想要对那个文件的本地修改了，否则请不要使用这个命令。

### 4.4 删除文件
在 `Git` 中，删除也是一个修改操作，我们先添加一个新文件`test.txt`到 `Git` 并且提交：

    $ git add test.txt

    $ git commit -m "add test.txt"
    [master c67077f] add test.txt
    1 file changed, 1 insertion(+)
    create mode 100644 test.txt
一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：

    $ rm test.txt
这个时候，`Git`知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：

    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add/rm <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

    no changes added to commit (use "git add" and/or "git commit -a")
现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

    $ git rm test.txt
    rm 'test.txt'

    $ git commit -m "remove test.txt"
    [master 5c7e5ea] remove test.txt
    1 file changed, 1 deletion(-)
    delete mode 100644 test.txt
现在，文件就从版本库中被删除了。

>小提示：先手动删除文件，然后使用`git rm` 和`git add`效果是一样的。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

    $ git checkout -- test.txt
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

>注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！

### 4.5 小结
- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`
- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作
- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，可以用命令`git reset --hard commit_id`，不过前提是没有推送到远程库

## 五、分支管理
### 5.1 git 分支
几乎所有的版本控制系统都以某种形式支持分支。 使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。 在很多版本控制系统中，这是一个略微低效的过程——常常需要完全创建一个源代码目录的副本。对于大项目来说，这样的过程会耗费很多时间。

有人把 `Git` 的分支模型称为它的“必杀技特性”，也正因为这一特性，使得 `Git` 从众多版本控制系统中脱颖而出。 为何 `Git` 的分支模型如此出众呢？ `Git` 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，并且在不同分支之间的切换操作也是一样便捷。 与许多其它版本控制系统不同，`Git` 鼓励在工作流程中频繁地使用分支与合并，哪怕一天之内进行许多次。 理解和精通这一特性，你便会意识到 `Git` 是如此的强大而又独特，并且从此真正改变你的开发方式。

### 5.2 git 分支简介
为了真正理解 `Git` 处理分支的方式，我们需要回顾一下 `Git` 是如何保存数据的。

前面我们了解到，`Git` 保存的不是文件的变化或者差异，而是一系列不同时刻的 快照 。

在进行提交操作时，`Git` 会保存一个提交对象（`commit object`）。 知道了 `Git` 保存数据的方式，我们可以很自然的想到——该提交对象会包含一个指向暂存内容快照的指针。 但不仅仅是这样，该提交对象还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。 首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象，

为了更加形象地说明，我们假设现在有一个工作目录，里面包含了三个将要被暂存和提交的文件。 暂存操作会为每一个文件计算校验和，然后会把当前版本的文件快照保存到 `Git` 仓库中 （`Git` 使用 `blob` 对象来保存它们），最终将校验和加入到暂存区域等待提交：

    $ git add readme.txt test.md LICENSE
    $ git commit -m 'The initial commit of my project'
当使用 `git commit` 进行提交操作时，`Git` 会先计算每一个子目录（本例中只有项目根目录）的校验和， 然后在 `Git` 仓库中这些校验和保存为树对象。随后，`Git` 便会创建一个提交对象， 它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。 如此一来，`Git` 就可以在需要的时候重现此次保存的快照。

现在，`Git` 仓库中有五个对象：三个 `blob` 对象（保存着文件快照）、一个 `树` 对象 （记录着目录结构和 `blob` 对象索引）以及一个 `提交` 对象（包含着指向前述树对象的指针和所有提交信息）。
![首次提交对象及其树结构](https://img-blog.csdnimg.cn/07a1525251924ad7b60caa73c4c66a76.png "首次提交对象及其树结构")

做些修改后再次提交，那么这次产生的提交对象会包含一个指向上次提交对象（父对象）的指针。
![提交对象及其父对象](https://img-blog.csdnimg.cn/26b5ded9e92649029c0a59ffee2b7131.png "提交对象及其父对象")

`Git` 的分支，其实本质上仅仅是指向提交对象的可变指针。 `Git` 的默认分支名字是 `master`。 在多次提交操作之后，你其实已经有一个指向最后那个提交对象的 `master` 分支。 `master` 分支会在每次提交时自动向前移动。
![分支及其提交历史](https://img-blog.csdnimg.cn/348189f202e94735b030aede0f6651af.png "分支及其提交历史")

### 5.3 创建分支
#### 5.3.1 git分支详解
`Git` 会把仓库中的每次提交串成一条时间线，这条时间线就是一个分支。在 `Git` 里，每个仓库都会有一个主分支，即`master`分支。`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

一开始的时候，`master`分支是一条线，`Git` 用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点：
![](https://img-blog.csdnimg.cn/c7d991e368664165a1b1ee86dc3759bc.png)

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。
当我们创建新的分支，例如`dev`时，`Git` 新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：
![](https://img-blog.csdnimg.cn/604bdf8522e549be8725a094a364bc59.png)

你看，`Git` 创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！
不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变：
![](https://img-blog.csdnimg.cn/5dd32268dcd7483c83424c6b048b4fb9.png)

假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。`Git` 怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：
![](https://img-blog.csdnimg.cn/2b007c88dcea4e42bd0a83510574899d.png)

所以 `Git` 合并分支也很快！就改改指针，工作区内容也不变！
合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：
![](https://img-blog.csdnimg.cn/364f888d27484685846edc3214362f39.png)
真是太神奇了，你看得出来有些提交是通过分支完成的吗？

#### 5.3.2 下面开始实战。
首先，我们创建`dev`分支，然后切换到`dev`分支：

    $ git checkout -b dev
    Switched to a new branch 'dev'
`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

    $ git branch dev
    $ git checkout dev
    Switched to branch 'dev'
然后，用`git branch`命令查看当前分支：

    $ git branch
    * dev
    master
`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

然后，我们就可以在`dev`分支上正常提交，比如对`readme.txt`做个修改，加上一行：

    Creating a new branch is quick.
然后提交：

    $ git add readme.txt 
    $ git commit -m "branch test"
    [dev 4aac6c7] branch test
    1 file changed, 1 insertion(+)
现在，`dev`分支的工作完成，我们就可以切换回`master`分支：

    $ git checkout master
    Switched to branch 'master'
切换回`master`分支后，再查看一个`readme.txt`文件，刚才添加的内容不见了！因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变：
![](https://img-blog.csdnimg.cn/b1455daa21824b2bbfb25c62a768a5e6.png)

现在，我们把`dev`分支的工作成果合并到`master`分支上：

    $ git merge dev
    Updating 599dbdb..4aac6c7
    Fast-forward
    readme.txt | 1 +
    1 file changed, 1 insertion(+)
`git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

注意到上面的`Fast-forward`信息，`Git` 告诉我们，这次合并是“`快进模式`”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能`Fast-forward`，我们后面会讲其他方式的合并。

合并完成后，就可以放心地删除`dev`分支了：

    $ git branch -d dev
    Deleted branch dev (was 4aac6c7).
删除后，查看`branch`，就只剩下`master`分支了：

    $ git branch
    * master
因为创建、合并和删除分支非常快，所以 `Git` 鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

#### 5.3.3 git switch
我们注意到切换分支使用`git checkout <branch>`，而 `Git` 中撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。
实际上，切换分支这个动作，用`switch`更科学。因此，最新版本的 `Git` 提供了新的`git switch`命令来切换分支：

创建并切换到新的`dev`分支，可以使用：

    $ git switch -c dev
直接切换到已有的master分支，可以使用：

    $ git switch master
使用新的`git switch`命令，比`git checkout`要更容易理解。

### 5.4 分支管理策略
通常，合并分支时，如果可能，`Git`会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，`Git` 就会在`merge`时生成一个新的`commit`，这样，从分支历史上就可以看出分支信息。

下面我们实战一下`--no-ff`方式的`git merge`：

首先，仍然创建并切换`dev`分支：
```shell
$ git switch -c dev
Switched to a new branch 'dev'
```
修改`readme.txt`文件，并提交一个新的`commit`：
```shell
$ git add readme.txt 
$ git commit -m "add merge"
[dev f52c633] add merge
1 file changed, 1 insertion(+)
```
现在，我们切换回`master`：
```shell
$ git switch master
Switched to branch 'master'
```
准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：
```shell
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
readme.txt | 1 +
1 file changed, 1 insertion(+)
```
因为本次合并要创建一个新的`commit`，所以加上`-m`参数，把`commit`描述写进去。

合并后，我们用`git log`看看分支历史：
```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   fc76cf7 (HEAD -> master) merge with no-ff
|\  
| * f52c633 (dev) add merge
|/  
*   cf810e4 conflict fixed
...
```
可以看到，不使用`Fast forward`模式，`merge`后就像这样：
![](https://img-service.csdnimg.cn/img_convert/3a8a514884a94bf4a616a560e955704b.png)



### 5.5 分支策略
在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

其次，干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如`1.0`版本发布时，再把`dev`分支合并到`master`上，并在`master`分支发布`1.0`版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：
![git-br-policy](https://img-service.csdnimg.cn/img_convert/6149d0c8f1b641cf9f036d497a548b78.png)
在日常的开发工作中，除了以上介绍的 `master` 和 `dev` 两个常用分支外，我们还会有其他类型的分支使用策略：

#### 5.5.1 bug分支
在开发过程中，`bug` 就像家常便饭一样。有了 `bug` 就需要修复，在 `Git` 中，由于分支是如此的强大，所以，每个 `bug` 都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号`101`的 `bug` 的任务时，很自然地，你想创建一个分支 `issue-101` 来修复它，但是，等等，当前正在`dev`上进行的工作还没有提交：

    $ git status
    On branch dev
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        new file:   hello.py

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt
并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该 `bug`，怎么办？

幸好，`Git`还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

    $ git stash
    Saved working directory and index state WIP on dev: f52c633 add merge
现在，用`git status`查看工作区，就是干净的（除非有没有被`Git`管理的文件），因此可以放心地创建分支来修复 `bug`。

首先确定要在哪个分支上修复 `bug`，假定需要在`master`分支上修复，就从`master`创建临时分支：

    $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 6 commits.
    (use "git push" to publish your local commits)

    $ git checkout -b issue-101
    Switched to a new branch 'issue-101'
现在修复`bug`，需要把“`Git is free software …`”改为“`Git is a free software …`”，然后提交：

    $ git add readme.txt 
    $ git commit -m "fix bug 101"
    [issue-101 8842ff5] fix bug 101
    1 file changed, 1 insertion(+), 1 deletion(-)
修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支：

    $ git switch master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 6 commits.
    (use "git push" to publish your local commits)

    $ git merge --no-ff -m "merged bug fix 101" issue-101
    Merge made by the 'recursive' strategy.
    readme.txt | 2 +-
    1 file changed, 1 insertion(+), 1 deletion(-)
太棒了，原计划两个小时的 `bug` 修复只花了5分钟！现在，是时候接着回到`dev`分支干活了！

    $ git switch dev
    Switched to branch 'dev'

    $ git status
    On branch dev
    nothing to commit, working tree clean
工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：

    $ git stash list
    stash@{0}: WIP on dev: f52c633 add merge
工作现场还在，`Git` 把`stash`内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，`stash`内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把`stash`内容也删了：

    $ git stash pop
    On branch dev
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        new file:   hello.py

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

    Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
再用`git stash list`查看，就看不到任何`stash`内容了：

    $ git stash list
你可以多次`stash`，恢复的时候，先用`git stash list`查看，然后恢复指定的`stash`，用命令：

    $ git stash apply stash@{0}
在`master`分支上修复了`bug`后，我们要想一想，`dev`分支是早期从`master`分支分出来的，所以，这个`bug`其实在当前`dev`分支上也存在。

那怎么在`dev`分支上修复同样的`bug`？重复操作一次，提交不就行了？在 `Git` 中还有比这更简单的方法可以实现。

同样的 `bug`，要在`dev`上修复，我们只需要把`8842ff5 fix bug 101`这个提交所做的修改“复制”到`dev`分支。注意：我们只想复制`8842ff5 fix bug 101`这个提交所做的修改，并不是把整个`master`分支`merge`过来。

为了方便操作，`Git` 专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：

    $ git branch
    * dev
    master
    $ git cherry-pick 8842ff5
    [dev 0944c8c] fix bug 101
    1 file changed, 1 insertion(+), 1 deletion(-)
`Git` 自动给`dev`分支做了一次提交，注意这次提交的`commit`是`0944c8c`，它并不同于`master`的`8842ff5`，因为这两个`commit`只是改动相同，但确实是两个不同的`commit`。用`git cherry-pick`，我们就不需要在`dev`分支上手动再把修 `bug` 的过程重复一遍。

有些聪明的童鞋会想了，既然可以在`master`分支上修复`bug`后，在`dev`分支上可以“重放”这个修复过程，那么直接在`dev`分支上修复 `bug`，然后在`master`分支上“重放”行不行？当然可以，不过你仍然需要`git stash`命令保存现场，才能从`dev`分支切换到`master`分支。

#### 5.5.2 feature分支
在开发过程中，除了 `bug` 外，也还会有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个`feature`分支，在上面开发，完成后，合并，最后，删除该`feature`分支。

现在，你终于接到了一个新任务：开发代号为`Vulcan`的新功能，该功能计划用于下一代星际飞船。

于是准备开发：

    $ git switch -c feature-vulcan
    Switched to a new branch 'feature-vulcan'
5分钟后，开发完毕：

    $ git add vulcan.md

    $ git status
    On branch feature-vulcan
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        new file:   vulcan.md

    $ git commit -m "add feature vulcan"
    [feature-vulcan d12cf23] add feature vulcan
    1 file changed, 3 insertions(+)
    create mode 100644 vulcan.md
切回`dev`，准备合并：

    $ git switch dev
一切顺利的话，`feature`分支和`bug`分支是类似的，合并，然后删除。

但是！就在此时，接到上级命令，因经费不足，新功能必须取消！虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

    $ git branch -d feature-vulcan
    error: The branch 'feature-vulcan' is not fully merged.
    If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
销毁失败。`Git` 友情提醒，`feature-vulcan`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。。

现在我们强行删除：

    $ git branch -D feature-vulcan
    Deleted branch feature-vulcan (was d12cf23).
终于删除成功！

### 5.6 Git 解决冲突
#### 5.6.1 解决冲突
人生不如意之事十之八九，合并分支往往也不是一帆风顺的。

准备新的`feature1`分支，继续我们的新分支开发：

    $ git switch -c feature1
    Switched to a new branch 'feature1'
修改`readme.txt`最后一行，改为：

    Creating a new branch is quick AND simple.
在`feature1`分支上提交：

    $ git add readme.txt
    $ git commit -m "AND simple"
    [feature1 2443c95] AND simple
    1 file changed, 1 insertion(+), 1 deletion(-)
切换到`master`分支：

    $ git switch master
    Switched to branch 'master'
在`master`分支上把`readme.txt`文件的最后一行改为：

    Creating a new branch is quick & simple.
提交：

    $ git add readme.txt 
    $ git commit -m "& simple"
    [master 06e5e3a] & simple
    1 file changed, 1 insertion(+), 1 deletion(-)
现在，`master`分支和`feature1`分支各自都分别有新的提交，变成了这样：
![](https://img-blog.csdnimg.cn/b62d8c46a2e643f5851dda6c75bd342c.png)
这种情况下，`Git` 无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：

    $ git merge feature1
    Auto-merging readme.txt
    CONFLICT (content): Merge conflict in readme.txt
    Automatic merge failed; fix conflicts and then commit the result.
果然冲突了！`Git` 告诉我们，`readme.txt`文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：

    $ git status
    On branch master
    You have unmerged paths.
    (fix conflicts and run "git commit")
    (use "git merge --abort" to abort the merge)

    Unmerged paths:
    (use "git add <file>..." to mark resolution)
        both modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")
我们可以直接查看`readme.txt`的内容：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes of files.
    <<<<<<< HEAD
    Creating a new branch is quick & simple.
    =======
    Creating a new branch is quick AND simple.
    >>>>>>> feature1
`Git` 用`<<<<<<<，=======，>>>>>>>`标记出不同分支的内容，我们修改如下后保存：

    Creating a new branch is quick and simple.
再提交：

    $ git add readme.txt 
    $ git commit -m "conflict fixed"
    [master fb8b190] conflict fixed
现在，`master`分支和`feature1`分支变成了下图所示：
![](https://img-blog.csdnimg.cn/1aaa80987fec4e6295cfacf53e9871ff.png)
用带参数的`git log`也可以看到分支的合并情况：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   fb8b190 (HEAD -> master) conflict fixed
    |\
    | * 2443c95 (feature1) add simple
    * | 06e5e3a & simple
    |/
    * 4aac6c7 branch test
    * 599dbdb understand how stage works
    * 1985ccf append GPL
    * 27f9df6 add distributed
    * e372504 wrote a readme file
最后，删除`feature1`分支：

    $ git branch -d feature1
    Deleted branch feature1 (was 2443c95).
工作完成。

#### 5.6.2 git cherry-pick
对于多分支的代码库，将代码从一个分支转移到另一个分支是常见需求。

这时分两种情况。一种情况是，你需要另一个分支的所有代码变动，那么就采用合并`git merge`。另一种情况是，你只需要部分代码变动（某几个提交），这时可以采用 `Cherry pick`。

##### 5.6.2.1 git cherry-pick 基本用法
`git cherry-pick`命令的作用，就是将指定的提交`commit`应用于其他分支。

    $ git cherry-pick <commitHash>
上面命令就会将指定的提交`commitHash`，应用于当前分支。这会在当前分支产生一个新的提交，当然它们的哈希值会不一样。

举例来说，代码仓库有`master`和`feature`两个分支。

    a - b - c - d   Master
         \
           e - f - g Feature
现在将提交`f`应用到`master`分支。

    # 切换到 master 分支
    $ git checkout master

    # Cherry pick 操作
    $ git cherry-pick f
上面的操作完成以后，代码库就变成了下面的样子。

    a - b - c - d - f   Master
         \
           e - f - g Feature
从上面可以看到，`master`分支的末尾增加了一个提交`f`。

`git cherry-pick`命令的参数，不一定是提交的哈希值，分支名也是可以的，表示转移该分支的最新提交。

    $ git cherry-pick feature
上面代码表示将`feature`分支的最近一次提交，转移到当前分支。

##### 5.6.2.2 git cherry-pick 转移多个提交
`Cherry pick` 支持一次转移多个提交。

    $ git cherry-pick <HashA> <HashB>
上面的命令将 `A` 和 `B` 两个提交应用到当前分支。这会在当前分支生成两个对应的新提交。

如果想要转移一系列的连续提交，可以使用下面的简便语法。

    $ git cherry-pick A..B 
上面的命令可以转移从 `A` 到 `B` 的所有提交。它们必须按照正确的顺序放置：提交 `A` 必须早于提交 `B`，否则命令将失败，但不会报错。

注意，使用上面的命令，提交 `A` 将不会包含在 `Cherry pick` 中。如果要包含提交 `A`，可以使用下面的语法。

    $ git cherry-pick A^..B 

##### 5.6.2.3 git cherry-pick 配置项
`git cherry-pick`命令的常用配置项如下。
- `-e，--edit`: 打开外部编辑器，编辑提交信息。
- `-n，--no-commit`: 只更新工作区和暂存区，不产生新的提交。
- `-x`: 在提交信息的末尾追加一行`cherry picked from commit ...`，方便以后查到这个提交是如何产生的。
- `-s，--signoff`: 在提交信息的末尾追加一行操作者的签名，表示是谁进行了这个操作。
- `-m parent-number，--mainline parent-number`: 如果原始提交是一个合并节点，来自于两个分支的合并，那么 `Cherry pick` 默认将失败，因为它不知道应该采用哪个分支的代码变动。
- `-m`配置项告诉 `Git`，应该采用哪个分支的变动。它的参数`parent-number`是一个从`1`开始的整数，代表原始提交的父分支编号。`git cherry-pick -m 1 <commitHash>`表示，`Cherry pick` 采用提交`commitHash`来自编号`1`的父分支的变动。一般来说，`1`号父分支是接受变动的分支，`2`号父分支是作为变动来源的分支。

##### 5.6.2.4 git cherry-pick 代码冲突
如果操作过程中发生代码冲突，`Cherry pick` 会停下来，让用户决定如何继续操作。
###### （1）–continue
用户解决代码冲突后，第一步将修改的文件重新加入暂存区（`git add .`），第二步使用下面的命令，让 `Cherry pick` 过程继续执行。

    $ git cherry-pick --continue
###### （2）–abort
发生代码冲突后，放弃合并，回到操作前的样子。
###### （3）–quit
发生代码冲突后，退出 `Cherry pick`，但是不回到操作前的样子。

##### 5.6.2.5 git cherry-pick 转移到另一个代码库
`Cherry pick` 也支持转移另一个代码库的提交，方法是先将该库加为远程仓库。

    $ git remote add target git://gitUrl
上面命令添加了一个远程仓库`target`。

然后，将远程代码抓取到本地。

    $ git fetch target
上面命令将远程代码仓库抓取到本地。

接着，检查一下要从远程仓库转移的提交，获取它的哈希值。

    $ git log target/master
最后，使用`git cherry-pick`命令转移提交。

    $ git cherry-pick <commitHash>

### 5.7 Git 多人协作
#### 5.7.1 查看远程分支
当你从远程仓库克隆时，实际上 `Git` 自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。

要查看远程库的信息，用`git remote`：

    $ git remote
    origin
或者，用`git remote -v`显示更详细的信息：

    $ git remote -v
    origin  git@github.com:michaelliao/learngit.git (fetch)
    origin  git@github.com:michaelliao/learngit.git (push)
上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到`push`的地址。

#### 5.7.2 推送分支
推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，`Git` 就会把该分支推送到远程库对应的远程分支上：

    $ git push origin master
如果要推送其他分支，比如`dev`，就改成：

    $ git push origin dev
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？
- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- `bug`分支只用于在本地修复`bug`，就没必要推到远程了；
- `feature`分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
  
总之，就是在 `Git` 中，分支完全可以在本地，你可以根据分支的需求来决定是否需要推送到远程！

#### 5.7.3 抓取分支
多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把`SSH Key`添加到 `GIT CODE`）或者同一台电脑的另一个目录下克隆：

    $ git clone git@gitcode.net:codechina/learngit.git
    Cloning into 'learngit'...
    remote: Counting objects: 40, done.
    remote: Compressing objects: 100% (21/21), done.
    remote: Total 40 (delta 14), reused 40 (delta 14), pack-reused 0
    Receiving objects: 100% (40/40), done.
    Resolving deltas: 100% (14/14), done.
当你的小伙伴从远程库`clone`时，默认情况下，你的小伙伴只能看到本地的`master`分支。不信可以用`git branch`命令看看：

    $ git branch
    * master
现在，你的小伙伴要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

    $ git checkout -b dev origin/dev
现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程：

    $ git add env.txt

    $ git commit -m "add env"
    [dev 34bde7f] add env
    1 file changed, 1 insertion(+)
    create mode 100644 env.txt

    $ git push origin dev
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 332 bytes | 332.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    remote:
    remote: To create a merge request for dev, visit:
    remote:   https://gitcode.net/xiongjiamu/learing/-/merge_requests/new?merge_request%5Bsource_branch%5D=dev
    remote:
    To gitcode.net:xiongjiamu/learing.git
你的小伙伴已经向`origin/dev`分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

    $ cat env.txt
    env

    $ git add env.txt

    $ git commit -m "Update env.txt"
    [dev d7e68661] Update env.txt
    1 file changed, 1 insertion(+)
    create mode 100644 env.txt

    $ git push origin dev
    To github.com:michaelliao/learngit.git
    ! [rejected]        dev -> dev (non-fast-forward)
    error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Integrate the remote changes (e.g.
    hint: 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.
推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，`Git` 已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送：

    $ git pull
    There is no tracking information for the current branch.
    Please specify which branch you want to merge with.
    See git-pull(1) for details.

        git pull <remote> <branch>

    If you wish to set tracking information for this branch you can do so with:

        git branch --set-upstream-to=origin/<branch> dev
`git pull`也失败了，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

    $ git branch --set-upstream-to=origin/dev dev
    Branch 'dev' set up to track remote branch 'dev' from 'origin'.
再`pull`：

    $ git pull
    Auto-merging env.txt
    CONFLICT (add/add): Merge conflict in env.txt
    Automatic merge failed; fix conflicts and then commit the result.
这回`git pull`成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再`push`：

$ git commit -m "fix env conflict"
[dev ad728db] fix env conflict

    $ git push origin dev
    Counting objects: 6, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (6/6), 621 bytes | 621.00 KiB/s, done.
    Total 6 (delta 0), reused 0 (delta 0)
    To github.com:michaelliao/learngit.git
    d7e6866..ad728db  dev -> dev

#### 5.7.4 多人协作小结
多人协作的工作模式通常是这样：
1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

### 5.8 git rebase
发布一个版本时，我们通常先在版本库中打一个标签（`tag`），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

`Git` 的标签虽然是版本库的快照，但其实它就是指向某个`commit`的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

在上一节我们看到了，多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后`push`的童鞋不得不先`pull`，在本地合并，然后才能`push`成功。

每次合并再`push`后，分支变成了这样：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   f4e7ea8 (HEAD -> master) Merge branch 'dev' into master
    |\
    | *   ad728db (origin/dev, dev) fix env conflict
    | |\
    | | * d7e6866 Update env.txt
    | * | 770ce3f new env
    | |/
    | * 34bde7f add env
    | * 0944c8c fix bug 101
    * |   b003293 (origin/master) merged bug fix 101
    |\ \
    | * | 8842ff5 (issue-101) fix bug 101
    |/ /
    * | fc76cf7 merge with no-ff
    |\|
    | * c7b409a add merge
    |/
    *   fb8b190 conflict fixed
总之看上去很乱，有强迫症的童鞋会问：为什么`Git`的提交历史不能是一条干净的直线？
其实是可以做到的！

`Git`有一种称为`rebase`的操作，有人把它翻译成“`变基`”。我们来看看 `rebase` 是怎么把分叉的提交变成直线。

在和远程分支同步后，我们对`hello.py`这个文件做了两次提交。用`git log`命令看看：

    $ git log --graph --pretty=oneline --abbrev-commit
    * 582d922 (HEAD -> master) add author
    * 8875536 add comment
    * d1be385 (origin/master) init hello
    *   e5e69f1 Merge branch 'dev'
    |\  
    | *   57c53ab (origin/dev, dev) fix env conflict
    | |\  
    | | * 7a5e5dd add env
    | * | d7e68661 Update env.txt
    ...
注意到`Git`用(`HEAD -> master`)和(`origin/master`)标识出当前分支的`HEAD`和远程`origin`的位置分别是`582d922 add author`和`d1be385 init hello`，本地分支比远程分支快两个提交。

现在我们尝试推送本地分支：

    $ git push origin master
    To gitcode.net:xiongjiamu/learning-git.git
    ! [rejected]        master -> master (fetch first)
    error: failed to push some refs to 'git@gitcode.net:xiongjiamu/learning-git.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.
很不幸，失败了，这说明有人先于我们推送了远程分支。按照经验，先`pull`一下：

    $ git pull
    remote: Counting objects: 3, done.
    remote: Compressing objects: 100% (1/1), done.
    remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
    Unpacking objects: 100% (3/3), done.
    From gitcode.net:xiongjiamu/learning-git.git
    3010226..9dbfd41  master     -> origin/master
    Auto-merging hello.py
    Merge made by the 'recursive' strategy.
    hello.py | 1 +
    1 file changed, 1 insertion(+)
再用`git status`看看状态：

    $ git status
    On branch master
    Your branch is ahead of 'origin/master' by 3 commits.
    (use "git push" to publish your local commits)

    nothing to commit, working tree clean
加上刚才合并的提交，现在我们本地分支比远程分支超前3个提交。

用`git log`看看：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   00f5a14 (HEAD -> master) Merge branch 'master' of gitcode.net:xiongjiamu/learning-git
    |\
    | * 9dbfd41 (origin/master) set exit=1
    * | 8789044 add author
    * | 01ebb65 add comment
    |/
    * 3010226 init hello
    ...
对强迫症童鞋来说，现在事情有点不对头，提交历史分叉了。如果现在把本地分支`push`到远程，有没有问题？

有！什么问题？不好看！有没有解决方法？有！
这个时候，`rebase`就派上了用场。我们输入命令`git rebase`试试：

    $ git rebase
    First, rewinding head to replay your work on top of it...
    Applying: add comment
    Using index info to reconstruct a base tree...
    M	hello.py
    Falling back to patching base and 3-way merge...
    Auto-merging hello.py
    Applying: add author
    Using index info to reconstruct a base tree...
    M	hello.py
    Falling back to patching base and 3-way merge...
    Auto-merging hello.py
输出了一大堆操作，到底是啥效果？再用`git log`看看：

    $ git log --graph --pretty=oneline --abbrev-commit
    * 22a1da7 - (HEAD -> master) add author
    * 00bae30 - add comment
    * 9dbfd41 - (origin/master) set exit=1 
    * 3010226 - init hello
    ...
原本分叉的提交现在变成一条直线了！这种神奇的操作是怎么实现的？其实原理非常简单。我们注意观察，发现`Git`把我们本地的提交“`挪动`”了位置，放到了`f005ed4 (origin/master) set exit=1`之后，这样，整个提交历史就成了一条直线。`rebase`操作前后，最终的提交内容是一致的，但是，我们本地的`commit`修改内容已经变化了，它们的修改不再基于`d1be385 init hello`，而是基于`f005ed4 (origin/master) set exit=1`，但最后的提交`7e61ed4`内容是一致的。

这就是`rebase`操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

最后，通过`push`操作把本地分支推送到远程：

    $ git push origin master
    Enumerating objects: 8, done.
    Counting objects: 100% (8/8), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (6/6), done.
    Writing objects: 100% (6/6), 565 bytes | 565.00 KiB/s, done.
    Total 6 (delta 3), reused 0 (delta 0), pack-reused 0
    To gitcode.net:xiongjiamu/learning-git.git
    9dbfd41..22a1da7  master -> master
再用`git log`看看效果：

    $ git log --graph --pretty=oneline --abbrev-commit
    * 22a1da7 (HEAD -> master, origin/master) add author
    * 00bae30 add comment
    * 9dbfd41 set exit=1
    * 3010226 init hello
    ...
远程分支的提交历史也是一条直线。

以上就是 `rebase` 的详细介绍了，现在让我们来回顾一下：
- rebase操作可以把本地未push的分叉提交历史整理成直线
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比

到目前，关于使用 git 进行协作开发的基本内容都介绍完了。

## 六、Git标签
发布一个版本时，我们通常先在版本库中打一个标签（`tag`），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

`Git` 的标签虽然是版本库的快照，但其实它就是指向某个`commit`的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

`Git` 有`commit`，为什么还要引入`tag`？

“请把上周一的那个版本打包发布，`commit`号是`6a5819e…`”
“一串乱七八糟的数字不好找！”
如果换一个办法：
“请把上周一的那个版本打包发布，版本号是`v1.2`”
“好的，按照`tag v1.2`查找`commit`就行！”

所以，`tag`就是一个让人容易记住的有意义的名字，它跟某个`commit`绑在一起。

### 6.1 创建标签
在 `Git` 中创建标签非常简单，首先，切换到需要打标签的分支上：

    $ git branch
    * dev
    master
    $ git checkout master
    Switched to branch 'master'
然后，敲命令`git tag <name>`就可以打一个新标签：

    $ git tag v1.0
可以用命令`git tag`查看所有标签：

    $ git tag
    v1.0
默认标签是打在最新提交的`commit`上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

方法是找到历史提交的`commit id`，然后打上就可以了：

    $ git log --pretty=oneline --abbrev-commit
    5c7e5ea (HEAD -> master) remove test.txt
    c67077f add test.txt
    fb8b190 conflict fixed
    06e5e3a & simple
    2443c95 add simple
    4aac6c7 branch test
    599dbdb understand how stage works
    1985ccf append GPL
    27f9df6 add distributed
    e372504 wrote a readme file
比方说要对`conflict fixed`这次提交打标签，它对应的`commit id`是`fb8b190`，敲入命令：

    $ git tag v0.9 fb8b190
再用命令`git tag`查看标签：

    $ git tag
    v0.9
    v1.0
注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show` 查看标签信息：

    $ git show v0.9
    commit fb8b1903706eeaa8141894f1af859f0dfee3e0d9 (tag: v0.9)
    Merge: 06e5e3a 2443c95
    Author: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Sun Nov 29 20:18:26 2020 +0800

        conflict fixed

    (END)
可以看到，`v0.9`确实打在`conflict fixed`这次提交上。

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

    $ git tag -a v0.1 -m "version 0.1 released" 1985ccf
用命令`git show <tagname>`可以看到说明文字：

    $ git show v0.1
    tag v0.1
    Tagger: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Sun Nov 29 22:06:05 2020 +0800

    version 0.1 released

    commit 1985ccf5b6710edf3bcd7c0700e6d96cab335e61 (tag: v0.1, tag: remove)
    Author: Miykael_xxm <xiongjiamu@gmail.com>
    Date:   Fri Nov 27 16:14:27 2020 +0800

        append GPL

    diff --git a/readme.txt b/readme.txt
    index 9247db6..8443d23 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
    Git is a distributed version control system.
    -Git is free software.
    +Git is free software distributed under the GPL.
    (END)
>注意：标签总是和某个`commit`挂钩。如果这个`commit`既出现在`master`分支，又出现在`dev`分支，那么在这两个分支上都可以看到这个标签。

### 6.2 标签操作
如果标签打错了，也可以删除：

    $ git tag -d v0.1
    Deleted tag 'v0.1' (was 3be0bf5)
因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

    $ git push origin v1.0
    Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
    To gitcode.net:xiongjiamu/learing.git
    * [new tag]         v1.0 -> v1.0
或者，一次性推送全部尚未推送到远程的本地标签：

    $ git push origin --tags
    Enumerating objects: 1, done.
    Counting objects: 100% (1/1), done.
    Writing objects: 100% (1/1), 170 bytes | 170.00 KiB/s, done.
    Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
    To gitcode.net:xiongjiamu/learing.git
    * [new tag]         remove -> remove
    * [new tag]         v0.1 -> v0.1
    * [new tag]         v0.9 -> v0.9
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

    $ git tag -d remove
    Deleted tag 'remove' (was 1985ccf)
然后，从远程删除。删除命令也是`push`，但是格式如下：

    $ git push origin :refs/tags/remove
    To gitcode.net:xiongjiamu/learing.git
    - [deleted]         remove
要看看是否真的从远程库删除了标签，可以登录 `GIT CODE` 上查看。

### 6.3 小结
- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签
- 命令`git tag -a <tagname> -m 'messages'`可以创建一个带附注的标签
- 命令`git tag -s <tagname> -m 'messages'`可以创建一个带 `gpg` 签名的标签

## 七、极简Git+VSCode(Mac)
[配置教程网址如下](https://edu.csdn.net/skill/git/git-23b8a7b7033540e388c911bb0298aa7b?category=1413)
https://edu.csdn.net/skill/git/git-23b8a7b7033540e388c911bb0298aa7b?category=1413

## 八、极简Git+VSCode(Windows)
[配置教程网址如下](https://edu.csdn.net/skill/git/git-0e1928df298a40fbbfc81e73809035ad?category=1413)
https://edu.csdn.net/skill/git/git-0e1928df298a40fbbfc81e73809035ad?category=1413

## 九、进阶任务
### 9.1 Git问答