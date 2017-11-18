# 克隆之后

clone之后，在文件中通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。

可以参考
https://www.zhihu.com/question/21193762

# 查看远程分支
$ git branch -a
'''
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/Release
  remotes/origin/master
'''

# 切换远程分支
切换到 origin/Release分支，并在本地新建分支 hexo
$ git checkout -b hexo origin/hexo
Branch myRelease set up to track remote branch Release from origin.
Switched to a new branch 'myRelease'
