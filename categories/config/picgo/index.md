# VScode PicGo插件配置


这里就是对于 Vscode 的 PicGo 插件进行配置

&lt;!--more--&gt;

## 上传配置

这里因为使用 vscode 来写 Markdown 语法，所以想着仿照之前 Typora &#43; PicGo 配置的剪切板粘贴上传图片功能。这里因为之前在 PicGo 应用中配置过，所以这次就直接获取参数然后输入即可。

![配置信息](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241031231806.png &#34;20241031231806&#34;)

这里的配置信息直接从软件中抄过来，或者随便看看阿里云图床的配置文章就可以知道大致该怎么填写了。之后这里 `Aliyun: Path` 选择之前自己创建的目录，可以通过 OSS -&gt; Bucket -&gt; 文件列表 来查看。另外选择 `Pic Bed: Current` 为 aliyun，这样上面的配置才可以生效。同时还设置了快捷键，对于使用截图软件，把图片放在剪切板上的情况，修改快捷键为 `ctrl &#43; alt &#43;w`，这样和普通粘贴分割开了，更加方便。

## 文件名设置

`Custom Output Format` 就是文件在 vscode 中的展现形式，这里针对 Fixit 主题中 [Image 的要求](https://fixit.lruihao.cn/zh-cn/documentation/content-management/shortcodes/extended/introduction/#image) 进行了修改。这样后面的 `title` 可以直接生成，然后我们需要修改的就是前面的 `alt` 了。 `layouts/partials/plugin/image.html` 文件在之前进行修改了，所以图片下面的图标显示的就只有 `alt` 了，只有鼠标放在图片上面和放大图片显示的才是 `title`。

![本地格式](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241031232237.png &#34;20241031232237&#34;)

`Custom Upload Format`就是上传文件的格式，随便设置即可，因为不会有太多的格式要求，这里就是默认的配置。
![上传格式](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241031232303.png &#34;20241031232303&#34;)

## 保存路径

这里有一个 `Data Path`，vscode 插件会把每次的提交形成 `json` 数据添加到这个文件中，所以可以设置存储路径，之后查看图片可以迅速找到相关信息。这里我也将路径设置为博客的仓库，使其作为附属信息上传到 github 上。
![路径配置](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241101101253.png &#34;20241101101253&#34;)

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/config/picgo/  

