---
layout: post
title:  "git rebase 用来合并多个commit 使用介绍"
date:   2016-08-31 14:49:00 +0800
categories: swumao update git rebase pick edit reword drop squash fixup
---

在使用git的过程中，有时会遇到需要将最近几次的commit合并为一个的情况，比如最近几次都是在修改一些无关痛痒的小毛病，如果是多个commit的情况的话，以后查看的时候一堆的一样的fix bugs信息，很不方便查看，所以可以把这个commits合并为一个fix bugs，这样就可以方便快速地找到自己的某次commit了。

要介绍的这个命令就是`rebase`命令。当然rebase命令不止这么简单，还有很多别的用法，这里只说明用于修改commit时的用法。

`git rebase -i <after-this-commit>`

上面就是rebase命令在这个场景中的使用方式，其中-i参数表示进入交互（interactive）模式。需要传入的参数是你要修改的那个commit之前的一个commit，查看commit的历史可以使用如下命令查看：

`git log`

或者每个commit显示一行

`git log --pretty=oneline`

输入rebase命令之后会打开一个编辑器，里面显示了如下格式的几行文本（会有一个以#开头的文本，这些都是注释，不用管）

```
pick deadbee The oneline of this commit
pick fa1afe1 The oneline of the next commit
...
```

其中每一行表示一次commit，第一个单词`pick`表示要对这个commit进行的操作命令，第二个非单词的字符串表示那个commit的id的钱七个字符，剩下的词表示该commit的commit message。

我们接下来的任务就是根据自己的需求对每行前面的代表命令的词进行修改。详细介绍请接着看如下：

动词一共有如下几个：

- edit
- reword
- drop
- squash
- fixup

## edit

edit命令表示你告诉了rebase，当在应用这个commit的时候，停下来，等待你修改了文件 和/或 修改了commit message之后在继续进行rebase。

上面说的很不好理解，建议自己尝试一下，就明白了。总之这个命令可以让你既能修改文件，又能修改commit message

## reword

reword命令可以让你修改commit message。当你使用这个命令后，保存这个文件并退出，执行`git rebase continue`命令之后会再次打开一个文件，让你对这个commit的commit message进行修改，再次保存退出之后继续进行rebase

## drop

drop命令表示你要丢弃这个commit以及它的修改。同样可以删除这一行来表示。（在git比较低的版本中，比如我使用的1.9.0版本中，只能通过删除那一行的方式来做，不支持drop命令）

## squash 和 fixup

这两个命令都是用来将几个commit合并为一个的。其中，fixup命令，rebase的时候将会直接忽略掉它的commit message，而squash命令，则会在`git rebase --continue`之后打开一个文件，该文件中将会出现所有设置为squash的commit，这时删除掉多余的commit message，留下（或者修改）一行作为合并之后的commit的commit message。

到此为止，讲这个文件保存并退出，输入git status查看需要进行什么操作（比如需要解决冲突），之后执行`git rebase --continue`即可。

执行之后会根据你使用的命令的不同进行不同的操作，比如有的可以直接rebase有的则会打开一个文件让你进行一些操作，具体信息见上面的描述。
