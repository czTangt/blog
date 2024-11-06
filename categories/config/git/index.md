# Some About Git


一些关于 git 的操作

&lt;!--more--&gt; 

![git流程](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241101105334.png &#34;20241101105334&#34;)

## 相关命令
```bash
# 本地仓库初始化
git init 

# 将文件提交到暂存区
git add [file name] 
git add .  # 添加全部修改和新增文件

# 将文件提交到本地仓库
git commit [file name] 
git commit -m &#34;commit information&#34; [file name]
git commit -m &#34;commit inforamtion&#34; 	# 提交全部到本地仓库

# 添加远程仓库，这里是因为存在多个 git 用户所进行的配置，正常为 git@github:czTangt/blog.git
git remote add origin git@github_czTangt:czTangt/blog.git

# 提交到远程仓库
git push -u origin [branch name]
git push -f # 强制提交

# 分支操作
git checkout [branch name] # 切换分支
git branch -a # 查看所有分支
git checkout -b local_dev origin/remote_dev # 创建本地分支并于远程分支连接
git push origin --delete remote_dev # 删除远程分支
```

## 文件修改

对于 git 而言，`git add .` 会将所有修改和新增的文件信息提交到暂存区，之后使用 `git commit -m &#34;xxx&#34;` 会将暂存区的文件信息提交到本地仓库。但是这种方法是对于新增和修改的文件而言，对于删除的文件信息不会进行更新，所以可以采用两种方案：

1. 使用 `git rm xxx` 一个一个手动删除文件（`rm` 命令没有办法使用 `git rm .` 一起更新全部的文件删除信息），之后这些文件删除信息就提交到暂存区了，后续就可以继续使用 `git commit -m &#34;xxx&#34;` 来将代码提交到本地仓库
2. 使用 `git commit -am` 命令，该命令会在提交到本地仓库时，先更新修改和删除的文件信息到暂存区（注意它不会提交新增加的文件信息）。所以加了 `-a` 在 `commit` 的时候，可以帮助省一步 `git add`，但是也只是对修改和删除文件有效，新文件还是要 `git add`，不然就是 `untracked` 状态。

综上所述：`git add` 和 `git rm` 都是等价的操作，前者添加修改和新增文件信息，后者添加删除文件信息，他们都是将文件信息提交到暂存区，之后使用 `git commit -m &#34;xxx&#34;` 来将暂存区的文件信息提交到本地仓库，最后使用 `git push -u origin main` 来提交本地仓库代码到远程仓库的 main 分支。

## 远程仓库到自己仓库
拉取别人的仓库到自己仓库，主要应对github中没有对应仓库的情况[繁琐指南](https://blog.csdn.net/wangyangzhizunwudi/article/details/127330070)，存在对应仓库，直接进行 fork，然后在本地添加自己的远程。简单的操作如下：

```bash
 git branch -r | grep -v &#39;\-&gt;&#39; | while read remote; do git branch --track &#34;${remote#origin/}&#34; &#34;$remote&#34;;done   # 获取所有远程分支到本地
 git fetch --all				 	 # 获取该项目远程库的所有分支及其内容
 git fetch --tags					 # 获取该项目远程库的标签(没标签就不必了)
 git remote rename origin old-origin # 将原来的origin重命名一下
 git remote add origin git@172.28.3.77:xs-soc/test-code.git  # 指定需要迁移到新的目标地址(自己的仓库)
 git push origin --all				  # 推送所有分支及其内容
 git push --tags					  # 推送所有标签及其内容
 git remote rm origin                 # 删除当前远程库
 git branch -M main					  # 重命名主要分支仓库	
 git push -u origin main			  # 推送到指定分支
```

## Git 加速
git失败的原因绝大多数都是网络问题，所以挂代理是最为推荐的选择。以下是起作用的一些方法

通用方法，更换 git 的代理为 443
- [SSH：连接到主机github.com端口22：连接时间超时](https://www.cnblogs.com/tsalita/p/16181711.html)

但是对于 wsl，直接使用最新 wsl2 共用主机的代理即可（**最为推荐**），不嫌麻烦可以给配置个代理
- [配置wsl镜像](https://github.com/microsoft/WSL/issues/10753)
- [Windows10系统下配置WSL2自动走Clash代理](https://wph.im/199.html)，之后clash打开allow lan模式即可
- [WSL2内使用Windows的v2ray代理 | Nafx&#39;s Blog](https://nafx.top/archives/88ca14b9#5-etc-resolv-conf重启丢失)，这是v2的模式，首先最后面设置，然后前面配置bashrc

有时候最后的方法会起点作用
- [git clone失败解决方案](https://blog.csdn.net/m0_38068229/article/details/108205928)



---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/config/git/  

