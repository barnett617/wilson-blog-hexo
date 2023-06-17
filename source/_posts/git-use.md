---
title: 再品Git, 深度解读
date: 2018-08-23
tags:
---

# 再品Git, 深度解读

## 背景

和一个长期使用IDE(eclipse)集成git进行代码版本管理的人交流项目，博文内容将采用类似《大话设计模式》的对话体进行，场景真实，有代入感，正文部门前面是对方参照我写的readme搭建基于vue-cli的demo项目，安装node并通过npm安装项目依赖并本地启动项目等等，由于对方为后端Java开发，对`新前端`技术栈了解比较少，在搭建环境和启动项目过程中滋生一些趣事，比如使用npm启动项目后如何在浏览器访问项目，我为了让其醒目意识到我们的项目首页就是未经改动的Vue-cli首页，于是我将首页改动并推到远程master分支，后面便是我们的搞笑对话。

## 正文

暂定对方昵称为`lzz`

Begin

我：“你再从master拉一下最新的代码，然后重跑一下”

lzz：“速度教一波拉代码，我没操作过命令行”

我：“git pull origin master”

lzz：“我天天在eclipse操作”

lzz：“要在项目目录下吗”

我：“嗯”

lzz：“这是pull下来吧，接下来的呢，ok了？”

我：“pull会自动合并进当前分支，fetch只拉，还需要手动merge”

> 此处涉及git pull和git fetch & git merge的区别

lzz：“怎么上传分支”

我：“你新建分支了吗，我瞅瞅”

lzz：“如何瞅，命令行在哪看”

我：“截图，git checkout -b 新建的分支名”

我：“git branch 看现在在哪个分支”

lzz：“你的分支”

lzz：“我拉你的分支还是从主分支上拉比较好”

我：“当然主分支，你为啥在我的分支”

lzz：“我看看怎么拉主分支”

我：“git checkout master”（我因得知他在我的分支上，所以让他先切到主分支上去）

lzz：“我又没上传这个分支”

我：“切换到主分支，你为啥会到我的分支”

lzz：“不知道”

我：“骚操作”

lzz：“昨晚zz教我拉的时候拉的吧”

我：“不学好”

lzz：”fxxk，还要提交，我直接把这个分支删了吧“（我内心os："... 为啥要删我分支 ..."）

我：”你改啥了，你改动后删不掉的，git status“

> 这里涉及使用`git status`查看本地仓库文件状态

lzz：”fxxk you“

我：”fxxk you too“

我：”git status, 看看改啥了“

lzz：”嗯“

我：”git checkout .“

我：”git checkout master“

我：”git checkout -b lzz“

我：”git push origin lzz“

lzz：”我还原了，这是依赖吧“

然后我看到他的控制台还有package.json和package-lock.json文件为已修改状态，于是提示他撤销全部

我：”git checkout .“

lzz：”npm clean？“

我：”no“

lzz：”你这是重放个位置了“

我：”你执行就是了“

lzz：”没鸟用，是../吧“

我：”git status“

他了一张截图，提示他所在目录不是一个git目录，因为他已经通过..到了项目上一级目录，自然不是git目录

我：”回到项目目录“

lzz：”git init?“

我：”你乱跑啥“

我：”cd ./项目目录名“

我：”init你妹“

此时他已回到项目目录再次git status，提示工作树干净(working tree clean)

我：”git checkout master“

我：”git checkout -b lzz“

我：”git push origin lzz“

我：”赶紧，让我看到你的分支，刷个存在感“

经过操作，他在远端仓库推送了新的分支lzz到项目

我：”不容易“

lzz：”这个主分支和你的分支就index.html不一样吗“(因为他看到的是旧的master，他并没有按照我的要求使用git pull origin master拉取我新提交的master)

我：”你就没改东西，一毛一样，现在三个分支内容一样“

lzz：”页面不一样“

我：”一样的，master我改了，你刚才看到的就是master的“

lzz：”嗯，提交就是，git push origin lzz？“

> 严谨来说这里并不一定，因为origin只是git约定俗成的远程主机名，其实这个参数可以自定义，比如你需要推到多个远程仓库时，需要在origin之外自定义远程主机名

