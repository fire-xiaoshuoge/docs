## Git

### 版本控制

版本控制是指对软件开发过程中各种程序代码、[配置文件](https://baike.baidu.com/item/配置文件/286550)及说明文档等文件变更的管理，是[软件配置管理](https://baike.baidu.com/item/软件配置管理/3765602)的核心思想之一。

#### **常用的版本控制优缺点**

**VSS**

Visual SourceSafe：微软的[**版本控制**](http://www.finalblog.net/archives/462)工具，仅支持Windows操作系统。虽然简单好用，但是仅适用于团队级开发，不能胜任企业级的开发工作。

VSS优点：安装、配置、使用均较简单，很容易上手使用；操作简单，容易掌握；权限划分可到文件夹级，有Read、Check-Out & Check-In、Add/Rename/Delete、Destroy四种权限级别。

缺点：权限管理基于文件共享形式，只能从文件夹共享的权限设定对整个库文件夹的权限，而且必须要有可写权限；版本管理和分支管理只能靠人为的手工设置；版本发行时，只能手工挑选对应的版本文件进行发布；安全性不高，基于文件系统共享实现对服务器的访问，需要共享存储目录，这样用户可以对VSS的文件夹执行删除操作。

**CVS**

CVS是一个典型的服务器/客户端软件，有Unix版本的CVS 、[**Linux**](http://www.finalblog.net/archives/540)版本的CVS和Windows版本的CVS。CVS支持远程管理，项目组分布开发时一般都采用CVS。安装、配置较复杂，但使用比较简单，只需对配置管理做简单培训即可。安全性高，CVS服务器有自己专用的数据库，文件存储并不采用 “共享目录”方式，所以不受限于局域网。CVS可以跨平台，支持并发版本控制，而且免费。CVS不支持文件改名，只针对文件控制版本而没有针对目录的管理,并且缺少相应的技术支持，许多问题的解决需要自已寻找资料，甚至是研究源代码。但也可以根据自己的需要进行编程。

相对功能单一、简陋，适用于几个人的小型团队，在数据量不大的情况下，性能可以接受。

**SVN**

SVN（Subversion） 是一种版本管理系统，其前身是CVS。SVN是根据CVS 的功能为基础来设计的，它除包括了CVS 的大多数特点外，还有一些新的功能，如：文件目录可以方便的改名、基于数据库的版本库、操作速度提升、权限管理更完善等。

CVS与SVN比较

| 比较项目                                 | CVS                | SVN              |        |
| ---------------------------------------- | ------------------ | ---------------- | ------ |
| 权限控制                                 | 是否依赖系统帐号   | 依赖             | 不依赖 |
| 可否对分支授权                           | 否                 | 是               |        |
| 是否支持LDAP认证                         | 否                 | 是               |        |
| 图形化帐号管理                           | 否                 | 是(集中管理平台) |        |
| 用户可否获取忘记口令，修改口令           | 否                 | 是(集中管理平台) |        |
| 目录，文件名变更                         | 否                 | 是               |        |
| 分支 管理                                | 创建分支时间       | 耗时*            | 快     |
| 分支可见、查询                           | 难                 | 易               |        |
| 二进制文件                               | 二进制优化         | 否               | 是     |
| 二进制文件标识                           | 手工               | 自动             |        |
| 二进制文件（图形文件）被破坏             | 易破坏             | 不易破坏         |        |
| 事物 处理                                | 原子提交           | 否               | 是     |
| 修改提交说明                             | 单个文件           | 是               |        |
| 换行 符                                  | 可否指定换行符类型 | 否               | 是     |
| 检查换行符设定，避免跨平台开发带来的混乱 | 否                 | 是               |        |
| 功能扩展                                 | CVSROOT            | hooks 脚本       |        |
| 网络 带宽                                | 网络带宽占用       | 高               | 低     |
| 脱机命令                                 | 否                 | 部分             |        |

**ClearCase（闭源集中式）**

ClearCase提供了全面的配置管理——包括版本控制、工作空间管理、建立管理和过程控制，而且无须软件开发者改变他们现有的环境、工具和工作方式。

ClearCase包括两套：ClearCase LT和ClearCase (MultiSite)。前者可以用于在同一个局域网的开发小组，适合于中小型开发组织；ClearCase (MultiSite)则适应于分布于不同地理位置、不同局域网的开发小组，适合于大型的开发组织。

优势:

增加团队效率――通过对并行开发的支持来实现，包括图形比较和归并、标签、版本目录结构。

增加个人效率 ――通过自动的工作空间管理来实现，如：直接的版本访问、消除了在拷贝文件上的时间的浪费。

简单的维护和提高对客户的支持――通过快速准确的重建先前的版本来实现。

快速准确的产品发布 ――通过保证构造的准确性和对软件的每一个元件进行版本控制来实现。

减少错误发生 ――通过事件发生以后对每一个元件的变更进行追踪来实现。

硬件资源的优化 ――通过分布式构造、减少文件拷贝、可用对象的共享等功能来实现。

提高项目协调和编制 ――通过文件注释和开发周期阶段变更的自动关联来实现。

提高产品质量 ――通过灵活的进程控制，和图形接口定制，使得软件开发在实际中保持一致。

更加有效的团队扩展――通过减少系统管理和维护的负担来实现。

支持分布式结构使得团队成长――通过Client/Server结构进行多点复制和及时的对象版本的更新来实现。

使用配置管理工具而降低风险――由于它不干扰软件程序员的工作，所以可以使用常用的工具和文件系统接口。

增加了软件的安全性和保护性 ――通过使用分布式的存储结构，所有的软件资源会随时更新、在硬盘或网络出现错误时那些被ClearCase存储的版本信息会立刻恢复。

减少培训和实现成本 ――ClearCase通过采用透明结构以及和标准开发工具进行集成来实现。

强有力的开发和维护 ――通过和其它工具（如：缺陷追踪）、系统、结构进行集成。

支持不同种类的开发 ――通过兼容不同平台的软件配置管理系统，如：Windows NT、UNIX、和一些Client端的软件，如：Windows 95、Windows NT、Windows 3.1和Windows for Workgroups。

缺点：ClearCase 太贵，易用性差，培训费用很贵，没有培训，很难上手使用。

**StarTeam（闭源集中式）**

StarTeam属于高端的工具，在易用性，功能和安全性等方面都很不错。 StarTeam的用户界面同VSS的类似，它的所有的操作都可通过图形用户界面来完成，同时，对于习惯使用命令方式的用户，StarTeam也提供命令集进行支持。而且StarTeam的随机文档也非常详细。 StarTeam还提供了流程定制的工具，用户可跟据自己的需求灵活的定制流程。与VSS和CVS不同，VSS和CVS是基于文件系统的配置管理工具，而StarTeam是基于数据库的。StarTeam的用户可根据项目的规模，选取多种数据库系统。StarTeam无需通过物理路径的权限设置，而是通过自己的数据库管理，实现了类似Windowsnt的域用户管理和目录文件ACL控制。StarTeam完全是域独立的。这个优势可以为用户模型提供灵活性，而不会影响到现有的安全设置。StarTeam的[**访问控制**](http://www.finalblog.net/archives/522)非常灵活并且系统。您可以对工程、视图、文件夹一直向下到每一个小的item设置权限。对于高级别的视图（view），访问控制可以与用户组、用户、项目甚至视图等链接起来。 StarTeam是按license来收费的，比起VSS，CVS来，企业在启动StarTeam进行配置管理需要投入一定资金。

优点：权限设置功能强大方便。StarTeam的图形化界面，能够使初学者易于接收，而且其缺陷控制功能的功能（基于数据库的Change Request），是相应工具中独树一帜的。

缺点：不支持并行开发，不能很好解决Merge的问题；不支持分支的自动合并，需要手动来处理；速度慢，一定程度上影响开发效率；故障恢复困难，需要有专职管理员维护；没有中文版本；另外，StarTeam集成度较高，移植过程复杂，需要的管理负担大，需要完善的备份计划。

**GIT（开源分布式）**

GIT 是一款免费的、开源的、分布式的版本控制系统。旨在快速高效地处理无论规模大小的任何软件工程。与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持，使源代码的发布和交流极其方便。每一个GIT克隆都是一个完整的文件库，含有全部历史记录和修订追踪能力。其最大特色就是“分支”及“合并”操作快速、简便。支持离线工作，GIT是整个项目范围的原子提交，而且GIT中的每个工作树都包含一个具有完整项目历史的仓库。

GIT 本来是面向 Linux 操作系统开发的软件。在 Linux 平台上使用GIT非常简单，都是命令行模式。但对windows以及中文的支持不是很好。

**Mercurial（开源分布式）**

Mercurial 是一种轻量级分布式版本控制系统，采用 Python 语言实现，易于学习和使用，扩展性强。其是基于 GNU General Public License (GPL) 授权的开源项目。

相对于传统的版本控制，具有如下优点：

更轻松的管理。传统的版本控制系统使用集中式的rep[**OSI**](http://www.finalblog.net/archives/1473)tory，一些和repository相关的管理就只能由管理员一个人进行。由于采用了分布式的模型，Mercurial 中就没有这样的困扰，每个用户管理自己的repository，管理员只需协调同步这些repository。

更健壮的系统。分布式系统比集中式的单服务器系统更健壮，单服务器系统一旦服务器出现问题整个系统就不能运行了，分布式系统通常不会因为一两个节点而受到影响。

对网络的依赖性更低。由于同步可以放在任意时刻进行，Mercurial 甚至可以离线进行管理，只需在有网络连接时同步。

简单易学、易于使用；轻量级，运行快速；可扩展性，易于根据用户需求自行定义、扩展。

### 配置Git

#### **配置全局用户名密码**

拥有git环境变量之后需要设置全局的git信息，该信息就是你提交的代码里记录的作者信息。

- 检查全局配置:

```cpp
git config --list
```

- 设置全局用户名（请将“”里内容替换成你自己的用户名）：**用户名是你提交代码之后证明你是作者的唯一凭证**

```csharp
git config --global user.name "maomao"
```

- 设置全局用户邮箱地址（请将“”里内容替换成你自己的邮箱）：

```css
git config --global user.email "maomao@qq.com"
```

#### **配置SSH**

配置了SSH到你项目到服务器可以每次拉代码和上传代码无需输入用户名密码。
 SSH相当于你到机器码，上传之后对你当前机器进行信任。

- 生成SSH key
   打开命令行，在根目录下输入（请将“”里内容替换成你自己的邮箱）：

```css
ssh-keygen -t rsa -C "maomao@qq.com"
```

- 紧接着输入下面命令检查SSH是否生成成功

```jsx
cat ~/.ssh/id_rsa.pub
```

成功之后会生成一串SSH字符串码：

```ruby
maomaodeMacBook-Pro:~ maomao$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRue8kjAd4feYW8w4qMT5kj5Awaf6f6p/QwCWVxI1im+wfPGteWQxPXD6aErzO4jo1GTIof/ugD7/lt6xfEtSRk6ru2m18NGq8t00xyU4zWTQJhOgLgwcx5zG9amn………………
```

- **从“ssh-rsa ”开始复制所有内容，粘贴到你服务器网站的SSH Key 配置项里面。**

#### **拉取代码**

所有上面配置完毕之后就可以拉取服务器代码了。

1、来到你准备放代码的目录

```bash
maomaodeMacBook-Pro:~ maomao$ cd Documents/demo/
```

2、将代码的SSH地址复制，并使用“git clone”命令克隆到本地

```ruby
maomaodeMacBook-Pro:demo maomao$ git clone git@git.XXX.git
```

这里的“[git@git.XXX](mailto:git@git.XXX).git”就是你项目代码对应的SSH地址，一般都有类似于这样的地址：

![img](https:////upload-images.jianshu.io/upload_images/3710986-958bc752a48054f7.png?imageMogr2/auto-orient/strip|imageView2/2/w/812/format/webp)

复制即可。

### Git基本原理

#### Git 目录结构

![img](https://images2017.cnblogs.com/blog/590093/201709/590093-20170910122805351-1396180991.png)

进入隐藏的 .git 目录之后可以看到如上图所示结构

核心文件：config，objects，HEAD，index，refs 这 5 个文件夹

config：这里存储项目的一些配置信息，比如是否以 bare 方式初始化，remote 信息等。git remote add 添加的远程分支信息就保存在这里

objects：这里保存 git 对象，git 中的一些操作以及文件都会以 git 对象形式保存在这里，git 对象分为 BLOB，tree，commit 三种类型，比如 git commit 就是 commit 类型变量，各个版本之间通过版本树进行组织，比如 HEAD 指向某个 commit 对象，而 commit 对象又会指向几个 BLOB 对象或者 tree 对象。objects 文件夹中有很多子文件夹，其中 git 对象保存在以其 sha-1 值的前两位为子文件夹后 38 位为文件名的文件中，此外 git 为了节省存储对象所占用的磁盘空间，会定期对 git 对象进行压缩和打包，其中 pack 文件夹用于存放打包压缩的对象，info 文件夹用于从打包的文件中查找 git 对象

HEAD：该文件指明了本地的分支结果，如本地分支是 master，那么 HEAD 就指向 master，分支在 refs 中就会表示成refs:refs/heads/master

index：该文件 stage 暂存区信息，也就是 add 之后保存到的区域，内容包括它指向的文件的时间戳，文件名，sha1 值等

refs：该文件夹保存了指向分支的提交对象也就是 commit 对象的指针，其中的 heads 文件夹存储了本地每一个分支最近一次提交的 sha-1 值，即 commit 对象的 sha-1 值，每个分支一个文件；remotes 文件夹则记录你最后一次和远程仓库的通信，也就是说这里会记录最后一次每个分支推送到远端的值；tags 文件夹存储分支别名

hooks：这里主要定义了客户端或服务端的 hook 脚本，这些脚本用于在特定命令和操作之前、之后进行特殊处理
description：仅供 GitWeb 程序使用

logs：记录了本地仓库和远程仓库的每一个分支的提交信息，即所有 commit 对象都会被记录在此，这个文件夹内容应该是我们查看最频繁的，如 git log

info：其中保存了一份不希望在 .gitignore 文件中管理的忽略的全局可执行文件
COMMIT_EDITMSG：记录了最后一次提交时的注释信息
我们可以看到 .git 目录中的文件会因为几次提交就成倍的增长，因为其中要生成对象

#### 基本原理

![img](https://images0.cnblogs.com/blog2015/687225/201508/211915189723300.jpg)

git跟传统的代码管理器（如:svn）不同， 主要区别在于git多了个**本地仓库**以及**缓存区**，所以即使无法联网也一样能提交代码。术语解释：

**工作区间:** 即我们创建的工程文件， 在编辑器可直观显示；

**缓存区:** 只能通过git GUI或git shell 窗口显示，提交代码、解决冲突的中转站；

**本地仓库:** 只能在git shell 窗口显示，连接本地代码跟远程代码的枢纽，不能联网时本地代码可先提交至该处；

**远程仓库:** 即保存我们代码的服务器，本文以公共版本控制系统：**github**为例，登录github账号后可直观显示；

### **常用git命令**

#### Git文件的4种状态

![img](https://git-scm.com/book/en/v2/book/02-git-basics/images/lifecycle.png)

- **Untracked**: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过`git add` 状态变为`Staged`
- **Unmodify**: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为`Modified`. 如果使用`git rm`移出版本库, 则成为`Untracked`文件
- **Modified**: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过`git add`可进入暂存`staged`状态, 使用`git checkout` 则丢弃修改过, 返回到`unmodify`状态, 这个`git checkout`即从库中取出文件, 覆盖当前修改
- **Staged**: 暂存状态. 执行`git commit`则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为`Unmodify`状态. 执行`git reset HEAD filename`取消暂存, 文件状态为`Modified`

#### 配置忽略文件

使用git版本管理时，通过touch .gitignore 创建文件来忽略指定的文件，具体做法是在Git工程目录下新建.gitignore文件，然后添加相应的忽略规则，一般忽略ide自建的文件，编译产生的.exe文件，_test测试文件等。

忽略文件配置可参考：

```xml
# Created by .ignore support plugin (hsz.mobi)
# my settings
*.csv
*.xlsx
*.xls
.vscode
*_test.go

# IntelliJ project files
.idea
*.iml
out
gen

### macOS template
# General
.DS_Store
.AppleDouble
.LSOverride

# Icon must end with two \r
Icon

# Thumbnails
._*

# Files that might appear in the root of a volume
.DocumentRevisions-V100
.fseventsd
.Spotlight-V100
.TemporaryItems
.Trashes
.VolumeIcon.icns
.com.apple.timemachine.donotpresent

# Directories potentially created on remote AFP share
.AppleDB
.AppleDesktop
Network Trash Folder
Temporary Items
.apdisk
### Windows template
# Windows thumbnail cache files
Thumbs.db
ehthumbs.db
ehthumbs_vista.db

# Dump file
*.stackdump

# Folder config file
[Dd]esktop.ini

# Recycle Bin used on file shares
$RECYCLE.BIN/

# Windows Installer files
*.cab
*.msi
*.msix
*.msm
*.msp

# Windows shortcuts
*.lnk
### Python template
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
.hypothesis/
.pytest_cache/

# Translations
*.mo
*.pot

# Django stuff:
local_settings.py
db.sqlite3

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
target/

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version

# celery beat schedule file
celerybeat-schedule

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# Spyder project settings
.spyderproject
.spyproject

# Rope project settings
.ropeproject

# mkdocs documentation
/site

# mypy
.mypy_cache/
### Go template
# Binaries for programs and plugins
*.exe
*.exe~
*.dll
*.dylib

# Test binary, build with `go test -c`
*.test

# Output of the go coverage tool, specifically when used with LiteIDE
*.out
### Java template
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
```

#### 常用命令

```java
git init //初始化本地git环境
git clone XXX//克隆一份代码到本地仓库
git pull //把远程库的代码更新到工作台
git pull --rebase origin master //强制把远程库的代码跟新到当前分支上面
git fetch //把远程库的代码更新到本地库
git add . //把本地的修改加到stage中
git commit -m 'comments here' //把stage中的修改提交到本地库
git push //把本地库的修改提交到远程库中
git branch -r/-a //查看远程分支/全部分支
git checkout master/branch //切换到某个分支
git checkout -b test //新建test分支
git checkout -d test //删除test分支
git merge master //假设当前在test分支上面，把master分支上的修改同步到test分支上
git merge tool //调用merge工具
git stash //把未完成的修改缓存到栈容器中
git stash list //查看所有的缓存
git stash pop //恢复本地分支到缓存状态
git blame someFile //查看某个文件的每一行的修改记录（）谁在什么时候修改的）
git status //查看当前分支有哪些修改
git log //查看当前分支上面的日志信息
git diff //查看当前没有add的内容
git diff --cache //查看已经add但是没有commit的内容
git diff HEAD //上面两个内容的合并
git reset --hard HEAD //撤销本地修改
echo $HOME //查看git config的HOME路径
export $HOME=/c/gitconfig //配置git config的HOME路径
git status [filename] //查看指定文件状态
git status //查看所有文件状态
git status -s  //精简的方式显示文件状态
```

### Git集成IDEA

git下载安装不多说，直接将git集成IDEA

1、去settings里面配置git，和github的参数

![img](https:////upload-images.jianshu.io/upload_images/14727936-0c58e444bb0f4022.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

![img](https:////upload-images.jianshu.io/upload_images/14727936-042cf77084cd262a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

配置完都拿test测试下

2、

![img](https:////upload-images.jianshu.io/upload_images/14727936-ca7be8e9396aad3c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

点击Create git Respository后，出现如下页面

![img](https:////upload-images.jianshu.io/upload_images/14727936-e658fc5a67947619.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png
 要选择当前工程的根目录，意味这创建好了本地仓库

3、

![img](https:////upload-images.jianshu.io/upload_images/14727936-dd2e1593ffa85c5d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1025/format/webp)

image.png

先  点击add  添加至git 缓存区

![img](https:////upload-images.jianshu.io/upload_images/14727936-e27db572252e3bd3.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

设置远程仓库
 出现如下



![img](https:////upload-images.jianshu.io/upload_images/14727936-23168f36a45bf4c7.png?imageMogr2/auto-orient/strip|imageView2/2/w/663/format/webp)

image.png

将github上的Respository的url粘贴

4、然后执行git的commit操作
 这里注意git的commit操作是将本地修改过的文件提交到本地库中。
 git  push是将本地库中的最新信息发送给远程库。(即github中)

5、流程图

![img](https:////upload-images.jianshu.io/upload_images/14727936-483319a5a7bfafbf.png?imageMogr2/auto-orient/strip|imageView2/2/w/876/format/webp)