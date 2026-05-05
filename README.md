# 樱落之境

简体中文 | [English](./README_EN.md)

这是一个基于 [imsyy/home](https://github.com/imsyy/home) 二次定制的个人主页，用于展示个人站点、常用链接、音乐播放和移动端首页效果。

当前版本已经从原模板改成「樱落之境」主题，背景图换成了洛天依图集，并针对手机端和 PC 端分别做了适配。

## 预览

https://nyaovo.com/

原模板里的网盘、网址集、今日热榜和其它默认外部链接已移除。

## 主要改动

- 站点文案改为「樱落之境」主题。
- 主标题改为英文花字体 `Sakuya`，副标题保留中文「樱落之境」。
- 全站主要中文字体改为 `mao`。
- 增加本地英文花字体，用于主标题和签名。
- 背景图改为远程随机图片接口。
- PC 和手机端按设备请求不同背景，避免手机端裁切不合适。
- 增加右上角「切换背景」按钮，PC 和手机端都可以手动切换当前设备对应的背景图。
- 移除主页原来的时钟图标 Logo，保留生成的月亮樱花 Logo 作为浏览器标签页图标。
- 调整卡片透明度、模糊和文字阴影，让背景更清楚。
- 将一言移动到个签卡片里，手机端展开面板单独显示。
- 移动端展开面板隐藏时间卡片，避免遮挡标题。
- 优化手机端卡片宽度、文字居中、菜单按钮位置和小屏高度。
- 接入网易云歌单音乐播放器。
- 社交链接保留 GitHub、Bilibili、QQ 和微信。
- 页脚链接改为指向 `nyaovo.com`。
- 天气模块未配置时不作为主要展示项。

## 背景图说明

背景图由远程随机图片接口提供，代码位置在：

```text
src/components/Background.vue
```

当前按设备分为两类：

- PC 背景：`https://api.nyaovo.com/image/api/random?device=pc`
- 手机背景：`https://api.nyaovo.com/image/api/random?device=mobile`

## 配置位置

常用站点信息在 `.env`、`.env.production` 中配置：

```bash
VITE_SITE_NAME="樱落之境"
VITE_SITE_DISPLAY_NAME="Sakuya"
VITE_SITE_URL="nyaovo.com"
VITE_SITE_START="2026-01-09"
VITE_SITE_ICP="赣ICP备2026003508号-2"
```

网站卡片链接在：

```text
src/assets/siteLinks.json
```

社交链接在：

```text
src/assets/socialLinks.json
```

音乐播放器配置在 `.env` 中：

```bash
VITE_SONG_API="https://api.injahow.cn/meting/"
VITE_SONG_SERVER="netease"
VITE_SONG_TYPE="playlist"
VITE_SONG_ID="17935143531"
```

## 本地运行

需要先安装 Node.js 和 pnpm。

```bash
pnpm install
pnpm dev
```

本地预览地址通常是：

```text
http://localhost:3000/
```

## 构建

```bash
pnpm build
```

构建后的静态文件会生成在 `dist` 目录中。

## Docker 部署

Docker 镜像地址：`ghcr.io/charyeahowo/home`

```bash
docker build -t home .
docker run -p 12445:12445 -d home
```

当前线上版本部署在 Docker 容器中，更新时将 `dist` 覆盖到容器内的 `/app/dist`。

## 技术栈

- Vue 3
- Vite
- Pinia
- Element Plus
- IconPark
- xicons
- APlayer / Meting
## 原项目

本项目基于 [imsyy/home](https://github.com/imsyy/home) 修改而来。感谢原作者提供的开源主页模板。

如果需要原版功能、部署说明或许可证信息，请参考原仓库。

---

Code:Codex-GPT-5.5