我：”不是“

lzz：”还是直接git push“

我：”这是把本地分支推到远程“

lzz：”嗯“

我：”提交是git commit“

lzz：”1，想起来了“

我：”直接git push需要把本地同名分支和远程同名分支绑定“

> 这里有待考究，我说的这是否是必要条件，但git的确是提供了-u参数用于绑定本地分支与一个默认远程分支，这样仅使用git pull和git push便可将该远程分支与本地分支进行互动

lzz：”git checkout . 放弃文件的修改吧？“

我：”是，其实是把远端的覆盖本地的“

> 关于git checkout的使用，理解为放弃修改，从效果上看的确是这样的，也是我一开始学习和使用git的理解，但从本质上git checkout实际还是在执行其本质行为，就是检出远程文件，只不过这样做的确就会将本地的覆盖，达到放弃本地文件的修改的效果

lzz：”我每次重新切换分支，要npm install吗？“

我：”新分支需要，已经install过的分支不需要“(此处涉及npm项目的知识，与本文关系不大)

lzz：”我push的时候不会将这些包带着吧“

我：”不会，.gitignore忽略node_modules目录“(这当然是我配置的，不过这个问题问的很好，说明其意识到的git对于项目文件的管理细节)

lzz：”貌似没有忽略啊“(他看到package.json和package-lock.json文件被提示尚未加进暂存区)

我：”这两个文件可以提交，也可以手动忽略，我加一下“(所谓手动忽略当然就是使用git checkout检出远程同名文件覆盖本地的修改，我加一下是指其实package-lock.json的确不需要提交，而package.json随着项目的迭代可能有包的更新，所以项目团队间需要互通，不过如果使用相同的node环境，这样的更新每个人在本地都会有及时的感知，所以这两个文件是否要纳入版本管理不重要)

lzz：”上面的.json文件我并未改动，应该就像maven的pom文件一样，为什么会变动呢“(此处涉及npm对于依赖包的管理)

我：”因为你又npm install了，会更新版本“

lzz：”拉下新的版本，拉下我的“

我：”干啥“(其实我已经在远端仓库客户端，我们会使用的gitee，看见他的修改，一个小小的恶作剧)

lzz：”你瞅瞅我的master piece“

我：”你发pull request“

> 此处涉及gitee、github这类用于公共开源，不同于gitlab的merge request，而是通过发起pull request将分支合入master

他给我发了一张他提交记录的截图，标识他已经将自己本地的分支提交到远端仓库

我：”你不提交pull request，你就只是在自己的分支玩，你看看你能merge进master吗“

lzz：”你拉我的分支不行吗，你把我的分支拉下来啊“

我：”我干嘛要拉你的分支“

我给他发一张我对于其提交的评论截图(基于gitee)

lzz：”你这个页面在哪，github还是码云上“

我：”gitee“

lzz：”什么鬼？？？“(因为码云这个对于代码评论的功能的确做的看上去和github很像)

我：”码云，就是gitee“

我：”洗洗睡吧“

lzz：”这样？“

他发了一张在操作pull request操作的截图，但填写是错误的，源分支为远程lzz，目标分支为远程lzz，其实pull request是要发起申请将你提交到远程仓库的分支merge进远程的master分支，经过操作，他最终成功发起了正确的pull request

lzz：”知道PR啥作用了“

我：”目标分支填master啊“

lzz：”嗯，知道了，睡觉“

END

## 总结

整个过程其实就是一个经典的新手入手git并结合第三方远程仓库管理个人项目的各种操作，对话看上去愚蠢，但其实基本覆盖了团队基于git进行协作的基本操作和重要核心的git flow

## 结论

在此过程中虽然我使用git有一些经验，但还是觉得仍有部分环节的细节有待深入研究，不过经验就是：使用git一定要随时保持清醒，知道自己所处位置（git仓库、暂存区、本地仓库、远程仓库），对分支的分分合合要有着清醒的头脑，否则很有可能在使用git这个管理代码和版本的强大工具之下也会搞的一团糟，它就像一把双刃剑，用好的话可以事倍功半，用不好也可能对代码项目带来严重的灾难