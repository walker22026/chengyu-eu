# chengyu.eu

基于 [Hugo](https://gohugo.io/) + [Blowfish](https://blowfish.page/) 主题搭建的占位/示例站点。

## 本地预览

```bash
hugo server -D
```

需要本机安装 Hugo（建议 extended 版本，>= 0.165.0）：`brew install hugo`

## 目录结构

- `content/` — 页面与文章内容。新增一篇成语故事：在 `content/posts/` 下新建一个文件夹（如 `content/posts/一个新成语/index.md`），参考已有文章的 front matter 格式。
- `config/_default/` — 站点配置（标题、菜单、主题参数等）。
- `themes/blowfish/` — Blowfish 主题文件。这里是**直接拷贝**的主题代码（不是 git submodule），因为本机的沙盒环境没有构建时的网络访问权限。以后升级主题版本，需要手动从 [Blowfish 仓库](https://github.com/nunocoracao/blowfish) 拷贝新版本文件覆盖此目录（保留本文件顶部的 `config/`、`content/` 不受影响）。

## 部署到 Cloudflare Pages

1. 把这个项目推送到一个 GitHub 仓库。
2. 在 Cloudflare Pages 新建项目，连接该仓库，构建设置：
   - **构建命令**：`hugo --gc --minify`
   - **构建输出目录**：`public`
   - **环境变量**：`HUGO_VERSION` = `0.165.0`
3. 部署成功后，在 Pages 项目的「自定义域」中添加 `chengyu.eu`（和可选的 `www.chengyu.eu`），因为域名已经在 Cloudflare 账号内，会自动完成 DNS 解析。
