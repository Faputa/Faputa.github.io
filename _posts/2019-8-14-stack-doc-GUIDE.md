---
layout: post
title: stack用户指南翻译
---

这是我对stack官网用户指南的翻译。  
GitHub源地址：[stack/GUIDE.md](https://github.com/commercialhaskell/stack/blob/master/doc/GUIDE.md)  
官网源地址：[User guide](https://docs.haskellstack.org/en/stable/GUIDE/)

---

<!-- <div class="hidden-warning"><a href="https://docs.haskellstack.org/"><img src="https://cdn.jsdelivr.net/gh/commercialhaskell/stack/doc/img/hidden-warning.svg"></a></div> -->

<!-- # User guide -->
# 用户指南

<!-- stack is a modern, cross-platform build tool for Haskell code. -->
stack是一个现代的，跨平台的Haskell代码构建工具。

<!-- This guide takes a new stack user through the typical workflows. This guide
will not teach Haskell or involve much code, and it requires no prior experience
with the Haskell packaging system or other build tools. -->
本指南为stack新用户说明典型的工作流程。本指南将不会教授Haskell或者涉及太多代码，并且不需要任何使用Haskell打包系统和构建工具的经验。

<!-- __NOTE__ This document is probably out of date in some places and
deserves a refresh. If you find this document helpful, please drop a
note on [issue #4252](https://github.com/commercialhaskell/stack/issues/4252). -->
__说明__ 此文档很可能在有的地方过时并且需要刷新。如果你觉得此文档有帮助的话，请在[issue #4252](https://github.com/commercialhaskell/stack/issues/4252)留下一个记录

<!-- ## Stack's functions -->
## Stack的功能

<!-- stack handles the management of your toolchain (including GHC — the Glasgow
Haskell Compiler — and, for Windows users, MSYS), building and registering
libraries, building build tool dependencies, and more. While it can use existing
tools on your system, stack has the capacity to be your one-stop shop for all
Haskell tooling you need. This guide will follow that stack-centric approach. -->
stack处理工具链的管理（包括GHC——Glasgow Haskell编译器，以及Windows用户的MSYS），构建和注册库，建立构建工具依赖，等等。而它可以使用你系统中现有的工具，stack有能力成为所有你所需的Haskell工具的一站式商店。本指南将遵循以stack为中心的方式。

<!-- ### What makes stack special? -->
### stack的特点

<!-- The primary stack design point is __reproducible builds__. If you run `stack build`
today, you should get the same result running `stack build` tomorrow.
There are some cases that can break that rule (changes in your operating system
configuration, for example), but, overall, stack follows this design philosophy
closely. To make this a simple process, stack uses curated package sets
called __snapshots__. -->
stack设计重点是 __可复现的构建__ 。如果你今天运行`stack build`，那么你在明天运行`stack build`应该会获得同样的结果。某些情况会打破这个规则（例如更改你操作系统的配置），但是，一般来说，stack严格遵循这种设计理念。为了让这个过程变得简单，stack使用被称为 __快照__ 的专门的包集合。

<!-- stack has also been designed from the ground up to be user friendly, with an
intuitive, discoverable command line interface. For many users, simply
downloading stack and reading `stack --help` will be enough to get up and
running. This guide provides a more gradual tour for users who prefer that
learning style. -->
stack也被彻底设计成用户友好的，直观的，一目了然的命令行接口。对于很多用户来说，只需要下载stack然后阅读`stack --help`就足够开始使用和运行。本指南为喜欢这种学习方式的用户提供更加循序渐进的过程。

<!-- To build your project, stack uses a `stack.yaml` file in the root directory of
your project as a sort of blueprint. That file contains a reference, called a
__resolver__, to the snapshot which your package will be built against. -->
为了构建你的项目，stack使用你项目根目录下面一个叫`stack.yaml`的文件作为一种蓝图。这个文件包含一个叫做 __resolver__ 的引用，指向将构建包的快照。

<!-- Finally, stack is __isolated__: it will not make changes outside of specific
stack directories. stack-built files generally go in either the stack root
directory (default `~/.stack` or, on Windows, `%LOCALAPPDATA%\Programs\stack`)
or `./.stack-work` directories local to each project. The stack root directory
holds packages belonging to snapshots and any stack-installed versions of GHC.
Stack will not tamper with any system version of GHC or interfere with packages
installed by `cabal` or any other build tools. -->
最后，stack是隔离的：他将不会改变stack相关之外的目录。stack构建的文件通常在stack的根目录下面（默认是`~/.stack`，或者在Windows下面为`%LOCALAPPDATA%\Programs\stack`）或者每个项目本地下的`./.stack-work`目录。stack根目录保存属于快照的包和任何由stack安装的各版本GHC。stack不
会篡改任何系统的GHC版本或干扰由`cabal`或其他构建工具安装的包。

<!-- _NOTE_ In this guide, we'll use commands as run on a GNU/Linux system
(specifically Ubuntu 14.04, 64-bit) and share output from that. Output on other
systems — or with different versions of stack — will be slightly different, but
all commands work cross-platform, unless explicitly stated otherwise. -->
_说明_ 在本指南中，我们将使用运行在GNU/Linux系统上的命令行并且分享其的输出。其他系统——或者不同版本的stack的输出——将会略有不同，但是除非另有明确说明，否则所有的命令都是跨平台的。

<!-- ## Downloading and Installation -->
## 下载和安装

<!-- The [documentation dedicated to downloading
stack](install_and_upgrade.md) has the most
up-to-date information for a variety of operating systems, including multiple
GNU/Linux flavors. Instead of repeating that content here, please go check out
that page and come back here when you can successfully run `stack --version`.
The rest of this session will demonstrate the installation procedure on a
vanilla Ubuntu 14.04 machine. -->
[documentation dedicated to downloading
stack](install_and_upgrade.md)有许多关于各种操作系统最新的信息，请去看看这些页面然后在你能够成功运行`stack --version`后回到此页。本节的其他部分将在一台vanilla Ubuntu 14.04机器上演示安装程序。

```
michael@d30748af6d3d:~$ sudo apt-get install wget
# installing ...
michael@d30748af6d3d:~$ wget -qO- https://get.haskellstack.org/ | sh
# downloading ...
michael@d30748af6d3d:~$ stack --help
# help output ...
```

<!-- With stack now up and running, you're good to go. Though not required, we
recommend setting your PATH environment variable to include `$HOME/.local/bin`: -->
很高兴，现在stack启动并运行了。尽管不是必须的，我们推荐设置你的path环境变量以包含`$HOME/.local/bin`：

```
michael@d30748af6d3d:~$ echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc
```

<!-- ## Hello World Example -->
## Hello World示例

<!-- With stack installed, let's create a new project from a template and walk
through the most common stack commands. -->
随着stack安装完成，让我们来从一个模板来创建一个新项目并且过一遍大多数通常的stack命令。

### stack new

<!-- We'll start off with the `stack new` command to create a new *project*, that
will contain a Haskell *package* of the same name. So let's pick a valid
package name first: -->
我们将开始使用`stack new`命令来创建一个新的*项目*，这将包含一个同名的Haskell*包*。所以让我们首先选一个有效的包名：

<!-- > A package is identified by a globally-unique package name, which consists
> of one or more alphanumeric words separated by hyphens. To avoid ambiguity,
> each of these words should contain at least one letter. -->
> 一个包是由全局唯一的包名称标识，该名称由一个或多个由连字符分隔的字母数字单词组成。为了避免歧义，每个单词必须最少包含一个字母。

(From the [Cabal users guide](https://www.haskell.org/cabal/users-guide/developing-packages.html#developing-packages))

<!-- We'll call our project `helloworld`, and we'll use the `new-template` project
template: -->
我们将称我们的项目为`helloworld`，并且我们将使用`new-template`项目模板：

```
michael@d30748af6d3d:~$ stack new helloworld new-template
```

<!-- For this first stack command, there's quite a bit of initial setup it needs to
do (such as downloading the list of packages available upstream), so you'll see
a lot of output. Over the course of this guide a lot of the content will begin
to make more sense. -->
对于第一个stack命令，它需要做很多初始设置（比如下载上游可用的包列表），所以你将看到一大堆输出。在本指南的过程中，许多内容将开始变得更有意义。

<!-- We now have a project in the `helloworld` directory! -->
我们现在在`helloworld`目录中有了一个项目！

### stack build

<!-- Next, we'll run the most important stack command: `stack build`. -->
接下来，我们将运行stack中最重要的命令：`stack build`。

```
michael@d30748af6d3d:~$ cd helloworld
michael@d30748af6d3d:~/helloworld$ stack build
# installing ... building ...
```

<!-- stack needs a GHC in order to build your project. stack will discover that you
are missing it and will install it for you. You can do this manually by using
the `stack setup` command. -->
stack需要GHC才能构建你的项目。stack将发现你缺少它并为你安装它。你可以使用`stack setup`命令手动执行此操作。

<!-- You'll get intermediate download percentage statistics while the download is
occurring. This command may take some time, depending on download speeds. -->
在下载过程中，你会看到百分比统计的下载进度。这个命令将会花费一些时间，这取决去下载速度。

<!-- __NOTE__: GHC will be installed to your global stack root directory, so
calling `ghc` on the command line won't work. See the `stack exec`,
`stack ghc`, and `stack runghc` commands below for more information. -->
__说明__：GHC将安装到你的全局stack根目录，所以在命令行调用`ghc`将不会起作用。参考`stack exec`，`stack ghc`和`stack runghc`命令了解更多信息。

<!-- Once a GHC is installed, stack will then build your project. -->
安装GHC后，stack将构建你的项目。

### stack exec

<!-- Looking closely at the output of the previous command, you can see that it built
both a library called "helloworld" and an executable called "helloworld-exe".
We'll explain more in the next section, but, for now, just notice that the
executables are installed in our project's `./.stack-work` directory. -->
仔细观察上一条命令的输出，你就能看到它同时构建了一个名叫“helloworld”的库和一个叫"helloworld-exe"的可执行文件。我们将在下一节说明，但现在，请注意这个可执行文件被安装在我们项目的`./.stack-work`目录中。

<!-- Now, Let's use `stack exec` to run our executable (which just outputs the string
"someFunc"): -->
现在，让我们使用`stack exec`来运行我们的可执行文件（它只会输出字符串"someFunc"）：

```
michael@d30748af6d3d:~/helloworld$ stack exec helloworld-exe
someFunc
```

<!-- `stack exec` works by providing the same reproducible environment that was used
to build your project to the command that you are running. Thus, it knew where
to find `helloworld-exe` even though it is hidden in the `./.stack-work`
directory. -->
`stack exec`通过提供相同的可复现环境来工作，被用来将项目构建到您正在运行的命令中。因此，它知道在哪找到`helloworld-exe`，即使它隐藏在`./.stack-work`目录中。

### stack test

<!-- Finally, like all good software, helloworld actually has a test suite. -->
最后，向所有好的软件一样，helloworld实际上有一个测试套件。

<!-- Let's run it with `stack test`: -->
让我们用`stack test`来运行它：

```
michael@d30748af6d3d:~/helloworld$ stack test
# build output ...
```

<!-- Reading the output, you'll see that stack first builds the test suite and then
automatically runs it for us. For both the `build` and `test` command, already
built components are not built again. You can see this by running `stack build`
and `stack test` a second time: -->
阅读这个输出，你将看到stack首先构建测试套件然后为我们运行它。对于`build`和`test`命令，已经构建好的组件不会再次构建。你可以通过运行`stack build`和再次运行`stack test`看到这些：

```
michael@d30748af6d3d:~/helloworld$ stack build
michael@d30748af6d3d:~/helloworld$ stack test
# build output ...
```

<!-- ## Inner Workings of stack -->
## stack的内部运作

<!-- In this subsection, we'll dissect the helloworld example in more detail. -->
在本节中，我们将从更多细节剖析helloworld示例。

<!-- ### Files in helloworld -->
### helloworld中的文件

<!-- Before studying stack more, let's understand our project a bit better. -->
在更多学习stack之前，先让我们更好地理解我们的项目。

```
michael@d30748af6d3d:~/helloworld$ find * -type f
LICENSE
README.md
Setup.hs
app/Main.hs
helloworld.cabal
package.yaml
src/Lib.hs
stack.yaml
test/Spec.hs
```

<!-- The `app/Main.hs`, `src/Lib.hs`, and `test/Spec.hs` files are all Haskell
source files that compose the actual functionality of our project (we won't
dwell on them here). -->
`app/Main.hs`，`src/Lib.hs`和`test/Spec.hs`文件都是构成项目实际功能的Haskell源文件（我们将不会在这里详细描述它们）。

<!-- The `LICENSE` file and `README.md` have no impact on the
build. -->
`LICENSE`和`README.md`文件对于构建没有影响。

<!-- The `helloworld.cabal` file is updated automatically as part of the
`stack build` process and should not be modified. -->
`helloworld.cabal`文件作为`stack build`过程的一部分被自动更新，不应该修改。

<!-- The files of interest here are `Setup.hs`, `stack.yaml`, and `package.yaml`. -->
这里感兴趣的文件是`Setup.hs`，`stack.yaml`和`package.yaml`。

<!-- The `Setup.hs` file is a component of the Cabal build system which stack uses.
It's technically not needed by stack, but it is still considered good practice
in the Haskell world to include it. The file we're using is straight
boilerplate: -->
`Setup.hs`文件是stack用到的cabal构建系统的一个组件。从技术上来说stack并不需要它，但将它包含进来仍然被认为是Haskell世界中很好的实践。我们使用的文件是直接的样版：

```haskell
import Distribution.Simple
main = defaultMain
```

<!-- Next, let's look at our `stack.yaml` file, which gives our project-level settings. -->
接下来，让我们看看我们的`stack.yaml`文件，它提供了我们的项目级设置。

<!-- If you're familiar with YAML, you may recognize that the `flags` and
`extra-deps` keys have empty values. We'll see more interesting usages for these
fields later.  Let's focus on the other two fields. `packages` tells stack which
local packages to build. In our simple example, we have only a single package in
our project, located in the same directory, so `'.'` suffices. However, stack
has powerful support for multi-package projects, which we'll elaborate on as
this guide progresses. -->
如果你熟悉YAML，你会发现`flags`和`extra-deps`是空的值。我们之后会看到更多有趣的字段用法。让我们注意到其他两个字段。`packages`告诉stack用于构建的本地包。在我们的简单的示例中，我们项目中只有一个单独的包，位于同一个目录中，所以`'.'`就足够了。然而stack对多包项目有强大的支持，随着本指南的进展我们将详细说明。

<!-- The final field is `resolver`. This tells stack *how* to build your package:
which GHC version to use, versions of package dependencies, and so on. Our
value here says to use [LTS Haskell version
3.2](https://www.stackage.org/lts-3.2), which implies GHC 7.10.2 (which is why
`stack setup` installs that version of GHC). There are a number of values you
can use for `resolver`, which we'll cover later. -->
最后一个字段是`resolver`。它告诉stack*怎么*去构建你的包：用哪个版本的GHC，依赖包的版本，等等。我们这里的值说要用[LTS Haskell version
3.2](https://www.stackage.org/lts-3.2)，这意味着GHC 7.10.2（这就是为什么`stack setup`会安装那个版本的GHC）。有许多值可以用于`resolver`，稍后我们会讨论这些值。

<!-- Another file important to the build is `package.yaml`. -->
其他对构建重要的文件是`package.yaml`。

<!-- Since Stack 1.6.1, the `package.yaml` is the preferred package format that is
provided built-in by stack through [the hpack tool](https://github.com/sol/hpack).
The default behaviour is to generate the `.cabal` file from this `package.yaml`,
and accordingly you should **not** modify the `.cabal` file. -->
从Stack 1.6.1开始，`package.yaml`是stack通过内置提供的首选包格式[the hpack tool](https://github.com/sol/hpack)。默认行为是从`package.yaml`生成`.cabal`文件，因此你不应该修改`.cabal`文件。

<!-- It is also important to remember that stack is built on top of the Cabal build system. Therefore, an
understanding of the moving parts in Cabal are necessary. In Cabal, we have individual
*packages*, each of which contains a single `.cabal` file. The `.cabal` file
can define 1 or more *components*: a library, executables, test suites, and benchmarks.
It also specifies additional information such as library dependencies, default language
pragmas, and so on. -->
同样重要的是记住stack是建立在Cabal构建系统之上的。因此，理解Cabal的活动部分是必要的。在Cabal中，我们有单独的*packages*，其中每个都包含一个`.cabal`文件。`.cabal`文件可以定义1个或者多个*组件*：一个库、可执行文件、测试套件、和基准程序。它也还指定了其他信息比如依赖库、默认编译参数、等等。

<!-- In this guide, we'll discuss the bare minimum necessary to understand how to
modify a `package.yaml` file. You can see a full list of the available options
at the [hpack documentation](https://github.com/sol/hpack#quick-reference). Haskell.org
has the definitive [reference for the `.cabal` file format](https://www.haskell.org/cabal/users-guide/developing-packages.html). -->
在本指南中，我们将讨论最低限度的理解如何修改一个`package.yaml`文件。你可以在[hpack documentation](https://github.com/sol/hpack#quick-reference)看到整个可用选项的列表。Haskell.org有明确的[`.cabal`文件格式参考](https://www.haskell.org/cabal/users-guide/developing-packages.html)

<!-- ### The setup command -->
### setup命令

<!-- As we saw above, the `build` command installed GHC for us. Just for kicks,
let's manually run the `setup` command: -->
如我们看过的，`build`命令为我们安装GHC。只是为了好玩，让我们来手动运行`setup`命令：

```lisp
michael@d30748af6d3d:~/helloworld$ stack setup
stack will use a sandboxed GHC it installed
; stack将使用它安装的沙箱GHC
For more information on paths, see 'stack path' and 'stack exec env'
; 关于paths的更多信息，请参考'stack path'和'stack exec env'
To use this GHC and packages outside of a project, consider using:
; 要在项目外使用GHC和packages，请考虑使用：
stack ghc, stack ghci, stack runghc, or stack exec
; stack ghc，stack ghci，stack runghc，或者stack exec
```

<!-- Thankfully, the command is smart enough to know not to perform an installation
twice. As the command output above indicates, you can use `stack path`
for quite a bit of path information (which we'll play with more later). -->
幸运的是，这个命令足够聪明，知道不要执行两次安装。如以上命令输出所示，你可以使用`stack path`来获取相当多的路径信息（我们稍后再讲）。

<!-- For now, we'll just look at where GHC is installed: -->
现在，我们只是看一下GHC被安装在哪里：

```
michael@d30748af6d3d:~/helloworld$ stack exec -- which ghc
/home/michael/.stack/programs/x86_64-linux/ghc-7.10.2/bin/ghc
```

<!-- As you can see from that path (and as emphasized earlier), the installation is
placed to not interfere with any other GHC installation, whether system-wide or
even different GHC versions installed by stack. -->
正如你从路径中看到的那样（并且如之前所强调的那样），安装的位置不会干扰任何其他GHC安装，无论是系统级还是任何被stack安装的各种版本的GHC。

<!-- ## Cleaning your project -->
## 清理你的项目

<!-- You can clean up build artifacts for your project using the `stack clean` and `stack purge` commands. -->
你可以使用`stack clean`和`stack purge`清理你项目的构建产物。

### `stack clean`

<!-- `stack clean` deletes the local working directories containing compiler output.
By default, that means the contents of directories in `.stack-work/dist`, for all the `.stack-work` directories within a project. -->
`stack clean`删除包含的编译器输出的本地工作目录。默认情况下，这意味着删除项目中所有`.stack-work`目录下的`.stack-work/dist`目录中的内容，

<!-- Use `stack clean <specific-package>` to delete the output for the package _specific-package_ only. -->
使用`stack clean <特定包>`仅会删除特定包的输出。

### `stack purge`

<!-- `stack purge` deletes the local stack working directories, including extra-deps, git dependencies and the compiler output (including logs).
It does not delete any snapshot packages, compilers or programs installed using `stack install`. This essentially
reverts the project to a completely fresh state, as if it had never been built.
`stack purge` is just a shortcut for `stack clean --full` -->
`stack purge`删除本地stack工作目录，包括extra-deps，git依赖和编译器的输出（包括日志）。他不会删除任何快照包和用`stack install`安装的编译器和程序。这实际上是把项目恢复到一个全新的状态，就像它从来没被构建过一样。`stack purge`只是`stack clean --full`的缩写。

<!-- ### The build command -->
### 构建命令

<!-- The build command is the heart and soul of stack. It is the engine that powers
building your code, testing it, getting dependencies, and more. Quite a bit of
the remainder of this guide will cover more advanced `build` functions and
features, such as building test and Haddocks at the same time, or constantly
rebuilding blocking on file changes. -->
构建命令是stack的核心和灵魂。它是构建你代码的引擎，测试代码，获取依赖，等等。本指南相当一部分剩余内容会介绍更多`build`的高级功能和特性，比如同时进行构建测试和Haddocks，或者经常在文件更改时重建blocking。

<!-- *On a philosophical note:* Running the build command twice with the same
options and arguments should generally be a no-op (besides things like
rerunning test suites), and should, in general, produce a reproducible result
between different runs. -->
*从哲学角度来看：*使用相同的选项和参数运行构建命令两次通常是一个空操作（除了像运行测试套件之类的事情），并且一般来说，多次运行会产生重复的结果。

<!-- ## Adding dependencies -->
## 添加依赖

<!-- Let's say we decide to modify our `helloworld` source a bit to use a new library,
perhaps the ubiquitous text package. In `src/Lib.hs`, we can, for example add: -->
假设我们决定稍微修改一下我们的`helloworld`源码来使用一个新库，可能是无处不在的text包。在`src/Lib.hs`中，我们可以例如添加：

```haskell
{-# LANGUAGE OverloadedStrings #-}
module Lib
    ( someFunc
    ) where

import qualified Data.Text.IO as T

someFunc :: IO ()
someFunc = T.putStrLn "someFunc"
```

<!-- When we try to build this, things don't go as expected: -->
当我们试图构建它时，事情不会按预期进行：

```haskell
michael@d30748af6d3d:~/helloworld$ stack build
# build failure output (abridged for clarity) ...
/helloworld/src/Lib.hs:5:1: error:
    Could not find module `Data.Text.IO'
    Use -v to see a list of the files searched for.
  |
5 | import qualified Data.Text.IO as T
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

<!-- This means that the package containing the module in question is not available. To tell
stack to use [text](https://hackage.haskell.org/package/text), you need to add it to your
`package.yaml` file — specifically in your `dependencies` section, like this: -->
这意思是包含相关模块的包是不可用的。为了告诉stack怎么使用[text](https://hackage.haskell.org/package/text)，你需要添加它到你的`package.yaml`文件中——具体是你的`dependencies`段中，像这样：

```yaml
dependencies:
- base >= 4.7 && < 5
- text # added here
```

<!-- Now if we rerun `stack build`, we should get a successful result: -->
现在如果我们重新运行`stack build`，我们将得到成功的结果：

```
michael@d30748af6d3d:~/helloworld$ stack build
# build output ...
```

<!-- This output means that the text package was downloaded, configured, built, and
locally installed. Once that was done, we moved on to building our local package
(helloworld). At no point did we need to ask stack to build dependencies — it
does so automatically. -->
这个输出的意思是text包被下载，配置，构建，和本地安装。当这些结束之后，我们继续编译我们的本地包（helloworld）。在任何时候我们都不需要请求stack构建依赖——这些是自动完成的。

<!-- ### Listing Dependencies -->
### 依赖列表

<!-- Let's have stack add a few more dependencies to our project. First, we'll include two new packages in the
`dependencies` section for our library in our `package.yaml`: -->
让stack为我们的项目添加更多的依赖。首先，我们将在我们的`package.yaml`中的`dependencies`中为我们库包含两个新包：

```yaml
dependencies:
- filepath
- containers
```

<!-- After adding these two dependencies, we can again run `stack build` to have them installed: -->
在添加完这两个依赖后，我们可以再次运行`stack build`来安装他们：

```
michael@d30748af6d3d:~/helloworld$ stack build
# build output ...
```

<!-- Finally, to find out which versions of these libraries stack installed, we can ask stack to `ls dependencies`: -->
最后，为了找到stack安装的包的版本，我们可以用`ls dependencies`来请求stack：

```
michael@d30748af6d3d:~/helloworld$ stack ls dependencies
# dependency output ...
```

### extra-deps

<!-- Let's try a more off-the-beaten-track package: the joke
[acme-missiles](http://www.stackage.org/package/acme-missiles) package. Our
source code is simple: -->
让我们试试一个更加少见的包：the joke [acme-missiles](http://www.stackage.org/package/acme-missiles)包。代码很简单：

```haskell
module Lib
    ( someFunc
    ) where

import Acme.Missiles

someFunc :: IO ()
someFunc = launchMissiles
```

<!-- Again, we add this new dependency to the `package.yaml` file like this: -->
再次，我们添加一个新依赖到`package.yaml`中，像这样：

```yaml
dependencies:
- base >= 4.7 && < 5
- text
- filepath
- containers
- acme-missiles # added
```

<!-- However, rerunning `stack build` shows us the following error message: -->
然而，重新运行`stack build`会像我们显示以下错误信息：

```
michael@d30748af6d3d:~/helloworld$ stack build
# build failure output ...
```

<!-- It says that it was unable to construct the build plan. -->
它说他不能够创建构建计划。

<!-- This brings us to the next major topic in using stack. -->
这带给我们使用stack的下一个重要话题。

<!-- ## Curated package sets -->
## 专门的包集合

<!-- Remember above when `stack new` selected some [LTS resolver](https://github.com/fpco/lts-haskell#readme)
for us? That defined our build plan and available packages. When we tried using the
`text` package, it just worked, because it was part of the LTS *package set*. -->
还记得上面`stack new`为我们选择了一些 [LTS解析器](https://github.com/fpco/lts-haskell#readme)吗？这定义了我们的构建计划和可用的包。当我们尝试用`text`包，它刚好能起作用，因为它是LTS*包集合*的一部分。

<!-- But `acme-missiles` is not part of that package set, so building failed. -->
但是`acme-missiles`并不是这个包集合的的一部分，所以构建失败。

<!-- To add this new dependency, we'll use the `extra-deps` field in `stack.yaml` to
define extra dependencies not present in the resolver. You can add this like so: -->
为了添加这个新依赖，我们将用`stack.yaml`中的`extra-deps`字段来定义不需要解析器的额外的依赖。你可以像这样添加它：

```yaml
extra-deps:
- acme-missiles-0.3 # not in the LTS
```

<!-- Now `stack build` will succeed. -->
现在`stack build`将会运行成功。

<!-- With that out of the way, let's dig a little bit more into these package sets,
also known as *snapshots*. We mentioned the LTS resolvers, and you can get quite a bit of
information about it at [https://www.stackage.org/lts](https://www.stackage.org/lts), including: -->
完成这些之后，让我们进一步研究这些包集合，即*快照*。我们提到过LTS解析器，你可以在[https://www.stackage.org/lts](https://www.stackage.org/lts)获得很多关于它的信息，包括：

<!-- * The appropriate resolver value (`resolver: lts-11.22`, as is currently the latest LTS) -->
* 恰当的解析器的版本号（当前最新的LTS是`resolver: lts-11.22`）
<!-- * The GHC version used -->
* 用过的GHC版本
<!-- * A full list of all packages available in this snapshot -->
* 此快照中所有可用的包的完整列表
<!-- * The ability to perform a Hoogle search on the packages in this snapshot -->
* 对快照中的包执行Hoogle搜索的能力
<!-- * A [list of all modules](https://www.stackage.org/lts/docs) in a snapshot,
  which can be useful when trying to determine which package to add to your
  `package.yaml` file. -->
* 这个快照有一个[所有模块列表](https://www.stackage.org/lts/docs)，当你试图确定添加哪个包到你的`package.yaml`文件中时可能会有用。

<!-- You can also see a [list of all available
snapshots](https://www.stackage.org/snapshots). You'll notice two flavors: LTS
(for "Long Term Support") and Nightly. You can read more about them on the
[LTS Haskell Github page](https://github.com/fpco/lts-haskell#readme). If you're
not sure which to use, start with LTS Haskell (which stack will lean towards by
default as well). -->
你也可以看看[所有可用快照列表](https://www.stackage.org/snapshots)。你会注意到两个flavors：LTS（即“长期支持”）和Nightly。你可以在[LTS Haskell Github page](https://github.com/fpco/lts-haskell#readme)上阅读更多相关的信息。

<!-- ## Resolvers and changing your compiler version -->
## 解析器和更改你的编译器版本

<!-- Let's explore package sets a bit further. Instead of lts-11.22, let's change our
`stack.yaml` file to use [the latest nightly](https://www.stackage.org/nightly). Right now,
this is currently 2018-07-25 - please see the resolve from the link above to get the latest. -->
让我们进一步浏览包集合。让我们修改我们的`stack.yaml`来使用[the latest nightly](https://www.stackage.org/nightly)，代替lts-11.22。现在，这是2018-07-25——请参阅上面链接中的解析以获得最新信息。

<!-- Then, Rerunning `stack build` will produce: -->
然后，运行`stack build`将生成：

```
michael@d30748af6d3d:~/helloworld$ stack build
Downloaded nightly-2018-07-31 build plan.
# build output ...
```

<!-- We can also change resolvers on the command line, which can be useful in a
Continuous Integration (CI) setting, like on Travis. For example: -->
我们也可以修改命令行中的解析器，这在连续集成(CI)设置中会很有用，就像在Travis上一样。比如:

```
michael@d30748af6d3d:~/helloworld$ stack --resolver lts-11.22 build
Downloaded lts-11.22 build plan.
# build output ...
```

<!-- When passed on the command line, you also get some additional "short-cut"
versions of resolvers: `--resolver nightly` will use the newest Nightly resolver
available, `--resolver lts` will use the newest LTS, and `--resolver lts-2` will
use the newest LTS in the 2.X series. The reason these are only available on the
command line and not in your `stack.yaml` file is that using them: -->
通过命令行，你还可以得到一些额外的“short-cut”解析器版本：`--resolver nightly`将使用最新可用的Nightly解析器，`--resolver lts`将使用最新的LTS，`--resolver lts-2`将使用最新的2.X版本的LTS。只适用于命令行而不是你的`stack.yaml`文件的原因是使用它们会：

<!-- 1. Will slow down your build (since stack then needs to download information on
   the latest available LTS each time it builds) -->
1. 将减慢你的构建速度（因为stack每次构建都需要下载最后可用的LTS信息）
<!-- 2. Produces unreliable results (since a build run today may proceed differently
   tomorrow because of changes outside of your control) -->
2. 生成不可靠的结果（因为你掌控之外的改变，今天构建运行可能会和明天有所不同）

<!-- ### Changing GHC versions -->
### 更改GHC版本

<!-- Finally, let's try using an older LTS snapshot. We'll use the newest 2.X
snapshot: -->
最后，让我是尝试用一个更老的LTS快照，我们将使用最新的2.X版本快照：

```
michael@d30748af6d3d:~/helloworld$ stack --resolver lts-2 build
# build output ...
```

<!-- This succeeds, automatically installing the necessary GHC along the way. So,
we see that different LTS versions use different GHC versions and stack can
handle that. -->
他成功了，通过这种方式自动安装必要的GHC。于是，我们看到不同版本的LTS使用不同版本的GHC并且stack能够处理这些。

<!-- ### Other resolver values -->
### 其他解析器版本号

<!-- We've mentioned `nightly-YYYY-MM-DD` and `lts-X.Y` values for the resolver.
There are actually other options available, and the list will grow over time.
At the time of writing: -->
我们已经提到过解析器的版本号`nightly-YYYY-MM-DD`和`lts-X.Y`。实际上还有其他可用的选项，并且这个列表会随着时间增长。在撰写本文时：

<!-- * `ghc-X.Y.Z`, for requiring a specific GHC version but no additional packages -->
* `ghc-X.Y.Z`，用于请求一个特殊版本的GHC，但是没有额外的包
<!-- * Experimental GHCJS support -->
* 检验GHCJS支持
<!-- * Experimental custom snapshot support -->
* 检验自定义快照支持

<!-- The most up-to-date information can always be found in the
[stack.yaml documentation](yaml_configuration.md#resolver). -->
相关最新的信息通常可以在[stack.yaml documentation](yaml_configuration.md#resolver)中找到。

<!-- ## Existing projects -->
## 现有项目

<!-- Alright, enough playing around with simple projects. Let's take an open source
package and try to build it. We'll be ambitious and use
[yackage](https://www.stackage.org/package/yackage), a local package server
using [Yesod](http://www.yesodweb.com/). To get the code, we'll use the
`stack unpack` command: -->
好了，简单项目玩够了。让我们拿一个开源包并且尝试构建它。雄心勃勃的我们将使用[yackage](https://www.stackage.org/package/yackage)，这是一个本地的使用[Yesod](http://www.yesodweb.com/)的包服务器。为了获取其代码，我们将使用`stack unpack`命令：

```
cueball:~$ stack unpack yackage-0.8.0
Unpacked yackage-0.8.0 to /var/home/harendra/yackage-0.8.0/
cueball:~$ cd yackage-0.8.0/
```

<!-- Note that you can also unpack to the directory of your liking instead of
the current one by issueing: -->
注意你也可以通过命令unpack到你喜欢的目录而不是当前目录：

```
cueball:~$ stack unpack yackage-0.8.0 --to ~/work
```

<!-- This will create a `yackage-0.8.0` directory inside `~/work` -->
这将会在`~/work`里面创建一个`yackage-0.8.0`目录

### stack init
<!-- This new directory does not have a `stack.yaml` file, so we need to make one
first. We could do it by hand, but let's be lazy instead with the `stack init`
command: -->
这个新目录没有`stack.yaml`文件，所以我们需要首先制作一个。我们可以手动来做，但是让我们懒惰一点用`stack init`命令代替：

```
cueball:~/yackage-0.8.0$ stack init
# init output ...
```

<!-- stack init does quite a few things for you behind the scenes: -->
stack init在幕后帮你做了很多事情：

<!-- * Finds all of the `.cabal` files in your current directory and subdirectories
  (unless you use `--ignore-subdirs`) and determines the packages and versions
  they require -->
* 找到你当前目录和所有子目录（除非你使用`--ignore-subdirs`）和中的所有的`.cabal`文件，并确认包和他们要求的版本一致
<!-- * Finds the best combination of snapshot and package flags that allows everything to
  compile with minimum external dependencies -->
* 找到快照和包标志的最佳组合，允许用任何最少的外部依赖来编译所有内容
<!-- * It tries to look for the best matching snapshot from latest LTS, latest
  nightly, other LTS versions in that order -->
* 它尝试按照最新的LTS，最新的Nightly，其他版本LTS这个顺序找到最匹配的快照

<!-- Assuming it finds a match, it will write your `stack.yaml` file, and everything
will work. -->
假设它找到一个匹配，他将写入到你的`stack.yaml`文件中，一切正常。

<!-- (Note: yackage does not currently support hpack, but you can also hpack-convert should you need to generate a package.yaml). -->
（说明：yackage当前没有支持hpack，但是如果你需要生成一个package.yaml，你也可以进行hpack-convert）

<!-- #### External Dependencies -->
#### 外部依赖

<!-- Given that LTS Haskell and Stackage Nightly have ~1400 of the most common
Haskell packages, this will often be enough to build most packages. However,
at times, you may find that not all dependencies required may be available in
the Stackage snapshots. -->
鉴于LTS Haskell和Stackage Nightly有最多1400多个Haskell包，这通常足够来构建大多数包。然而，有时候，你也许会发现不是所有需要的依赖都可从Stackage快照中得到。

<!-- Let's simulate an unsatisfied dependency by adding acme-missiles to the list of dependencies
the build requires. This is done by including it in the `Build-depends` section in the .cabal file
and then re-initing: -->
让我们通过添加acme-missiles到构建需要的依赖列表来模拟一个不如意的依赖项。这是通过将其包含在.cabal文件的`Build-depends`部分中然后重新初始化来完成的：

```
cueball:~/yackage-0.8.0$ stack init --force
# init failure output
```

<!-- stack has tested six different snapshots, and in every case discovered that
acme-missiles is not available. In the end it suggested that you use the
`--solver` command line switch if you want to use packages outside Stackage. So
let's give it a try: -->
stack测试完六种不同的快照，并且每种情况都发现acme-missiles是不可用的。最后如果你想使用Stackage之外的包，它建议你使用`--solver`命令行开关。所以让我们试试看：

```
cueball:~/yackage-0.8.0$ stack init --force --solver
# solver output ...
```

<!-- stack will complain that it needs a `cabal-install` installation. Let's get that: -->
stack会抱怨它需要安装一个`cabal-install`。让我们获取它：

```
cueball:~/yackage-0.8.0$ stack install cabal-install
```

<!-- Then run the above `stack init` command again and it will succeed. -->
然后再次运行上面的`stack init`命令，它将成功。

<!-- As you can verify by viewing `stack.yaml`, three external dependencies were added
by stack init under the `extra-deps` field. Of course, you could have added the
external dependencies by manually editing `stack.yaml` but stack init does the
hard work for you. -->
你可以通过查看`stack.yaml`来验证，三个外部依赖被stack init添加到`extra-deps`字段下面。当然，你可以通过手动编辑`stack.yaml`来添加外部依赖，但是stack init会帮你做这个困难的工作。

<!-- #### Excluded Packages -->
#### 排除包

<!-- Sometimes multiple packages in your project may have conflicting requirements.
In that case `stack init` will fail, so what do you do? -->
有时候你项目中的许多包可能相互冲突。这种情况下`stack init`将会失败，那么你怎么做呢？

<!-- You could manually create `stack.yaml` by omitting some packages to resolve the
conflict. Alternatively you can ask `stack init` to do that for you by
specifying `--omit-packages` flag on the command line. Let's see how that
works. -->
你可以手动创建遗漏一些包的`stack.yaml`来解决冲突。或者你可以在命令行里通过指定`--omit-packages`选项来请求`stack init`为你做这些。让我们看看怎么运作的。

<!-- To simulate a conflict we will use acme-missiles-0.3 in yackage and we will
also copy `yackage.cabal` to another directory and change the name of the file
and package to yackage-test. In this new package we will use acme-missiles-0.2
instead. Let's see what happens when we run solver: -->
我们将在yackage使用acme-missiles-0.3来模拟一个冲突，并且我们也将拷贝`yackage.cabal`到其他目录，并且将文件和包的名字改为yackage-test。在这个新包中我们将使用acme-missiles-0.2代替。让我们看看当我们运行solver的时候会发生什么：

```
cueball:~/yackage-0.8.0$ stack init --force --solver --omit-packages
# init failure output ...
```

<!-- Looking at `stack.yaml`, you will see that the excluded packages have been
commented out under the `packages` field. In case wrong packages are excluded
you can uncomment the right one and comment the other one. -->
查看`stack.yaml`，你将会看到`packages`字段下面被排除的包被注释掉了。如果错误的包被排除掉，那么你可以取消注释正确的包，然后注释掉另外一个包。

<!-- Packages may get excluded due to conflicting requirements among user packages
or due to conflicting requirements between a user package and the resolver
compiler. If all of the packages have a conflict with the compiler then all of
them may get commented out. -->
由于用户包之间的需求冲突，或者用户包和解析器编译器之间的需求冲突，包可能排除。如果所有的包都和编译器冲突，那么这些包都可能被注释掉。

<!-- When packages are commented out you will see a warning every time you run a
command which needs the configuration file. The warning can be disabled by
editing the configuration file and removing it. -->
当包被注释之后你每次运行命令时将会看到一个需要配置文件的警告。可以通过编辑配置文件并移除它来禁用警告。

<!-- #### Using a specific resolver -->
#### 使用特定的解析器

<!-- Sometimes you may want to use a specific resolver for your project instead of
`stack init` picking one for you. You can do that by using
`stack init --resolver <resolver>`. -->
有时你可能想在你的项目中使用特定的解析器来代替`stack init`给你选择的那个。你可以使用`stack init --resolver <resolver>`来实现这一点。

<!-- You can also init with a compiler resolver if you do not want to use a
snapshot. That will result in all of your project's dependencies being put
under the `extra-deps` section. -->
如果你不想使用一个快照，你也可以用一个编译器解析器来初始化。这将导致你项目下的所有依赖被放到`extra-deps`下面。

<!-- #### Installing the compiler -->
#### 安装编译器

<!-- stack will automatically install the compiler when you run `stack build` but you can
manually specify the compiler by running `stack setup <GHC-VERSION>`. -->
当你运行`stack build`时，stack将自动安装编译器，但你可以通过运行`stack setup <GHC-VERSION>`来手动安装特定的编译器。

<!-- #### Miscellaneous and diagnostics -->
#### 杂项和诊断

<!-- _Add selected packages_: If you want to use only selected packages from your
project directory you can do so by explicitly specifying the package directories
on the command line. -->
_添加选中的包_：如果你想只想使用从你项目目录中选中的包，可以通过在命令行显示指定包目录来实现。

<!-- _Duplicate package names_: If multiple packages under the directory tree have
same name, stack init will report those and automatically ignore one of them. -->
_重复的包名_：如果目录树下的很多包有相同的名字，stack init将会报告这些并且会自动忽略其中的一个包。

<!-- _Ignore subdirectories_: By default stack init searches all the subdirectories
for `.cabal` files. If you do not want that then you can use `--ignore-subdirs`
command line switch. -->
_忽略子目录_：默认情况下stack init会搜索所有子目录下的`.cabal`文件。如果你不想这么做的话你可以使用`--ignore-subdirs`命令行选项。

<!-- _Cabal warnings_: stack init will show warnings if there were issues in reading
a cabal package file. You may want to pay attention to the warnings as
sometimes they may result in incomprehensible errors later on during dependency
solving. -->
_Cabal警告_：如果在读取某个cabal包文件时出现问题，stack init将会显示警告。你可能需要注意这些警告，因为有时它们可​​能会在确定依赖项时导致难以理解的错误。

<!-- _Package naming_: If the `Name` field defined in a cabal file does not match
with the cabal file name then `stack init` will refuse to continue. -->
_包的命名_：如果一个cabal文件定义的`Name`字段不能和cabal名字匹配，那么`stack init`将拒绝继续运行。

<!-- _Cabal install errors_: stack init uses `cabal-install` to determine external
dependencies. When cabal-install encounters errors, cabal errors are displayed
as is by stack init for diagnostics. -->
_Cabal安装错误_：stack init用`cabal-install`来处理外部依赖。当cabal-install遇到错误，stack init将显示cabal的错误信息用于诊断。

<!-- _User warnings_: When packages are excluded or external dependencies added
stack will show warnings every time configuration file is loaded. You can
suppress the warnings by editing the config file and removing the warnings from
it. You may see something like this: -->
_用户警告_：当包被排除或者添加了外部包时，stack将在每次配置文件被加载时显示警告。你可以通过编辑配置文件并从中移出警告来禁用警告。你可能会看到类似这样的信息。

```
cueball:~/yackage-0.8.0$ stack build
Warning: Some packages were found to be incompatible with the resolver and have been left commented out in the packages section.
Warning: Specified resolver could not satisfy all dependencies. Some external packages have been added as dependencies.
You can suppress this message by removing it from stack.yaml
```
### stack solver

<!-- While `stack init` is used to create stack configuration file from existing
cabal files, `stack solver` can be used to fine tune or fix an existing stack
configuration file. -->
`stack init`被用来根据现有的cabal文件创建stack配置文件，而`stack solver`被用来微调或者修复现有的stack配置文件。

<!-- `stack solver` uses the existing file as a constraint. For example it will
use only those packages specified in the existing config file or use existing
external dependencies as constraints to figure out other dependencies. -->
`stack solver`使用现有的文件为约束。比如他将会只使用配置文件中现有的那些包，或者使用现有的外部依赖作为限制来确定其他依赖项。

<!-- Let's try `stack solver` to verify the config that we generated earlier with
`stack init`: -->
让我们试一下用`stack solver`来校验我们之前用`stack init`生成的配置。

```
cueball:~/yackage-0.8.0$ stack solver
# solver output ...
```

<!-- It says there are no changes needed to your configuration. Notice that it also reports
`example/yackage-test.cabal` as missing from the config. It was purposely
omitted by `stack init` to resolve a conflict. -->
它说你的配置不需要修改。注意他同时报告此配置中缺少`example/yackage-test.cabal`。这是被`stack init`为了解决冲突故意省略掉的。

<!-- Sometimes `stack init` may not be able to give you a perfect configuration. In
that case, you can tweak the configuration file as per your requirements and then
run `stack solver`, it will check the file and suggest or apply any fixes
needed. -->
有时`stack init`可能不能给你一个完美的配置。这种情况，你可以按照你的需要调整配置文件然后运行`stack solver`，它将检查这个文件然后提出或应用任何需要的修复。

<!-- For example, if `stack init` ignored certain packages due to name conflicts or
dependency conflicts, the choice that `stack init` made may not be the correct
one. In that case you can revert the choice and use solver to fix things. -->
例如，如果`stack init`由于名字冲突或依赖冲突而忽略某些包，那么`stack init`的选择可能不是正确的。这种情况下你可以恢复这个选择，然后用solver来做一些修复。

<!-- Let's try commenting out `.` and uncommenting `examples/` in our previously
generated `stack.yaml` and then run `stack solver`: -->
让我们试着在我们之前生成的`stack.yaml`中注释掉`.`并且取消注释`examples/`，然后运行`stack solver`：

```
cueball:~/yackage-0.8.0$ stack solver
# solver failure output ...
```

<!-- Due to the change that we made, solver suggested some new dependencies.
By default it does not make changes to the config. As it suggested you can use
`--update-config` to make changes to the config. -->
因为我们所做的变动，solver建议一些新的依赖。默认情况下它不会改变配置。正如它所建议的，你可以使用`--update-config`来修改配置。

<!-- NOTE: You should probably back up your `stack.yaml` before doing this, such as
committing to Git/Mercurial/Darcs. -->
注意：你应该在做这些之前备份你的`stack.yaml`，比如提交到Git/Mercurial/Darcs。

<!-- Sometimes, you may want to use specific versions of certain packages for your
project. To do that you can fix those versions by specifying them in the
extra-deps section and then use `stack solver` to figure out whether it is
feasible to use those or what other dependencies are needed as a result. -->
有时，你可能想在你的项目中使用特定版本的某些包。为此，您可以通过在extra-deps部分指定这些版本，然后使用“`stack solver`”来确定使用它们是否可行，或者因此需要哪些其他的依赖。

<!-- If you want to change the resolver for your project, you can run
`stack solver --resolver <resolver name>` and it will figure out the changes needed for you. -->
如果你想修改你项目的解析器，你可以运行`stack solver --resolver <resolver name>`，它将为你找到所需的更改。

<!-- Let's see what happens if we change the resolver to an older resolver - lts-2.22: -->
让我们看看如果我们将解析器改成一个更老的解析器——lts-2.22时将会发生什么：

```
cueball:~/yackage-0.8.0$ stack solver --resolver lts-2.22
# solver failure output ...
```

<!-- As you can see, it automatically suggested changes in `extra-deps` due to the
change of resolver. -->
如你所见，由于解析器的修改，它自动建议修改`extra-deps`。

<!-- ## Different databases -->
## 不同数据库

<!-- Time to take a short break from hands-on examples and discuss a little
architecture. stack has the concept of multiple *databases*. A database
consists of a GHC package database (which contains the compiled version of a
library), executables, and a few other things as well. To give you an idea: -->
是时候从动手实例中休息一下，讨论一下架构了。stack有多*数据库*的概念。一个数据库由一个GHC包数据库，可执行程序，和一些其他东西组成。给你一个主意：

```
michael@d30748af6d3d:~/helloworld$ ls .stack-work/install/x86_64-linux/lts-3.2/7.10.2/
bin  doc  flag-cache  lib  pkgdb
```

<!-- Databases in stack are *layered*. For example, the database listing we just gave
is called a *local* database. That is layered on top of a *snapshot* database,
which contains the libraries and executables specified in the snapshot itself.
Finally, GHC itself ships with a number of libraries and executables, which
forms the *global* database. To get a quick idea of this, we can look at the
output of the `stack exec -- ghc-pkg list` command in our helloworld project: -->
stack中的数据库是*分层的*。例如，我们刚刚给出的数据库列表被叫做*本地*数据库。它是*快照*数据库的上层，包含快照本身的库和指定的可执行文件。最后GHC本身附带了大量的库和可执行文件，它们构成了*全局*数据库。为了快速了解这一点，我们可以在我们的helloworld项目中查看`stack exec -- ghc-pkg list`命令的输出。

```
/home/michael/.stack/programs/x86_64-linux/ghc-7.10.2/lib/ghc-7.10.2/package.conf.d
   Cabal-1.22.4.0
   array-0.5.1.0
   base-4.8.1.0
   bin-package-db-0.0.0.0
   binary-0.7.5.0
   bytestring-0.10.6.0
   containers-0.5.6.2
   deepseq-1.4.1.1
   directory-1.2.2.0
   filepath-1.4.0.0
   ghc-7.10.2
   ghc-prim-0.4.0.0
   haskeline-0.7.2.1
   hoopl-3.10.0.2
   hpc-0.6.0.2
   integer-gmp-1.0.0.0
   pretty-1.1.2.0
   process-1.2.3.0
   rts-1.0
   template-haskell-2.10.0.0
   terminfo-0.4.0.1
   time-1.5.0.1
   transformers-0.4.2.0
   unix-2.7.1.0
   xhtml-3000.2.1
/home/michael/.stack/snapshots/x86_64-linux/nightly-2015-08-26/7.10.2/pkgdb
   stm-2.4.4
/home/michael/helloworld/.stack-work/install/x86_64-linux/nightly-2015-08-26/7.10.2/pkgdb
   acme-missiles-0.3
   helloworld-0.1.0.0
```

<!-- Notice that acme-missiles ends up in the *local* database. Anything which is
not installed from a snapshot ends up in the local database. This includes:
your own code, extra-deps, and in some cases even snapshot packages, if you
modify them in some way. The reason we have this structure is that: -->
注意，acme-missiles最终在*本地*数据库里。任何不是来自快照安装的东西最终会在本地数据库中。这包括：你自己的代码，extra-deps，某些情况下甚至是快照包（如果你通过一些方式修改它们）。我们有这种结构的原因是：

<!-- * it lets multiple projects reuse the same binary builds of many snapshot
  packages, -->
* 它让不同版本的快照包的多个项目重用相同的二进制构件，
<!-- * but doesn't allow different projects to "contaminate" each other by putting
  non-standard content into the shared snapshot database -->
* 但是不允许不同的项目通过把不标准的内容放到共享快照数据库来相互*污染*

<!-- Typically, the process by which a snapshot package is marked as modified is
referred to as "promoting to an extra-dep," meaning we treat it just like a
package in the extra-deps section. This happens for a variety of reasons,
including: -->
通常，一个快照包被标记为已修改的过程称为“提升为一个extra-dep”，意味着我们对待它就像对待一个extra-deps下面的包一样。出现这种情况的原因很多，包括：

<!-- * changing the version of the snapshot package -->
* 改变快照包的版本
<!-- * changing build flags -->
* 改变构建标志
<!-- * one of the packages that the package depends on has been promoted to an
  extra-dep -->
* 该包依赖的某个包被提升为一个extra-dep

<!-- As you probably guessed, there are multiple snapshot databases available, e.g.: -->
你可能已经猜到，有多个快照数据库可用，例如：

```
michael@d30748af6d3d:~/helloworld$ ls ~/.stack/snapshots/x86_64-linux/
lts-2.22  lts-3.1  lts-3.2  nightly-2015-08-26
```

<!-- These databases don't get layered on top of each other; they are each used
separately. -->
这些数据库不会彼此叠加；它们各自单独使用。

<!-- In reality, you'll rarely — if ever — interact directly with these databases,
but it's good to have a basic understanding of how they work so you can
understand why rebuilding may occur at different points. -->
事实上，你将很少会——如果有的话——直接与这些数据库交互，但是理解它们怎么工作对有一个好基础有好处，这样你就能理解为什么重建会发生在不同的点。

<!-- ## The build synonyms -->
## build的同义词

<!-- Let's look at a subset of the `stack --help` output: -->
让我们看一下`stack --help`的部分输出：

```
build    Build the package(s) in this directory/configuration
install  Shortcut for 'build --copy-bins'
test     Shortcut for 'build --test'
bench    Shortcut for 'build --bench'
haddock  Shortcut for 'build --haddock'
```

<!-- Note that four of these commands are just synonyms for the `build` command. They
are provided for convenience for common cases (e.g., `stack test` instead of
`stack build --test`) and so that commonly expected commands just work. -->
注意这四个命令仅仅是`build`命令的同义词。它们通常情况下会提供方便（比如，用`stack test`代替`stack build --test`），所以通常期望的命令才有效。

<!-- What's so special about these commands being synonyms? It allows us to make
much more composable command lines. For example, we can have a command that
builds executables, generates Haddock documentation (Haskell API-level docs),
and builds and runs your test suites, with: -->
这些命令是同义词有什么特别的？它允许我们创建更多的可组合的命令行。比如，我们可以使用一个命令用来构建可执行程序，生成Haddock文档（Haskell API-level docs），并且构建和运行你的测试套件，通过：

```
stack build --haddock --test
```

<!-- You can even get more inventive as you learn about other flags. For example,
take the following: -->
当你学习其他标志的时候，你甚至可以变得更有创造力。比如，跟着下面做：

```
stack build --pedantic --haddock --test --exec "echo Yay, it succeeded" --file-watch
```

<!-- This will: -->
这将：

<!-- * turn on all warnings and errors -->
* 打开所有的警告和错误
<!-- * build your library and executables -->
* 构建你的库和可执行程序
<!-- * generate Haddocks -->
* 生成Haddocks
<!-- * build and run your test suite -->
* 构建并运行你的测试套件
<!-- * run the command `echo Yay, it succeeded` when that completes -->
* 完成后，运行命令`echo Yay, it succeeded`
<!-- * after building, watch for changes in the files used to build the project, and
  kick off a new build when done -->
* 在构建完之后，观察用于构建项目的文件的更改，并在完成后启动新构建

<!-- ### install and copy-bins -->
### install和copy-bins

<!-- It's worth calling out the behavior of the install command and `--copy-bins`
option, since this has confused a number of users (especially when compared to
behavior of other tools like cabal-install). The `install` command does
precisely one thing in addition to the build command: it copies any generated
executables to the local bin path. You may recognize the default value for that
path: -->
值得一提的是安装命令和`--copy-bins`选项的行为，因为这使许多用户感到困惑（特别是当和其他工具例如cabal-install的行为相比较时）。除了构建命令之外，`install`命令只做一件事：它拷贝生成的所有可执行文件到本地的bin路径。

```
michael@d30748af6d3d:~/helloworld$ stack path --local-bin
/home/michael/.local/bin
```

<!-- That's why the download page recommends adding that directory to your `PATH`
environment variable. This feature is convenient, because now you can simply
run `executable-name` in your shell instead of having to run
`stack exec executable-name` from inside your project directory. -->
这就是为什么下载页建议添加这个目录到你的`PATH`环境变量里。这个功能很方便，因为现在你可以在你的shell中直接运行`可执行文件名`，而不必在你项目目录里运行`stack exec 可执行文件名`。

<!-- Since it's such a point of confusion, let me list a number of things stack does
*not* do specially for the install command: -->
因为这是一个困惑的点，让我列出一些stack特地*不为*install命令做的事情：

<!-- * stack will always build any necessary dependencies for your code. The install
  command is not necessary to trigger this behavior. If you just want to build a
  project, run `stack build`. -->
* stack会总是为你的代码构建所有必要的依赖。而install命令没有必要触发这个行为。如果你只是想构建一个项目，那么就运行`stack build`。
<!-- * stack will *not* track which files it's copied to your local bin path nor
  provide a way to automatically delete them. There are many great tools out
  there for managing installation of binaries, and stack does not attempt to
  replace those. -->
* stack将*不会*跟踪被拷贝到你本地bin路径下面的文件，也没有提供自动删除他们的方式。有很多很棒的工具可以管理二进制文件的安装，所以stack不会试图取代它们。
<!-- * stack will not necessarily be creating a relocatable executable. If your
  executables hard-codes paths, copying the executable will not change those
  hard-coded paths. -->
* stack将不一定会创建一个可重定位的可执行文件。如果你的可执行文件硬编码路径，拷贝这个可执行文件将不会改变它被硬编码的路径。
    <!-- * At the time of writing, there's no way to change those kinds of paths with
      stack, but see [issue #848 about
      --prefix](https://github.com/commercialhaskell/stack/issues/848) for
      future plans. -->
    * 在编写本文时，没有办法通过stack来更改那些路径，但是请参阅[issue #848 about
      --prefix](https://github.com/commercialhaskell/stack/issues/848)以了解未来的计划

<!-- That's really all there is to the install command: for the simplicity of what
it does, it occupies a much larger mental space than is warranted. -->
这就是install命令的全部内容：为了操作的简便，它占用了比实际需要大得多的思维空间。

<!-- ## Targets, locals, and extra-deps -->
## targets，locals，和extra-deps

<!-- We haven't discussed this too much yet, but, in addition to having a number of
synonyms *and* taking a number of options on the command line, the build command
*also* takes many arguments. These are parsed in different ways, and can be used
to achieve a high level of flexibility in telling stack exactly what you want
to build. -->
虽然我们还没有太多讨论它，但是，除了有许多同义词*和*接收许多命令行选项外，build命令*也*需要一些参数。它们被使用不同方式解析，并且可以告诉stack执行你想要的构建以实现高度的灵活性。

<!-- We're not going to cover the full generality of these arguments here; instead,
there's [documentation covering the full build command
syntax](build_command.md).
Here, we'll just point out a few different types of arguments: -->
我们不打算在这里讨论所有这些参数；作为代替，这是[所有构建命令的文档](build_command.md)。这里，我们将只指出几个不同类型的参数：

<!-- * You can specify a *package name*, e.g. `stack build vector`. -->
* 你可以指定一个*包名*，比如`stack build vector`。
    <!-- * This will attempt to build the vector package, whether it's a local
      package, in your extra-deps, in your snapshot, or just available upstream.
      If it's just available upstream but not included in your locals,
      extra-deps, or snapshot, the newest version is automatically promoted to
      an extra-dep. -->
    * 这将尝试编译vector包，无论它是否是一个本地包、你extra-deps里的包、你快照里的包，或只是上游发布的包。如果它只是上游发布的包但是没有在你本地、extra-deps、或是快照里，那么它的最新版本将会被自动提升为一个extra-dep。
<!-- * You can also give a *package identifier*, which is a package name plus
  version, e.g. `stack build yesod-bin-1.4.14`. -->
* 你也可以规定一个*包标识符*，即一个包名加上版本号，比如`stack build yesod-bin-1.4.14`。
    <!-- * This is almost identical to specifying a package name, except it will (1)
      choose the given version instead of latest, and (2) error out if the given
      version conflicts with the version of a local package. -->
    * 这和指定包名几乎相同，除了它将（1）选择规定的版本来代替latest，并且（2）如果规定的版本和本地包的版本有冲突将会输出错误。
<!-- * The most flexibility comes from specifying individual *components*, e.g.
  `stack build helloworld:test:helloworld-test` says "build the test suite
  component named helloworld-test from the helloworld package." -->
* 最大的灵活性来自于指定单独的组件，比如`stack build helloworld:test:helloworld-test`，意思是“通过helloworld包构建名为helloworld-test的测试套件组件”。
    <!-- * In addition to this long form, you can also shorten it by skipping what
      type of component it is, e.g. `stack build helloworld:helloworld-test`, or
      even skip the package name entirely, e.g. `stack build :helloworld-test`. -->
    * 除了这种长形式，你也可以通过省略组件的类型来缩短它，例如`stack build helloworld:helloworld-test`，或者甚至完全省略掉包名，比如`stack build :helloworld-test`。
<!-- * Finally, you can specify individual *directories* to build to trigger building
  of any local packages included in those directories or subdirectories. -->
* 最后，你可以指定构建单独的*目录*来触发被包含在这些目录和子目录中的任何本地包的构建。

<!-- When you give no specific arguments on the command line (e.g., `stack build`),
it's the same as specifying the names of all of your local packages. If you
just want to build the package for the directory you're currently in, you can
use `stack build .`. -->
当你没有在命令行中指定具体的参数时（比如，`stack build`），它相当于指定你本地包中的所有名称。如果你只想构建你当前所在目录中的包，你可以使用`stack build .`。

<!-- ### Components, --test, and --bench -->
### 组件，--test，和--bench

<!-- Here's one final important yet subtle point. Consider our helloworld package:
it has a library component, an executable helloworld-exe, and a test suite
helloworld-test. When you run `stack build helloworld`, how does it know which
ones to build? By default, it will build the library (if any) and all of the
executables but ignore the test suites and benchmarks. -->
这里是最后一个重要但微妙的点。考虑我们的helloworld包：它有一个组件、一个可执行程序helloworld-exe、和一个测试套件helloworld-test。当你运行`stack build helloworld`时，它怎么知道该构建哪一个呢？默认情况下，它将构建库（如果有的话）和所有的可执行程序，但是会忽略测试套件和基准程序。

<!-- This is where the `--test` and `--bench` flags come into play. If you use them,
those components will also be included. So `stack build --test helloworld` will
end up including the helloworld-test component as well. -->
这就是把`--test`和`--bench`标志发挥作用的地方。如果你使用它们，那些组件也会被包含进去。所以`stack build --test helloworld`同时最终会包含helloworld-test组件。

<!-- You can bypass this implicit adding of components by being much more explicit,
and stating the components directly. For example, the following will not build
the helloworld-exe executable: -->
你可以通过更加显式的方式来绕过隐式添加组件，并且直接声明组件。比如，下面将不会构建helloworld-exe可执行程序：

```
michael@d30748af6d3d:~/helloworld$ stack clean
michael@d30748af6d3d:~/helloworld$ stack build :helloworld-test
helloworld-0.1.0.0: configure (test)
Configuring helloworld-0.1.0.0...
helloworld-0.1.0.0: build (test)
Preprocessing library helloworld-0.1.0.0...
[1 of 1] Compiling Lib              ( src/Lib.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.4.0/build/Lib.o )
In-place registering helloworld-0.1.0.0...
Preprocessing test suite 'helloworld-test' for helloworld-0.1.0.0...
[1 of 1] Compiling Main             ( test/Spec.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.4.0/build/helloworld-test/helloworld-test-tmp/Main.o )
Linking .stack-work/dist/x86_64-linux/Cabal-1.22.4.0/build/helloworld-test/helloworld-test ...
helloworld-0.1.0.0: test (suite: helloworld-test)
Test suite not yet implemented
```

<!-- We first cleaned our project to clear old results so we know exactly what stack
is trying to do. Notice that it builds the helloworld-test test suite, and the
helloworld library (since it's used by the test suite), but it does not build
the helloworld-exe executable. -->
我们首先清除了我们的项目旧的的结果，因此我们能准确地知道stack试图做什么。注意它构建了helloworld-test测试套件，和halloworld库（因为它会被测试套件使用），但是它没有构建helloworld-exe可执行程序。

<!-- And now the final point: the last line shows that our command also *runs* the
test suite it just built. This may surprise some people who would expect tests
to only be run when using `stack test`, but this design decision is what allows
the `stack build` command to be as composable as it is (as described
previously). The same rule applies to benchmarks. To spell it out completely: -->
现在最后一点：最后一行显示我们的命令还*运行了*刚刚生成的测试套件。这可能会让一些人感到惊讶，他们原本以为只有用`stack test`时才会运行测试，但是这个设计决策允许`stack build`命令像它一样可组合（如前所述）。同样的规则也适用于基准程序。把它完整地拼写出来：

<!-- * The --test and --bench flags simply state which components of a package should
  be built, if no explicit set of components is given -->
* 如果没有显示指定组件集合，那么`--test`和`--bench`标志会简单地说明包里的哪些组件会被构建
<!-- * The default behavior for any test suite or benchmark component which has been
  built is to also run it -->
* 任何已构建的测试套件和基准程序组件的默认行为都是运行它

<!-- You can use the `--no-run-tests` and `--no-run-benchmarks` (from stack-0.1.4.0
and on) flags to disable running of these components. You can also use
`--no-rerun-tests` to prevent running a test suite which has already passed and
has not changed. -->
你可以用 `--no-run-tests`和`--no-run-benchmarks`（从stack-0.1.4.0开始）标志来禁止运行这些组件。你也可以使用`--no-rerun-tests`来防止运行已经通过测试且没有改动的测试套件。

<!-- NOTE: stack doesn't build or run test suites and benchmarks for non-local
packages. This is done so that running a command like `stack test` doesn't need
to run 200 test suites! -->
注意：stack不会构建或运行非本地包的测试套件和基准程序。这样一来，运行`stack test`这样的命令就不需要运行200个测试套件！

<!-- ## Multi-package projects -->
## 多包项目

<!-- Until now, everything we've done with stack has used a single-package project.
However, stack's power truly shines when you're working on multi-package
projects. All the functionality you'd expect to work just does: dependencies
between packages are detected and respected, dependencies of all packages are
just as one cohesive whole, and if anything fails to build, the build commands
exits appropriately. -->
直到现在，我们用stack完成的任何事情都是用的单包项目。然而，当你做多包项目时，stack的力量真的很耀眼。所有你期望的功能就是这样：检查并遵守包之间的依赖关系、把所有包之间的依赖关系作为一个内聚的整体、并且如果构建失败，构建命令将合适地退出。

<!-- Let's demonstrate this with the wai-app-static and yackage packages: -->
让我们用wai-app-static和yackage包演示这一点：

```
michael@d30748af6d3d:~$ mkdir multi
michael@d30748af6d3d:~$ cd multi/
michael@d30748af6d3d:~/multi$ stack unpack wai-app-static-3.1.1 yackage-0.8.0
wai-app-static-3.1.1: download
Unpacked wai-app-static-3.1.1 to /home/michael/multi/wai-app-static-3.1.1/
Unpacked yackage-0.8.0 to /home/michael/multi/yackage-0.8.0/
michael@d30748af6d3d:~/multi$ stack init
Writing default config file to: /home/michael/multi/stack.yaml
Basing on cabal files:
- /home/michael/multi/yackage-0.8.0/yackage.cabal
- /home/michael/multi/wai-app-static-3.1.1/wai-app-static.cabal

Checking against build plan lts-3.2
Selected resolver: lts-3.2
Wrote project config to: /home/michael/multi/stack.yaml
michael@d30748af6d3d:~/multi$ stack build --haddock --test
# Goes off to build a whole bunch of packages
```

<!-- If you look at the `stack.yaml`, you'll see exactly what you'd expect: -->
如果你查看`stack.yaml`，你将确切地看到你期待的东西：

```yaml
flags:
  yackage:
    upload: true
  wai-app-static:
    print: false
packages:
- yackage-0.8.0/
- wai-app-static-3.1.1/
extra-deps: []
resolver: lts-3.2
```

<!-- Notice that multiple directories are listed in the `packages` key. -->
注意，在`packages`键中列出了多个目录。

<!-- In addition to local directories, you can also refer to packages available in a
Git repository or in a tarball over HTTP/HTTPS. This can be useful for using a
modified version of a dependency that hasn't yet been released upstream. -->
除了本地目录，你也可以通过HTTP/HTTPS引用Git仓库或者tarball里可用的包。这对使用没有被上游发布的修改版的依赖有用。

<!-- Please note that when adding upstream packages directly to your project it is
important to distinguish _local packages_ from the upstream _dependency
packages_. Otherwise you may have trouble running `stack ghci`. See
[stack.yaml documentation](yaml_configuration.md#packages) for more details. -->
请注意，当直接添加一个上游包到你的项目里时，区分 _本地包_ 和上游 _依赖包_ 是很重要的。否则你可能在运行`stack ghci`时遇到麻烦。更多详细信息，请参考[stack.yaml documentation](yaml_configuration.md#packages)。

<!-- ## Flags and GHC options -->
## 标志和GHC选项

<!-- There are two common ways to alter how a package will install: with Cabal flags
and with GHC options. -->
安装包有两种通用方式：用Cabal标志或用GHC选项。

<!-- ### Cabal flag management -->
### Cabal标志管理


<!-- In the `stack.yaml` file above, you can see that `stack init` has detected that —
for the yackage package — the upload flag can be set to true, and for
wai-app-static, the print flag to false (it's chosen those values because
they're the default flag values, and their dependencies are compatible with the
snapshot we're using.) To change a flag setting, we can use the command
line `--flag` option: -->
在上面的`stack.yaml`文件里，你可以看到，`stack init`已经发现，yackage包的上传标志可以设置为true，wai-app-static的打印标志可以设置为false（之所以选择这些值，是因为它们是默认的标志值，并且它们的依赖和我们用的快照兼容）。我们可以使用命令行选项`--flag`来改变标志设置：

    stack build --flag yackage:-upload

<!-- This means: when compiling the yackage package, turn off the upload flag (thus
the `-`). Unlike other tools, stack is explicit about which package's flag you
want to change. It does this for two reasons: -->
它的意思是：当编译yackage包时，关闭上传标志（所以是`-`）。不像其他工具，stack会显示你想修改的包的标签。这样做有两个原因：

<!-- 1. There's no global meaning for Cabal flags, and therefore two packages can
   use the same flag name for completely different things. -->
1. Cabal标志没有全局意义，因此两个包可以使用相同的标志名做不同的事情。
<!-- 2. By following this approach, we can avoid unnecessarily recompiling snapshot
   packages that happen to use a flag that we're using. -->
2. 通过以下途径，我们可以使用标志避免不必要地重新编译我们正在用的快照包

<!-- You can also change flag values on the command line for extra-dep and snapshot
packages. If you do this, that package will automatically be promoted to an
extra-dep, since the build plan is different than what the plan snapshot
definition would entail. -->
你也可以在命令行中修改extra-dep和快照包的标志值。如果你这么做，这个包将会自动提升为一个extra-dep，因为会导致构建计划和快照中定义的不一样。

<!-- ### GHC options -->
### GHC选项

<!-- GHC options follow a similar logic as in managing Cabal flags, with a few
nuances to adjust for common use cases. Let's consider: -->
GHC选项遵循类似Cabal标志管理的逻辑，但和常见用例有少量细微差别。让我们考虑一下：

    stack build --ghc-options="-Wall -Werror"

<!-- This will set the `-Wall -Werror` options for all *local targets*. Note that
this will not affect extra-dep and snapshot packages at all. This design
provides us with reproducible and fast builds. -->
它将为所有*本地目标*设置`-Wall -Werror`选项。注意它将一点也不会影响extra-dep和快照包。这个设计支持我们可以重复和快速地构建。

<!-- (By the way: the above GHC options have a special convenience flag:
`--pedantic`.) -->
（顺便说一下：以上的GHC选项有一个特别方便的标志代替：`--pedantic`）

<!-- There's one extra nuance about command line GHC options: Since they only apply
to local targets, if you change your local targets, they will no longer apply
to other packages. Let's play around with an example from the wai repository,
which includes the wai and warp packages, the latter depending on the former.
If we run: -->
命令行GHC选项有一个关于额外的细微差别：因为它们仅仅作用于本地目标，如果你修改你的本地目标，它们将不再作用于其他的包。让我们看看wai仓库里的一个例子，它包括wai和warp包，后者依赖former。如果我们运行：

    stack build --ghc-options=-O0 wai

<!-- It will build all of the dependencies of wai, and then build wai with all
optimizations disabled. Now let's add in warp as well: -->
它将构建wai的所有依赖，然后关闭所有优化构建wai。现在让我们像这样把warp添加进去：

    stack build --ghc-options=-O0 wai warp

<!-- This builds the additional dependencies for warp, and then builds warp with
optimizations disabled. Importantly: it does not rebuild wai, since wai's
configuration has not been altered. Now the surprising case: -->
warp为构建提供了额外的依赖项，然后关闭优化构建warp。重要的是：他不会重新构建wai，因为wai的配置没有被修改。现在出人意料的是：

```
michael@d30748af6d3d:~/wai$ stack build --ghc-options=-O0 warp
wai-3.0.3.0-5a49351d03cba6cbaf906972d788e65d: unregistering (flags changed from ["--ghc-options","-O0"] to [])
warp-3.1.3-a91c7c3108f63376877cb3cd5dbe8a7a: unregistering (missing dependencies: wai)
wai-3.0.3.0: configure
```

<!-- You may expect this to be a no-op: neither wai nor warp has changed. However,
stack will instead recompile wai with optimizations enabled again, and then
rebuild warp (with optimizations disabled) against this newly built wai. The
reason: reproducible builds. If we'd never built wai or warp before, trying to
build warp would necessitate building all of its dependencies, and it would do
so with default GHC options (optimizations enabled). This dependency would
include wai. So when we run: -->
你也许会认为这是一个空操作：即wai和warp都没改变。然而，stack将开启优化的情况下重新编译wai，然后重新构建warp（在关闭优化的情况下）。原因是：可重现的构建。如果我们之前不构建wai或者warp，而尝试去构建warp将必要地构建它的所有依赖，并且它将使用默认的GHC选项（开启优化）来做这个。这些依赖将会包括wai。所以当我们运行：

    stack build --ghc-options=-O0 warp

<!-- We want its behavior to be unaffected by any previous build steps we took.
While this specific corner case does catch people by surprise, the overall goal
of reproducible builds is- in the stack maintainers' views- worth the
confusion. -->
我们希望它的行为不受我们之前任何构建步骤的影响。虽然这种特殊的情况确实让人们感到意外，但在Haskell维护者看来，为了可复现构建的总体目标，这样的混乱是值得的。

<!-- Final point: if you have GHC options that you'll be regularly passing to your
packages, you can add them to your `stack.yaml` file (starting with
stack-0.1.4.0). See [the documentation section on
ghc-options](yaml_configuration.md#ghc-options)
for more information. -->
最后一点：如果你有你要经常传递到你的包里的GHC选项，你可以把它添加到你的`stack.yaml`文件里（从stack-0.1.4.0开始）。参阅[关于GHC选项部分的文档](yaml_configuration.md#ghc-options)以获取更多信息。

## path

<!-- NOTE: That's it, the heavy content of this guide is done! Everything from here
on out is simple explanations of commands. Congratulations! -->
注意：到这里，本指南重要的内容已经结束！从现在开始，一切都是对命令的简单说明。恭喜！

<!-- Generally, you don't need to worry about where stack stores various files. But
some people like to know this stuff. That's when the `stack path` command is
useful. -->
通常，你不需要当心stack把各种各样的文件保存到哪里了。但有的人喜欢知道这些东西。这就是`stack path`有用的时候了。

```
michael@d30748af6d3d:~/wai$ stack path
global-stack-root: /home/michael/.stack
stack-root: /home/michael/.stack
project-root: /home/michael/wai
config-location: /home/michael/wai/stack.yaml
bin-path: /home/michael/.stack/snapshots/x86_64-linux/lts-2.17/7.8.4/bin:/home/michael/.stack/programs/x86_64-linux/ghc-7.8.4/bin:/home/michael/.stack/programs/x86_64-linux/ghc-7.10.2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
programs: /home/michael/.stack/programs/x86_64-linux
compiler: /home/michael/.stack/programs/x86_64-linux/ghc-7.8.4/bin/ghc
compiler-bin: /home/michael/.stack/programs/x86_64-linux/ghc-7.8.4/bin
local-bin-path: /home/michael/.local/bin
extra-include-dirs:
extra-library-dirs:
snapshot-pkg-db: /home/michael/.stack/snapshots/x86_64-linux/lts-2.17/7.8.4/pkgdb
local-pkg-db: /home/michael/wai/.stack-work/install/x86_64-linux/lts-2.17/7.8.4/pkgdb
global-pkg-db: /home/michael/.stack/programs/x86_64-linux/ghc-7.8.4/lib/ghc-7.8.4/package.conf.d
ghc-package-path: /home/michael/wai/.stack-work/install/x86_64-linux/lts-2.17/7.8.4/pkgdb:/home/michael/.stack/snapshots/x86_64-linux/lts-2.17/7.8.4/pkgdb:/home/michael/.stack/programs/x86_64-linux/ghc-7.8.4/lib/ghc-7.8.4/package.conf.d
snapshot-install-root: /home/michael/.stack/snapshots/x86_64-linux/lts-2.17/7.8.4
local-install-root: /home/michael/wai/.stack-work/install/x86_64-linux/lts-2.17/7.8.4
snapshot-doc-root: /home/michael/.stack/snapshots/x86_64-linux/lts-2.17/7.8.4/doc
local-doc-root: /home/michael/wai/.stack-work/install/x86_64-linux/lts-2.17/7.8.4/doc
dist-dir: .stack-work/dist/x86_64-linux/Cabal-1.18.1.5
local-hpc-root: /home/michael/wai/.stack-work/install/x86_64-linux/lts-2.17/7.8.4/hpc
```

<!-- In addition, `stack path` accepts command line arguments to state which of
these keys you're interested in, which can be convenient for scripting. As a
simple example, let's find out the sandboxed versions of GHC that stack installed: -->
此外，`stack path`接受命令行参数来声明你感兴趣的键，这对编写脚本很方便。作为一个简单的例子，让我们找到stack安装的GHC沙盒版本：

```
michael@d30748af6d3d:~/wai$ ls $(stack path --programs)/*.installed
/home/michael/.stack/programs/x86_64-linux/ghc-7.10.2.installed
/home/michael/.stack/programs/x86_64-linux/ghc-7.8.4.installed
```

<!-- (Yes, that command requires a \*nix shell, and likely won't run on Windows.) -->
（是的，这个命令需要一个\*nix shell，所以在Windows不能上像这样运行。）

<!-- While we're talking about paths, to wipe our stack install completely, here's
what needs to be removed: -->
当我们讨论路径的时候，为了完全清楚stack的安装，这里是需要被移除的东西：

<!-- 1. The stack executable itself -->
1. stack可执行程序本身
<!-- 2. The stack root, e.g. `$HOME/.stack` on non-Windows systems or, on Windows,
   `%LOCALAPPDATA%\Programs\stack`. -->
2. stack根目录，比如非Windows系统上的`$HOME/.stack`，或者Windows系统上的`%LOCALAPPDATA%\Programs\stack`。
    <!-- * See `stack path --stack-root` -->
    * 查看`stack path --stack-root`
    <!-- * On Windows, you will also need to delete `stack path --programs` -->
    * 在Windows系统上，你同时需要删除`stack path --programs`
<!-- 3. Any local `.stack-work` directories inside a project -->
3. 任何项目中的本地`.stack-work`目录

## exec

<!-- We've already used `stack exec` multiple times in this guide. As you've
likely already guessed, it allows you to run executables, but with a slightly
modified environment. In particular: `stack exec` looks for executables on
stack's bin paths, and sets a few additional environment variables (like adding
those paths to `PATH`, and setting `GHC_PACKAGE_PATH`, which tells GHC which
package databases to use). -->
在本指南中我们已经用过`stack exec`很多次。就如你可能已经猜到的，它允许你运行可执行命令，但是有一个稍微修改过的环境。特别是：`stack exec`在stack的路径中寻找可执行程序，和设置一些额外的环境变量（像添加这些路径到`path`、设置`GHC_PACKAGE_PATH`，这将告诉GHC使用哪一个包数据库）。

<!-- If you want to see exactly what the modified environment looks like, try: -->
如果你想准确地看看修改后的环境是什么样的，试试：

    stack exec env

<!-- The only issue is how to distinguish flags to be passed to stack versus those
for the underlying program. Thanks to the optparse-applicative library, stack
follows the Unix convention of `--` to separate these, e.g.: -->
这唯一的问题是怎么区分传递给stack的标志与传递给那些底层程序的标志。感谢optparse-applicative库，stack跟随Unix惯例使用`--`来区分它们，例如：

```
michael@d30748af6d3d:~$ stack exec --package stm -- echo I installed the stm package via --package stm
Run from outside a project, using implicit global project config
Using latest snapshot resolver: lts-3.2
Writing global (non-project-specific) config file to: /home/michael/.stack/global/stack.yaml
Note: You can change the snapshot via the resolver field there.
I installed the stm package via --package stm
```

<!-- Flags worth mentioning: -->
值得一提的标志：

<!-- * `--package foo` can be used to force a package to be installed before running
  the given command. -->
* `--package foo`可以用来在运行特定命令前强制安装一个包。
<!-- * `--no-ghc-package-path` can be used to stop the `GHC_PACKAGE_PATH` environment
  variable from being set. Some tools — notably cabal-install — do not behave
  well with that variable set. -->
* `--no-ghc-package-path`可以用来阻止`GHC_PACKAGE_PATH`环境变量被设置。一些工具——特别是cabal-install——在设置这个变量的情况下表现不好。

<!-- ## ghci (the repl) -->
## ghci（交互式解释器）

<!-- GHCi is the interactive GHC environment, a.k.a. the REPL. You *could* access it
with: -->
GHCi是一个交互式的GHC环境，又名REPL（交互式解释器）。你*可以*这样访问它：

    stack exec ghci

<!-- But that won't load up locally written modules for access. For that, use the
`stack ghci` command. To then load modules from your project, use the `:m`
command (for "module") followed by the module name. -->
但这不会加载本地编写的模块来访问。因此，使用`stack ghci`命令。为了从你的项目中加载模块，使用`:m`命令（即“module”），后跟模块名。

<!-- IMPORTANT NOTE: If you have added upstream packages to your project please make
sure to mark them as *dependency package*s for faster and reliable usage of
`stack ghci`. Otherwise GHCi may have trouble due to conflicts of compilation
flags or having to unnecessarily interpret too many modules. See
[stack.yaml documentation](yaml_configuration.md#packages) to learn how to mark
a package as a *dependency package*. -->
重要提示：如果你添加过上游包到你的项目里，那么请确认让它们标记为*依赖包*，以便更快和更可靠地使用`stack ghci`。否则GHCi可能由于编译标志的冲突或是不必要地解释太多模块而出问题。参考[stack.yaml documentation](yaml_configuration.md#packages)学习怎么把一个包做成一个*依赖包*。

## ghc/runghc

<!-- You'll sometimes want to just compile (or run) a single Haskell source file,
instead of creating an entire Cabal package for it. You can use `stack exec ghc`
or `stack exec runghc` for that. As simple helpers, we also provide the
`stack ghc` and `stack runghc` commands, for these common cases. -->
你有时候会仅仅想编译（或运行）一个单独的Haskell源文件，而不是为它创建一整个Cabal包。为此，你可以使用`stack exex ghc`或者`stack exec runghc`。作为简单的帮助，我们也为这些常见场景提供了`stack ghc`和`stack runghc`命令。

<!-- ## script interpreter -->
## 脚本解释器

<!-- stack also offers a very useful feature for running files: a script
interpreter. For too long have Haskellers felt shackled to bash or Python
because it's just too hard to create reusable source-only Haskell scripts.
stack attempts to solve that. -->
stack也提供一个非常有用的功能用来运行文件：一个脚本解释器。长期以来，Haskellers一直觉得自己被束缚在bash或Python中，因为创建一个可重复使用的纯Haskell源码的脚本太困难了。stack尝试去解决这个问题。

<!-- You can use `stack <file name>` to execute a Haskell source file or specify
`stack` as the interpreter using a shebang line on a Unix like operating systems.
Additional stack options can be specified using a special Haskell comment in
the source file to specify dependencies and automatically install them before
running the file. -->
你可以用`stack <文件名>`来运行一个Haskell源文件，或者在类Unix操作系统上指定`stack`作为`shebang`行的解释器。使用一个特殊的Haskell注释可以在源文件中指定额外的stack选项，用以指定依赖并在文件运行前自动安装他们。

<!-- An example will be easiest to understand: -->
一个例子最容易理解：

```
michael@d30748af6d3d:~$ cat turtle-example.hs
#!/usr/bin/env stack
-- stack --resolver lts-6.25 script --package turtle
{-# LANGUAGE OverloadedStrings #-}
import Turtle
main = echo "Hello World!"
michael@d30748af6d3d:~$ chmod +x turtle-example.hs
michael@d30748af6d3d:~$ ./turtle-example.hs
Completed 5 action(s).
Hello World!
michael@d30748af6d3d:~$ ./turtle-example.hs
Hello World!
```

<!-- The first run can take a while (as it has to download GHC if necessary and build
dependencies), but subsequent runs are able to reuse everything already built,
and are therefore quite fast. -->
第一次运行需要一段时间（因为如果必要的话它要下载GHC，并且构建依赖），但是随后的运行将会重用已经构建好的一切，并且因此非常快。

<!-- The first line in the source file is the usual "shebang" to use stack as a
script interpreter. The second line, is a Haskell comment providing additional
options to stack (due to the common limitation of the "shebang" line only being
allowed a single argument). In this case, the options tell stack to use the
lts-3.2 resolver, automatically install GHC if it is not already installed, and
ensure the turtle package is available. -->
源文件的第一行通常是“shebang”，用来使用stack脚本解释器。第二行，是一个Haskell注释，支持添加选项到stack（因为“shebang”行限制只允许单个参数）。在本实例中，选项告诉stack使用lts-3.2版本解析器、如果GHC没有安装的话自动安装、并确保turtle包是可用的。

<!-- If you're on Windows: you can run `stack turtle.hs` instead of `./turtle.hs`.
The shebang line is not required in that case. -->
如果你是在Windows：你可以运行`stack turtle.hs`来代替`./turtle.hs`。在这种情况下不需要shebang行。

<!-- ### Using multiple packages -->
### 使用多个包

<!-- You can also specify multiple packages, either with multiple `--package`
arguments, or by providing a comma or space separated list. For example: -->
你也可是指定多个包，要么用多个`--package`参数，或者通过提供一个由逗号或空格分隔的列表。例如：

```
#!/usr/bin/env stack
{- stack
  script
  --resolver lts-6.25
  --package turtle
  --package "stm async"
  --package http-client,http-conduit
-}
```

<!-- ### Stack configuration for scripts -->
### 用于编写脚本的stack配置

<!-- With the `script` command, all Stack configuration files are ignored to provide a
completely reliable script running experience. However, see the example below
with `runghc` for an approach to scripts which will respect your configuration
files. When using `runghc`, if the current working directory is inside a
project then that project's stack configuration is effective when running the
script. Otherwise the script uses the global project configuration specified in
`~/.stack/global-project/stack.yaml`. -->
在使用`script`命令的时候，所有stack配置文件将被忽略，以提供完整可靠的运行体验。然而，参考下面用`runghc`的例子，一种遵循你配置文件的编写脚本的方法。当使用`runghc`的时候，如果当前工作目录在一个项目中，那么当运行脚本的时候这个项目的stack配置将是有效的。否则脚本将使用定义`~/.stack/global-project/stack.yaml`中的在全局项目配置。

<!-- ### Specifying interpreter options -->
### 指定解释器选项

<!-- The stack interpreter options comment must specify a single valid stack command
line, starting with `stack` as the command followed by the stack options to use
for executing this file. The comment must always be on the line immediately
following the shebang line when the shebang line is present otherwise it must
be the first line in the file. The comment must always start in the first
column of the line. -->
stack解释器选项注释必须指定一个单独有效的stack命令行，命令的开始是`stack`，接着是用来运行这个文件的stack选项。当shebang行存在时这个注释必须始终位于紧跟在shebang行的下一行，否则必须在文件的第一行。而且这个注释必须始终从该行的第一列开始。

<!-- When many options are needed a block style comment may be more convenient to
split the command on multiple lines for better readability. You can also
specify ghc options the same way as you would on command line i.e. by
separating the stack options and ghc options with a `--`. Here is an example of
a multi line block comment with ghc options: -->
当一些选项需要一个块风格的注释时，为更好的阅读，也许把注释分割成多行更便利。您也可以使用与命令行相同的方式指定ghc选项，即通过用`--`分隔stack选项和ghc选项。这里是一个关于使用ghc选项的多行块注释的例子：

```
#!/usr/bin/env stack
{- stack
  script
  --resolver lts-6.25
  --package turtle
  --
  +RTS -s -RTS
-}
```

<!-- ### Writing independent and reliable scripts -->
### 编写独立可靠的脚本

<!-- With the release of Stack 1.4.0, there is a new command, `script`, which will
automatically: -->
随着Stack 1.4.0的发布，一个新的命令，`script`，将自动做到：

<!-- * Install GHC and libraries if missing -->
* 安装GHC和库，如果缺少的话
<!-- * Require that all packages used be explicitly stated on the command line -->
* 使用在命令行中显示声明的方式请求所有的包

<!-- This ensures that your scripts are _independent_ of any prior deployment
specific configuration, and are _reliable_ by using exactly the same version of
all packages every time it runs so that the script does not break by
accidentally using incompatible package versions. -->
这确保你的脚本是 _独立_ 于任何之前部署的具体配置，并且通过在每次运行的时候使用所有的包的完全相同版本，以便于脚本不会因为使用其他不相容版本的包出现意外退出，从而确保脚本是 _可靠的_。

<!-- In previous versions of Stack, the `runghc` command was used for scripts
instead. In order to achieve the same effect with the `runghc` command, you can
do the following: -->
在之前版本的stack中，`runghc`命令被用来代替这个脚本。为了用`runghc`命令实现相同的作用，你可以像下面这样做：

<!-- 1. Use the `--install-ghc` option to install the compiler automatically -->
1. 使用`--install-ghc`选项来自动安装编译器
<!-- 2. Explicitly specify all packages required by the script using the
`--package` option. Use `-hide-all-packages` ghc option to force
explicit specification of all packages. -->
2. 通过脚本使用`--package`选项显示指定所有需要的包。使用ghc选项`-hide-all-packages`来强制显示指定所有的包。
<!-- 3. Use the `--resolver` Stack option to ensure a specific GHC version and
   package set is used. -->
3. 使用Stack选项`--resolver`来确保使用特定的GHC版本和包集合。

<!-- Even with this configuration, it is still possible for configuration
files to impact `stack runghc`, which is why `stack script` is strongly
recommended in general. For those curious, here is an example with `runghc`: -->
即使像这样设置，配置文件仍然可能影响`stack runghc`，这就是为什么一般强烈推荐`stack script`。对于那些特殊情况，这里是一个使用`runghc`的例子：

```
#!/usr/bin/env stack
{- stack
  --resolver lts-6.25
  --install-ghc
  runghc
  --package base
  --package turtle
  --
  -hide-all-packages
  -}
```

<!-- The `runghc` command is still very useful, especially when you're working on a
project and want to access the package databases and configurations used by
that project. See the next section for more information on configuration files. -->
`runghc`命令仍然是非常有用的，特别是当你工作在一个项目上并且希望访问这个项目使用的包数据库和配置时。参考下一节来获取配置文件的更多信息。

<!-- ### Loading scripts in ghci -->
### 在ghci中加载脚本

<!-- Sometimes you want to load your script in ghci REPL to play around with your
program. In those cases, you can use `exec ghci` option in the script to achieve
it. Here is an example: -->
有时你希望在ghci交互式解释器中加载你的脚本来摆弄你的程序。这种情况下，你可以在脚本中使用`exec ghci`选项实现它。这里是一个例子：

```
#!/usr/bin/env stack
{- stack
     --resolver lts-8.2
     --install-ghc
     exec ghci
     --package text
-}
```

<!-- ## Finding project configs, and the implicit global project -->
## 找到项目配置和隐式全局项目

<!-- Whenever you run something with stack, it needs a `stack.yaml` project file. The
algorithm stack uses to find this is: -->
每当你用stack运行一些东西的时候，他都需要一个`stack.yaml`项目文件。stack用来寻找它的算法是：

<!-- 1. Check for a `--stack-yaml` option on the command line -->
1. 检查命令行中的`--stack-yaml`选项
<!-- 2. Check for a `STACK_YAML` environment variable -->
2. 检查`STACK_YAML`环境变量
<!-- 3. Check the current directory and all ancestor directories for a `stack.yaml` -->
3. 检查当前目录和所有祖先目录里的`stack.yaml`

<!-- The first two provide a convenient method for using an alternate configuration.
For example: `stack build --stack-yaml stack-7.8.yaml` can be used by your CI
system to check your code against GHC 7.8. Setting the `STACK_YAML` environment
variable can be convenient if you're going to be running commands like `stack ghc`
in other directories, but you want to use the configuration you defined in
a specific project. -->
前两个提供了一个使用备用配置的方便方法。例如：`stack build --stack-yaml stack-7.8.yaml`可以被用于你的CI系统根据GHC 7.8来检查你的代码。如果你打算在别的目录里运行`stack ghc`之类命令，但是你想使用你定义在具体项目中的配置，设置`STACK_YAML`环境变量会变得很方便。

<!-- If stack does not find a `stack.yaml` in any of the three specified locations,
the *implicit global* logic kicks in. You've probably noticed that phrase a few
times in the output from commands above. Implicit global is essentially a hack
to allow stack to be useful in a non-project setting. When no implicit global
config file exists, stack creates one for you with the latest LTS snapshot as
the resolver. This allows you to do things like: -->
如果stack没有在那三个规定的位置中找到`stack.yaml`，那么*隐式全局*将生效。你很可能在之前命令的输出中注意到几次这个短语。隐式全局实际上是一个hack，用来允许stack在没有项目设置的情况下可以使用。当隐式全局配置文件不存在时，stack将为使用最新的LTS快照作为解释器为你创建一个。它允许你做以下操作：

<!-- * compile individual files easily with `stack ghc` -->
* 使用`stack ghc`简单地编译单独的文件
<!-- * build executables without starting a project, e.g. `stack install pandoc` -->
* 无需启动项目即可构建可执行文件，例如`stack install pandoc`

<!-- Keep in mind that there's nothing magical about this implicit global
configuration. It has no impact on projects at all. Every package you install
with it is put into isolated databases just like everywhere else. The only magic
is that it's the catch-all project whenever you're running stack somewhere else. -->
记住关于隐式全局配置没有魔法。它对项目没有任何影响。和其他地方一样，您安装的每个软件包都被放入独立的数据库中。唯一的魔法是，任何时候在其他地方运行stack，它都是一个面面俱到的项目。

<!-- ## Setting stack root location -->
## 设置stack的根位置

<!-- `stack path --stack-root` will tell you the location of the "stack root". Among
other things, this is where stack stores downloaded programs and snapshot
packages. This location can be configured by setting the STACK_ROOT environment
variable or passing the `--stack-root` commandline option. It is particularly
useful to do this on Windows, where filepaths are limited (MAX_PATH), and things
can break when this limit is exceeded. -->
`stack path --stack-root`会告诉你“stack根”的位置。除了别的以外，这是stack存储下载的程序和快照包的位置。这个位置可以通过设置STACK_ROOT环境变量或者传递`--stack-root`命令行选项来配置。在Windows平台上这样做尤其有用，因为Windows的文件路径有限的（最大路径），当超过这个限制时，文件路径可能会崩溃。

<!-- ## `stack.yaml` vs `.cabal` files -->
## 对比`stack.yaml`和`.cabal`文件

<!-- Now that we've covered a lot of stack use cases, this quick summary of
`stack.yaml` vs `.cabal` files will hopefully make sense and be a good reminder for
future uses of stack: -->
现在我们已经涵盖率许多stack用例，这个关于对比`stack.yaml`和`.cabal`文件的简短摘要希望很有意义，并且对将来使用stack是一个好的提醒：

<!-- * A project can have multiple packages. -->
* 一个项目可以有多个包。
<!-- * Each project has a `stack.yaml`. -->
* 每一个项目都有一个`stack.yaml`。
<!-- * Each package has a `.cabal` file. -->
* 每一个项目都有一个`.cabal`文件。
<!-- * The `.cabal` file specifies which packages are dependencies. -->
* `.cabal`文件指定哪些包是依赖项。
<!-- * The `stack.yaml` file specifies which packages are available to be used. -->
* `stack.yaml`文件指定哪些包是可用的。
<!-- * `.cabal` specifies the components, modules, and build flags provided by a package -->
* `.cabal`指定包提供的组件、模块、和构建标志。
<!-- * `stack.yaml` can override the flag settings for individual packages -->
* `stack.yaml`可以覆盖单个包的标志设置。
<!-- * `stack.yaml` specifies which packages to include -->
* `stack.yaml`指定包含那些包

<!-- ## Comparison to other tools -->
## 对照其他工具

<!-- stack is not the only tool around for building Haskell code. stack came into
existence due to limitations with some of the existing tools. If you're
unaffected by those limitations and are happily building Haskell code, you may
not need stack. If you're suffering from some of the common problems in other
tools, give stack a try instead. -->
stack不是构建Haskell代码的唯一工具。stack的出现是由于现有工具的一些限制。如果你没有被这些限制影响，并且能开心的构建Haskell代码，那么你也许就不需要stack。如果你痛苦与其他工具的一些常见的问题，那就试试stack。

<!-- If you're a new user who has no experience with other tools, we recommend going
with stack. The defaults match modern best practices in Haskell development, and
there are less corner cases you need to be aware of. You *can* develop Haskell
code with other tools, but you probably want to spend your time writing code,
not convincing a tool to do what you want. -->
如果你是一个没有其他工具使用经验的新用户，我们推荐使用stack。默认与Haskell开发的现代最佳用法相匹配，并且你需要知道的边角案例更少。你*可以*使用其他工具开发Haskell，但是你大概希望花时间写代码，而不是花时间说服工具执行您想要的操作。

<!-- Before jumping into the differences, let me clarify an important similarity: -->
在开始讨论差异之前，让我先阐明一个重要的相似之处:

<!-- __Same package format.__ stack, cabal-install, and presumably all other tools
share the same underlying Cabal package format, consisting of a `.cabal` file,
modules, etc. This is a Good Thing: we can share the same set of upstream
libraries, and collaboratively work on the same project with stack,
cabal-install, and NixOS. In that sense, we're sharing the same ecosystem. -->
__同样的包格式。__ stack、cabal-insall、和大概所有其他的工具共享同样的底层包格式，由一个`.cabal`文件、模块等组成。这是一件好事：我们可以共享同样的上游库集合，并且使用stack、cabal-install、和NixOS协作处理同一个项目。从这个意义上说，我们共享了同一个生态系统。

<!-- Now the differences: -->
现在是差异：

<!-- * __Curation vs dependency solving as a default__. -->
* __默认情况下，对比综合管理和依赖解析__。
    <!-- * stack defaults to using curation (Stackage snapshots, LTS Haskell,
      Nightly, etc) as a default instead of defaulting to dependency solving, as
      cabal-install does. This is just a default: as described above, stack can
      use dependency solving if desired, and cabal-install can use curation.
      However, most users will stick to the defaults. The stack team firmly
      believes that the majority of users want to simply ignore dependency
      resolution nightmares and get a valid build plan from day 1, which is why
      we've made this selection of default behavior. -->
    * stack默认使用综合管理（Stackage快照、LTS Haskell、Nightly等等），而不是像cabal-install那样默认使用依赖解析。这只是默认用法：如上所述，如果需要的话，stack可以使用依赖解析，并且cabal-install也可以使用综合管理。然而，大多数用户都将坚持默认用法。stack团队坚信多数用户希望简单地忽略依赖解析的噩梦，并在第一天就得到有效的构建，这就是我们选择默认行为的原因。
<!-- * __Reproducible__. -->
* __可复现性__。
    <!-- * stack goes to great lengths to ensure that `stack build` today does the
      same thing tomorrow. cabal-install does not: build plans can be affected
      by the presence of preinstalled packages, and running `cabal update` can
      cause a previously successful build to fail. With stack, changing the
      build plan is always an explicit decision. -->
    * stack竭尽全力来确保今天`stack build`做事情的和明天一样。而cabal-install则不会：预安装包的存在可能会影响构建计划，并且运行`cabal update`可能导致之前成功的构建失败。使用stack，改变构建计划将总是显式的决策。
<!-- * __Automatically building dependencies__. -->
* __自动地构建依赖项__。
    <!-- * In cabal-install, you need to use `cabal install` to trigger dependency
      building. This is somewhat necessary due to the previous point, since
      building dependencies can, in some cases, break existing installed
      packages. So for example, in stack, `stack test` does the same job as
      `cabal install --run-tests`, though the latter *additionally* performs an
      installation that you may not want. The closer command equivalent is
      `cabal install --enable-tests --only-dependencies && cabal configure --enable-tests && cabal build && cabal test`
      (newer versions of
      cabal-install may make this command shorter). -->
    * 使用cabal-install，你需要用`cabal intall`来触发依赖构建。由于前面的原因，这有一点必要，因为在某些情况下，构建依赖可能破坏现有已安装的包。所以举个例子，在stack中，`stack test`做和`cabal install --run-tests`同样的事情，虽然后者*额外*执行了一个你可能不想要的安装。最接近的命令等同于`cabal install --enable-tests --only-dependencies && cabal configure --enable-tests && cabal build && cabal test`（更新版本的cabal-install可能让这个命令更短）。
<!-- * __Isolated by default__. -->
* __默认隔离__
    <!-- * This has been a pain point for new stack users. In cabal, the
      default behavior is a non-isolated build where working on two projects can
      cause the user package database to become corrupted. The cabal solution to
      this is sandboxes. stack, however, provides this behavior by default via
      its databases. In other words: when you use stack, there's __no need for
      sandboxes__, everything is (essentially) sandboxed by default. -->
    * 这对stack新用户来说已经成为了一个痛点。在cabal中，默认行为是不隔离的构建，在这种构建中，处理两个项目可能会导致用户包数据库被破坏。cabal的解决方案是沙箱。然而，stack通过它的数据库默认支持这个行为。换句话说：当你使用stack时，__不需要沙箱__，任何事情（基本上）默认都是沙盒化了。

<!-- __Other tools for comparison (including active and historical)__ -->
__对照其他工具（包括现在的和以前的）__

<!-- * [cabal-dev](https://hackage.haskell.org/package/cabal-dev) (deprecated in favor of cabal-install) -->
* [cabal-dev](https://hackage.haskell.org/package/cabal-dev)（已过时。建议使用cabal-install）
<!-- * [cabal-meta](https://hackage.haskell.org/package/cabal-meta) inspired a lot of the multi-package functionality of stack. If you're still using cabal-install, cabal-meta is relevant. For stack work, the feature set is fully subsumed by stack. -->
* [cabal-meta](https://hackage.haskell.org/package/cabal-meta)启发了很多stack的多包功能。如果你仍然在使用cabal-install，那么cabal-meta就是有用的。对于用stack工作，stack已经包含了它的全部特性集合。
<!-- * [cabal-src](https://hackage.haskell.org/package/cabal-src) is mostly irrelevant in the presence of both stack and cabal sandboxes, both of which make it easier to add additional package sources easily. The mega-sdist executable that ships with cabal-src is, however, still relevant. Its functionality may some day be folded into stack -->
* [cabal-src](https://hackage.haskell.org/package/cabal-src)在stack和cabal沙箱的存在下大多无关紧要，这两者都可以更容易地轻松添加其他包源。然而，cabal-src附带的mega-sdist可执行文件仍然有用。也许有一天它的功能会吸纳进stack。
<!-- * [stackage-cli](https://hackage.haskell.org/package/stackage-cli) was an initial attempt to make cabal-install work more easily with curated snapshots, but due to a slight impedance mismatch between cabal.config constraints and snapshots, it did not work as well as hoped. It is deprecated in favor of stack. -->
* [stackage-cli](https://hackage.haskell.org/package/stackage-cli)曾是一个试图让cabal-install更简单地使用专门快照工作的最初尝试，但是由于cabal.config和快照之间的轻微不匹配的阻力，它不能像希望的那样有效工作。它已经过时，建议使用stack。


<!-- ## Fun features -->
## 有趣的功能

<!-- This is just a quick collection of fun and useful feature stack supports. -->
这是一个关于stack支持的有趣和有用的功能的简短归纳。

<!-- ### Templates -->
### 模板

<!-- We started off using the `new` command to create a project. stack provides
multiple templates to start a new project from: -->
我们开始使用`new`命令来创建一个项目。stack提供多个模板来开始创建一个新项目：

```lisp
michael@d30748af6d3d:~$ stack templates
# Stack Templates

The `stack new` command will create a new project based on a project template.
Templates can be located on the local filesystem, on Github, or arbitrary URLs.
For more information, please see the user guide:
; `stack new`命令将创建一个基于一个项目模板的新项目。模板可以位于本地文件系统、GitHub、或是其他URLs。更多信息，请参考用户手册：

https://docs.haskellstack.org/en/stable/GUIDE/#templates

There are many templates available, some simple examples:
; 这里是一些可用的模板，和一些简单的例子：

    stack new myproj # uses the default template ; 使用默认模板
    stack new myproj2 rio # uses the rio template ; 使用rio模板
    stack new website yesodweb/sqlite # Yesod server with SQLite DB ; 使用yesodweb的SQLite DB

For more information and other templates, please see the `stack-templates`
Wiki:
; 更多信息和其他模板，请参考`stack-templates` Wiki：

https://github.com/commercialhaskell/stack-templates/wiki

Please feel free to add your own templates to the Wiki for discoverability.
; 请随意地添加你自己的模板到这个Wiki，以便于发现。

Want to improve this text? Send us a PR!
; 想要改进这段文本？发送PR给我们！

https://github.com/commercialhaskell/stack-templates/edit/master/STACK_HELP.md
```

<!-- You can specify one of these templates following your project name
in the `stack new` command: -->
你可以在`stack new`命令中指定这些模板中的一个放在你项目名的后面：

```
michael@d30748af6d3d:~$ stack new my-yesod-project yesodweb/simple
Downloading template "yesod-simple" to create project "my-yesod-project" in my-yesod-project/ ...
Using the following authorship configuration:
author-email: example@example.com
author-name: Example Author Name
Copy these to /home/michael/.stack/config.yaml and edit to use different values.
Writing default config file to: /home/michael/my-yesod-project/stack.yaml
Basing on cabal files:
- /home/michael/my-yesod-project/my-yesod-project.cabal

Checking against build plan lts-3.2
Selected resolver: lts-3.2
Wrote project config to: /home/michael/my-yesod-project/stack.yaml
```

<!-- The default `stack-templates` repository is on [Github](https://github.com/commercialhaskell/stack-templates),
under the user account `commercialstack`. You can download templates from a
different Github user by prefixing the username and a slash: -->
默认的`stack-templates`仓库在[Github](https://github.com/commercialhaskell/stack-templates)上，用户账号为`commercialstack`。你可以通过前缀用户名加斜杠的方式从不同的GitHub用户那里下载模板：

```
stack new my-yesod-project yesodweb/simple
```

<!-- Then it would be downloaded from Github, user account `yesodweb`,
repo `stack-templates`, and file `yesod-simple.hsfiles`. -->
然后它将从GitHub上下载用户名为`yesodweb`、仓库为`stack-templates`、名字为`yesod-simple.hsfiles`的文件。

<!-- You can even download templates from a service other that Github, such as
[Gitlab](https://gitlab.com) or [Bitbucket](https://bitbucket.com): -->
你甚至可以从GitHub之外的服务下载模板，比如[Gitlab](https://gitlab.com)或者[Bitbucket](https://bitbucket.com)：

```
stack new my-project gitlab:user29/foo
```

<!-- That template would be downloaded from Gitlab, user account `user29`,
repo `stack-templates`, and file `foo.hsfiles`. -->
这个模板将会从Gitlab下载用户名为`user29`、仓库为`stack-templates`、名字为`foo.hsfiles`的文件。

<!-- If you need more flexibility, you can specify the full URL of the template: -->
如果你需要更多的灵活性，你可以为模板指定一个全URL：

```
stack new my-project https://my-site.com/content/template9.hsfiles
```

<!-- (The `.hsfiles` extension is optional; it will be added if it's not specified.) -->
（拓展名`.hsfiles`是可选的；如果没有指定它将被自动添加上。）

<!-- Alternatively you can use a local template by specifying the path: -->
另外你可以通过指定路径来使用本地的模板：

```
stack new project ~/location/of/your/template.hsfiles
```

<!-- As a starting point for creating your own templates, you can use [the "simple" template](https://github.com/commercialhaskell/stack-templates/blob/master/simple.hsfiles).
An introduction into template-writing and a place for submitting official templates,
you will find at [the stack-templates repository](https://github.com/commercialhaskell/stack-templates#readme). -->
作为创建你自己模板的起点，你可以使用["simple"模板](https://github.com/commercialhaskell/stack-templates/blob/master/simple.hsfiles)。你将在[stack-templates仓库](https://github.com/commercialhaskell/stack-templates#readme)中找到模板编写的向导和官方提交模板的地方。

<!-- ### Editor integration -->
### 编辑器集成

<!-- For editor integration, stack has a related project called
[intero](https://github.com/commercialhaskell/intero).  It is particularly well
supported by emacs, but some other editors have integration for it as well. -->
关于编辑器集成，stack有一个相关的项目叫做[intero](https://github.com/commercialhaskell/intero)。Emacs对它的支持尤其好，但是一些的其他编辑器也对它有不错的集成。

<!-- ### Visualizing dependencies -->
### 可视化依赖

<!-- If you'd like to get some insight into the dependency tree of your packages, you
can use the `stack dot` command and Graphviz. More information is
[available in the Dependency visualization documentation](dependency_visualization.md). -->
如果你想要深入了解你包中的依赖树，你可以使用`stack dot`命令和Graphviz。更多信息可在[依赖可视化文档]中找到(dependency_visualization.md)。

<!-- ### Travis with caching -->
### 带缓存的Travis

<!-- This content has been moved to a dedicated
[Travis CI document](https://docs.haskellstack.org/en/stable/travis_ci/). -->
这个内容已经被移动到专门的[Travis CI 文档](https://docs.haskellstack.org/en/stable/travis_ci/)里。

<!-- ### Shell auto-completion -->
### Shell自动补全

<!-- Love tab-completion of commands? You're not alone. If you're on bash, just run
the following (or add it to `.bashrc`): -->
喜欢命令行的tab自动补全？你不是一个人。如果你使用bash，只需要运行下面的命令（或者添加它到`.bashrc`）：

    eval "$(stack --bash-completion-script stack)"

<!-- For more information and other shells, see [the Shell auto-completion wiki
page](https://docs.haskellstack.org/en/stable/shell_autocompletion) -->
更多信息和其他shell，请参考[shell自动补全wiki页](https://docs.haskellstack.org/en/stable/shell_autocompletion)

### Docker

<!-- Stack is able to build your code inside a Docker image, which means
even more reproducibility to your builds, since you and the rest of
your team will always have the same system libraries. -->
Stack能够在Docker镜像中构建你的代码，这意味着构建的可重复性更强，因为您和您的团队的其他成员将始终拥有相同的系统库。

### Nix

<!-- stack provides an integration with [Nix](http://nixos.org/nix),
providing you with the same two benefits as the first Docker
integration discussed above: -->
stack提供与[Nix](http://nixos.org/nix)的集成，为你提供和前文讨论的Docker集成相同的两个好处：

<!-- * more reproducible builds, since fixed versions of any system
  libraries and commands required to build the project are
  automatically built using Nix and managed locally per-project. These
  system packages never conflict with any existing versions of these
  libraries on your system. That they are managed locally to the
  project means that you don't need to alter your system in any way to
  build any odd project pulled from the Internet. -->
* 更强的可复现构建，由于构建项目所需的任何系统库和命令的固定版本都是使用Nix自动构建的，并且每个项目都在本地进行管理。这些系统包决不会和任何你系统上这些库现有的版本冲突。它们被本地的项目管理，意味着你不需要用任何方式改变你的系统来构建任何从网上拉下来的奇怪项目。
<!-- * implicit sharing of system packages between projects, so you don't
  have more copies on-disk than you need to. -->
* 在项目间隐式共享系统包，因此磁盘上的副本不会超过所需的数量。

<!-- When using the Nix integration, Stack downloads and builds Haskell dependencies
as usual, but resorts on Nix to provide non-Haskell dependencies that exist in
the Nixpkgs. -->
当使用Nix集成时，Stack像通常那样下载和构建Haskell依赖，但是依靠Nix来提供Nixpkgs中存在的非Haskell依赖项。

<!-- Both Docker and Nix are methods to *isolate* builds and thereby make
them more reproducible. They just differ in the means of achieving
this isolation. Nix provides slightly weaker isolation guarantees than
Docker, but is more lightweight and more portable (Linux and OS
X mainly, but also Windows). For more on Nix, its command-line
interface and its package description language, read the
[Nix manual](http://nixos.org/nix/manual). But keep in mind that the
point of stack's support is to obviate the need to write any Nix code
in the common case or even to learn how to use the Nix tools (they're
called under the hood). -->
Dorker和Nix都是*独立*构建的方法，从而使它们更具可复现性。他们仅存的区别在于实现隔离的方式。Nix提供的隔离保证稍微弱于Docker，但是更轻量和更便携（主要是Linux和OS X，而不是Windows）。关于Nix的更多信息——它的命令行接口和它的包描述语言，请阅读[Nix手册](http://nixos.org/nix/manual)。但是记住，stack支持的重点是避免在通常情况下，甚至是学习怎么使用Nix工具的情况下，写任何Nix代码（它们被称为底层工具）。

<!-- For more information, see
[the Nix-integration documentation](nix_integration.md). -->
更多信息，请参考[Nix集成文档](nix_integration.md)。

<!-- ## Power user commands -->
## 强大的用户命令

<!-- The following commands are a little more powerful, and won't be needed by all
users. Here's a quick rundown: -->
下面的命令更强大一点，并且不是所有用户通常都需要它们。这里是一个简短的概述：

<!-- * `stack update` will download the most recent set of packages from your package
  indices (e.g. Hackage). Generally, stack runs this for you automatically
  when necessary, but it can be useful to do this manually sometimes (e.g.,
  before running `stack solver`, to guarantee you have the most recent
  upstream packages available). -->
* `stack update`将下载最从你的包索引（例如Hackage）下载最新的包集合。通常，stack在必要的时候会自动为你运行它，当时有时手动做这件事可能会很有用（比如，再运行`stack solver`之前，来确保你有最新的上游可用的包）。
<!-- * `stack unpack` is a command we've already used quite a bit for examples, but
  most users won't use it regularly. It does what you'd expect: downloads a
  tarball and unpacks it. It accept optional `--to` argument to specify
  the destination directory. -->
* `stack unpack`是一个我们已经在例子中大量使用过的命令，当时大多数用户不会经常使用它。它做了你所期望的：下载并解压一个tarball。它接收可选的`--to`参数来指定目标目录。
<!-- * `stack sdist` generates an uploading tarball containing your package code -->
* `stack sdist`生成一个包含你包代码的上传用的tarball
<!-- * `stack upload` uploads an sdist to Hackage. As of
  version [1.1.0](https://docs.haskellstack.org/en/v1.1.0/ChangeLog/) stack
  will also attempt to GPG sign your packages as
  per
  [our blog post](https://www.fpcomplete.com/blog/2016/05/stack-security-gnupg-keys). -->
* `stack upload`上传一个sdist到Hackage。从版本[1.1.0](https://docs.haskellstack.org/en/v1.1.0/ChangeLog/)开始，stack还将尝试对你的包采用GPG签名，按照我们的博客(https://www.fpcomplete.com/blog/2016/05/stack-security-gnupg-keys)介绍的那样。
    <!-- * `--no-signature` disables signing of packages -->
    * `--no-signature`禁用包签名
<!-- * `stack upgrade` will build a new version of stack from source. -->
* `stack upgrade`将从源代码构建新版本的stack。
    <!-- * `--git` is a convenient way to get the most recent version from master for
      those testing and living on the bleeding edge. -->
    * `--git`是一个从主分支获取最新版本的便捷方法，用于测试和处于前沿的用户。
<!-- * `stack ls snapshots` will list all the local snapshots by
  default. You can also view the remote snapshots using `stack ls
  snapshots remote`. It also supports option for viewing only lts
  (`-l`) and nightly (`-n`) snapshots. -->
* `stack ls snapshots`默认将列出所有本地快照。你也可以使用`stack ls snapshots remote`查看远程的快照。它也支持使用选项来只查看lts（`-l`）和nightly（`-n`）快照。
<!-- * `stack ls dependencies` lists all of the packages and versions used for a
  project -->
* `stack ls dependencies`列出一个项目使用的所有包和版本
<!-- * `stack sig` subcommand can help you with GPG signing & verification -->
* `stack sig`子命令可以帮助你使用GPG签名和验证
    <!-- * `sign` will sign an sdist tarball and submit the signature to
      sig.commercialhaskell.org for storage in the sig-archive git repo.
      (Signatures will be used later to verify package integrity.) -->
    * `sign`将会签名一个sdist tarball，并把这个签名提交给sig.commercialhaskell.org，用来存储到sig-archive git仓库中。（签名将被用在之后校验包的完整性。）

<!-- ## Debugging -->
## 调试

<!-- To profile a component of the current project, simply pass the `--profile`
flag to `stack`. The `--profile` flag turns on the `--enable-library-profiling`
and `--enable-executable-profiling` Cabal options _and_ passes the `+RTS -p`
runtime options to any testsuites and benchmarks. -->
要分析当前项目中的一个组件，只需简单地传送`--profile`标志到`stack`即可。`--profile`标志将开启Cabal选项`--enable-library-profiling`和`--enable-executable-profiling`，并传送运行时参数`+RTS -p`到任何测试套件和基准程序中。

<!-- For example the following command will build the `my-tests` testsuite with
profiling options and create a `my-tests.prof` file in the current directory
as a result of the test run. -->
比如下面的命令将使用分析选项构建`my-tests`测试套件，并在当前目录创建一个`my-tests.prof`文件作为测试运行的结果。

    stack test --profile my-tests

<!-- The `my-tests.prof` file now contains time and allocation info for the test run. -->
`my-tests.prof`文件现在包含测试运行的时间和分配信息。

<!-- To create a profiling report for an executable, e.g. `my-exe`, you can
run -->
为了创建一个可执行程序的分析报告，比如`my-exe`，你可以运行

     stack exec -- my-exe +RTS -p

<!-- For more fine-grained control of compilation options there are the
`--library-profiling` and `--executable-profiling` flags which will turn on the
`--enable-library-profiling` and `--enable-executable-profiling` Cabal
options respectively. -->
Custom GHC options can be passed in with `--ghc-options "more options here"`.
为了更细粒度的编译选项控制，`--library-profiling`和`--executable-profiling`标志分别将开启Cabal选项`--enable-library-profiling`和`--enable-executable-profiling`。

<!-- To enable compilation with profiling options by default you can add the
following snippet to your `stack.yaml` or `~/.stack/config.yaml`: -->
为了默认使用分析选项开启编译，你可以添加下面的片段到你的`stack.yaml`或`~/.stack/config.yaml`中：

```yaml
build:
  library-profiling: true
  executable-profiling: true
```

<!-- ### Further reading -->
### 进一步阅读

<!-- For more commands and uses, see [the official GHC chapter on
profiling](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/profiling.html),
[the Haskell wiki](https://wiki.haskell.org/How_to_profile_a_Haskell_program),
and [the chapter on profiling in Real World
Haskell](http://book.realworldhaskell.org/read/profiling-and-optimization.html). -->
有关更多命令和用法，请参考[the official GHC chapter on profiling](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/profiling.html)，[the Haskell wiki](https://wiki.haskell.org/How_to_profile_a_Haskell_program)，和[the chapter on profiling in Real World
Haskell](http://book.realworldhaskell.org/read/profiling-and-optimization.html)。

<!-- ### Tracing -->
### 追踪

To generate a backtrace in case of exceptions during a test or benchmarks run,
use the `--trace` flag. Like `--profile` this compiles with profiling options,
but adds the `+RTS -xc` runtime option.
为了在测试或基准运行期间发生异常时生成一个回溯，可以使用`--trace`标志。与`——profile`类似，它使用分析选项编译，但是多了`+RTS -xc`运行时选项。

### DWARF

<!-- `stack` now supports debugging and profiling with
[DWARF information](https://ghc.haskell.org/trac/ghc/wiki/DWARF),
using the `--no-strip`, `--no-library-stripping`, and `--no-executable-stripping`
flags to disable the default behavior of removing such information from compiled
libraries and executables. -->
`stack`现在支持使用[DWARF信息](https://ghc.haskell.org/trac/ghc/wiki/DWARF)来调试和分析，使用`--no-strip`、`--no-library-stripping`、和`--no-executable-stripping`标记来禁用从编译好的库和可执行文件中移除相关信息的默认行为。

<!-- ## More resources -->
## 更多资源

<!-- There are lots of resources available for learning more about stack: -->
这里有很多资源可用于了解有关stack的更多信息：

* `stack --help`
<!-- * `stack --version` — identify the version and Git hash of the stack executable -->
* `stack --version`——确认stack可执行文件的版本和Git Hash
<!-- * `--verbose` (or `-v`) — much more info about internal operations (useful for bug reports) -->
* `--verbose`（或`-v`）——关于内部运作的更多信息（对bug报告很有用）
<!-- * The [home page](http://haskellstack.org) -->
* [主页](http://haskellstack.org)
<!-- * The [stack mailing list](https://groups.google.com/d/forum/haskell-stack) -->
* [stack邮件列表](https://groups.google.com/d/forum/haskell-stack)
<!-- * The [FAQ](faq.md) -->
* [问题和解答](faq.md)
<!-- * The [stack wiki](https://github.com/commercialhaskell/stack/wiki) -->
* [stack维基](https://github.com/commercialhaskell/stack/wiki)
<!-- * The [haskell-stack tag on Stack Overflow](http://stackoverflow.com/questions/tagged/haskell-stack) -->
* [Stack Overflow上的haskell-stack标签](http://stackoverflow.com/questions/tagged/haskell-stack)
<!-- * [Another getting started with stack tutorial](http://seanhess.github.io/2015/08/04/practical-haskell-getting-started.html) -->
* [另一个stack入门教程](http://seanhess.github.io/2015/08/04/practical-haskell-getting-started.html)
<!-- * [Why is stack not cabal?](https://www.fpcomplete.com/blog/2015/06/why-is-stack-not-cabal) -->
* [为什么选择stack而不是cabal](https://www.fpcomplete.com/blog/2015/06/why-is-stack-not-cabal)
