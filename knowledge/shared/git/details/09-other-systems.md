
title: "Git 与其他系统"
source: "https://git-scm.com/book/zh/v2"
version: "v2"

# Git 与其他系统

> 原始文档来源：https://git-scm.com/book/zh/v2 (Pro Git 中文版)

9.1 Git 与其他系统 - 作为客户端的 Git

现实并不总是尽如人意。 通常，你不能立刻就把接触到的每一个项目都切换到 Git。 有时候你被困在使用其他 VCS 的项目中，却希望使用 Git。 在本章的第一部分我们将会了解到，怎样在你的那些托管在不同系统的项目上使用 Git 客户端。

在某些时候，你可能想要将已有项目转换到 Git。 本章的第二部分涵盖了从几个特定系统将你的项目迁移至 Git 的方法，即使没有预先构建好的导入工具，我们也有办法手动导入。

作为客户端的 Git

Git 为开发者提供了如此优秀的体验，许多人已经找到了在他们的工作站上使用 Git 的方法，即使他们团队其余的人使用的是完全不同的 VCS。 有许多这种可用的适配器，它们被叫做“桥接”。 下面我们将要介绍几个很可能会在实际中用到的桥接。

Git 与 Subversion

很大一部分开源项目与相当多的企业项目使用 Subversion 来管理它们的源代码。 而且在大多数时间里，它已经是开源项目 VCS 选择的 事实标准。 它在很多方面都与曾经是源代码管理世界的大人物的 CVS 相似。

Git 中最棒的特性就是有一个与 Subversion 的双向桥接，它被称作 git svn。 这个工具允许你使用 Git 作为连接到 Subversion 有效的客户端，这样你可以使用 Git 所有本地的功能然后如同正在本地使用 Subversion 一样推送到 Subversion 服务器。 这意味着你可以在本地做新建分支与合并分支、使用暂存区、使用变基与拣选等等的事情，同时协作者还在继续使用他们黑暗又古老的方式。 当你试图游说公司将基础设施修改为完全支持 Git 的过程中，一个好方法是将 Git 偷偷带入到公司环境，并帮助周围的开发者提升效率。 Subversion 桥接就是进入 DVCS 世界的诱饵。

git svn

在 Git 中所有 Subversion 桥接命令的基础命令是 git svn。 它可以跟很多命令，所以我们会通过几个简单的工作流程来为你演示最常用的命令。

需要特别注意的是当你使用 git svn 时，就是在与 Subversion 打交道，一个与 Git 完全不同的系统。 尽管 可以 在本地新建分支与合并分支，但是你最好还是通过变基你的工作来保证你的历史尽可能是直线，并且避免做类似同时与 Git 远程服务器交互的事情。

不要重写你的历史然后尝试再次推送，同时也不要推送到一个平行的 Git 仓库来与其他使用 Git 的开发者协作。 Subversion 只能有一个线性的历史，弄乱它很容易。 如果你在一个团队中工作，其中有一些人使用 SVN 而另一些人使用 Git，你需要确保每个人都使用 SVN 服务器来协作——这样做会省去很多麻烦。

设置

为了演示这个功能，需要一个有写入权限的典型 SVN 仓库。 如果想要拷贝这些例子，你必须获取一份可写入的 SVN 测试仓库的副本。 为了轻松地拷贝，可以使用 Subversion 自带的一个名为 svnsync 的工具。 为了这些测试，我们在 Google Code 上创建了一个 protobuf 项目部分拷贝的新 Subversion 仓库。protobuf 是一个将结构性数据编码用于网络传输的工具。

接下来，你需要先创建一个新的本地 Subversion 仓库：

$ mkdir /tmp/test-svn
$ svnadmin create /tmp/test-svn

然后，允许所有用户改变版本属性——最容易的方式是添加一个返回值为 0 的 pre-revprop-change 脚本。

$ cat /tmp/test-svn/hooks/pre-revprop-change
#!/bin/sh
exit 0;
$ chmod +x /tmp/test-svn/hooks/pre-revprop-change

现在可以调用加入目标与来源仓库参数的 svnsync init 命令同步这个项目到本地的机器。

$ svnsync init file:///tmp/test-svn \
  http://your-svn-server.example.org/svn/

这样就设置好了同步所使用的属性。 可以通过运行下面的命令来克隆代码：

$ svnsync sync file:///tmp/test-svn
Committed revision 1.
Copied properties for revision 1.
Transmitting file data .............................[...]
Committed revision 2.
Copied properties for revision 2.
[…]

虽然这个操作可能只会花费几分钟，但如果你尝试拷贝原始的仓库到另一个非本地的远程仓库时，即使只有不到 100 个的提交，这个过程也可能会花费将近一个小时。 Subversion 必须一次复制一个版本然后推送回另一个仓库——这低效得可笑，但却是做这件事唯一简单的方式。

开始

既然已经有了一个有写入权限的 Subversion 仓库，那么你可以开始一个典型的工作流程。 可以从 git svn clone 命令开始，它会将整个 Subversion 仓库导入到一个本地 Git 仓库。 需要牢记的一点是如果是从一个真正托管的 Subversion 仓库中导入，需要将 file:///tmp/test-svn 替换为你的 Subversion 仓库的 URL：

$ git svn clone file:///tmp/test-svn -T trunk -b branches -t tags
Initialized empty Git repository in /private/tmp/progit/test-svn/.git/
r1 = dcbfb5891860124cc2e8cc616cded42624897125 (refs/remotes/origin/trunk)
    A	m4/acx_pthread.m4
    A	m4/stl_hash.m4
    A	java/src/test/java/com/google/protobuf/UnknownFieldSetTest.java
    A	java/src/test/java/com/google/protobuf/WireFormatTest.java
…
r75 = 556a3e1e7ad1fde0a32823fc7e4d046bcfd86dae (refs/remotes/origin/trunk)
Found possible branch point: file:///tmp/test-svn/trunk => file:///tmp/test-svn/branches/my-calc-branch, 75
Found branch parent: (refs/remotes/origin/my-calc-branch) 556a3e1e7ad1fde0a32823fc7e4d046bcfd86dae
Following parent with do_switch
Successfully followed parent
r76 = 0fb585761df569eaecd8146c71e58d70147460a2 (refs/remotes/origin/my-calc-branch)
Checked out HEAD:
  file:///tmp/test-svn/trunk r75

这相当于运行了两个命令—— git svn init 以及紧接着的 git svn fetch ——你提供的 URL。 这会花费一些时间。例如，如果测试项目只有 75 个左右的提交并且代码库并不是很大， 但是 Git 必须一次一个地检出一个版本同时单独地提交它。 对于有成百上千个提交的项目，这真的可能会花费几小时甚至几天来完成。

-T trunk -b branches -t tags 部分告诉 Git Subversion 仓库遵循基本的分支与标签惯例。 如果你命名了不同的主干、分支或标签，可以修改这些参数。 因为这是如此地常见，所以能用 -s 来替代整个这部分，这表示标准布局并且指代所有那些选项。 下面的命令是相同的：

$ git svn clone file:///tmp/test-svn -s

至此，应该得到了一个已经导入了分支与标签的有效的 Git 仓库：

$ git branch -a
* master
  remotes/origin/my-calc-branch
  remotes/origin/tags/2.0.2
  remotes/origin/tags/release-2.0.1
  remotes/origin/tags/release-2.0.2
  remotes/origin/tags/release-2.0.2rc1
  remotes/origin/trunk

注意这个工具是如何将 Subversion 标签作为远程引用来管理的。 让我们近距离看一下 Git 的底层命令 show-ref：

$ git show-ref
556a3e1e7ad1fde0a32823fc7e4d046bcfd86dae refs/heads/master
0fb585761df569eaecd8146c71e58d70147460a2 refs/remotes/origin/my-calc-branch
bfd2d79303166789fc73af4046651a4b35c12f0b refs/remotes/origin/tags/2.0.2
285c2b2e36e467dd4d91c8e3c0c0e1750b3fe8ca refs/remotes/origin/tags/release-2.0.1
cbda99cb45d9abcb9793db1d4f70ae562a969f1e refs/remotes/origin/tags/release-2.0.2
a9f074aa89e826d6f9d30808ce5ae3ffe711feda refs/remotes/origin/tags/release-2.0.2rc1
556a3e1e7ad1fde0a32823fc7e4d046bcfd86dae refs/remotes/origin/trunk

Git 在从 Git 服务器克隆时并不这样做；下面是在刚刚克隆完成的有标签的仓库的样子：

$ git show-ref
c3dcbe8488c6240392e8a5d7553bbffcb0f94ef0 refs/remotes/origin/master
32ef1d1c7cc8c603ab78416262cc421b80a8c2df refs/remotes/origin/branch-1
75f703a3580a9b81ead89fe1138e6da858c5ba18 refs/remotes/origin/branch-2
23f8588dde934e8f33c263c6d8359b2ae095f863 refs/tags/v0.1.0
7064938bd5e7ef47bfd79a685a62c1e2649e2ce7 refs/tags/v0.2.0
6dcb09b5b57875f334f61aebed695e2e4193db5e refs/tags/v1.0.0

Git 直接将标签抓取至 refs/tags，而不是将它们看作分支。

提交回 Subversion

现在你有了一个工作目录，你可以在项目上做一些改动，然后高效地使用 Git 作为 SVN 客户端将你的提交推送到上游。 一旦编辑了一个文件并提交它，你就有了一个存在于本地 Git 仓库的提交，这提交在 Subversion 服务器上并不存在：

$ git commit -am 'Adding git-svn instructions to the README'
[master 4af61fd] Adding git-svn instructions to the README
 1 file changed, 5 insertions(+)

接下来，你需要将改动推送到上游。 注意这会怎样改变你使用 Subversion 的方式——你可以离线做几次提交然后一次性将它们推送到 Subversion 服务器。 要推送到一个 Subversion 服务器，运行 git svn dcommit 命令：

$ git svn dcommit
Committing to file:///tmp/test-svn/trunk ...
    M	README.txt
Committed r77
    M	README.txt
r77 = 95e0222ba6399739834380eb10afcd73e0670bc5 (refs/remotes/origin/trunk)
No changes between 4af61fd05045e07598c553167e0f31c84fd6ffe1 and refs/remotes/origin/trunk
Resetting to the latest refs/remotes/origin/trunk

这会拿走你在 Subversion 服务器代码之上所做的所有提交，针对每一个做一个 Subversion 提交，然后重写你本地的 Git 提交来包含一个唯一的标识符。 这很重要因为这意味着所有你的提交的 SHA-1 校验和都改变了。 部分由于这个原因，同时使用一个基于 Git 的项目远程版本和一个 Subversion 服务器并不是一个好主意。 如果你查看最后一次提交，有新的 git-svn-id 被添加：

$ git log -1
commit 95e0222ba6399739834380eb10afcd73e0670bc5
Author: ben <ben@0b684db3-b064-4277-89d1-21af03df0a68>
Date:   Thu Jul 24 03:08:36 2014 +0000

    Adding git-svn instructions to the README

    git-svn-id: file:///tmp/test-svn/trunk@77 0b684db3-b064-4277-89d1-21af03df0a68

注意你原来提交的 SHA-1 校验和原来是以 4af61fd 开头，而现在是以 95e0222 开头。 如果想要既推送到一个 Git 服务器又推送到一个 Subversion 服务器，必须先推送（dcommit）到 Subversion 服务器，因为这个操作会改变你的提交数据。

拉取新改动

如果你和其他开发者一起工作，当在某一时刻你们其中之一推送时，另一人尝试推送修改会导致冲突。 那次修改会被拒绝直到你合并他们的工作。 在 git svn 中，它看起来是这样的：

