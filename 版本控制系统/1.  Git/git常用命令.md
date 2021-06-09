常用命令:

### 1. 本地git仓库关联多个远程仓库

通常情况下，一个本地Git仓库对应一个远程仓库，每次`pull`和`push`仅涉及本地仓库和该远程仓库的同步；然而，在一些情况下，一个本地仓库需要同时关联多个远程仓库，比如：同时将一个项目发布在[Github](https://link.zhihu.com/?target=https%3A//github.com/)和[Coding](https://link.zhihu.com/?target=https%3A//coding.net/)上，以兼顾国内外的访客。

```bash
$ git remote -v
origin  git@github.com:keithnull/keithnull.github.io.git (fetch)
origin  git@github.com:keithnull/keithnull.github.io.git (push)
```

用`git remote add <name> <url>`添加一个远程仓库，其中`name`可以任意指定（对应上面的`origin`部分），比如：

```bash
$ git remote add coding.net git@git.coding.net:KeithNull/keithnull.github.io.git
```

再次查看本地仓库所关联的远程仓库，可以发现成功关联了两个远程仓库：

```bash
$ git remote -v
coding.net      git@git.coding.net:KeithNull/keithnull.github.io.git (fetch)
coding.net      git@git.coding.net:KeithNull/keithnull.github.io.git (push)
origin  git@github.com:keithnull/keithnull.github.io.git (fetch)
origin  git@github.com:keithnull/keithnull.github.io.git (push)
```

此后，若需进行`push`操作，则需要指定目标仓库，`git push <repo> <branch>`，对这两个远程仓库分别操作：

```bash
$ git push origin master
$ git push coding.net master
```

同理，`pull`操作也需要指定从哪个远程仓库拉取，`git pull <repo> <branch>`，从这两个仓库中选择其一:

```bash
$ git pull origin master
$ git pull coding.net master
```

[https://zhuanlan.zhihu.com/p/82388563]: 本地Git仓库关联多个远程仓库的两种方法 - 知乎 (zhihu.com)(https://zhuanlan.zhihu.com/p/82388563)

### 2. 移除本地关联远程仓库

```bash
1. git remote rm origin // 移除本地关联 
```

```bash
2. git remote add origin git@github.com/example.git // 添加线上仓库
```

 

```bash
3. git push -u origin master // 注意：更改后，第一次上传需要指定 origin
```

