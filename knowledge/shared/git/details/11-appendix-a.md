
title: "附录 A：在其它环境中使用 Git"
source: "https://git-scm.com/book/zh/v2"
version: "v2"

# 附录 A：在其它环境中使用 Git

> 原始文档来源：https://git-scm.com/book/zh/v2 (Pro Git 中文版)

A1.1 附录 A: 在其它环境中使用 Git - 图形界面

如果你读完了本书，那就已经掌握了很多在命令行中使用 Git 的知识了。 你可以用它来处理本地文件，通过网络连接到他人的仓库，以及高效地与他人协同工作。 不过故事到这儿还没结束。Git 通常还会作为一个组件在更大的生态系统中使用， 而终端并不总是最佳的使用方式。现在我们来看看 Git 在其它环境中的使用， 以及其它应用（包括你的应用）是如何与 Git 协同使用的。

图形界面

Git 的原生环境是终端。 在那里，你可以体验到最新的功能，也只有在那里，你才能尽情发挥 Git 的全部能力。 但是对于某些任务而言，纯文本并不是最佳的选择；有时候你确实需要一个可视化的展示方式，而且有些用户更习惯那种能点击的界面。

有一点请注意，不同的界面是为不同的工作流程设计的。 一些客户端的作者为了支持他认为高效的特定的工作流程，经过精心挑选，只显示了 Git 功能的一个子集。 每种工具都有其特定的目的和意义，从这个角度来看，不能说某种工具比其它的“更好”。 还有请注意，没有什么事情是图形界面客户端可以做而命令行客户端不能做的，命令行始终是你可以完全操控仓库并发挥出全部力量的地方。

gitk 和 git-gui

在安装 Git 的同时，你也装好了它提供的可视化工具，gitk 和 git-gui。

gitk 是一个历史记录的图形化查看器。 你可以把它当作是基于 git log 和 git grep 命令的一个强大的图形操作界面。 当你需要查找过去发生的某次记录，或是可视化查看项目历史的时候，你将会用到这个工具。

使用 Gitk 的最简单方法就是从命令行打开。 只需 cd 到一个 Git 仓库，然后键入：

$ gitk [git log options]

Gitk 可以接受很多命令行选项，其中的大部分都直接传给底层的 git log 去执行了。 --all 可能是这其中最有用的一个, 它告诉 gitk 去尽可能地从 任何 引用查找提交并显示，而不仅仅是从 HEAD。 Gitk 的界面看起来长这样：

Figure 152. gitk 历史查看器。

这张图看起来就和执行 git log --graph 命令的输出差不多；每个点代表一次提交，线代表父子关系，而彩色的方块则用来标示一个个引用。 黄点表示 HEAD，红点表示尚未提交的本地变动。 下方的窗口用来显示当前选中的提交的具体信息；评论和补丁显示在左侧，摘要显示在右侧。 中间则是一组用来搜索历史的控件。

与之相比，git-gui 则主要是一个用来制作提交的工具。 打开它的最简单方法也是从命令行启动：

$ git gui

它的界面长这个样子：

Figure 153. git-gui 提交工具。

左侧是索引区，未暂存的修改显示在上方，已暂存的修改显示在下方。 你可以通过点击文件名左侧的图标来将该文件在暂存状态与未暂存状态之间切换，你也可以通过选中一个文件名来查看它的详情。

右侧窗口的上方以 diff 格式来显示当前选中文件发生了变动的地方。 你可以通过右击某一区块或行从而将这一区块或行放入暂存区。

右侧窗口的下方是写日志和执行操作的地方。 在文本框中键入日志然后点击“提交”就和执行 git commit 的效果差不多。 如果你想要修订上一次提交, 可以选中“修订”按钮，上次一提交的内容就会显示在“暂存区”。 然后你就可以简单的对修改进行暂存和取消暂存操作，更新提交日志，然后再次点击“提交”用这个新的提交来覆盖上一次提交。

gitk 和 git-gui 就是针对某种任务设计的工具的两个例子。 它们分别为了不同的目的（即查看历史和制作提交）而进行了精简，略去了用不到的功能。

macOS 和 Windows 上的 GitHub 客户端

GitHub 发布了两个面向工作流程的 Git 客户端：Windows 版，和 macOS 版。 它们很好的展示了一个面向工作流程的工具应该是什么样子——专注于提升那些常用的功能及其协作的可用性，而不是实现 Git 的 所有 功能. 它们看起来长这个样子：

