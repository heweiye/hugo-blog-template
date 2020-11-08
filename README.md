[![hugo](https://img.shields.io/badge/powered%20by-hugo-orange)](https://gohugo.io/)
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/yourname/hugo-blog/Deploy)](https://github.com/yourname/hugo-blog/actions)
[![Website](https://img.shields.io/website?url=https%3A%2F%2Fyourname.github.io)](https://yourname.github.io)

这里是 yourname 的博客的源码，访问地址：<https://yourname.github.io>

## LICENSE

All components in the `content` folder, `static/post_doc` folder, `static/post_img` folder are distributed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), other components except for third party submodules are distributed under [MIT License](LICENSE).

---

使用方式：

```bash
git clone --recurse-submodules https://github.com/ouuan/hugo-blog-template hugo-blog
cd hugo-blog
hugo server
```

使用 VS Code 或 grep 命令等方式，将所有含有 `yourname` 的地方进行修改。（并不是所有 `yourname` 都是用户名，只是为了方便搜索全用的 `yourname` 。）

参照 [GitHub Actions 的文档](https://docs.github.com/en/free-pro-team@latest/actions/reference) 按需修改 [Workflows](.github/workflows)，尤其是 Deploy 方式，secrets 等需要修改。

## 记录


## Motivation

这次搬迁最直接的原因，是我把 hexo 博客从 Windows 搬到 Linux 失败了..

之前魔改太多，主题里各种文件都改过，甚至 node_modules 里面都被我改过很多地方，之前因此都放弃升级 hexo 和 NexT 主题，而最近开始主用 Manjaro，于是博客必须得搬到 Linux 上，但无论是简单复制过来还是重新使用 npm 安装包都未能如愿。

其实说不定还有很多解决方法，但我之前就听说过 hugo，于是就决定干脆搬过来好了。

但为什么是搬到 hugo，而不是升级 hexo 呢？

从 hugo 本身来说，它最显著的优点，就是快：

1. 安装快。这在本地安装时倒影响不大，~~而且如果你要修改主题还得用 yarn 安装包，得装上几分钟~~（只在某些主题需要，如果无需压缩/使用 hugo 而非 node 进行压缩就不需要）。最主要的是，安装快使得 CI 快，使用 CI 发布 hugo 博客就会非常容易且快捷。
2. 构建快。hugo 比起 hexo 的构建速度不是快了一点点，hexo 需要几十秒才能构建完的博客，hugo 只需不到一秒。这点更加重要，它不仅加速了 CI，而且本地构建几乎没有延时，加上 server 预览使用 websocket，直接分屏当预览写博客体验良好。

但弊端也同样明显：没有插件，导致很多功能得自己写，或者复制别人的代码。只不过对于 hexo 都能改到 node_modules 里面的魔改玩家来说，这都不算什么（

只不过我一直用的是 2018 年 12 月那时的 hexo 和 NexT，这一年来 hexo 可能也有很多改进，所以我的比较不一定准确。

## Process

hugo 的配置步骤就不说了，网上很多教程，而且这个博客是 [开源](https://github.com/ouuan/hugo-blog) 的，可以自己看。

本博客使用 GitHub Actions 进行自动发布，详见 [配置文件](https://github.com/ouuan/hugo-blog/blob/master/.github/workflows/deploy.yml)。`PERSONAL_TOKEN` 需要在 repo 的 settings 里设置，但如果源码和博客在同一个 repo 里就可以直接用 `GITHUB_TOKEN` 了。

从 hexo 搬到 hugo 可以参考这篇博客：[Hugo 与 Hexo 的异同 | reuixiy](https://io-oi.me/tech/hugo-vs-hexo/) 。

我自己使用的脚本（fork 然后稍微改了一点）：[ouuan/hexo2hugo](https://github.com/ouuan/hexo2hugo) 。

hugo 还支持 [aliases](https://gohugo.io/content-management/urls/#aliases)，所以链接改动可以轻松处理。

如果对我博客的配置感兴趣，还可以参考 [config.toml](https://github.com/ouuan/hugo-blog/commits/master/config.toml) 和 [even 主题](https://github.com/ouuan/hugo-theme-even/commits/) 的历史记录。

几个值得提醒的地方：（大约是我认为“已经完全搞好了！”之后遇到的问题，也就是你按网上的教程搞完之后仍然容易遇到的问题）

### 站内搜索
   
可以参考 [1d0901f](https://github.com/ouuan/hugo-blog/commit/1d0901fca6725480450581bb7bec28e0b2afc4d6)，如果主题不是 even 的话我这个应该就不太能直接用了，得改一下 `layout/_default/search.html` 还有 `static/js/search.js` 中的 `render` 函数。

### 代码高亮
   
推荐使用 highlight.js 而不是 chroma。even 主题把 highlight.js 的 CSS 放到了自己的 `_code.scss` 里，所以光是加上一个 highlight.js 的 CSS 是不够的，还得在 `_code.scss` 里把 hljs 相关的删掉，如果是 dark theme 还得修改背景颜色。

### 代码复制按钮
   
可以参考 [8347acf](https://github.com/ouuan/hugo-theme-even/commit/8347acfe30f386f00dd81c843a879755377cccf5)。全靠搜索引擎学来的 CSS 果然不够..就这么点东西我搞了四个小时，主要是滚动条、字体大小、行高、padding 之类杂七杂八的问题。

### GitInfo with Unicode
   
如果路径有中文，想用 GitInfo 的话就得 `git config --global core.quotePath false`，参见 [gohugoio/hugo#3071](https://github.com/gohugoio/hugo/issues/3071) 。

### Table Of Content with h1
   
默认情况下右侧目录是从 `<h2>` 开始的，如果你的文章中含有 `<h1>`（也就是 Markdown 中的单个 `#`），目录就会挂掉。在文章中使用 `<h1>` 是 **不被推荐** 的，但我自己一年前写的一些博客里有 `<h1>`，虽然批量修改也不难，但还有一种解决方案：在 `config.toml` 中加入：

```toml
[markup.tableOfContents]
  startLevel = 1
  endLevel = 4
```

这样从 `<h1>` 到 `<h4>` 都会在目录中显示。

### HTML in links

例如：`[~~qwq~~](/post/from-hexo-to-hugo)`, `[![favicon](/favicon.ico)](/favicon.ico)`。

默认情况下会渲染成这样：

[\<del\>qwq\</del\>](/post/from-hexo-to-hugo)

[\<img src="/favicon.ico" alt="favicon"\>](/favicon.ico)

可以将 `/layouts/_default/_markup/render-link.html` 设成这样：（这个板子还包含了外链打开新标签页，不被渲染成 HTML 源码的关键在于 `safeHTML`）

```html
<a href="{{ .Destination | safeURL }}"{{ with .Title}} title="{{ . }}"{{ end }}{{ if strings.HasPrefix .Destination "http" }} target="_blank"{{ end }}>{{ safeHTML .Text }}</a>
```

然后就好了：

[<del>qwq</del>](/post/from-hexo-to-hugo)

[![favicon](/favicon.ico)](/favicon.ico)

### Baidu push

大家知道，百度收录个人博客是很慢的。

于是我用了 ~~黑魔法~~ GitHub Actions 每天自动推送，详见 [配置文件](https://github.com/ouuan/hugo-blog/blob/master/.github/workflows/baidu-push.yml)。（需要先在 [百度搜索资源平台](https://ziyuan.baidu.com/) 上进行一些设置，TOKEN 可以在“链接提交”页面找到。）

### Comments loading spinner

见 [commit](https://github.com/ouuan/hugo-theme-even/commit/57d931297c7aa885cea90f5b5b5d6093f95b8a86)。

## Issues

因为是用脚本批量改的，改完之后也没有一篇篇去检查，难免会有改错的地方以及一些死链，可以直接在评论区指出，多谢大家了。

如果有原博客链接没有 redirect 而是 404 了，也请务必告诉我。

如果需要原 hexo 博客的，可以看 [ouuan.github.io/hexo-archive](https://github.com/ouuan/ouuan.github.io/tree/hexo-archive) 。