$ git svn dcommit
Committing to file:///tmp/test-svn/trunk ...

ERROR from SVN:
Transaction is out of date: File '/trunk/README.txt' is out of date
W: d5837c4b461b7c0e018b49d12398769d2bfc240a and refs/remotes/origin/trunk differ, using rebase:
:100644 100644 f414c433af0fd6734428cf9d2a9fd8ba00ada145 c80b6127dd04f5fcda218730ddf3a2da4eb39138 M	README.txt
Current branch master is up to date.
ERROR: Not all changes have been committed into SVN, however the committed
ones (if any) seem to be successfully integrated into the working tree.
Please see the above messages for details.

为了解决这种情况，可以运行 git svn rebase，它会从服务器拉取任何你本地还没有的改动，并将你所有的工作变基到服务器的内容之上：

$ git svn rebase
Committing to file:///tmp/test-svn/trunk ...

ERROR from SVN:
Transaction is out of date: File '/trunk/README.txt' is out of date
W: eaa029d99f87c5c822c5c29039d19111ff32ef46 and refs/remotes/origin/trunk differ, using rebase:
:100644 100644 65536c6e30d263495c17d781962cfff12422693a b34372b25ccf4945fe5658fa381b075045e7702a M	README.txt
First, rewinding head to replay your work on top of it...
Applying: update foo
Using index info to reconstruct a base tree...
M	README.txt
Falling back to patching base and 3-way merge...
Auto-merging README.txt
ERROR: Not all changes have been committed into SVN, however the committed
ones (if any) seem to be successfully integrated into the working tree.
Please see the above messages for details.

现在，所有你的工作都已经在 Subversion 服务器的内容之上了，你就可以顺利地 dcommit：

$ git svn dcommit
Committing to file:///tmp/test-svn/trunk ...
    M	README.txt
Committed r85
    M	README.txt
r85 = 9c29704cc0bbbed7bd58160cfb66cb9191835cd8 (refs/remotes/origin/trunk)
No changes between 5762f56732a958d6cfda681b661d2a239cc53ef5 and refs/remotes/origin/trunk
Resetting to the latest refs/remotes/origin/trunk

注意，和 Git 需要你在推送前合并本地还没有的上游工作不同的是，git svn 只会在修改发生冲突时要求你那样做（更像是 Subversion 工作的行为）。 如果其他人推送一个文件的修改然后你推送了另一个文件的修改，你的 dcommit 命令会正常工作：

$ git svn dcommit
Committing to file:///tmp/test-svn/trunk ...
    M	configure.ac
Committed r87
    M	autogen.sh
r86 = d8450bab8a77228a644b7dc0e95977ffc61adff7 (refs/remotes/origin/trunk)
    M	configure.ac
r87 = f3653ea40cb4e26b6281cec102e35dcba1fe17c4 (refs/remotes/origin/trunk)
W: a0253d06732169107aa020390d9fefd2b1d92806 and refs/remotes/origin/trunk differ, using rebase:
:100755 100755 efa5a59965fbbb5b2b0a12890f1b351bb5493c18 e757b59a9439312d80d5d43bb65d4a7d0389ed6d M	autogen.sh
First, rewinding head to replay your work on top of it...

记住这一点很重要，因为结果是当你推送后项目的状态并不存在于你的电脑中。 如果修改并未冲突但却是不兼容的，可能会引起一些难以诊断的问题。 这与使用 Git 服务器并不同——在 Git 中，可以在发布前完全测试客户端系统的状态，然而在 SVN 中，你甚至不能立即确定在提交前与提交后的状态是相同的。

你也应该运行这个命令从 Subversion 服务器上拉取修改，即使你自己并不准备提交。 可以运行 git svn fetch 来抓取新数据，但是 git svn rebase 会抓取并更新你本地的提交。

$ git svn rebase
    M	autogen.sh
r88 = c9c5f83c64bd755368784b444bc7a0216cc1e17b (refs/remotes/origin/trunk)
First, rewinding head to replay your work on top of it...
Fast-forwarded master to refs/remotes/origin/trunk.

每隔一会儿运行 git svn rebase 确保你的代码始终是最新的。 虽然需要保证当运行这个命令时工作目录是干净的。 如果有本地的修改，在运行 git svn rebase 之前要么储藏你的工作要么做一次临时的提交，不然，当变基会导致合并冲突时，命令会终止。

Git 分支问题

当适应了 Git 的工作流程，你大概会想要创建主题分支，在上面做一些工作，然后将它们合并入主分支。 如果你正通过 git svn 推送到一个 Subversion 服务器，你可能想要把你的工作变基到一个单独的分支上，而不是将分支合并到一起。 比较喜欢变基的原因是因为 Subversion 有一个线性的历史并且无法像 Git 一样处理合并，所以 git svn 在将快照转换成 Subversion 提交时，只会保留第一父提交。

假设你的历史像下面这样：创建了一个 experiment 分支，做了两次提交，然后将它们合并回 master。 当 dcommit 时，你看到输出是这样的：

$ git svn dcommit
Committing to file:///tmp/test-svn/trunk ...
    M	CHANGES.txt
Committed r89
    M	CHANGES.txt
r89 = 89d492c884ea7c834353563d5d913c6adf933981 (refs/remotes/origin/trunk)
    M	COPYING.txt
    M	INSTALL.txt
Committed r90
    M	INSTALL.txt
    M	COPYING.txt
r90 = cb522197870e61467473391799148f6721bcf9a0 (refs/remotes/origin/trunk)
No changes between 71af502c214ba13123992338569f4669877f55fd and refs/remotes/origin/trunk
Resetting to the latest refs/remotes/origin/trunk

在一个合并过历史提交的分支上 dcommit 命令工作得很好，除了当你查看你的 Git 项目历史时，它并没有重写所有你在 experiment 分支上所做的任意提交——相反，所有这些修改显示一个单独合并提交的 SVN 版本中。

当其他人克隆那些工作时，他们只会看到一个被塞入了所有改动的合并提交，就像运行了 git merge --squash；他们无法看到修改从哪来或何时提交的信息。

Subversion 分支

在 Subversion 中新建分支与在 Git 中新建分支并不相同；如果你能不用它，那最好就不要用。 然而，你可以使用 git svn 在 Subversion 中创建分支并在分支上做提交。

创建一个新的 SVN 分支

要在 Subversion 中创建一个新分支，运行 git svn branch <new-branch>：

$ git svn branch opera
Copying file:///tmp/test-svn/trunk at r90 to file:///tmp/test-svn/branches/opera...
Found possible branch point: file:///tmp/test-svn/trunk => file:///tmp/test-svn/branches/opera, 90
Found branch parent: (refs/remotes/origin/opera) cb522197870e61467473391799148f6721bcf9a0
Following parent with do_switch
Successfully followed parent
r91 = f1b64a3855d3c8dd84ee0ef10fa89d27f1584302 (refs/remotes/origin/opera)

这与 Subversion 中的 svn copy trunk branches/opera 命令作用相同并且是在 Subversion 服务器中操作。 需要重点注意的是它并不会检出到那个分支；如果你在这时提交，提交会进入服务器的 trunk 分支，而不是 opera 分支。

切换活动分支

Git 通过查找在历史中 Subversion 分支的头部来指出你的提交将会到哪一个分支——应该只有一个，并且它应该是在当前分支历史中最后一个有 git-svn-id 的。

如果想要同时在不止一个分支上工作，可以通过在导入的那个分支的 Subversion 提交开始来设置本地分支 dcommit 到特定的 Subversion 分支。 如果想要一个可以单独在上面工作的 opera 分支，可以运行

$ git branch opera remotes/origin/opera

现在，如果想要将你的 opera 分支合并入 trunk（你的 master 分支），可以用一个正常的 git merge 来这样做。 但是你需要通过 -m 来提供一个描述性的提交信息，否则合并信息会是没有用的 “Merge branch opera”。

记住尽管使用的是 git merge 来做这个操作，而且合并可能会比在 Subversion 中更容易一些 （因为 Git 会为你自动地检测合适的合并基础），但这并不是一个普通的 Git 合并提交。 你不得不将这个数据推送回一个 Subversion 服务器，Subversion 服务器不支持那些跟踪多个父结点的提交； 所以，当推送完成后，它看起来会是一个将其他分支的所有提交压缩在一起的单独提交。 在合并一个分支到另一个分支后，你并不能像 Git 中那样轻松地回到原来的分支继续工作。 你运行的 dcommit 命令会将哪个分支被合并进来的信息抹掉，所以后续的合并基础计算会是错的—— dcommit 会使你的 git merge 结果看起来像是运行了 git merge --squash。 不幸的是，没有一个好的方式来避免这种情形—— Subversion 无法存储这个信息， 所以当使用它做为服务器时你总是会被它的限制打垮。 为了避免这些问题，应该在合并到主干后删除本地分支（本例中是 opera）。

Subversion 命令

git svn 工具集通过提供很多功能与 Subversion 中那些相似的命令来帮助简化转移到 Git 的过程。 下面是一些提供了 Subversion 中常用功能的命令。

SVN 风格历史

如果你习惯于使用 Subversion 并且想要看 SVN 输出风格的提交历史，可以运行 git svn log 来查看 SVN 格式的提交历史：

$ git svn log
------------------------------------------------------------------------
r87 | schacon | 2014-05-02 16:07:37 -0700 (Sat, 02 May 2014) | 2 lines

autogen change

------------------------------------------------------------------------
r86 | schacon | 2014-05-02 16:00:21 -0700 (Sat, 02 May 2014) | 2 lines

Merge branch 'experiment'

------------------------------------------------------------------------
r85 | schacon | 2014-05-02 16:00:09 -0700 (Sat, 02 May 2014) | 2 lines

updated the changelog

关于 git svn log，有两件重要的事你应该知道。 首先，它是离线工作的，并不像真正的 svn log 命令，会向 Subversion 服务器询问数据。 其次，它只会显示已经提交到 Subversion 服务器上的提交。 还未 dcommit 的本地 Git 提交并不会显示；同样也不会显示这段时间中其他人推送到 Subversion 服务器上的提交。 它更像是最后获取到的 Subversion 服务器上的提交状态。

SVN 注解

类似 git svn log 命令离线模拟了 svn log 命令，你可以认为 git svn blame [FILE] 离线模拟了 svn annotate。 输出看起来像这样：

$ git svn blame README.txt
 2   temporal Protocol Buffers - Google's data interchange format
 2   temporal Copyright 2008 Google Inc.
 2   temporal http://code.google.com/apis/protocolbuffers/
 2   temporal
22   temporal C++ Installation - Unix
22   temporal =======================
 2   temporal
79    schacon Committing in git-svn.
78    schacon
 2   temporal To build and install the C++ Protocol Buffer runtime and the Protocol
 2   temporal Buffer compiler (protoc) execute the following:
 2   temporal

重复一次，它并不显示你在 Git 中的本地提交，也不显示同一时间被推送到 Subversion 的其他提交。

SVN 服务器信息

可以通过运行 git svn info 得到与 svn info 相同种类的信息。

$ git svn info
Path: .
URL: https://schacon-test.googlecode.com/svn/trunk
Repository Root: https://schacon-test.googlecode.com/svn
Repository UUID: 4c93b258-373f-11de-be05-5f7a86268029
Revision: 87
Node Kind: directory
Schedule: normal
Last Changed Author: schacon
Last Changed Rev: 87
Last Changed Date: 2009-05-02 16:07:37 -0700 (Sat, 02 May 2009)

这就像是在你上一次和 Subversion 服务器通讯时同步了之后，离线运行的 blame 与 log 命令。

忽略 SUBVERSION 所忽略的