Figure 154. GitHub macOS 客户端。
Figure 155. GitHub Windows 客户端。

我们在设计的时候就努力将二者的外观和操作体验都保持一致，因此本章会把他们当做同一个产品来介绍。 我们并不会详细地介绍该工具的每一个功能（因为它们本身也有文档），但请快速了解一下“变更”窗口（你大部分时间都会花在使用该窗口上）的以下几点：

左侧是正在追踪的仓库的列表；通过点击左上方的 “+” 图标，你可以添加一个需要追踪的仓库（既可以是通过 clone，也可以从本地添加）。

中间是输入-提交区，你可以在这里输入提交日志，以及选择哪些文件需要被提交。 （在 Windows 上，提交历史就显示在这个区域的下方；在 macOS 上，提交历史有一个单独的窗口）

右侧是修改查看区，它会告诉你工作目录里哪些东西被修改了（译注：修改模式），或选中的提交里包括了哪些修改（译注：历史模式）。

最后需要熟悉的是右上角的 “Sync” 按钮，你主要通过这个按钮来进行网络上的交互。

Note
	

你不需要注册 GitHub 账号也可以使用这些工具。 尽管它们是按照 GitHub 推荐的工作流程来设计的，并突出提升了一些 GitHub 的服务体验，但它们可以在任何 Git 仓库上工作良好，也可以通过网络连接到任意 Git 主机。

安装

GitHub 的 Windows 客户端可以从 https://windows.github.com 下载，macOS 客户端可以从 https://mac.github.com 下载。 第一次打开软件时，它会引导你进行一系列的首次使用设置，例如设置你的姓名和电子邮件，它还会智能地帮你调整一些常用的默认设置，例如凭证缓存和 CRLF 的处理方式。

它们都是“绿色软件”——如果软件打开发现有更新，下载和安装升级包都是在后台完成的。 为方便起见它们还打包了一份 Git，也就是说你一旦安装好就再也无需劳心升级的事情了。 Windows 的客户端还提供了快捷方式，可以启动装了 Posh-git 插件的 Powershell，在本章的后面一节我们会详细介绍这方面的内容。

接下来我们给它设置一些工作仓库。 客户端会显示你在 GitHub 上有权限操作的仓库的列表，你可以选择一个然后一键克隆。 如果你本地已经建立了仓库，只需要用鼠标把它从 Finder 或 Windows 资源管理器拖进 GitHub 客户端窗口，就可以把该仓库添加到左侧的仓库列表里面去了。

推荐的工作流程

安装并配置好以后，你就可以使用 GitHub 客户端来执行一些常见的 Git 任务。 该工具所推荐的工作流程有时也被叫做“GitHub 流”。 我们在 GitHub 流程 一节中对此有详细的介绍，其要点是 (a) 你会提交到一个分支；(b) 你需要经常与远程仓库保持同步。

两个平台上的客户端在分支管理上有所不同。 在 macOS 上，创建分支的按钮在窗口的上方：

Figure 156. macOS 上的“创建分支”按钮。

在 Windows 上，你可以通过在分支切换挂件中输入新分支的名称来完成创建：

Figure 157. 在 Windows 上创建分支。

分支创建好以后，新建提交就变得非常简单直接了。 现在工作目录中做一些修改，然后切换到 GitHub 客户端窗口，你所做的修改就会显示在那里。 输入提交日志，选中那些需要被包含在本次提交中的文件，然后点击“提交”按钮（也可以在键盘上按 ctrl-enter 或 ⌘-enter）。

“同步”功能是你在网络上和其它仓库交互的主要途径。 push，fetch，merge，和 rebase 在 Git 内部是一连串独立的操作, 而 GitHub 客户端将这些操作都合并成了单独一个功能。 你点击同步按钮时实际上会发生如下这些操作：

git pull --rebase。 如果上述命令由于存在合并冲突而失败，则会退而执行 git pull --no-rebase。

git push。

如果你遵循推荐的工作流程，以上就是最常用的一系列命令，因此将它们合并为一个让事情简单了很多。

小结

