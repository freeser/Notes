---
title: Mac电脑初使化配置及常用命令
date: 2017-12-07 17:53:47
tags: [mac, nvm, vim, linux, npm, git, vscode, nodejs]
categories: [摘抄备份]
---

Mac电脑初使化配置时需要安装的软件及步骤和注意事项。
以及一些常用的vim、linux、vscode命令。

<!-- more --> 

# 安装加速度vpn

用于同步谷歌浏览器个人收藏

# 安装Chrome

开发常用浏览器

# 安装git

MAC上最好的GIT免费GUI工具是SourceTree（没有之一）。此外，最好的GIT代码开源网站是GitHub，最好的GIT代码私有库是BitBucket。

**安装过程：**

1. 下载[Git installer](https://git-scm.com/downloads)
2. 下载之后打开，双击.pkg安装
3. 打开终端，使用`git --version`或者`which git`命令查看安装版本，有就是安装成功了
4. 创建一个全球用户名、全球邮箱
```
git config --global user.name "freeser.work"
git config --global user.email "freeser@work.com"
```
5. 安装成功后打开终端
`cd ~进入根目录` 输入命令 `ssh-keygen` 生成 `ssh-key`，如果有提示，一直按回车
{% img /img/init-mac-work-environment-1.png %}

6. 将SSH key添加到GitHub。步骤如下：

  * 登录到GitHub页面，`Account Settings->SSH Public Keys->Add another key`
  * 将生成的key(`id_rsa.pub`文件）内容copy到输入框中，save。

  > `commd+shift+g`进入`/Users/chen/.ssh/`就可以看得见私钥和公钥
私钥的名字是 `id_rsa`，是服务器确定你身份的唯一凭证。公钥的名字是 `id_rsa.pub`。
把这个文件发给仓库管理员，仓库管理员会把这个公钥放到服务器上，以后git就通过上面的私钥跟服务器交互了。
如果使用github就是自己把公钥内容添加上去

7. 找一个目录执行`git clone http://xxx.git`（从服务器端克隆git库，当然这个要服务器管理员给你权限和帐号），以后xxx目录就是一个git目录，可以在这个目录下执行git操作

# 安装nvm

> nodejs版本管理工具

**安装步骤：**
1. `ls -a` 查找看有没有`~/.bash_profile`。如果没有则运行`touch ~/.bash_profile`
2. 打开[nvm官网](https://github.com/creationix/nvm)，找到最新的版本号替换如下命令中的`v0.33.6`, 拷贝至mac命令，运行
  ```
  sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash
  ```
3. *（非必需）*运行`export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node` 把环境变量 `NVM_NODEJS_ORG_MIRROR`加入到 `.bash_profile` 文件中，提高安装node速度
4. 安装nodejs `nvm install 6.12.0` `nvm use v6.12.0`
5. 配置npm的国内安装源，加快安装速度`npm config set registry https://registry.npm.taobao.org --global`，配置后可通过`npm config get registry`来验证是否成功
6. *（非必需）*指定npm下载包映射地址`npm config set disturl https://npm.taobao.org/dist --global` 
7. *（非必需）*如果以上都还太慢，可以试试关闭npm的https `npm config set strict-ssl false`

> 自从 Node.js 基金会成立，Node.js 就有一个发布计划，就是同时存在两个发布版本：稳定版和试验版。
> 在 Node.js 中，带有长期支持（LTS）的稳定版是以偶数开始（4,6,8…），而试验版是从奇数开始（5, 7…）。我们推荐在生产环境中用 LTS 版本，而用试验版尝试新东西。
> 使用 cnpm `npm install -g cnpm --registry=https://registry.npm.taobao.org`

# 安装vscode

开发用的工具，必备

## vscode增加code命令

运行VS code并打开命令面板（ `⇧⌘P` ），然后输入 `shell command` 找到: `Install ‘code' command in PATH` 就行了。

## vscode常用插件

> 可以先安装`settings-sync`可以从服务器上拉取之前保存在github上的配置

```
"sync.name": "vscode_sync_setting",
"sync.gist": "e8ce5b103c06d5180dc1782124fdbf8a"(私人配置)
```

> 自定义插件设置

```json
{
    // 项目文件图标主题
    "workbench.iconTheme": "vscode-icons",
    // 搜索时要排除的目录
    "search.exclude": {
        "**/node_modules": true,
        "**/dist": true,
        "**/bower_components": true
    },

    // 启用或禁用所有css/scss验证
    "css.validate": false,
    "scss.validate": false,

    // 保存后自动使用eslint格式化文件
    "eslint.autoFixOnSave": true,
    "eslint.options": {},
    "eslint.validate": [
        "javascript",
        "javascriptreact"
    ],
    // 控件小地图显示。
    "editor.minimap.enabled": false,

    // 启用vue文件里使用html自动实例方案
    "emmet.includeLanguages": {
        "vue-html": "html",
        "vue": "html"
    },
    "emmet.syntaxProfiles": {
        "vue-html": "html",
        "vue": "html"
    },
    // 启用后，按下 TAB 键，将展开 Emmet 缩写。
    "emmet.triggerExpansionOnTab": true,

    // 保存时，自动补充浏览器兼容
    "autoprefixer.formatOnSave": true,

    // 终端在 Windows 使用的 shell 路径。使用随 Windows 一起提供的 shell (cmd、PowerShell 或 Bash on Ubuntu) 时。
    "terminal.integrated.shell.windows": "C:\\WINDOWS\\Sysnative\\cmd.exe",
    // 控制编辑器中呈现空白字符的方式，可能为“无”、“边界”和“全部”。“边界”选项不会在单词之间呈现单空格。
    "editor.renderWhitespace": "all",

    // 是否启用自动拉取git
    "git.autofetch": true,
    // 没有暂存的时候，自动暂存并提交
    "git.enableSmartCommit": true,

    // 同步本地vscode配置到github
    "sync.gist": "e8ce5b103c06d5180dc1782124fdbf8a",
    "sync.lastUpload": "2017-12-19T02:35:36.271Z",
    "sync.autoDownload": false,
    "sync.autoUpload": false,
    "sync.lastDownload": "",
    "sync.forceDownload": false,
    "sync.anonymousGist": false,
    "sync.host": "",
    "sync.pathPrefix": "",
    "sync.quietSync": false,
    "sync.askGistName": false,

    // 在函数参数括号前定义空格处理。需要 TypeScript >= 2.1.5。
    "javascript.format.insertSpaceBeforeFunctionParenthesis": true,

    // 格式化时script启用缩进
    "vetur.format.scriptInitialIndent": true,
    // 格式化时style启用缩进
    "vetur.format.styleInitialIndent": true,
    // 格式化js时采用ts规范
    "vetur.format.defaultFormatter.js": "vscode-typescript",
    "vetur.format.defaultFormatter.html": "js-beautify-html",
    // 使用 eslint-plugin-vue 在 <template> 内验证 vue-html
    // "vetur.validation.template": false,
    // 缩进 <head> 和 <body> 部分。
    "html.format.indentInnerHtml": true,
    // 设置格式化时缩进空格
    "prettier.tabWidth": 4,
}
```
扩展名称|作者|版本号
---|---|---
html-snippets|abu|0.1.0
vscode-caniuse|aka|0.5.3
path-intellisense|chr|1.4.2
bracket-pair-colorizer|Coe|0.10.14
vscode-eslint|dba|1.4.3
githistory|don|0.2.3
jquerysnippets|don|0.0.1
vscode-html-css|ecm|0.1.8
EditorConfig|Edi|0.11.1
vscode-npm-script|eg2|0.3.3
prettier-vscode|esb|1.0.1
auto-rename-tag|for|0.0.15
vscode-filesize|mkx|1.0.0
vscode-autoprefixer|mrm|2.1.2
vetur|oct|0.11.5
vuehelper|oys|0.1.0
java|red|0.16.0
vscode-icons|rob|7.19.0
code-settings-sync|Sha|2.8.7
vscode-java-debug|vsc|0.4.0
vscode-java-pack|vsc|0.2.0
JavaScriptSnippets|xab|1.5.0
html-css-class-completion|Zig|1.14.0

## code命令

参数|描述
---|---
`.`                             | 打开当前目录，打开指定路径 `code C:\src\WebApp`
`file`                          | 打开一个或多个文件。如果文件不存在，此文件将被创建并标记为已编辑
`file:line:column?`	            | 以文件的名称在指定行和可选的列的位置打开，你可以以这个方式指定多个文件。但是在使用 `file:line:column?` 之前必须使用 `-g` 参数。例如：`code -g file:10`
`folder`                        | 打开指定一个或多个目录。你可以指定多个文件夹。例如：`code folder folder`
`-h、--help`                    | code使用说明
`-v、--version`                 | VS Code版本（例如：0.10.10）
`-n、--new-window`              | 打开一个VS Code新的版本替代默认版本
`-r、--reuse-window`            | 强制打开最后活动窗口的文件或文件夹
`-g、--goto`                    | 当和 `file:line:column?`使用时 ，打开文件并定位到一个的特定行和可选的列位置的文件。
`-d、--diff`                    | 打开一个不同的编辑器。需要两个文件路径作为参数。例如：`code -d file file`
`--locale`                      | 为VS Code设置显示语言，支持语言环境有：`en-US` (英语) ，`zh-TW` （中文繁体），`zh-CN` (中文简体)，es 。例如： `code . --locale=en-US` 设置显示语言为英语
`--disable-extensions`          | 禁用所有安装的插件。下拉选 `Show installed Extensions` 后插件依然可见，但是永远不会被激活。
`--list-extensions`             | `code --list-extensions` 列出被安装的插件
`--install-extension`           | 安装一个插件。提供完整的扩展名 `publisher.extension` 作为参数。例如：`code --install-extension ms-vscode.cpptools`
`--uninstall-extension`         | 卸载一个插件。提供完整的扩展名 `publisher.extension` 作为参数。例如：`code --uninstall-extension ms-vscode.csharp`
`-w、--wait`                    | 等待窗口返回之前关闭


# mac打开.bash_profile

1. 启动终端Terminal
2. 进入当前用户的home目录，输入`cd ~`
3. 使用`touch .bash_profile`创建`.bash_profile`
4. 输入`open -e .bash_profile`编辑`.bash_profile`文件
5. 保存文件，关闭`.bash_profile`
6. 输入`source .bash_profile`更新刚配置的环境变量

# npm命令

> [NPM 使用介绍](http://www.runoob.com/nodejs/nodejs-npm.html)

简写命令|完整命令|说明
--|--|---
`-i`                    |  `install`               | npm 安装 Node.js 模块语法格式`$ npm install <Module Name>`(安装在当前项目下)
`ls`                    |  `list`                  | 查看所有当前项目安装的模块, `-g` 查看所有全局的模块；如果要查看某个模块的版本号 `npm list grunt`
`  `                    |  `uninstall`             | `npm uninstall express` 卸载`express`模块
`  `                    |  `update`                | `npm update express` 更新`express`模块
`  `                    |  `search`                | `npm search express` 搜索`express`模块
`  `                    |  `init`                  | `npm init` 创建模块，package.json 文件是必不可少的。我们可以使用 NPM 生成 package.json 文件，生成的文件包含了基本的结果。
`-v`                    |  `--version`             | 
`-h, -?, --help, -H`    |  `--usage`               | 
`-s, --silent`          |  `--loglevel silent`     | 
`-q, --quiet`           |  `--loglevel warn`       | 
`-d`                    |  `--loglevel info`       | 
`-dd,--verbose`         |  `--loglevel verbose`    | 
`-ddd`                  |  `--loglevel silly`      | 
`-g`                    |  `--global`              | 使用全局模式，安装包放在 `/usr/local` 下或者你 `node` 的安装目录
`-C`                    |  `--prefix`              | 
`-l`                    |  `--long`                | 
`-m`                    |  `--message`             | 
`-p, --porcelain`       |  `--parseable`           | 
`-reg`                  |  `--registry`            | 
`-f`                    |  `--force`               | 
`-desc`                 |  `--description`         | 
`-S`                    |  `--save`                | 安装包时，作为`dependencies`保存到`package.json`
`-D`                    |  `--save-dev`            | 安装包时，作为`devDenpendencies`保存到`package.json`
`-O`                    |  `--save-optional`       | 保存安装包到`package.json`里的`optionalDependencies`里
`-B`                    |  `--save-bundle`         | 
`-E`                    |  `--save-exact`          | 
`-y`                    |  `--yes`                 | 
`-n`                    |  `--yes false`           | 
`ll and la`             |  `ls --long`             | 

如果多个单个字符缩写串在一起，并且组合没有去其他配置参数产生歧义，那么就会扩展成多个不同的组成

```bash
npm ls -gpld
# 等同与
npm ls --global --parseable --long --loglevel info
```

如果出现以下错误：
``` bash
npm err! Error: connect ECONNREFUSED 127.0.0.1:8087 
```
解决方案：
```bash
$ npm config set proxy null
```

## 读取package.json配置

```json
{
  "name": "foo",
  "config": {
    "port": "8080"
  },
  "scripts": {
    "start": "node server.js"
  }
}
```


```js
process.env.npm_package_config_port // 8080
process.env.npm_package_name        // foo
```

# linux基础命令

> [Linux 命令大全](http://www.runoob.com/linux/linux-command-manual.html)
> [Linux常用命令大全](https://www.cnblogs.com/fnlingnzb-learner/p/5831284.html)

命令|说明
---|---
`sudo -s`       | 获取绝对用户权限
`cd xxx`        | `cd /home` 进入 '/ home' 目录' 
                | `cd ..` 返回上一级目录 
                | `cd ../..` 返回上两级目录 
                | `cd` 进入个人的主目录 
                | `cd ~user1` 进入个人的主目录 
                | `cd -` 返回上次所在的目录 
`pwd`           | 显示工作路径显示当前绝对路径
`mkdir  xxx`    | 创建文件夹xxx
`mkdir dir1 dir2` | 同时创建两个目录 
`mkdir -p /tmp/dir1/dir2` | 创建一个目录树 
`man xxx`       | 查看xxx命令手册
`ls (-a/-A)`    | 显示当前路径下所有文件及目录(隐藏的); `-F` 查看目录中的文件
`ls (-l)`       | 列出文件详细信息l(list); `*[0-9]*` 显示包含数字的文件名和目录名
`mkdir -p`      | 创建目录，`-p`(若无父目录，则创建p(parent))
`cd`            | 切换目录
`touch`         | 创建空文件
`echo`          | 创建带有内容的文件。
`cat`           | 查看文件内容
`cp`            | 拷贝
`cp file1 file2`     | 复制一个文件 
`cp dir/* .`         | 复制一个目录下的所有文件到当前工作目录 
`cp -a /tmp/dir1 .`  | 复制一个目录到当前工作目录 
`cp -a dir1 dir2`    | 复制一个目录 
`mv`            | 移动或重命名
`rm (-r / -f)`  | 删除文件 `-r` 递归删除，可删除子目录及文件 `-f` 强制删除
`find`          | 在文件系统中搜索某文件
`wc`            | 统计文本中行数、字数、字符数
`grep`          | 在文本文件中查找某个字符串
`rmdir`         | 删除空目录
`tree`          | 树形结构显示目录，需要安装tree包
`lstree`        | 显示文件和目录由根目录开始的树形结构(2) 
`pwd`           | 显示当前目录
`ln`            | 创建链接文件
`more/less`     | 分页显示文本文件内容
`head/tail`     | 显示文件头、尾内容
`ctrl+alt+F1`   | 命令行全屏模式
**系统管理命令** | 
`stat`              | 显示指定文件的详细信息，比ls更详细
`who`               | 显示在线登陆用户
`whoami`            | 显示当前操作用户
`hostname`          | 显示主机名
`uname`             | 显示系统信息
`top`               | 动态显示当前耗费资源最多进程信息
`ps`                | 显示瞬间进程状态 `ps -aux`
`du`                | 查看目录大小 `du -h /home`带有单位显示目录信息
`df`                | 查看磁盘大小 `df -h` 带有单位显示磁盘信息
`ifconfig`          | 查看网络情况
`ping`              | 测试网络连通
`netstat`           | 显示网络状态信息
`man`               | 命令不会用了，找男人  如：`man ls`
`clear`             | 清屏
`alias`             | 对命令重命名 如：`alias showmeit="ps -aux"` ，另外解除使用`unaliax showmeit`
`kill`              | 杀死进程，可以先用`ps` 或 `top`命令查看进程的id，然后再用`kill`命令杀死进程。
**关机/重启机器** |
`shutdown (-r -h now)` `-r`       | 关机重启； `-h`：关机不重启； `now`：立刻关机；
`halt`                            | 关机
`reboot`                          | 重启
**Linux软件包管理** |
`sudo dpkg -i tree_1.5.3-1_i386.deb`         | 不能联网，安装软件（比如安装tree命令的安装包，先将tree.deb传到Linux系统中。再使用如下命令安装）
`sudo dpkg -r tree`                          | 不能联网，卸载软件
`sudo apt-get install tree`                  | 能够连接互联网，安装tree
`sudo apt-get remove tree`                   | 能够连接互联网，卸载tree
`sudo apt-get update`                        | 能够连接互联网，更新软件
`sudo apt-get upgrade`                       | 
`sudo alien abc.rpm`                         | 将.rpm文件转为.deb文件; .rpm为RedHat使用的软件格式。在Ubuntu下不能直接使用，所以需要转换一下。
**打包压缩相关命令** |
`gzip / bzip2 / tar`   | 打包压缩； `-c`：归档文件；`-x`：压缩文件；`-z`：gzip压缩文件；`-j`：bzip2压缩文件；`-v`：显示压缩或解压缩过程 v(view)；`-f`：使用档名；
`tar -cvf /home/abc.tar /home/abc`            | 只打包，不压缩
`tar -zcvf /home/abc.tar.gz /home/abc`        | 打包，并用gzip压缩
`tar -jcvf /home/abc.tar.bz2 /home/abc`       | 打包，并用bzip2压缩

> 当然，如果想解压缩，就直接替换上面的命令  `tar -cvf`  / `tar -zcvf`  / `tar -jcvf` 中的“c” 换成“x” 就可以了。

# vim常用命令

## 光标移动(Cursor Movement)

命令|说明
---|---
`h,j,k,l`         |	h表示往左，j表示往下，k表示往右，l表示往上
`Ctrl+f`          |	上一页
`Ctrl+b`          |	下一页
`w, e, W, E`      |	跳到单词的后面，小写包括标点
`b, B`            |	以单词为单位往前跳动光标，小写包含标点
`O`               |	开启新的一行
`^`               |	一行的开始
`$`               |	一行的结尾
`gg`              |	文档的第一行
`[N]G`            |	文档的第N行或者最后一行

## 插入模式(Insert Mode)

命令|说明
---|---
`i`            |	插入到光标前面
`I`            |	插入到行的开始位置
`a`            |	插入到光标的后面
`A`            |	插入到行的最后位置
`o, O`         |	新开一行
`Esc`          |	关闭插入模式

## 编辑(Editing)

命令|说明
---|---
`r`            |	在插入模式替换光标所在的一个字符
`J`            |	合并下一行到上一行
`s`            |	删除光标所在的一个字符, 光标还在当行
`S`            |	删除光标所在的一行，光标还在当行，不同于`dd`
`u`            |	撤销上一步操作
`ctrl+r`       |	恢复上一步操作
`.`            |	重复最后一个命令
`~`            |	变换为大写
`[N]>>`        |	一行或`N`行往右移动一个`tab`
`[N]<<`        |	一行或`N`行往左移动一个`tab`

## 关闭(Exiting)

命令|说明
---|---
`:w`          | 保存
`:wq, :x`     | 保存并关闭
`:q`          | 关闭（已保存）
`:q!`         | 强制关闭

## 搜索(Search)

命令|说明
---|---
`/pattern`        |	搜索（非插入模式)
`?pattern`        |	往后搜索
`n`               |	光标到达搜索结果的前一个目标
`N`               |	光标到达搜索结果的后一个目标

## 视觉模式(Visual Mode)

命令|说明
---|---
`v`       |	选中一个或多个字符
`V`       |	选中一行

## 剪切和复制(Cut and Paste)

命令|说明
---|---
`dd`        |	删除一行
`dw`        |	删除一个单词
`x`         |	删除后一个字符
`X`         |	删除前一个字符
`D`         |	删除一行最后一个字符
`[N]yy`     |	复制一行或者`N`行
`yw`        |	复制一个单词
`p`         |	粘贴

## 窗口操作

命令|说明
---|---
`:split`     |	水平方向分割出一个窗口
`:vsplit`    |	垂直方向分割出一个窗口
`:close`     |	关闭窗口
`Ctrl+W`     |	切换窗口, `h`到左边窗口，`j`到下方窗口，`k`到上方窗口，`l`到右边窗口

# nvm命令

命令|说明
---|---
  nvm --help                                | 显示帮助
  nvm --version                             | 当前安装的nvm版本号
  nvm install [-s] <version>                | 安装指定版本 `<version>`， 指定来源`[-s]` 的node，可模糊安装，如：`nvm install v4.4.0 / nvm install 4.4`
    --reinstall-packages-from=<version>     | When installing, reinstall packages installed in <node、iojs、node version number>
    --lts                                   | When installing, only select from LTS (long-term support) versions
    --lts=<LTS name>                        | When installing, only select from versions for a specific LTS line
    --skip-default-packages                 | When installing, skip the default-packages file if it exists
    --latest-npm                            | After installing, attempt to upgrade to the latest working npm on the given node version
  nvm uninstall <version>                   | 删除已安装的指定版本，语法与install类似
  nvm uninstall --lts                       | Uninstall using automatic LTS (long-term support) alias `lts/*`, if available.
  nvm uninstall --lts=<LTS name>            | Uninstall using automatic alias for provided LTS line, if available.
  nvm use [--silent] <version>              | 切换使用指定的版本 <version> 的node. Uses .nvmrc if available
    --lts                                   | Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        | Uses automatic alias for provided LTS line, if available.
  nvm exec [--silent] <version> [<command>] | Run <command> on <version>. Uses .nvmrc if available
    --lts                                   | Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        | Uses automatic alias for provided LTS line, if available.
  nvm run [--silent] <version> [<args>]     | Run `node` on <version> with <args> as arguments. Uses .nvmrc if available
    --lts                                   | Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        | Uses automatic alias for provided LTS line, if available.
  nvm current                               | 显示当前使用的node版本
  nvm ls                                    | 列出已安装的node列表
  nvm ls <version>                          | 列出指定版本的node
  nvm ls-remote                             | 列出所有远程服务器的版本（官方node version list）
    --lts                                   | When listing, only show LTS (long-term support) versions
  nvm ls-remote <version>                   | List remote versions available for install, matching a given <version>
    --lts                                   | When listing, only show LTS (long-term support) versions
    --lts=<LTS name>                        | When listing, only show versions for a specific LTS line
  nvm version <version>                     | Resolve the given description to a single local version
  nvm version-remote <version>              | Resolve the given description to a single remote version
    --lts                                   | When listing, only select from LTS (long-term support) versions
    --lts=<LTS name>                        | When listing, only select from versions for a specific LTS line
  nvm deactivate                            | Undo effects of `nvm` on current shell
  nvm alias [<pattern>]                     | Show all aliases beginning with <pattern>
  nvm alias <name> <version>                | 给不同的版本号添加别名
  nvm unalias <name>                        | 删除已定义的别名
  nvm install-latest-npm                    | Attempt to upgrade to the latest working `npm` on the current node version
  nvm reinstall-packages <version>          | 在当前版本node环境下，重新全局安装指定版本号的npm包
  nvm unload                                | Unload `nvm` from shell
  nvm which [<version>]                     | Display path to installed node version. Uses .nvmrc if available
  nvm cache dir                             | Display path to the cache directory for nvm
  nvm cache clear                           | Empty cache directory for nvm

例子|说明
---|---
  nvm install 8.0.0                     | Install a specific version number
  nvm use 8.0                           | Use the latest available 8.0.x release
  nvm run 6.10.3 app.js                 | Run app.js using node 6.10.3
  nvm exec 4.8.3 node app.js            | Run `node app.js` with the PATH pointing to node 4.8.3
  nvm alias default 8.1.0               | 设置shell里面默认的node版本为8.1.0
  nvm alias default node                | 在shell里面总是使用已安装的最高的node版本