如果克隆一个在任意一处设置 svn:ignore 属性的 Subversion 仓库时，你也许会想要设置对应的 .gitignore 文件，这样就不会意外的提交那些不该提交的文件。 git svn 有两个命令来帮助解决这个问题。 第一个是 git svn create-ignore，它会为你自动地创建对应的 .gitignore 文件，这样你的下次提交就能包含它们。

第二个命令是 git svn show-ignore，它会将你需要放在 .gitignore 文件中的每行内容打印到标准输出，这样就可以将输出内容重定向到项目的例外文件中：

$ git svn show-ignore > .git/info/exclude

这样，你就不会由于 .gitignore 文件而把项目弄乱。 当你是 Subversion 团队中唯一的 Git 用户时这是一个好的选项，并且你的队友并不想要项目内存在 .gitignore 文件。

Git-Svn 总结

当你不得不使用 Subversion 服务器或者其他必须运行一个 Subversion 服务器的开发环境时，git svn 工具很有用。 你应该把它当做一个不完全的 Git，然而，你要是不用它的话，就会在做转换的过程中遇到很多麻烦的问题。 为了不惹麻烦，尽量遵守这些准则：

保持一个线性的 Git 历史，其中不能有 git merge 生成的合并提交。 把你在主线分支外开发的全部工作变基到主线分支；而不要合并入主线分支。

不要建立一个单独的 Git 服务器，也不要在 Git 服务器上协作。 可以用一台 Git 服务器来帮助新来的开发者加速克隆，但是不要推送任何不包含 git-svn-id 条目的东西。 你可能会需要增加一个 pre-receive 钩子来检查每一个提交信息是否包含 git-svn-id 并且拒绝任何未包含的提交。

如果你遵守了那些准则，忍受用一个 Subversion 服务器来工作可以更容易些。 然而，如果有可能迁移到一个真正的 Git 服务器，那么迁移过去能使你的团队获得更多好处。

Git 与 Mercurial

DVCS 的宇宙里不只有 Git。 实际上，在这个空间里有许多其他的系统。对于如何正确地进行分布式版本管理，每一个系统都有自己的视角。 除了 Git，最流行的就是 Mercurial，并且它们两个在很多方面都很相似。

好消息是，如果你更喜欢 Git 的客户端行为但是工作在源代码由 Mercurial 控制的项目中，有一种使用 Git 作为 Mercurial 托管仓库的客户端的方法。 由于 Git 与服务器仓库是使用远程交互的，那么由远程助手实现的桥接方法就不会让人很惊讶。 这个项目的名字是 git-remote-hg，可以在 https://github.com/felipec/git-remote-hg 找到。

git-remote-hg

首先，需要安装 git-remote-hg。 实际上需要将它的文件放在 PATH 变量的某个目录中，像这样：

$ curl -o ~/bin/git-remote-hg \
  https://raw.githubusercontent.com/felipec/git-remote-hg/master/git-remote-hg
$ chmod +x ~/bin/git-remote-hg

假定 ~/bin 在 $PATH 变量中。 Git-remote-hg 有一个其他的依赖：mercurial Python 库。 如果已经安装了 Python，安装它就像这样简单：

$ pip install mercurial

（如果未安装 Python，访问 https://www.python.org/ 来获取它。）

需要做的最后一件事是安装 Mercurial 客户端。 如果还没有安装的话请访问 https://www.mercurial-scm.org/ 来安装。

现在已经准备好摇滚了。 你所需要的一切就是一个你可以推送的 Mercurial 仓库。 很幸运，每一个 Mercurial 仓库都可以这样做，所以我们只需要使用大家用来学习 Mercurial 的“hello world”仓库就可以了：

$ hg clone http://selenic.com/repo/hello /tmp/hello
开始

既然有一个可用的 “server-side” 仓库，我们可以通过一个典型的工作流来了解。 你将会看到，这两种系统非常相似，没有太多的出入。

和 Git 一样，首先我们克隆：

$ git clone hg::/tmp/hello /tmp/hello-git
$ cd /tmp/hello-git
$ git log --oneline --graph --decorate
* ac7955c (HEAD, origin/master, origin/branches/default, origin/HEAD, refs/hg/origin/branches/default, refs/hg/origin/bookmarks/master, master) Create a makefile
* 65bb417 Create a standard "hello, world" program

你会注意到与 Mercurial 仓库工作时使用了标准的 git clone 命令。 那是因为 git-remote-hg 工作在相当低的底层，使用类似于 Git HTTP/S 协议的机制实现的（远程助手）。 由于 Git 与 Mercurial 都设计为每一个客户端有一个仓库的完整历史，所以这个命令做了一次完整的克隆，包括所有的项目历史，并且相当快地完成。

log 命令显示了两次提交，最后一次提交指向了一大堆引用。 那说明这其中的一部分实际上并没有在那儿。 让我们看看 .git 目录中实际上有什么：

$ tree .git/refs
.git/refs
├── heads
│   └── master
├── hg
│   └── origin
│       ├── bookmarks
│       │   └── master
│       └── branches
│           └── default
├── notes
│   └── hg
├── remotes
│   └── origin
│       └── HEAD
└── tags

9 directories, 5 files

Git-remote-hg 尝试让结构更有 Git 风格，但是在隐藏在下面的是它管理两个轻微不同系统之间概念的映射。 refs/hg 目录中存储了实际的远程引用。 例如，refs/hg/origin/branches/default 是一个包含以 “ac7955c” 开始的 SHA-1 值的 Git 引用文件，是 master 所指向的提交。 所以 refs/hg 目录是一种类似 refs/remotes/origin 的替代品，但是它引入了书签与分支的区别。

notes/hg 文件是 git-remote-hg 如何在 Git 的提交散列与 Mercurial 变更集 ID 之间建立映射的起点。 让我们来探索一下：

$ cat notes/hg
d4c10386...

$ git cat-file -p d4c10386...
tree 1781c96...
author remote-hg <> 1408066400 -0800
committer remote-hg <> 1408066400 -0800

Notes for master

$ git ls-tree 1781c96...
100644 blob ac9117f...	65bb417...
100644 blob 485e178...	ac7955c...

$ git cat-file -p ac9117f
0a04b987be5ae354b710cefeba0e2d9de7ad41a9

所以 refs/notes/hg 指向了一个树，即在 Git 对象数据库中的一个有其他对象名字的列表。 git ls-tree 输出 tree 对象中所有项目的模式、类型、对象哈希与文件名。 如果深入挖掘 tree 对象中的一个项目，我们会发现在其中是一个名字为 “ac9117f” 的 blob 对象（master 所指向提交的 SHA-1 散列值），包含内容 “0a04b98” （是 default 分支指向的 Mercurial 变更集的 ID）。

好消息是大多数情况下我们不需要关心以上这些。 典型的工作流程与使用 Git 远程仓库并没有什么不同。

在我们继续之前，这里还有一件需要注意的事情：忽略。 Mercurial 与 Git 使用非常类似的机制实现这个功能，但是一般来说你不会想要把一个 .gitignore 文件提交到 Mercurial 仓库中。 幸运的是，Git 有一种方式可以忽略本地磁盘仓库的文件，而且 Mercurial 格式是与 Git 兼容的，所以你只需将这个文件拷贝过去：

$ cp .hgignore .git/info/exclude

.git/info/exclude 文件的作用像是一个 .gitignore，但是它不包含在提交中。

工作流程

假设我们已经做了一些工作并且在 master 分支做了几次提交，而且已经准备将它们推送到远程仓库。 这是我们仓库现在的样子：

$ git log --oneline --graph --decorate
* ba04a2a (HEAD, master) Update makefile
* d25d16f Goodbye
* ac7955c (origin/master, origin/branches/default, origin/HEAD, refs/hg/origin/branches/default, refs/hg/origin/bookmarks/master) Create a makefile
* 65bb417 Create a standard "hello, world" program

我们的 master 分支领先 origin/master 分支两个提交，但是那两个提交只存在于我们的本地机器中。 让我们看看在同一时间有没有其他人做过什么重要的工作：

$ git fetch
From hg::/tmp/hello
   ac7955c..df85e87  master     -> origin/master
   ac7955c..df85e87  branches/default -> origin/branches/default
$ git log --oneline --graph --decorate --all
* 7b07969 (refs/notes/hg) Notes for default
* d4c1038 Notes for master
* df85e87 (origin/master, origin/branches/default, origin/HEAD, refs/hg/origin/branches/default, refs/hg/origin/bookmarks/master) Add some documentation
| * ba04a2a (HEAD, master) Update makefile
| * d25d16f Goodbye
|/
* ac7955c Create a makefile
* 65bb417 Create a standard "hello, world" program

因为使用了 --all 标记，我们看到被 git-remote-hg 内部使用的 “notes” 引用，但是可以忽略它们。 剩下的部分是我们期望的；origin/master 已经前进了一次提交，同时我们的历史现在分叉了。 Mercurial 和我们本章中讨论的其他系统不一样，它能够处理合并，所以我们不需要做任何其他事情。

$ git merge origin/master
Auto-merging hello.c
Merge made by the 'recursive' strategy.
 hello.c | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git log --oneline --graph --decorate
*   0c64627 (HEAD, master) Merge remote-tracking branch 'origin/master'
|\
| * df85e87 (origin/master, origin/branches/default, origin/HEAD, refs/hg/origin/branches/default, refs/hg/origin/bookmarks/master) Add some documentation
* | ba04a2a Update makefile
* | d25d16f Goodbye
|/
* ac7955c Create a makefile
* 65bb417 Create a standard "hello, world" program

完美。 运行测试然后所有测试都通过了，所以我们准备将工作共享给团队的其他成员。

$ git push
To hg::/tmp/hello
   df85e87..0c64627  master -> master

就是这样！ 如果你现在查看一下 Mercurial 仓库，你会发现这样实现了我们所期望的：

$ hg log -G --style compact
o    5[tip]:4,2   dc8fa4f932b8   2014-08-14 19:33 -0700   ben
|\     Merge remote-tracking branch 'origin/master'
| |
| o  4   64f27bcefc35   2014-08-14 19:27 -0700   ben
| |    Update makefile
| |
| o  3:1   4256fc29598f   2014-08-14 19:27 -0700   ben
| |    Goodbye
| |
@ |  2   7db0b4848b3c   2014-08-14 19:30 -0700   ben
|/     Add some documentation
|
o  1   82e55d328c8c   2005-08-26 01:21 -0700   mpm
|    Create a makefile
|
o  0   0a04b987be5a   2005-08-26 01:20 -0700   mpm
     Create a standard "hello, world" program

序号 2 的变更集是由 Mercurial 生成的，序号 3 与序号 4 的变更集是由 git-remote-hg 生成的，通过 Git 推送上来的提交。

分支与书签

Git 只有一种类型的分支：当提交生成时移动的一个引用。 在 Mercurial 中，这种类型的引用叫作 “bookmark”，它的行为非常类似于 Git 分支。

Mercurial 的 “branch” 概念则更重量级一些。 变更集生成时的分支会记录 在变更集中，意味着它会永远地存在于仓库历史中。 这个例子描述了一个在 develop 分支上的提交：

$ hg log -l 1
changeset:   6:8f65e5e02793
branch:      develop
tag:         tip
user:        Ben Straub <ben@straub.cc>
date:        Thu Aug 14 20:06:38 2014 -0700
summary:     More documentation

注意开头为 “branch” 的那行。 Git 无法真正地模拟这种行为（并且也不需要这样做；两种类型的分支都可以表达为 Git 的一个引用），但是 git-remote-hg 需要了解其中的区别，因为 Mercurial 关心。

创建 Mercurial 书签与创建 Git 分支一样容易。 在 Git 这边：

