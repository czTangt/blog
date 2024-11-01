# FixIt配置


自己对于 FixIt 主题的一些配置

&lt;!--more--&gt; 

## 大致流程

这里我使用的就是[Git 子模块](https://fixit.lruihao.cn/zh-cn/documentation/installation/#git-submodule)的安装方式，更详细的流程参照这篇[快速上手](https://fixit.lruihao.cn/zh-cn/documentation/getting-started/quick-start/)。在必要的配置后，博客就可以进行本地浏览了。之后我使用 Github Action 的方式，将本地博客的所有文件上传到一个私密仓库，之后创建 Github Action 将通过 `hugo --gc --minify` 命令构建的 `public` 目录上传到另一个公开仓库 blog 中，这样就可以设置静态网站进行博客访问了，具体可以参考[Huogo 主题配置](https://letere-gzj.github.io/hugo-stack/p/hugo/custom-blog/#32-github-action%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2)。这样博客基本上就好了，可以开始额外的操作了。

## 图片自适应

我使用的图片都是存储在图床中的，使用的就是 Markdown 的经典语法。但是它在 FixIt 的渲染中会左对齐，有时还会在图片后面跟随文字，感官很不好，所以思考怎么进行改进。在参考[FixIt主题使用lightgallery自适应显示图片](https://zhuanlan.zhihu.com/p/640895881)之后，自己进行了调整，达到了现在的效果。

这里主要参照上述参考文章的两种尝试，采用 `lightgallery` 来呈现图片。我参照 [FixIt配置篇](https://fixit.lruihao.cn/zh-cn/documentation/getting-started/configuration/#page) 将 lightgallery 设置为 `&#34;force&#34;` 但是如果没有 `alt` 和 `title` 属性，我的图片不会按照画廊形式呈现，所以我只能另辟蹊径。因为我使用 PicGo 进行图床配置，刚好它支持修改本地输出图片链接的格式，因此我修改[PicGo的配置](https://cztangt.github.io/blog/categories/config/picgo/#%e6%96%87%e4%bb%b6%e5%90%8d%e8%ae%be%e7%bd%ae)，把 `Custom Output Format` 配置为`![${uploadedName}](${url} &#34;${uploadedName}&#34;)`。这样它会自己填充 `alt` 和 `title`，我只需要按照自身需求修改 `alt` 属性即可。

另外点击图片之后会显示 `alt` 和 `title` 两个信息，所以打开 F12 进行观察，发现第一行来自标签 `&lt;h2&gt;`，也是 alt 属性，第二行来自标签 `&lt;p&gt;`，是 title 属性。这部分都是在 `themes/layouts/partials/plugin/image.html` 中，所以直接修改主题文件，把这里的 `&lt;p&gt;` 删除，然后 `&lt;h2&gt;` 移动到中间，这样渲染的时候只会在下面出现 `alt` 属性了，放大图片和鼠标移动到图片上才会看到 `title` 属性。

![修改配置文件](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241101122906.png &#34;20241101122906&#34;)




---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/config/fixit/  

