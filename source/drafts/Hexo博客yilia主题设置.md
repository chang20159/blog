## 添加subnav的图标

在目录文件：source-src/css/fonts.scss中找到想要的字体图标：

```css
.icon-share:before { content: "\e618"; }
```

在文件source-src/css/social.scss中添加：

```css
a.share {
    background: #086ef6;
    border:1px solid #086ef6;
    &:hover {
        border:1px solid #086ef6;
    }
}
```
背景色自己设置

然后在yilia主题根目录下执行

```
npm install
npm run dist
```

最后再执行:

```
hexo generate
hexo server
```

查看效果