$ git checkout -b featureA
Switched to a new branch 'featureA'
$ git push origin featureA
To hg::/tmp/hello
 * [new branch]      featureA -> featureA

这就是所要做的全部。 在 Mercurial 这边，它看起来像这样：

$ hg bookmarks
   featureA                  5:bd5ac26f11f9
$ hg log --style compact -G
@  6[tip]   8f65e5e02793   2014-08-14 20:06 -0700   ben
|    More documentation
|
o    5[featureA]:4,2   bd5ac26f11f9   2014-08-14 20:02 -0700   ben
|\     Merge remote-tracking branch 'origin/master'
| |
| o  4   0434aaa6b91f   2014-08-14 20:01 -0700   ben
| |    update makefile
| |
| o  3:1   318914536c86   2014-08-14 20:00 -0700   ben
| |    goodbye
| |
o |  2   f098c7f45c4f   2014-08-14 20:01 -0700   ben
|/     Add some documentation
|
o  1   82e55d328c8c   2005-08-26 01:21 -0700   mpm
|    Create a makefile
|
o  0   0a04b987be5a   2005-08-26 01:20 -0700   mpm
     Create a standard "hello, world" program

注意在修订版本 5 上的新 [featureA] 标签。 在 Git 这边这些看起来像是 Git 分支，除了一点：不能从 Git 这边删除书签（这是远程助手的一个限制）。

你也可以工作在一个 “重量级” 的 Mercurial branch：只需要在 branches 命名空间内创建一个分支：

$ git checkout -b branches/permanent
Switched to a new branch 'branches/permanent'
$ vi Makefile
$ git commit -am 'A permanent change'
$ git push origin branches/permanent
To hg::/tmp/hello
 * [new branch]      branches/permanent -> branches/permanent

下面是 Mercurial 这边的样子：

$ hg branches
permanent                      7:a4529d07aad4
develop                        6:8f65e5e02793
default                        5:bd5ac26f11f9 (inactive)
$ hg log -G
o  changeset:   7:a4529d07aad4
|  branch:      permanent
|  tag:         tip
|  parent:      5:bd5ac26f11f9
|  user:        Ben Straub <ben@straub.cc>
|  date:        Thu Aug 14 20:21:09 2014 -0700
|  summary:     A permanent change
|
| @  changeset:   6:8f65e5e02793
|/   branch:      develop
|    user:        Ben Straub <ben@straub.cc>
|    date:        Thu Aug 14 20:06:38 2014 -0700
|    summary:     More documentation
|
o    changeset:   5:bd5ac26f11f9
|\   bookmark:    featureA
| |  parent:      4:0434aaa6b91f
| |  parent:      2:f098c7f45c4f
| |  user:        Ben Straub <ben@straub.cc>
| |  date:        Thu Aug 14 20:02:21 2014 -0700
| |  summary:     Merge remote-tracking branch 'origin/master'
[...]

分支名字 “permanent” 记录在序号 7 的变更集中。

在 Git 这边，对于其中任何一种风格的分支的工作都是相同的：仅仅是正常做的检出、提交、抓取、合并、拉取与推送。 还有需要知道的一件事情是 Mercurial 不支持重写历史，只允许添加历史。 下面是我们的 Mercurial 仓库在交互式的变基与强制推送后的样子：

$ hg log --style compact -G
o  10[tip]   99611176cbc9   2014-08-14 20:21 -0700   ben
|    A permanent change
|
o  9   f23e12f939c3   2014-08-14 20:01 -0700   ben
|    Add some documentation
|
o  8:1   c16971d33922   2014-08-14 20:00 -0700   ben
|    goodbye
|
| o  7:5   a4529d07aad4   2014-08-14 20:21 -0700   ben
| |    A permanent change
| |
| | @  6   8f65e5e02793   2014-08-14 20:06 -0700   ben
| |/     More documentation
| |
| o    5[featureA]:4,2   bd5ac26f11f9   2014-08-14 20:02 -0700   ben
| |\     Merge remote-tracking branch 'origin/master'
| | |
| | o  4   0434aaa6b91f   2014-08-14 20:01 -0700   ben
| | |    update makefile
| | |
+---o  3:1   318914536c86   2014-08-14 20:00 -0700   ben
| |      goodbye
| |
| o  2   f098c7f45c4f   2014-08-14 20:01 -0700   ben
|/     Add some documentation
|
o  1   82e55d328c8c   2005-08-26 01:21 -0700   mpm
|    Create a makefile
|
o  0   0a04b987be5a   2005-08-26 01:20 -0700   mpm
     Create a standard "hello, world" program

变更集 8、9 与 10 已经被创建出来并且属于 permanent 分支，但是旧的变更集依然在那里。 这会让使用 Mercurial 的团队成员非常困惑，所以要避免这种行为。

Mercurial 总结

Git 与 Mercurial 如此相似，以至于跨这两个系统进行工作十分流畅。 如果能注意避免改变在你机器上的历史（就像通常建议的那样），你甚至并不会察觉到另一端是 Mercurial。

Git 与 Perforce

在企业环境中 Perforce 是非常流行的版本管理系统。 它大概起始于 1995 年，这使它成为了本章中介绍的最古老的系统。 就其本身而言，它设计时带有当时时代的局限性；它假定你始终连接到一个单独的中央服务器，本地磁盘只保存一个版本。 诚然，它的功能与限制适合几个特定的问题，但实际上，在很多情况下，将使用 Perforce 的项目换做使用 Git 会更好。

如果你决定混合使用 Perforce 与 Git 这里有两种选择。 第一个我们要介绍的是 Perforce 官方制作的 “Git Fusion” 桥接，它可以将 Perforce 仓库中的子树表示为一个可读写的 Git 仓库。 第二个是 git-p4，一个客户端桥接允许你将 Git 作为 Perforce 的客户端使用，而不用在 Perforce 服务器上做任何重新的配置。

Git Fusion

Perforce 提供了一个叫作 Git Fusion 的产品（可在 http://www.perforce.com/git-fusion 获得），它将会在服务器这边同步 Perforce 服务器与 Git 仓库。

设置

针对我们的例子，我们将会使用最简单的方式安装 Git Fusion：下载一个虚拟机来运行 Perforce 守护进程与 Git Fusion。 可以从 http://www.perforce.com/downloads/Perforce/20-User 获得虚拟机镜像，下载完成后将它导入到你最爱的虚拟机软件中（我们将会使用 VirtualBox）。

在第一次启动机器后，它会询问你自定义三个 Linux 用户（root、perforce 与 git）的密码， 并且提供一个实例名字来区分在同一网络下不同的安装。当那些都完成后，将会看到这样：

Figure 146. Git Fusion 虚拟机启动屏幕。

应当注意显示在这儿的 IP 地址，我们将会在后面用到。 接下来，我们将会创建一个 Perforce 用户。 选择底部的 “Login” 选项并按下回车（或者用 SSH 连接到这台机器），然后登录为 root。 然后使用这些命令创建一个用户：

$ p4 -p localhost:1666 -u super user -f john
$ p4 -p localhost:1666 -u john passwd
$ exit

第一个命令将会打开一个 VI 编辑器来自定义用户，但是可以通过输入 :wq 并回车来接受默认选项。 第二个命令将会提示输入密码两次。 这就是所有我们要通过终端提示符做的事情，所以现在可以退出当前会话了。

接下来要做的事就是告诉 Git 不要验证 SSL 证书。 Git Fusion 镜像内置一个证书，但是域名并不匹配你的虚拟主机的 IP 地址，所以 Git 会拒绝 HTTPS 连接。 如果要进行永久安装，查阅 Perforce Git Fusion 手册来安装一个不同的证书；然而，对于我们这个例子来说，这已经足够了。

$ export GIT_SSL_NO_VERIFY=true

现在我们可以测试所有东西是不是正常工作。

$ git clone https://10.0.1.254/Talkhouse
Cloning into 'Talkhouse'...
Username for 'https://10.0.1.254': john
Password for 'https://john@10.0.1.254':
remote: Counting objects: 630, done.
remote: Compressing objects: 100% (581/581), done.
remote: Total 630 (delta 172), reused 0 (delta 0)
Receiving objects: 100% (630/630), 1.22 MiB | 0 bytes/s, done.
Resolving deltas: 100% (172/172), done.
Checking connectivity... done.

虚拟机镜像自带一个可以克隆的样例项目。 这里我们会使用之前创建的 john 用户，通过 HTTPS 进行克隆；Git 询问此次连接的凭证，但是凭证缓存会允许我们跳过这步之后的任意后续请求。

FUSION 配置

一旦安装了 Git Fusion，你会想要调整配置。 使用你最爱的 Perforce 客户端做这件事实际上相当容易；只需要映射 Perforce 服务器上的 //.git-fusion 目录到你的工作空间。 文件结构看起来像这样：

$ tree
.
├── objects
│   ├── repos
│   │   └── [...]
│   └── trees
│       └── [...]
│
├── p4gf_config
├── repos
│   └── Talkhouse
│       └── p4gf_config
└── users
    └── p4gf_usermap

498 directories, 287 files

objects 目录被 Git Fusion 内部用来双向映射 Perforce 对象与 Git 对象，你不必弄乱那儿的任何东西。 在这个目录中有一个全局的 p4gf_config 文件，每个仓库中也会有一份——这些配置文件决定了 Git Fusion 的行为。 让我们看一下根目录下的文件：

[repo-creation]
charset = utf8

[git-to-perforce]
change-owner = author
enable-git-branch-creation = yes
enable-swarm-reviews = yes
enable-git-merge-commits = yes
enable-git-submodules = yes
preflight-commit = none
ignore-author-permissions = no
read-permission-check = none
git-merge-avoidance-after-change-num = 12107

[perforce-to-git]
http-url = none
ssh-url = none

[@features]
imports = False
chunked-push = False
matrix2 = False
parallel-push = False

[authentication]
email-case-sensitivity = no

这里我们并不会深入介绍这些选项的含义，但是要注意这是一个 INI 格式的文本文件，就像 Git 的配置。 这个文件指定了全局选项，但它可以被仓库特定的配置文件覆盖，像是 repos/Talkhouse/p4gf_config。 如果打开这个文件，你会看到有一些与全局默认不同设置的 [@repo] 区块。 你也会看到像下面这样的区块：

[Talkhouse-master]
git-branch-name = master
view = //depot/Talkhouse/main-dev/... ...

这是一个 Perforce 分支与一个 Git 分支的映射。 这个区块可以被命名成你喜欢的名字，只要保证名字是唯一的即可。 git-branch-name 允许你将在 Git 下显得笨重的仓库路径转换为更友好的名字。 view 选项使用标准视图映射语法控制 Perforce 文件如何映射到 Git 仓库。 可以指定一个以上的映射，就像下面的例子：

[multi-project-mapping]
git-branch-name = master
view = //depot/project1/main/... project1/...
       //depot/project2/mainline/... project2/...

通过这种方式，如果正常工作空间映射包含对目录结构的修改，可以将其复制为一个 Git 仓库。

最后一个我们讨论的文件是 users/p4gf_usermap，它将 Perforce 用户映射到 Git 用户，但你可能不会需要它。 当从一个 Perforce 变更集转换为一个 Git 提交时，Git Fusion 的默认行为是去查找 Perforce 用户，然后把邮箱地址与全名存储在 Git 的 author/commiter 字段中。 当反过来转换时，默认的行为是根据存储在 Git 提交中 author 字段中的邮箱地址来查找 Perforce 用户，然后以该用户提交变更集（以及权限的应用）。 大多数情况下，这个行为工作得很好，但是考虑下面的映射文件：

john john@example.com "John Doe"
john johnny@appleseed.net "John Doe"
bob employeeX@example.com "Anon X. Mouse"
joe employeeY@example.com "Anon Y. Mouse"

