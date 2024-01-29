# 克隆之后

clone之后，在文件中通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。

可以参考
https://www.zhihu.com/question/21193762


# 查看远程分支

```shell
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/Release
  remotes/origin/master
```

# 切换远程分支
切换到 origin/Release分支，并在本地新建分支 hexo
```shell
$ git checkout -b hexo origin/hexo
```
$ git checkout -b hexo(这个是本地新建分支hexo) origin/hexo(这个是远端已经存在的分支)

# hexo常用命令
1. 生成静态文件
```shell
hexo g
```
2. 本地启动
> 由于hexo很久不更新，`hexo g`生成的html文件都是空的，需要很老的nodejs才行，那就不更新到github pages上了，就用`hexo s`本地看吧
```shell
hexo s
```
3. 部署到github pages
```shell
hexo clean
hexo deploy
```
4. 清除已经生成的静态文件
```shell
hexo clean
```