这些工具是为其各自针对的工作流程所量身定做的。 开发者和非开发者可以轻松地在分分钟内就搭建起项目协作环境，它们还内置了其它辅助最佳实践的功能。 但是，如果你的工作流程有所不同，或者你需要在进行网络操作时有更多的控制，那么建议你考虑一下其它客户端或者使用命令行。

其它图形界面

除此之外，还有许许多多其它的图形化 Git 客户端，其中既有单一功能的定制工具，也有试图提供 Git 所有功能的复杂应用。 Git 的官方网站整理了一份时下最流行的客户端的清单 https://git-scm.com/downloads/guis。 在 Git 的维基站点还可以看到一份更全的清单 https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools#Graphical_Interfaces。

A1.2 附录 A: 在其它环境中使用 Git - Visual Studio 中的 Git
Visual Studio 中的 Git

从 Visual Studio 2019 16.8 版本开始，Visual Studio 将 Git 工具直接内置到 IDE 中。

工具支持以下 Git 功能：

创建与克隆仓库。

打开与浏览仓库的历史。

创建与检出分支与标签。

储藏、暂存与提交改动。

抓取、拉取、推送或同步提交。

合并与变基分支。

解决合并冲突。

查看差异。

… 等等。

阅读 official documentation 以了解更多内容。

A1.3 附录 A: 在其它环境中使用 Git - Visual Studio Code 中的 Git
Visual Studio Code 中的 Git

Visual Studio Code 自带对 Git 的支持。 你需要安装 2.0.0（及以上）版本的 Git。

主要功能如下：

在行号槽显示你正在编辑的文件的改动情况。

Git 状态栏（位于左下角）会显示当前所在分支，编辑指示符以及未提交或者未拉取的提交的数量。

你能够在编辑器内完成常用的 Git 操作：

初始化一个仓库。

克隆一个仓库。

新建分支和标签。

暂存和提交修改。

对一个远程分支进行推送/拉取/同步。

解决合并冲突。

查看比较。

配合一个扩展，你也能够处理 GitHub 的拉取请求： https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github。

官方文档请访问： https://code.visualstudio.com/Docs/editor/versioncontrol。

A1.4 附录 A: 在其它环境中使用 Git - IntelliJ / PyCharm / WebStorm / PhpStorm / RubyMine 中的 Git
IntelliJ / PyCharm / WebStorm / PhpStorm / RubyMine 中的 Git

JetBrains IDEs（比如 IntelliJ IDEA，PyCharm，WebStorm，PhpStorm，RubyMine，以及其他）自带 Git 集成插件。 在 IDE 中提供了一个专门的页面，可以使用 Git 和 GitHub 的 Pull Request。

Figure 158. JetBrains IDEs 中的版本控制工具窗口。

该集成插件依赖于 Git 的命令行客户端，所以需要先安装一个 Git 客户端。 官方文档请访问 https://www.jetbrains.com/help/idea/using-git-integration.html。

A1.5 附录 A: 在其它环境中使用 Git - Sublime Text 中的 Git
Sublime Text 中的 Git

从 3.2 版本开始，Sublime Text 在编辑器内集成了 Git。

功能如下：

侧边栏将会使用图标来指明文件及文件夹的 Git 状态。

被你的 .gitignore 文件所指定忽略的文件以及文件夹会在侧边栏褪色显示。

在状态栏，你能够查看当前所在分支以及你做了多少修改。

对一个文件的所有改动都会通过行号槽上的记号显示出来。

你能够在 Sublime Text 内使用 Sublime Merge 这个 Git 客户端的部分功能。 要求安装 Sublime Merge： https://www.sublimemerge.com/。

Sublime Text 的官方文档请访问：https://www.sublimetext.com/docs/3/git_integration.html。

A1.6 附录 A: 在其它环境中使用 Git - Bash 中的 Git
Bash 中的 Git

如果你是一名 Bash 用户，你可以从中发掘出一些 Shell 的特性，让你在使用 Git 时更加随心所欲。 实际上 Git 附带了几个 Shell 的插件，但是这些插件并不是默认打开的。

首先，你需要从你使用的 Git 发行版的源代码中获得一份自动补全文件的拷贝。 输入 git version 检查版本，然后使用 git checkout tags/vX.Y.Z，其中 vX.Y.Z 对应于你正在使用的 Git 版本。 将这个 contrib/completion/git-completion.bash 文件复制到一个相对便捷的目录，例如你的 Home 目录，并且将它的路径添加到 .bashrc 中：