每一行的格式都是 <user> <email> "<full name>"，创建了一个单独的用户映射。 前两行映射不同的邮箱地址到同一个 Perforce 用户账户。 当使用几个不同的邮箱地址（或改变邮箱地址）生成 Git 提交并且想要让他们映射到同一个 Perforce 用户时这会很有用。 当从一个 Perforce 变更集创建一个 Git 提交时，第一个匹配 Perforce 用户的行会被用作 Git 作者信息。

最后两行从创建的 Git 提交中掩盖了 Bob 与 Joe 的真实名字与邮箱地址。 当你想要将一个内部项目开源，但不想将你的雇员目录公布到全世界时这很不错。 注意邮箱地址与全名需要是唯一的，除非想要所有的 Git 提交都属于一个虚构的作者。

工作流程

Perforce Git Fusion 是在 Perforce 与 Git 版本控制间双向的桥接。 让我们看一下在 Git 这边工作是什么样的感觉。 假定我们在 “Jam” 项目中使用上述的配置文件映射了，可以这样克隆：

$ git clone https://10.0.1.254/Jam
Cloning into 'Jam'...
Username for 'https://10.0.1.254': john
Password for 'https://john@10.0.1.254':
remote: Counting objects: 2070, done.
remote: Compressing objects: 100% (1704/1704), done.
Receiving objects: 100% (2070/2070), 1.21 MiB | 0 bytes/s, done.
remote: Total 2070 (delta 1242), reused 0 (delta 0)
Resolving deltas: 100% (1242/1242), done.
Checking connectivity... done.
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/origin/rel2.1
$ git log --oneline --decorate --graph --all
* 0a38c33 (origin/rel2.1) Create Jam 2.1 release branch.
| * d254865 (HEAD, origin/master, origin/HEAD, master) Upgrade to latest metrowerks on Beos -- the Intel one.
| * bd2f54a Put in fix for jam's NT handle leak.
| * c0f29e7 Fix URL in a jam doc
| * cc644ac Radstone's lynx port.
[...]

当首次这样做时，会花费一些时间。 这里发生的是 Git Fusion 会将在 Perforce 历史中所有合适的变更集转换为 Git 提交。 这发生在服务器端本地，所以会相当快，但是如果有很多历史，那么它还是会花费一些时间。 后来的抓取会做增量转换，所以会感觉更像 Git 的本地速度。

如你所见，我们的仓库看起来像之前使用过的任何一个 Git 仓库了。 这里有三个分支，Git 已经帮助创建了一个跟踪 origin/master 的本地 master 分支。 让我们做一些工作，创建几个新提交：

# ...
$ git log --oneline --decorate --graph --all
* cfd46ab (HEAD, master) Add documentation for new feature
* a730d77 Whitespace
* d254865 (origin/master, origin/HEAD) Upgrade to latest metrowerks on Beos -- the Intel one.
* bd2f54a Put in fix for jam's NT handle leak.
[...]

我们有两个新提交。 现在我们检查下是否有其他人在工作：

$ git fetch
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 2), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://10.0.1.254/Jam
   d254865..6afeb15  master     -> origin/master
$ git log --oneline --decorate --graph --all
* 6afeb15 (origin/master, origin/HEAD) Update copyright
| * cfd46ab (HEAD, master) Add documentation for new feature
| * a730d77 Whitespace
|/
* d254865 Upgrade to latest metrowerks on Beos -- the Intel one.
* bd2f54a Put in fix for jam's NT handle leak.
[...]

看起来有人在工作！ 从这个视图来看你并不知道这点，但是 6afeb15 提交确实是使用 Perforce 客户端创建的。 从 Git 的视角看它仅仅只是另一个提交，准确地说是一个点。 让我们看看 Perforce 服务器如何处理一个合并提交：

$ git merge origin/master
Auto-merging README
Merge made by the 'recursive' strategy.
 README | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git push
Counting objects: 9, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (9/9), done.
Writing objects: 100% (9/9), 917 bytes | 0 bytes/s, done.
Total 9 (delta 6), reused 0 (delta 0)
remote: Perforce: 100% (3/3) Loading commit tree into memory...
remote: Perforce: 100% (5/5) Finding child commits...
remote: Perforce: Running git fast-export...
remote: Perforce: 100% (3/3) Checking commits...
remote: Processing will continue even if connection is closed.
remote: Perforce: 100% (3/3) Copying changelists...
remote: Perforce: Submitting new Git commit objects to Perforce: 4
To https://10.0.1.254/Jam
   6afeb15..89cba2b  master -> master

Git 认为它成功了。 让我们从 Perforce 的视角看一下 README 文件的历史，使用 p4v 的版本图功能。

Figure 147. Git 推送后的 Perforce 版本图

如果你在之前从未看过这个视图，它似乎让人困惑，但是它显示出了作为 Git 历史图形化查看器相同的概念。 我们正在查看 README 文件的历史，所以左上角的目录树只显示那个文件在不同分支的样子。 右上方，我们有不同版本文件关系的可视图，这个可视图的全局视图在右下方。 视图中剩余的部分显示出选择版本的详细信息（在这个例子中是 2）

还要注意的一件事是这个图看起来很像 Git 历史中的图。 Perforce 没有存储 1 和 2 提交的命名分支，所以它在 .git-fusion 目录中生成了一个 “anonymous” 分支来保存它。 这也会在 Git 命名分支不对应 Perforce 命名分支时发生（稍后你可以使用配置文件来映射它们到 Perforce 分支）。

这些大多数发生在后台，但是最终结果是团队中的一个人可以使用 Git，另一个可以使用 Perforce，而所有人都不知道其他人的选择。

GIT-FUSION 总结

如果你有（或者能获得）接触你的 Perforce 服务器的权限，那么 Git Fusion 是使 Git 与 Perforce 互相交流的很好的方法。 这里包含了一点配置，但是学习曲线并不是很陡峭。 这是本章中其中一个不会出现无法使用 Git 全部能力的警告的章节。 这并不是说扔给 Perforce 任何东西都会高兴——如果你尝试重写已经推送的历史，Git Fusion 会拒绝它——虽然 Git Fusion 尽力让你感觉是原生的。 你甚至可以使用 Git 子模块（尽管它们对 Perforce 用户看起来很奇怪），合并分支（在 Perforce 这边会被记录了一次整合）。

如果不能说服你的服务器管理员设置 Git Fusion，依然有一种方式来一起使用这两个工具。

Git-p4

Git-p4 是 Git 与 Perforce 之间的双向桥接。 它完全运行在你的 Git 仓库内，所以你不需要任何访问 Perforce 服务器的权限（当然除了用户验证）。 Git-p4 并不像 Git Fusion 一样灵活或完整，但是它允许你在无需修改服务器环境的情况下，做大部分想做的事情。

Note
	

为了与 git-p4 一起工作需要在你的 PATH 环境变量中的某个目录中有 p4 工具。 在写这篇文章的时候，它可以在 http://www.perforce.com/downloads/Perforce/20-User 免费获得。

设置

出于演示的目的，我们将会从上面演示的 Git Fusion OVA 运行 Perforce 服务器，但是我们会绕过 Git Fusion 服务器然后直接进行 Perforce 版本管理。

为了使用 p4 命令行客户端（git-p4 依赖项），你需要设置两个环境变量：

$ export P4PORT=10.0.1.254:1666
$ export P4USER=john
开始

像在 Git 中的任何事情一样，第一个命令就是克隆：

$ git p4 clone //depot/www/live www-shallow
Importing from //depot/www/live into www-shallow
Initialized empty Git repository in /private/tmp/www-shallow/.git/
Doing initial import of //depot/www/live/ from revision #head into refs/remotes/p4/master

这样会创建出一种在 Git 中名为 “shallow” 的克隆；只有最新版本的 Perforce 被导入至 Git；记住，Perforce 并未被设计成给每一个用户一个版本。 使用 Git 作为 Perforce 客户端这样就足够了，但是为了其他目的的话这样可能不够。

完成之后，我们就有一个全功能的 Git 仓库：

$ cd myproject
$ git log --oneline --all --graph --decorate
* 70eaf78 (HEAD, p4/master, p4/HEAD, master) Initial import of //depot/www/live/ from the state at revision #head

注意有一个 “p4” 远程代表 Perforce 服务器，但是其他东西看起来就像是标准的克隆。 实际上，这有一点误导：其实远程仓库并不存在。

$ git remote -v

在当前仓库中并不存在任何远程仓库。 Git-p4 创建了一些引用来代表服务器的状态，它们看起来类似 git log 显示的远程引用，但是它们并不被 Git 本身管理，并且你无法推送它们。

工作流程

好了，让我们开始一些工作。 假设你已经在一个非常重要的功能上做了一些工作，然后准备好将它展示给团队中的其他人。

$ git log --oneline --all --graph --decorate
* 018467c (HEAD, master) Change page title
* c0fb617 Update link
* 70eaf78 (p4/master, p4/HEAD) Initial import of //depot/www/live/ from the state at revision #head

我们已经生成了两次新提交并已准备好推送它们到 Perforce 服务器。 让我们检查一下今天其他人是否做了一些工作：

$ git p4 sync
git p4 sync
Performing incremental import into refs/remotes/p4/master git branch
Depot paths: //depot/www/live/
Import destination: refs/remotes/p4/master
Importing revision 12142 (100%)
$ git log --oneline --all --graph --decorate
* 75cd059 (p4/master, p4/HEAD) Update copyright
| * 018467c (HEAD, master) Change page title
| * c0fb617 Update link
|/
* 70eaf78 Initial import of //depot/www/live/ from the state at revision #head

看起来他们做了，master 与 p4/master 已经分叉了。 Perforce 的分支系统一点也 不 像 Git 的，所以提交合并提交没有任何意义。 Git-p4 建议变基你的提交，它甚至提供了一个快捷方式来这样做：

$ git p4 rebase
Performing incremental import into refs/remotes/p4/master git branch
Depot paths: //depot/www/live/
No changes to import!
Rebasing the current branch onto remotes/p4/master
First, rewinding head to replay your work on top of it...
Applying: Update link
Applying: Change page title
 index.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

从输出中可能大概得知，git p4 rebase 是 git p4 sync 接着 git rebase p4/master 的快捷方式。 它比那更聪明一些，特别是工作在多个分支时，但这是一个进步。

现在我们的历史再次是线性的，我们准备好我们的改动贡献回 Perforce。 git p4 submit 命令会尝试在 p4/master 与 master 之间的每一个 Git 提交创建一个新的 Perforce 修订版本。 运行它会带我们到最爱的编辑器，文件内容看起来像是这样：

# A Perforce Change Specification.
#
#  Change:      The change number. 'new' on a new changelist.
#  Date:        The date this specification was last modified.
#  Client:      The client on which the changelist was created.  Read-only.
#  User:        The user who created the changelist.
#  Status:      Either 'pending' or 'submitted'. Read-only.
#  Type:        Either 'public' or 'restricted'. Default is 'public'.
#  Description: Comments about the changelist.  Required.
#  Jobs:        What opened jobs are to be closed by this changelist.
#               You may delete jobs from this list.  (New changelists only.)
#  Files:       What opened files from the default changelist are to be added
#               to this changelist.  You may delete files from this list.
#               (New changelists only.)

Change:  new

Client:  john_bens-mbp_8487

User: john

Status:  new

Description:
   Update link

Files:
   //depot/www/live/index.html   # edit

