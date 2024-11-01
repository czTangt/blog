# Some About Git


一些关于 git 的操作

&lt;!--more--&gt; 

![git流程](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241101105334.png &#34;20241101105334&#34;)

## 文件修改

对于 git 而言，`git add .` 会将所有修改和新增的文件信息提交到暂存区，之后使用 `git commit -m &#34;xxx&#34;` 会将暂存区的文件信息提交到本地仓库。但是这种方法是对于新增和修改的文件而言，对于删除的文件信息不会进行更新，所以可以采用两种方案：

1. 使用 `git rm xxx` 一个一个手动删除文件（`rm` 命令没有办法使用 `git rm .` 一起更新全部的文件删除信息），之后这些文件删除信息就提交到暂存区了，后续就可以继续使用 `git commit -m &#34;xxx&#34;` 来将代码提交到本地仓库
2. 使用 `git commit -am` 命令，该命令会在提交到本地仓库时，先更新修改和删除的文件信息到暂存区（注意它不会提交新增加的文件信息）。所以加了 `-a` 在 `commit` 的时候，可以帮助省一步 `git add`，但是也只是对修改和删除文件有效，新文件还是要 `git add`，不然就是 `untracked` 状态。

综上所述：`git add` 和 `git rm` 都是等价的操作，前者添加修改和新增文件信息，后者添加删除文件信息，他们都是将文件信息提交到暂存区，之后使用 `git commit -m &#34;xxx&#34;` 来将暂存区的文件信息提交到本地仓库，最后使用 `git push -u origin main` 来提交本地仓库代码到远程仓库的 main 分支。

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/config/git/  