. ~/git-completion.bash

做完这些之后，请将你当前的目录切换到某一个 Git 仓库，并且输入：

$ git chec<tab>

……此时 Bash 将会把上面的命令自动补全为 git checkout。 在适当的情况下，这项功能适用于 Git 所有的子命令、命令行参数、以及远程仓库与引用名。

这项功能也可以用于你自己定义的提示符（prompt），显示当前目录下 Git 仓库的信息。 根据你的需要，这个信息可以简单或复杂，这里通常有大多数人想要的几个关键信息，比如当前分支信息和当前工作目录的状态信息。 要添加你自己的提示符（prompt），只需从 Git 源版本库复制 contrib/completion/git-prompt.sh 文件到你的 Home 目录（或其他便于你访问与管理的目录）， 并在 .bashrc 里添加这个文件路径，类似于下面这样：

. ~/git-prompt.sh
export GIT_PS1_SHOWDIRTYSTATE=1
export PS1='\w$(__git_ps1 " (%s)")\$ '

\w 表示打印当前工作目录，\$ 打印 $ 部分的提示符（prompt），__git_ps1 " (%s)" 表示通过格式化参数符（%s）调用`git-prompt.sh`脚本中提供的函数。 因为有了这个自定义提示符，现在你的 Bash 提示符（prompt）在 Git 仓库的任何子目录中都将显示成这样：

Figure 159. 自定义的 bash 提示符（prompt）.

这两个脚本都提供了很有帮助的文档；浏览 git-completion.bash 和 git-prompt.sh 的内容以获得更多信息。

A1.7 附录 A: 在其它环境中使用 Git - Zsh 中的 Git
Zsh 中的 Git

Zsh 还为 Git 提供了一个 Tab 补全库。 想要使用它，只需在你的 .zshrc 中执行 autoload -Uz compinit && compinit 即可。 相对于 Bash，Zsh 的接口更加强大：

$ git che<Tab>
check-attr        -- 显示 gitattributes 信息
check-ref-format  -- 检查引用名称是否符合规范
checkout          -- 从工作区中检出分支或路径
checkout-index    -- 从暂存区拷贝文件至工作目录
cherry            -- 查找没有被合并至上游的提交
cherry-pick       -- 从一些已存在的提交中应用更改

有歧义的 Tab 补全不仅会被列出，它们还会有帮助性的描述，你可以通过不断敲击 Tab 以图形方式浏览补全列表。 该功能可用于 Git 命令、它们的参数和在仓库中内容的名称（例如 refs 和 remotes），还有文件名和其他所有 Zsh 知道如何去补全的项目。

Zsh 提供了一个从版本控制系统中获取信息的框架，叫做 vcs_info 。 把如下代码添加至你的 ~/.zshrc 文件中，就可以在右侧显示分支名称：

autoload -Uz vcs_info
precmd_vcs_info() { vcs_info }
precmd_functions+=( precmd_vcs_info )
setopt prompt_subst
RPROMPT=\$vcs_info_msg_0_
# PROMPT=\$vcs_info_msg_0_'%# '
zstyle ':vcs_info:git:*' formats '%b'

当你的命令行位于一个 Git 仓库目录时，在任何时候，都可以在命令行窗口右侧显示当前分支。 （当然也可以在左侧显示，只需把上面 PROMPT 的注释去掉即可。） 它看起来像这样：

Figure 160. 自定义 zsh 提示符.

关于 vcs_info 的更多信息，可参见 zshcontrib(1) 手册页面中对应的文档，或访问 http://zsh.sourceforge.net/Doc/Release/User-Contributions.html#Version-Control-Information 在线浏览。

比起 vcs_info 而言，你可能更偏好提供了 Git 的命令提示符定制脚本 git-prompt.sh；更多信息见 https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh。 git-prompt.sh 同时兼容 Bash 和 Zsh。

Zsh 本身已足够强大，但还有一些专门为它打造的完整框架，使它更加完善。 其中之一名为 "oh-my-zsh"，你可以在 https://github.com/robbyrussell/oh-my-zsh 找到它。 oh-my-zsh 的扩展系统包含强大的 Git Tab 补全功能，且许多提示符 "主题" 可以展示版本控制数据。 一个 oh-my-zsh 主题的示例. 只是可以其中一个可以通过该系统实现的例子。