######## git author ben@straub.cc does not match your p4 account.
######## Use option --preserve-user to modify authorship.
######## Variable git-p4.skipUserNameCheck hides this message.
######## everything below this line is just the diff #######
--- //depot/www/live/index.html  2014-08-31 18:26:05.000000000 0000
+++ /Users/ben/john_bens-mbp_8487/john_bens-mbp_8487/depot/www/live/index.html   2014-08-31 18:26:05.000000000 0000
@@ -60,7 +60,7 @@
 </td>
 <td valign=top>
 Source and documentation for
-<a href="http://www.perforce.com/jam/jam.html">
+<a href="jam.html">
 Jam/MR</a>,
 a software build tool.
 </td>

除了结尾 git-p4 给我们的帮助性的提示，其它的与你运行 p4 submit 后看到的内容大多相同。 当提交或变更集需要一个名字时 git-p4 会分别尝试使用你的 Git 与 Perforce 设置，但是有些情况下你会想要覆盖默认行为。 例如，如果你正导入的提交是由没有 Perforce 用户账户的贡献者编写的，你还是会想要最终的变更集看起来像是他们写的（而不是你）。

Git-p4 帮助性地将 Git 的提交注释导入到 Perforce 变更集的内容，这样所有我们必须做的就是保存并退出，两次（每次一个提交）。 这会使 shell 输出看起来像这样：

$ git p4 submit
Perforce checkout for depot path //depot/www/live/ located at /Users/ben/john_bens-mbp_8487/john_bens-mbp_8487/depot/www/live/
Synchronizing p4 checkout...
... - file(s) up-to-date.
Applying dbac45b Update link
//depot/www/live/index.html#4 - opened for edit
Change 12143 created with 1 open file(s).
Submitting change 12143.
Locking 1 files ...
edit //depot/www/live/index.html#5
Change 12143 submitted.
Applying 905ec6a Change page title
//depot/www/live/index.html#5 - opened for edit
Change 12144 created with 1 open file(s).
Submitting change 12144.
Locking 1 files ...
edit //depot/www/live/index.html#6
Change 12144 submitted.
All commits applied!
Performing incremental import into refs/remotes/p4/master git branch
Depot paths: //depot/www/live/
Import destination: refs/remotes/p4/master
Importing revision 12144 (100%)
Rebasing the current branch onto remotes/p4/master
First, rewinding head to replay your work on top of it...
$ git log --oneline --all --graph --decorate
* 775a46f (HEAD, p4/master, p4/HEAD, master) Change page title
* 05f1ade Update link
* 75cd059 Update copyright
* 70eaf78 Initial import of //depot/www/live/ from the state at revision #head

结果恰如我们只是做了一次 git push，就像是应当实际发生的最接近的类比。

注意在这个过程中每一个 Git 提交都会被转化为一个 Perforce 变更集。 如果想要将它们压缩成为一个单独的提交，可以在运行 git p4 submit 前进行一次交互式变基。 同样注意的是所有被转化为变更集的提交的 SHA-1 校验和都改变了， 这是因为 git-p4 在每一个转化的提交增加一行到提交注释结尾：

$ git log -1
commit 775a46f630d8b46535fc9983cf3ebe6b9aa53145
Author: John Doe <john@example.com>
Date:   Sun Aug 31 10:31:44 2014 -0800

    Change page title

    [git-p4: depot-paths = "//depot/www/live/": change = 12144]

当尝试提交一次合并提交时会发生什么？ 让我们尝试一下。 这是我们可能会遇到的一种情形：

$ git log --oneline --all --graph --decorate
* 3be6fd8 (HEAD, master) Correct email address
*   1dcbf21 Merge remote-tracking branch 'p4/master'
|\
| * c4689fc (p4/master, p4/HEAD) Grammar fix
* | cbacd0a Table borders: yes please
* | b4959b6 Trademark
|/
* 775a46f Change page title
* 05f1ade Update link
* 75cd059 Update copyright
* 70eaf78 Initial import of //depot/www/live/ from the state at revision #head

Git 与 Perforce 历史在 775a46f 后分叉了。 Git 这边有两次提交，然后一次与 Perforce 头部的合并提交，还有另一个提交。 我们将会尝试提交这些到 Perforce 那边单独的变更集之上。 让我们看看如果尝试这样提交会发生什么：

$ git p4 submit -n
Perforce checkout for depot path //depot/www/live/ located at /Users/ben/john_bens-mbp_8487/john_bens-mbp_8487/depot/www/live/
Would synchronize p4 checkout in /Users/ben/john_bens-mbp_8487/john_bens-mbp_8487/depot/www/live/
Would apply
  b4959b6 Trademark
  cbacd0a Table borders: yes please
  3be6fd8 Correct email address

-n 标记是 --dry-run 的缩写，将会报告如果提交命令真的运行会发生什么。 这本例中，它看起来像是我们会创建三个 Perforce 变更集，对应着不在 Perforce 服务器端的三次非合并提交。 那听起来像是我们想要的，让我们看看它会如何做：

$ git p4 submit
[…]
$ git log --oneline --all --graph --decorate
* dadbd89 (HEAD, p4/master, p4/HEAD, master) Correct email address
* 1b79a80 Table borders: yes please
* 0097235 Trademark
* c4689fc Grammar fix
* 775a46f Change page title
* 05f1ade Update link
* 75cd059 Update copyright
* 70eaf78 Initial import of //depot/www/live/ from the state at revision #head

我们的历史变成线性了，就像在提交前刚刚变基过（实际上也是这样）。 这意味着你可以在 Git 这边自由地创建、工作、扔掉与合并分支而不用害怕你的历史会变得与 Perforce 不兼容。 如果你可以变基它，你就可以将它贡献到 Perforce 服务器。

分支

如果你的 Perforce 项目有多个分支，你并不会不走运；git-p4 可以以一种类似 Git 的方式来处理那种情况。 假定你的 Perforce 仓库平铺的时候像这样：

//depot
  └── project
      ├── main
      └── dev

并且假定你有一个 dev 分支，有一个视图规格像下面这样：

//depot/project/main/... //depot/project/dev/...

Git-p4 可以自动地检测到这种情形并做正确的事情：

$ git p4 clone --detect-branches //depot/project@all
Importing from //depot/project@all into project
Initialized empty Git repository in /private/tmp/project/.git/
Importing revision 20 (50%)
    Importing new branch project/dev

    Resuming with change 20
Importing revision 22 (100%)
Updated branches: main dev
$ cd project; git log --oneline --all --graph --decorate
* eae77ae (HEAD, p4/master, p4/HEAD, master) main
| * 10d55fb (p4/project/dev) dev
| * a43cfae Populate //depot/project/main/... //depot/project/dev/....
|/
* 2b83451 Project init

注意在仓库路径中的 “@all” 说明符；那会告诉 git-p4 不仅仅只是克隆那个子树最新的变更集，更包括那些路径未接触的所有变更集。 这有点类似于 Git 的克隆概念，但是如果你工作在一个具有很长历史的项目，那么它会花费一段时间。

--detect-branches 标记告诉 git-p4 使用 Perforce 的分支规范来映射到 Git 的引用中。 如果这些映射不在 Perforce 服务器中（使用 Perforce 的一种完美有效的方式），你可以告诉 git-p4 分支映射是什么，然后你会得到同样的结果：

$ git init project
Initialized empty Git repository in /tmp/project/.git/
$ cd project
$ git config git-p4.branchList main:dev
$ git clone --detect-branches //depot/project@all .

设置 git-p4.branchList 配置选项为 main:dev 告诉 git-p4 那个 “main” 与 “dev” 都是分支，第二个是第一个的子分支。

如果我们现在运行 git checkout -b dev p4/project/dev 并且做一些提交，在运行 git p4 submit 时 git-p4 会聪明地选择正确的分支。 不幸的是，git-p4 不能混用 shallow 克隆与多个分支；如果你有一个巨型项目并且想要同时工作在不止一个分支上，可能不得不针对每一个你想要提交的分支运行一次 git p4 clone。

为了创建与整合分支，你不得不使用一个 Perforce 客户端。 Git-p4 只能同步或提交已有分支，并且它一次只能做一个线性的变更集。 如果你在 Git 中合并两个分支并尝试提交新的变更集，所有这些会被记录为一串文件修改；关于哪个分支参与的元数据在整合中会丢失。

Git 与 Perforce 总结

Git-p4 将与 Perforce 服务器工作时使用 Git 工作流成为可能，并且它非常擅长这点。 然而，需要记住的重要一点是 Perforce 负责源头，而你只是在本地使用 Git。 在共享 Git 提交时要相当小心：如果你有一个其他人使用的远程仓库，不要在提交到 Perforce 服务器前推送任何提交。

如果想要为源码管理自由地混合使用 Perforce 与 Git 作为客户端，可以说服服务器管理员安装 Git Fusion，Git Fusion 使 Git 作为 Perforce 服务器的首级版本管理客户端。

9.2 Git 与其他系统 - 迁移到 Git
迁移到 Git

如果你现在有一个正在使用其他 VCS 的代码库，但是你已经决定开始使用 Git，必须通过某种方式将你的项目迁移至 Git。 这一部分会介绍一些通用系统的导入器，然后演示如何开发你自己定制的导入器。 你将会学习如何从几个大型专业应用的 SCM 系统中导入数据，不仅因为它们是大多数想要转换的用户正在使用的系统，也因为获取针对它们的高质量工具很容易。

Subversion

如果你阅读过前面关于 git svn 的章节，可以轻松地使用那些指令来 git svn clone 一个仓库，停止使用 Subversion 服务器，推送到一个新的 Git 服务器，然后就可以开始使用了。 如果你想要历史，可以从 Subversion 服务器上尽可能快地拉取数据来完成这件事（这可能会花费一些时间）。

然而，导入并不完美；因为花费太长时间了，你可能早已用其他方法完成导入操作。 导入产生的第一个问题就是作者信息。 在 Subversion 中，每一个人提交时都需要在系统中有一个用户，它会被记录在提交信息内。 在之前章节的例子中几个地方显示了 schacon，比如 blame 输出与 git svn log。 如果想要将上面的 Subversion 用户映射到一个更好的 Git 作者数据中，你需要一个 Subversion 用户到 Git 用户的映射。 创建一个 users.txt 的文件包含像下面这种格式的映射：

schacon = Scott Chacon <schacon@geemail.com>
selse = Someo Nelse <selse@geemail.com>

为了获得 SVN 使用的作者名字列表，可以运行这个：

$ svn log --xml --quiet | grep author | sort -u | \
  perl -pe 's/.*>(.*?)<.*/$1 = /'

这会将日志输出为 XML 格式，然后保留作者信息行、去除重复、去除 XML 标记。 很显然这只会在安装了 grep、sort 与 perl 的机器上运行。 然后，将输出重定向到你的 users.txt 文件中，这样就可以在每一个记录后面加入对应的 Git 用户数据。

Note
	

如果你在 Windows 上运行它，那么到这里就会遇到问题。微软提供了一些不错的建议和示例： https://docs.microsoft.com/en-us/azure/devops/repos/git/perform-migration-from-svn-to-git.

你可以将此文件提供给 git svn 来帮助它更加精确地映射作者数据。 也可以通过传递 --no-metadata 给 clone 与 init 命令，告诉 git svn 不要包括 Subversion 通常会导入的元数据。在导入过程中，Git 会在每个提交说明的元数据中生成一个 git-svn-id。

Note
	

当你想要将 Git 仓库中的提交镜像回原 SVN 仓库中时，需要保留元数据。 如果你不想在提交记录中同步它，请直接省略掉 --no-metadata 选项。

这会使你的 import 命令看起来像这样：

$ git svn clone http://my-project.googlecode.com/svn/ \
      --authors-file=users.txt --no-metadata --prefix "" -s my_project
$ cd my_project

现在在 my_project 目录中应当有了一个更好的 Subversion 导入。 并不像是下面这样的提交：

