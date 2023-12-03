---
title: 使用Hugo搭建博客站点
date: 2023-12-03 15:54:37
---

# 使用Hugo搭建博客站点

> 本篇介绍将博客站点从 Hexo 迁移为 Hugo 的过程

## Hugo 简介以及和 Hexo 的对比

Hugo 是一款基于 Go 的开源静态站点框架，从 [Github](https://github.com/gohugoio/hugo) 记录来看，应该是 [Hexo](https://github.com/hexojs/hexo) 的前身（一款基于 Node.js 的博客框架）。

Hugo 的简介是“世界上构建网站最快的框架”，这个不可否认，因为 Go 语言的优势就是并发性能高。不过对于搭建博客这种相对静态的站点来说，多少有点 Over kill。

那么既然它的简介是可以“构建网站“，扩展性上应该还是不错的吧，可能如果真的用于维护相对大型的网站，可能效果要比 Hexo 好一些？

## 环境准备

按照 Hugo 安装说明准备好环境，我选择使用 Hugo 源码本地构建的方式，这里主要是准备 git、go 以及 c 编译器的配置。

这几项对于 Mac OS 来说都还好，主要是就是安装 Go，不过之前本地装过 Go，最终准备好的环境状态如下：

```sh
➜  ~ git --version
git version 2.39.2 (Apple Git-143)
➜  ~ go version
go version go1.21.3 darwin/arm64
➜  ~ gcc --version
Apple clang version 14.0.3 (clang-1403.0.22.14.1)
Target: arm64-apple-darwin22.5.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
```

## 安装 Hugo

使用源码构建的方式，就是执行一条命令

```
GGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
```

这应该是把 Hugo 这个模块安装到 Go 的环境里，但是一上来就遇到了国内老生长谈的资源代理问题

```
GGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
go: github.com/gohugoio/hugo@latest: module github.com/gohugoio/hugo: Get "https://proxy.golang.org/github.com/gohugoio/hugo/@v/list": dial tcp 142.251.43.17:443: i/o timeout
```

于是查了一下网上的解决方法，尽管使用了谷歌搜索，排名靠前的搜索结却依然是 CSDN 的回答，方法就是改用国内代理

```
go env -w GOPROXY=https://goproxy.cn
```

这种方法可能立竿见影，但是怕后续安装其他包有问题，所以继续看看有没有更通用的解决方法。

接着搜到 Go 的 Github 仓库下相关的 [issue](https://github.com/golang/go/issues/57185#issuecomment-1343966825) ，里面讨论出的方法看上去更加简单粗暴

```
go env -w GOPROXY=direct
go env -w GOSUMDB=off
```

看上去像是把代理修改为了直连，还关闭了一个配置，但这确实大家一致认同（👍🏻）的解决方法

再看看还有没有更靠谱的方法（开始好奇国内那么多 Go 开发者又是怎么面对代理这个问题的，真是难）

还发现了第一个方法（修改代理源）相关的 [Github 讨论](https://github.com/goproxy/goproxy.cn/issues/93)，这个工具应该是中国人开发的，以至于 issue 内都直接是全中文讨论。

> 开始感慨国内环境下学一门新的编程技术时，往往遇到的最大问题就是环境问题，这也是最劝退的环节，但这本不应该是阻挠初学者入门的问题。

接着打开了一篇 StackOverflow 的[帖子](https://stackoverflow.com/questions/72726715/timeout-when-i-try-to-install-go-dependencies)

没想到这问题在 2022 年也有讨论，好像是 Go 本身存在已久的问题，再次感叹 Go 的开发者不易。

翻了下一开始检查的 Go 版本号，是 go1.21.3，而 Node.js 的主版本好像都到 20 了吧，难道 Go 生态的迭代好像还不是很成熟的状态？

不过经过 StackOverflow 帖子的确认，准备考虑试一下把代理修改为直连的方式，希望奏效。

> 不过我开始好奇对 Go 有更多的了解，比如如何查看我本地 Go 的环境的完整配置现状是什么样呢？比如修改 GOPROXY=direct，那其他配置是什么样的呢？我修改完这个怎么确认我的修改有效呢？以及修改完这个配置还需要做什么来让这个配置修改生效呢？

就在思考这些的同时，刚刚修改完配置的执行已经结束了，结果是再次失败

```
GGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
go: downloading github.com/gohugoio/hugo v0.120.4
go: github.com/gohugoio/hugo@latest: github.com/gohugoio/hugo@v0.120.4: verifying module: github.com/gohugoio/hugo@v0.120.4: Get "https://sum.golang.org/lookup/github.com/gohugoio/hugo@v0.120.4": dial tcp 142.251.43.17:443: i/o timeout
```
不过这次的失败原因好像不一样，看上去 Hugo 本身的下载好像有些进展，最后失败的是下载 sum 相关的东西。

这不就跟上面查到的对上了么，因为我只是把代理修改为了直连，而没有关闭那个和 sum 相关的配置，原因是为了做最小改动，但是看样子这个还是避不开要做。

sum 这个东西从名字上来看像是对包的校验，所以我决定暂时完全按照大家试过的方式先解决这个问题。

而且刚才我使用的是 StackOverflow 帖子里提供的方式，也就是

```
export GOPROXY=direct
```

这种 export 的方式我记得是修改 shell 环境的环境变量设置，不知道下面的设置方式本质是不是也是这个效果。

```
go env -w GOPROXY=direct
go env -w GOSUMDB=off
```

不过，这样来看 go env 应该是 go 的一个命令，那么可以通过 help 命令看下具体的当前配置情况，也顺便解开刚才的迷惑。

```
go help   
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies
        clean       remove object files and cached files
        doc         show documentation for package or symbol
        env         print Go environment information
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources
        generate    generate Go files by processing source
        get         add dependencies to current module and install them
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        work        workspace maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.
```

发现一个好东西，go env ，原来这个命令默认是可以查看所有的环境配置信息，如果在后面跟随内容则是对环境配置进行设置，这样看 -w 应该是写入的意思，有点像 git 修改配置的方式。

同时，我也在打印出的环境配置信息中发现了我想知道的，如下：

```
GOPROXY='direct'
```

这意味着刚才通过 export 设置的应该是生效了，但是 GOSUMDB 如下：

```
GOSUMDB='sum.golang.org'
```

这明显和预期的 off 不同，可能就是这个默认配置导致我的步骤进行不下去，那么赶紧修改它试一下

```
go env -w GOSUMDB=off
```

这次果然顺利，控制台展示出各个模块的下载

```
GGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
go: downloading github.com/gohugoio/hugo v0.120.4
go: downloading github.com/alecthomas/chroma/v2 v2.10.0
go: downloading github.com/bep/clocks v0.5.0
go: downloading github.com/bep/debounce v1.2.0
go: downloading github.com/bep/lazycache v0.2.0
go: downloading github.com/bep/logg v0.3.0
go: downloading github.com/bep/mclib v1.20400.20402
go: downloading github.com/bep/overlayfs v0.6.0
go: downloading github.com/bep/simplecobra v0.3.2
go: downloading github.com/fsnotify/fsnotify v1.7.0
go: downloading github.com/spf13/afero v1.10.0
go: downloading github.com/spf13/cobra v1.7.0
go: downloading github.com/spf13/fsync v0.9.0
go: downloading github.com/spf13/pflag v1.0.5
go: downloading go.uber.org/automaxprocs v1.5.3
go: downloading golang.org/x/sync v0.4.0
go: downloading gopkg.in/yaml.v2 v2.4.0
go: downloading github.com/hashicorp/golang-lru/v2 v2.0.1
go: downloading github.com/bep/godartsass v1.2.0
...
```

> 后面有时间等有必要时再了解一下这里的 sum 究竟是什么作用，目前配置上先把它设置为 off 了，如果是包校验什么的，应该也不会影响 go module 的正常使用

模块的下载过程就是普通的网络 IO，不过看到控制台展示的内容突然想起来之前看到过的 Go 代码，Go 的模块引用路径好像是可以写远程 URL 的，难道 Deno（Node.js之后的 JavaScript 服务端运行时）就是借鉴了这种方式？

还在想这些的时候，没想到下载过程又失败了，这次虽然应该是成功下载了一部分模块，但还是有很多模块因 tcp 拨号超时导致没下载下来。

```
go: downloading github.com/josharian/intern v1.0.0
pkg/mod/github.com/bep/mclib@v1.20400.20402/internal/main.go:27:2: unrecognized import path "golang.org/x/net": https fetch: Get "https://golang.org/x/net?go-get=1": dial tcp 172.217.163.49:443: i/o timeout
pkg/mod/software.sslmate.com/src/go-pkcs12@v0.2.0/crypto.go:20:2: unrecognized import path "golang.org/x/crypto": https fetch: Get "https://golang.org/x/crypto?go-get=1": dial tcp 172.217.163.49:443: i/o timeout
pkg/mod/github.com/spf13/afero@v1.10.0/util.go:28:2: unrecognized import path "golang.org/x/text": https fetch: Get "https://golang.org/x/text?go-get=1": dial tcp 142.251.43.17:443: i/o timeout
pkg/mod/github.com/spf13/afero@v1.10.0/util.go:29:2: unrecognized import path "golang.org/x/text": https fetch: Get "https://golang.org/x/text?go-get=1": dial tcp 142.251.43.17:443: i/o timeout
pkg/mod/github.com/spf13/afero@v1.10.0/util.go:30:2: unrecognized import path "golang.org/x/text": https fetch: Get "https://golang.org/x/text?go-get=1": dial tcp 142.251.43.17:443: i/o timeout
pkg/mod/github.com/fsnotify/fsnotify@v1.7.0/backend_kqueue.go:16:2: unrecognized import path "golang.org/x/sys": https fetch: Get "https://golang.org/x/sys?go-get=1": dial tcp 172.217.163.49:443: i/o timeout
...
```

先试一下 Hugo 现在能不能运行

```
hugo --version 
zsh: command not found: hugo
```

可惜，看来 hugo 还没成功下载。于是接着拿控制台的内容去搜索解决方法。

这回搜到的是[知乎的回答](https://zhuanlan.zhihu.com/p/408157841)

感觉标准的方式行不通了，为了不在环境准备这一步花费太多时间，我决定妥协使用国人的工具，也就是 goproxy.cn，毕竟有些问题本质就是国家网络环境的问题。

```
go env -w GOPROXY=https://goproxy.cn
warning: go env -w GOPROXY=... does not override conflicting OS environment variable
```

竟然这也不顺利

> 有点回想起当初折腾 Java 的感觉，果然服务端的东西很多时候还是环境的问题，docker 也算解决了一部分这些问题吧，毕竟对于应用部署来说。

不过再仔细看控制台输出，想起了刚才使用过 export 设置，感觉这就是和 go env 冲突的地方，也是刚才担心过的问题。

本来准备研究一下 shell 的 export 和 source 以及 go env 的区别，但是在搜索的过程中发现 goproxy.cn 的[官网](https://goproxy.cn/)，看了一下受欢迎程度还行。

那就先试试用这个工具来搭建国内使用 Go 的环境吧，毕竟对于 Node.js 在国内使用也是要经常考虑把包管理源设置为国内的镜像。

于是准备按照 goproxy.cn 的使用介绍进行配置，不过依然在进行到设置 GOPROXY 这一步会失败，看输出内容应该就是和 shell 环境里的设置冲突了。不过我记得通过 export 设置的环境变量只在一次 shell 的生命周期有效，也就是只要我重启 shell ，刚才通过 export 设置过的应该就会没了。

经过验证，果然如此，在重启终端后再次使用 go env 设置 GOPROXY 果然成功了，安装继续。

```
GGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
go: downloading golang.org/x/sync v0.4.0
go: downloading golang.org/x/exp v0.0.0-20221031165847-c99f073a8326
go: downloading golang.org/x/text v0.13.0
go: downloading google.golang.org/api v0.138.0
go: downloading golang.org/x/sys v0.13.0
go: downloading golang.org/x/tools v0.14.0
go: downloading google.golang.org/protobuf v1.31.0
go: downloading golang.org/x/image v0.13.0
go: downloading cloud.google.com/go/compute/metadata v0.2.3
go: downloading cloud.google.com/go/compute v1.23.0
go: downloading cloud.google.com/go/iam v1.1.1
go: downloading cloud.google.com/go/storage v1.31.0
go: downloading golang.org/x/oauth2 v0.11.0
go: downloading cloud.google.com/go v0.110.7
go: downloading golang.org/x/net v0.17.0
go: downloading golang.org/x/mod v0.13.0
go: downloading golang.org/x/xerrors v0.0.0-20220907171357-04be3eba64a2
go: downloading google.golang.org/grpc v1.57.0
go: downloading golang.org/x/crypto v0.14.0
go: downloading google.golang.org/genproto/googleapis/api v0.0.0-20230803162519-f966b187b2e5
go: downloading google.golang.org/genproto v0.0.0-20230803162519-f966b187b2e5
go: downloading google.golang.org/genproto/googleapis/rpc v0.0.0-20230807174057-1744710a1577
go: downloading github.com/golang/protobuf v1.5.3
go: downloading github.com/google/s2a-go v0.1.5
go: downloading github.com/googleapis/enterprise-certificate-proxy v0.2.5
go: downloading google.golang.org/appengine v1.6.7
```

这次果然异常平静得顺利🙄，看来在国内开发 Go，还是得有一层自己的代理。

> 这个代理层应该是在国内服务器架设了模块仓库，然后定期更新国外源的资源并做缓存。所有具备模块化的语言基本都逃不开这种做法，不过说到底这还是网络的问题，但好像这样做也是符合各个国家分布式使用网络资源的最合理方式，有点像网络模块的 CDN 。

问题不大，赶紧试试 hugo 装成功了吗

```
hugo --version
zsh: command not found: hugo
```

竟然还是不行，感觉被模块下载缠了这么久，有点忘记主线的流程，返回看了下 hugo 的[文档](https://gohugo.io/installation/macos/#build-from-source)

这回好像又发现了自己的问题，可以是因为之前就安装过 Go ，因此对于 GOBIN 这一步的设置忽视了。好像是因为这里没按照 hugo 文档进行设置，因此无法在任意目录使用 hugo 的可执行文件。

于是按照 hugo 文档的提示去看了看 Go 的文档，大概理解了 GOBIN 的定位，这是存放 Go 可执行模块的地方，类似于 Node.js 中 .bin（包括 Linux 操作系统对于可执行文件的设计也差不多是这样的）

于是找到 GOPATH 所在的目录，果然找到了 bin 目录，进到 bin 目录后果然看到了可执行文件 hugo 。

既然 hugo 已经安装成功，那就好办了。

看到 Go 文档中有介绍可以设置 GOBIN 的目录，但是此时还是不太理解如果不设置用默认的有什么问题。反复看了几遍关于 go mod 的介绍，感觉和 Node.js 对于 node_modules 的管理差不多。

直到看到 Hugo 社区的一篇[帖子](https://discourse.gohugo.io/t/hugo-not-found/27870/2)才恍然大悟。

> 对于 hugo 有社区这一现象感觉还不错，说明用户群体应该挺大的，而且 hugo 貌似也有些年头了。

关键问题就在于我没有设置 GOBIN，因此对于 Go 下载下来的可执行模块都放在了 GOPATH 下的 bin 目录。

但在没有做任何事情的情况下，这些可执行文件只能进到这个目录才能执行，而不能任何其他位置执行，这不就是 Linux 系统对于 bin 目录的设计吗

因此只有我把 GOBIN 设置为系统的用户 bin 目录下，这样操作系统才会对于我在任意目录下输入某个文件时查找其是否存在于可执行文件目录并执行。

> 而且这篇帖子的[讨论](https://discourse.gohugo.io/t/hugo-not-found/27870/16)也顺带提了一下关于 source 命令的使用，这里应该意思就是 source 是用来执行 shell 配置文件的，因此当修改完 shell 配置文件后，如果希望立刻对当前 shell 环境生效，那么需要通过 source 来显式地告诉 shell。

于是我仔细检查了我的 $PATH 和 ~.zshrc 配置

我的 $PATH 中有 `/usr/local/go/bin` 一项，同时我的 ~.zshrc 中有关于 $PATH 相关的导出 `export PATH="$BUN_INSTALL/bin:$PATH"`

因此对于 /usr/local/go/bin 目录下的可执行文件我是可以在任意目录执行的，但问题在于我当前的 go env 中并未设置 GOBIN，所以安装在该目录的 hugo 暂时还无法被检索到。

问题清晰了就离解决不远了

先总结一下，shell 的配置文件，默认可能是 bash 的配置文件，使用了 on my zsh 的则会是 .zshrc。

这个文件声明了 shell 启动时的环境配置，里面一般会将 $PATH 变量导出，这也意味着该变量内的路径都成为了某个 shell 内的环境变量，因此在 shell 内执行文件时都会寻着 $PATH 变量设置过的所有路径找一遍。

因此一般会将可执行文件存放的目录设置在 PATH 中，这一特征对于类 Unix 系统（Linux、MacOS）和 Windows 都差不多

总结完，我们就知道应该如果做了。

因为我现在的 .zshrc 是导出了 PATH 的，而 PATH 中也存在 Go 相关的可执行文件目录，只是 Go 下载可执行模块的安装目录由于未设置而被默认为 ~/go/bin。而这个目录并不在 PATH 之列，因此无法在执行文件时被索引到，这可能是之前装 Go 时因为不熟悉而留下的问题吧。

那么现在可做的方法有两个，一个是把 GOBIN 的默认目录设置在 PATH 中，另一个则是把 PATH 中已有的 Go bin 目录设置为 GOBIN 目录，我选择后者，因为改动最小。

改动之前可以先到 /usr/local/go/bin 目录看看现在都有什么

```
cd /usr/local/go/bin
➜  bin ls
go    gofmt
```

啊哈，原来就是 go 本身，那 Go 只是默认不希望所有下载的可执行模块都被注册到这里来吧，因此默认的 GOBIN 留给了用户自己设置

```
CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
github.com/gohugoio/hugo: go install github.com/gohugoio/hugo: copying /var/folders/56/pfj7c2qs30j1n92sqrrq471c0000gn/T/go-build113185545/b001/exe/a.out: open /usr/local/go/bin/hugo: permission denied
➜  bin sudo CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest
Password:
➜  bin ls
go    gofmt hugo
```

效果不错，只是很明显这样下载可执行模块时需要通过 sudo 授权，因为貌似 /usr/local/go/bin 是需要授权的目录，而 ~/go/bin 是用户目录则没有这个问题。而且这个问题也只有在下载模块到 go 全局模块目录才会有这个问题，对于一般的 Go 项目都会下载到项目目录（类比下载 npm 包到 node_modules）。

不过为了不给未来留坑，貌似还是把 GOBIN 默认的 ~/go/bin 设置到 PATH 更合理些，毕竟发现 PATH 中有关于 bun（JavaScript另一个服务端运行时）的设置就是将用户目录设置在 PATH 的，而且 PATH 中大多设置的都是用户目录，并附加系统的那几个 bin 目录。

经过一顿关于环境变量的设置，终于如愿以偿地看到了下面的输出

```
hugo version
hugo v0.120.4+extended darwin/arm64 BuildDate=unknown
```

真是有点为了砸一个钉子，研究了锤子是怎么做的，不过索性就当复习了一下操作系统基础知识。

## 使用 Hugo 新建站点

未完待续