Figure 161. 一个 oh-my-zsh 主题的示例.

A1.8 附录 A: 在其它环境中使用 Git - PowerShell 中的 Git
PowerShell 中的 Git

Windows 中早期的命令行终端 cmd.exe 无法自定义 Git 使用体验，但是如果你正在使用 Powershell，那么你就十分幸运了。 这种方法同样适用于 Linux 或 macOS 上运行的 PowerShell Core。 一个名为 posh-git (https://github.com/dahlbyk/posh-git) 的扩展包提供了强大的 tab 补全功能，并针对提示符进行了增强，以帮助你聚焦于你的仓库状态。 它看起来像：

Figure 162. 附带了 posh-git 扩展包的 Powershell。
安装
前提需求（仅限 Windows）

在可以运行 PowerShell 脚本之前，你需要将本地的 ExecutionPolicy 设置为 RemoteSigned（可以说是允许除了 Undefined 和 Restricted 之外的任何内容）。 如果你选择了 AllSigned 而非 RemoteSigned，那么你的本地脚本还需要数字签名后才能执行。 如果设置为 RemoteSigned，那么只有 ZoneIdentifier 设置为 Internet，即从 Web 上下载的脚本才需要签名，其它则不需要。 如果你是管理员，想要为本机上的所有用户设置它，请使用 -Scope LocalMachine。 如果你是没有管理权限的普通用户，可使用 -Scope CurrentUser 来只为自己设置它。

有关 PowerShell Scopes 的更多详情： https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scopes

有关 PowerShell ExecutionPolicy 的更多详情： https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy

对于所有用户使用以下命令来设置 ExecutionPolicy 为 RemoteSigned：

> Set-ExecutionPolicy -Scope LocalMachine -ExecutionPolicy RemoteSigned -Force
PowerShell Gallery

如果你有 PowerShell 5 以上或安装了 PackageManagement 的 PowerShell 4，那么可以用包管理器来安装 posh-git。

有关 PowerShell Gallery 的更多详情： https://docs.microsoft.com/en-us/powershell/scripting/gallery/overview

> Install-Module posh-git -Scope CurrentUser -Force
> Install-Module posh-git -Scope CurrentUser -AllowPrerelease -Force # 带有 PowerShell Core 支持的更新的 beta 版

如果你想为所有的用户安装 posh-git，请使用 -Scope AllUsers 并在管理员权限启动的 PowerShell 控制台中执行。 如果第二条命令执行失败并出现类似 Module 'PowerShellGet' was not installed by using Install-Module 这样的错误，那么你需要先运行另一条命令：

> Install-Module PowerShellGet -Force -SkipPublisherCheck

之后你可以再试一遍。 出现这个错误的原因是 Windows PowerShell 搭载的模块是以不同的发布证书签名的。

更新 PowerShell 提示符

要在你的提示符中包含 Git 信息，那么需要导入 posh-git 模块。 要让 PowerShell 在每次启动时都导入 posh-git，请执行 Add-PoshGitToProfile 命令，它会在你的 $profile 脚本中添加导入语句。 此脚本会在每次打开新的 PowerShell 终端时执行。 注意，存在多个 $profile 脚本。 例如，其中一个是控制台的，另一个则属于 ISE。

> Import-Module posh-git
> Add-PoshGitToProfile -AllHosts
从源码安装

只需从 https://github.com/dahlbyk/posh-git 下载一份 posh-git 的发行版并解压即可。 接着使用 posh-git.psd1 文件的完整路径导入此模块：

> Import-Module <path-to-uncompress-folder>\src\posh-git.psd1
> Add-PoshGitToProfile -AllHosts

它将会向你的 profile.ps1 文件添加适当的内容，posh-git 将会在下次打开 PowerShell 时启用。

命令提示符显示的 Git 状态信息的解释见： https://github.com/dahlbyk/posh-git/blob/master/README.md#git-status-summary-information 如何定制 Posh-Git 提示符的详情见： https://github.com/dahlbyk/posh-git/blob/master/README.md#customization-variables。

A1.9 附录 A: 在其它环境中使用 Git - 总结
总结

现在你已经学会如何在日常使用的工具中驾驭强大的 Git，以及如何在自己的程序中访问 Git 仓库了。