commit 37efa680e8473b615de980fa935944215428a35a
Author: schacon <schacon@4c93b258-373f-11de-be05-5f7a86268029>
Date:   Sun May 3 00:12:22 2009 +0000

    fixed install - go to trunk

    git-svn-id: https://my-project.googlecode.com/svn/trunk@94 4c93b258-373f-11de-
    be05-5f7a86268029

反而它们看起来像是这样：

commit 03a8785f44c8ea5cdb0e8834b7c8e6c469be2ff2
Author: Scott Chacon <schacon@geemail.com>
Date:   Sun May 3 00:12:22 2009 +0000

    fixed install - go to trunk

不仅是 Author 字段更好看了，git-svn-id 也不在了。

之后，你应当做一些导入后的清理工作。 第一步，你应当清理 git svn 设置的奇怪的引用。 首先移动标签，这样它们就是标签而不是奇怪的远程引用，然后你会移动剩余的分支这样它们就是本地的了。

为了将标签变为合适的 Git 标签，运行：

$ for t in $(git for-each-ref --format='%(refname:short)' refs/remotes/tags); do git tag ${t/tags\//} $t && git branch -D -r $t; done

这会使原来在 refs/remotes/tags/ 里的远程分支引用变成真正的（轻量）标签。

接下来，将 refs/remotes 下剩余的引用移动为本地分支：

$ for b in $(git for-each-ref --format='%(refname:short)' refs/remotes); do git branch $b refs/remotes/$b && git branch -D -r $b; done

你可能会看到一些额外的分支，这些分支的后缀是 @xxx (其中 xxx 是一个数字)，而在 Subversion 中你只会看到一个分支。这实际上是 Subversion 一个叫做“peg-revisions”的功能，Git在语法上没有与之对应的功能。因此， git svn 只是简单地将 SVN peg-revision 版本号添加到分支名称中，这同你在 SVN 中修改分支名称来定位一个分支的“peg-revision”是一样的。如果你对于 peg-revisions 完全不在乎，通过下面的命令可以轻易地移除他们：

$ for p in $(git for-each-ref --format='%(refname:short)' | grep @); do git branch -D $p; done

现在所有的旧分支都是真正的 Git 分支，并且所有的旧标签都是真正的 Git 标签。

还有最后一点东西需要清理。git svn 会创建一个名为 trunk 的额外分支，它对应于 Subversion 的默认分支，然而 trunk 引用和 master 指向同一个位置。 鉴于在 Git 中 master 最为常用，因此我们可以移除额外的分支：

$ git branch -d trunk

最后一件要做的事情是，将你的新 Git 服务器添加为远程仓库并推送到上面。 下面是一个将你的服务器添加为远程仓库的例子：

$ git remote add origin git@my-git-server:myrepository.git

因为想要上传所有分支与标签，你现在可以运行：

$ git push origin --all
$ git push origin --tags

通过以上漂亮、干净地导入操作，你的所有分支与标签都应该在新 Git 服务器上。

Mercurial

因为 Mercurial 与 Git 在表示版本时有着非常相似的模型，也因为 Git 拥有更加强大的灵活性，将一个仓库从 Mercurial 转换到 Git 是相当直接的，使用一个叫作“hg-fast-export”的工具，需要从这里拷贝一份：

$ git clone https://github.com/frej/fast-export.git

转换的第一步就是要先得到想要转换的 Mercurial 仓库的完整克隆：

$ hg clone <remote repo URL> /tmp/hg-repo

下一步就是创建一个作者映射文件。 Mercurial 对放入到变更集作者字段的内容比 Git 更宽容一些，所以这是一个清理的好机会。 只需要用到 bash 终端下的一行命令：

$ cd /tmp/hg-repo
$ hg log | grep user: | sort | uniq | sed 's/user: *//' > ../authors

这会花费几秒钟，具体要看项目提交历史有多少，最终 /tmp/authors 文件看起来会像这样：

bob
bob@localhost
bob <bob@company.com>
bob jones <bob <AT> company <DOT> com>
Bob Jones <bob@company.com>
Joe Smith <joe@company.com>

在这个例子中，同一个人（Bob）使用不同的名字创建变更集，其中一个实际上是正确的， 另一个完全不符合 Git 提交的规范。hg-fast-export 通过对每一行应用规则 "<input>"="<output>" ，将 <input> 映射到 <output> 来修正这个问题。 在 <input> 和 <output> 字符串中，所有 Python 的 string_escape 支持的转义序列都会被解释。如果作者映射文件中并不包含匹配的 <input>， 那么该作者将原封不动地被发送到 Git。 如果所有的用户名看起来都是正确的，那我们根本就不需要这个文件。 在本例中，我们会使文件看起来像这样：

"bob"="Bob Jones <bob@company.com>"
"bob@localhost"="Bob Jones <bob@company.com>"
"bob <bob@company.com>"="Bob Jones <bob@company.com>"
"bob jones <bob <AT> company <DOT> com>"="Bob Jones <bob@company.com>"

当分支和标签 Mercurial 中的名字在 Git 中不允许时，这种映射文件也可以用来重命名它们。

下一步是创建一个新的 Git 仓库，然后运行导出脚本：

$ git init /tmp/converted
$ cd /tmp/converted
$ /tmp/fast-export/hg-fast-export.sh -r /tmp/hg-repo -A /tmp/authors

-r 选项告诉 hg-fast-export 去哪里寻找我们想要转换的 Mercurial 仓库，-A 标记告诉它在哪找到作者映射文件（分支和标签的映射文件分别通过 -B 和 -T 选项来指定）。 这个脚本会分析 Mercurial 变更集然后将它们转换成 Git“fast-import”功能（我们将在之后详细讨论）需要的脚本。 这会花一点时间（尽管它比通过网格 更 快），输出相当的冗长：

$ /tmp/fast-export/hg-fast-export.sh -r /tmp/hg-repo -A /tmp/authors
Loaded 4 authors
master: Exporting full revision 1/22208 with 13/0/0 added/changed/removed files
master: Exporting simple delta revision 2/22208 with 1/1/0 added/changed/removed files
master: Exporting simple delta revision 3/22208 with 0/1/0 added/changed/removed files
[…]
master: Exporting simple delta revision 22206/22208 with 0/4/0 added/changed/removed files
master: Exporting simple delta revision 22207/22208 with 0/2/0 added/changed/removed files
master: Exporting thorough delta revision 22208/22208 with 3/213/0 added/changed/removed files
Exporting tag [0.4c] at [hg r9] [git :10]
Exporting tag [0.4d] at [hg r16] [git :17]
[…]
Exporting tag [3.1-rc] at [hg r21926] [git :21927]
Exporting tag [3.1] at [hg r21973] [git :21974]
Issued 22315 commands
git-fast-import statistics:
---------------------------------------------------------------------
Alloc'd objects:     120000
Total objects:       115032 (    208171 duplicates                  )
      blobs  :        40504 (    205320 duplicates      26117 deltas of      39602 attempts)
      trees  :        52320 (      2851 duplicates      47467 deltas of      47599 attempts)
      commits:        22208 (         0 duplicates          0 deltas of          0 attempts)
      tags   :            0 (         0 duplicates          0 deltas of          0 attempts)
Total branches:         109 (         2 loads     )
      marks:        1048576 (     22208 unique    )
      atoms:           1952
Memory total:          7860 KiB
       pools:          2235 KiB
     objects:          5625 KiB
---------------------------------------------------------------------
pack_report: getpagesize()            =       4096
pack_report: core.packedGitWindowSize = 1073741824
pack_report: core.packedGitLimit      = 8589934592
pack_report: pack_used_ctr            =      90430
pack_report: pack_mmap_calls          =      46771
pack_report: pack_open_windows        =          1 /          1
pack_report: pack_mapped              =  340852700 /  340852700
---------------------------------------------------------------------

$ git shortlog -sn
   369  Bob Jones
   365  Joe Smith

那看起来非常好。 所有 Mercurial 标签都已被转换成 Git 标签，Mercurial 分支与书签都被转换成 Git 分支。 现在已经准备好将仓库推送到新的服务器那边：

$ git remote add origin git@my-git-server:myrepository.git
$ git push origin --all
Perforce

下一个将要看到导入的系统是 Perforce。 就像我们之前讨论过的，有两种方式让 Git 与 Perforce 互相通信：git-p4 与 Perforce Git Fusion。

Perforce Git Fusion

Git Fusion 使这个过程毫无痛苦。 只需要使用在 Git Fusion 中讨论过的配置文件来配置你的项目设置、用户映射与分支，然后克隆整个仓库。 Git Fusion 让你处在一个看起来像是原生 Git 仓库的环境中，如果愿意的话你可以随时将它推送到一个原生 Git 托管中。 如果你喜欢的话甚至可以使用 Perforce 作为你的 Git 托管。

Git-p4

Git-p4 也可以作为一个导入工具。 作为例子，我们将从 Perforce 公开仓库中导入 Jam 项目。 为了设置客户端，必须导出 P4PORT 环境变量指向 Perforce 仓库：

$ export P4PORT=public.perforce.com:1666
Note
	

为了继续后续步骤，需要连接到 Perforce 仓库。 在我们的例子中将会使用在 public.perforce.com 的公开仓库，但是你可以使用任何你有权限的仓库。

运行 git p4 clone 命令从 Perforce 服务器导入 Jam 项目，提供仓库、项目路径与你想要存放导入项目的路径：

$ git-p4 clone //guest/perforce_software/jam@all p4import
Importing from //guest/perforce_software/jam@all into p4import
Initialized empty Git repository in /private/tmp/p4import/.git/
Import destination: refs/remotes/p4/master
Importing revision 9957 (100%)

这个特定的项目只有一个分支，但是如果你在分支视图（或者说一些目录）中配置了一些分支，你可以将 --detect-branches 选项传递给 git p4 clone 来导入项目的所有分支。 查看 分支 来了解关于这点的更多信息。

此时你几乎已经完成了。 如果进入 p4import 目录中并运行 git log，可以看到你的导入工作：

$ git log -2
commit e5da1c909e5db3036475419f6379f2c73710c4e6
Author: giles <giles@giles@perforce.com>
Date:   Wed Feb 8 03:13:27 2012 -0800

    Correction to line 355; change </UL> to </OL>.

    [git-p4: depot-paths = "//public/jam/src/": change = 8068]

commit aa21359a0a135dda85c50a7f7cf249e4f7b8fd98
Author: kwirth <kwirth@perforce.com>
Date:   Tue Jul 7 01:35:51 2009 -0800

    Fix spelling error on Jam doc page (cummulative -> cumulative).

    [git-p4: depot-paths = "//public/jam/src/": change = 7304]

你可以看到 git-p4 在每一个提交里都留下了一个标识符。 如果之后想要引用 Perforce 的修改序号的话，标识符保留在那里也是可以的。 然而，如果想要移除标识符，现在正是这么做的时候——在你开始在新仓库中工作之前。 可以使用 git filter-branch 将全部标识符移除。

$ git filter-branch --msg-filter 'sed -e "/^\[git-p4:/d"'
Rewrite e5da1c909e5db3036475419f6379f2c73710c4e6 (125/125)
Ref 'refs/heads/master' was rewritten

如果运行 git log，你会看到所有提交的 SHA-1 校验和都改变了，但是提交信息中不再有 git-p4 字符串了：

$ git log -2
commit b17341801ed838d97f7800a54a6f9b95750839b7
Author: giles <giles@giles@perforce.com>
Date:   Wed Feb 8 03:13:27 2012 -0800

    Correction to line 355; change </UL> to </OL>.

commit 3e68c2e26cd89cb983eb52c024ecdfba1d6b3fff
Author: kwirth <kwirth@perforce.com>
Date:   Tue Jul 7 01:35:51 2009 -0800

    Fix spelling error on Jam doc page (cummulative -> cumulative).

现在导入已经准备好推送到你的新 Git 服务器上了。

一个自定义的导入器

如果你的系统不是上述中的任何一个，你需要在线查找一个导入器——针对许多其他系统有很多高质量的导入器， 包括 CVS、Clear Case、Visual Source Safe，甚至是一个档案目录。 如果没有一个工具适合你，需要一个不知名的工具，或者需要更大自由度的自定义导入过程，应当使用 git fast-import。 这个命令从标准输入中读取简单指令来写入特定的 Git 数据。 通过这种方式创建 Git 对象比运行原始 Git 命令或直接写入原始对象 （查看 Git 内部原理 了解更多内容）更容易些。 通过这种方式你可以编写导入脚本，从你要导入的系统中读取必要数据，然后直接打印指令到标准输出。 然后可以运行这个程序并通过 git fast-import 重定向管道输出。

为了快速演示，我们会写一个简单的导入器。 假设你在 current 工作，有时候会备份你的项目到时间标签 back_YYYY_MM_DD 备份目录中，你想要将这些导入到 Git 中。 目录结构看起来是这样：

$ ls /opt/import_from
back_2014_01_02
back_2014_01_04
back_2014_01_14
back_2014_02_03
current

为了导入一个 Git 目录，需要了解 Git 如何存储它的数据。 你可能记得，Git 在底层存储指向内容快照的提交对象的链表。 所有要做的就是告诉 fast-import 哪些内容是快照，哪个提交数据指向它们，以及它们进入的顺序。 你的策略是一次访问一个快照，然后用每个目录中的内容创建提交，并且将每一个提交与前一个连接起来。

如同我们在 使用强制策略的一个例子 里做的， 我们将会使用 Ruby 写这个，因为它是我们平常工作中使用的并且它很容易读懂。 可以使用任何你熟悉的东西来非常轻松地写这个例子——它只需要将合适的信息打印到 标准输出。 然而，如果你在 Windows 上，这意味着需要特别注意不要引入回车符到行尾—— git fast-import 非常特别地只接受换行符（LF）而不是 Windows 使用的回车换行符（CRLF）。

现在开始，需要进入目标目录中并识别每一个子目录，每一个都是你要导入为提交的快照。 要进入到每个子目录中并为导出它打印必要的命令。 基本主循环像这个样子：

last_mark = nil

# loop through the directories
Dir.chdir(ARGV[0]) do
  Dir.glob("*").each do |dir|
    next if File.file?(dir)

    # move into the target directory
    Dir.chdir(dir) do
      last_mark = print_export(dir, last_mark)
    end
  end
end

在每个目录内运行 print_export，将会拿到清单并标记之前的快照，然后返回清单并标记现在的快照；通过这种方式，可以将它们合适地连接在一起。 “标记”是一个给提交标识符的 fast-import 术语；当你创建提交，为每一个提交赋予一个标记来将它与其他提交连接在一起。 这样，在你的 print_export 方法中第一件要做的事就是从目录名字生成一个标记：

mark = convert_dir_to_mark(dir)

可以创建一个目录的数组并使用索引做为标记，因为标记必须是一个整数。 方法类似这样：

$marks = []
def convert_dir_to_mark(dir)
  if !$marks.include?(dir)
    $marks << dir
  end
  ($marks.index(dir) + 1).to_s
end

既然有一个整数代表你的提交，那还要给提交元数据一个日期。 因为目录名字表达了日期，所以你将会从中解析出日期。 你的 print_export 文件的下一行是：

date = convert_dir_to_date(dir)

convert_dir_to_date 定义为：

def convert_dir_to_date(dir)
  if dir == 'current'
    return Time.now().to_i
  else
    dir = dir.gsub('back_', '')
    (year, month, day) = dir.split('_')
    return Time.local(year, month, day).to_i
  end
end

那会返回每一个目录日期的整数。 最后一项每个提交需要的元数据是提交者信息，它将会被硬编码在全局变量中：

$author = 'John Doe <john@example.com>'

现在准备开始为你的导入器打印出提交数据。 初始信息声明定义了一个提交对象与它所在的分支，紧接着一个你生成的标记、提交者信息与提交信息、然后是一个之前的提交，如果它存在的话。 代码看起来像这样：

# print the import information
puts 'commit refs/heads/master'
puts 'mark :' + mark
puts "committer #{$author} #{date} -0700"
export_data('imported from ' + dir)
puts 'from :' + last_mark if last_mark

我们将硬编码时区信息（-0700），因为这样很容易。 如果从其他系统导入，必须指定为一个偏移的时区。 提交信息必须指定为特殊的格式：

data (size)\n(contents)

这个格式包括文本数据、将要读取数据的大小、一个换行符、最终的数据。 因为之后还需要为文件内容指定相同的数据格式，你需要创建一个帮助函数，export_data：

def export_data(string)
  print "data #{string.size}\n#{string}"
end

剩下的工作就是指定每一个快照的文件内容。 这很轻松，因为每一个目录都是一个快照——可以在目录中的每一个文件内容后打印 deleteall 命令。 Git 将会适当地记录每一个快照：

puts 'deleteall'
Dir.glob("**/*").each do |file|
  next if !File.file?(file)
  inline_data(file)
end

注意：因为大多数系统认为他们的版本是从一个提交变化到另一个提交，fast-import 也可以为每一个提交执行命令来指定哪些文件是添加的、删除的或修改的与新内容是哪些。 可以计算快照间的不同并只提供这些数据，但是这样做会很复杂——也可以把所有数据给 Git 然后让它为你指出来。 如果这更适合你的数据，查阅 fast-import man 帮助页来了解如何以这种方式提供你的数据。

这种列出新文件内容或用新内容指定修改文件的格式如同下面的内容：

M 644 inline path/to/file
data (size)
(file contents)

这里，644 是模式（如果你有可执行文件，反而你需要检测并指定 755），inline 表示将会立即把内容放在本行之后。 你的 inline_data 方法看起来像这样：

def inline_data(file, code = 'M', mode = '644')
  content = File.read(file)
  puts "#{code} #{mode} inline #{file}"
  export_data(content)
end

可以重用之前定义的 export_data 方法，因为它与你定义的提交信息数据的方法一样。

最后一件你需要做的是返回当前的标记以便它可以传给下一个迭代：

return mark
Note
	

如果在 Windows 上还需要确保增加一个额外步骤。 正如之前提到的，Windows 使用 CRLF 作为换行符而 git fast-import 只接受 LF。 为了修正这个问题使 git fast-import 正常工作，你需要告诉 ruby 使用 LF 代替 CRLF：

$stdout.binmode

就是这样。 这是全部的脚本：

#!/usr/bin/env ruby

$stdout.binmode
$author = "John Doe <john@example.com>"

$marks = []
def convert_dir_to_mark(dir)
    if !$marks.include?(dir)
        $marks << dir
    end
    ($marks.index(dir)+1).to_s
end

def convert_dir_to_date(dir)
    if dir == 'current'
        return Time.now().to_i
    else
        dir = dir.gsub('back_', '')
        (year, month, day) = dir.split('_')
        return Time.local(year, month, day).to_i
    end
end

def export_data(string)
    print "data #{string.size}\n#{string}"
end

def inline_data(file, code='M', mode='644')
    content = File.read(file)
    puts "#{code} #{mode} inline #{file}"
    export_data(content)
end

def print_export(dir, last_mark)
    date = convert_dir_to_date(dir)
    mark = convert_dir_to_mark(dir)

    puts 'commit refs/heads/master'
    puts "mark :#{mark}"
    puts "committer #{$author} #{date} -0700"
    export_data("imported from #{dir}")
    puts "from :#{last_mark}" if last_mark

    puts 'deleteall'
    Dir.glob("**/*").each do |file|
        next if !File.file?(file)
        inline_data(file)
    end
    mark
end

# Loop through the directories
last_mark = nil
Dir.chdir(ARGV[0]) do
    Dir.glob("*").each do |dir|
        next if File.file?(dir)

        # move into the target directory
        Dir.chdir(dir) do
            last_mark = print_export(dir, last_mark)
        end
    end
end

如果运行这个脚本，你会得到类似下面的内容：

$ ruby import.rb /opt/import_from
commit refs/heads/master
mark :1
committer John Doe <john@example.com> 1388649600 -0700
data 29
imported from back_2014_01_02deleteall
M 644 inline README.md
data 28
# Hello

This is my readme.
commit refs/heads/master
mark :2
committer John Doe <john@example.com> 1388822400 -0700
data 29
imported from back_2014_01_04from :1
deleteall
M 644 inline main.rb
data 34
#!/bin/env ruby

puts "Hey there"
M 644 inline README.md
(...)

为了运行导入器，将这些输出用管道重定向到你想要导入的 Git 目录中的 git fast-import。 可以创建一个新的目录并在其中运行 git init 作为开始，然后运行你的脚本：

$ git init
Initialized empty Git repository in /opt/import_to/.git/
$ ruby import.rb /opt/import_from | git fast-import
git-fast-import statistics:
---------------------------------------------------------------------
Alloc'd objects:       5000
Total objects:           13 (         6 duplicates                  )
      blobs  :            5 (         4 duplicates          3 deltas of          5 attempts)
      trees  :            4 (         1 duplicates          0 deltas of          4 attempts)
      commits:            4 (         1 duplicates          0 deltas of          0 attempts)
      tags   :            0 (         0 duplicates          0 deltas of          0 attempts)
Total branches:           1 (         1 loads     )
      marks:           1024 (         5 unique    )
      atoms:              2
Memory total:          2344 KiB
       pools:          2110 KiB
     objects:           234 KiB
---------------------------------------------------------------------
pack_report: getpagesize()            =       4096
pack_report: core.packedGitWindowSize = 1073741824
pack_report: core.packedGitLimit      = 8589934592
pack_report: pack_used_ctr            =         10
pack_report: pack_mmap_calls          =          5
pack_report: pack_open_windows        =          2 /          2
pack_report: pack_mapped              =       1457 /       1457
---------------------------------------------------------------------

正如你所看到的，当它成功完成时，它会给你一串关于它完成内容的统计。 这本例中，一共导入了 13 个对象、4 次提交到 1 个分支。 现在，可以运行 git log 来看一下你的新历史：

$ git log -2
commit 3caa046d4aac682a55867132ccdfbe0d3fdee498
Author: John Doe <john@example.com>
Date:   Tue Jul 29 19:39:04 2014 -0700

    imported from current

commit 4afc2b945d0d3c8cd00556fbe2e8224569dc9def
Author: John Doe <john@example.com>
Date:   Mon Feb 3 01:00:00 2014 -0700

    imported from back_2014_02_03

做得很好——一个漂亮、干净的 Git 仓库。 要注意的一点是并没有检出任何东西——一开始你的工作目录内并没有任何文件。 为了得到他们，你必须将分支重置到 master 所在的地方：

$ ls
$ git reset --hard master
HEAD is now at 3caa046 imported from current
$ ls
README.md main.rb

可以通过 fast-import 工具做很多事情——处理不同模式、二进制数据、多个分支与合并、标签、进度指示等等。 一些更复杂情形下的例子可以在 Git 源代码目录中的 contrib/fast-import 目录中找到。

9.3 Git 与其他系统 - 总结
总结

你会觉得将 Git 作为其他版本控制系统的客户端，或者在数据无损的情况下将几乎任何一个现有的仓库导入到 Git，都是一件很惬意的事。 在下一章，我们将要讲解 Git 的原始内部数据，如果需要的话你就可以加工每一个字节。

