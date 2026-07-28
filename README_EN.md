# Sakura Realm

English | [简体中文](./README.md)

This is a customized personal homepage based on [imsyy/home](https://github.com/imsyy/home). It is used as a personal start page with site links, social links, music playback, responsive backgrounds, and mobile-friendly layout.

This version has been redesigned for the "Sakura Realm" theme. Backgrounds are loaded from local image collections, with landscape images for desktop and portrait images for mobile devices.

## Preview

https://nyaovo.com/

The original template links such as cloud drive, link collection, hot list, and other default external links have been removed.

## Custom Changes

- Reworked the site copy and identity for the "Sakura Realm" theme.
- Changed the main title to the script-style English text `Sakuya`.
- Kept the Chinese subtitle `樱落之境` under the title.
- Changed the main Chinese font to `mao`.
- Added a local script font for the title and signature text.
- Added local Luo Tianyi background collections.
- Use landscape images on desktop and portrait images on mobile for better cropping.
- Added a top-right background switch button for both desktop and mobile.
- Removed the old clock-style homepage logo from the main view.
- Kept the generated moon-and-sakura logo as the browser favicon.
- Adjusted card opacity, blur, and text shadow to keep the background more visible.
- Moved Hitokoto text into the signature card on desktop.
- Added a separate mobile Hitokoto card inside the expanded mobile panel.
- Hid the mobile time card in the expanded panel to avoid blocking the title.
- Improved mobile card width, text centering, menu position, and small-screen layout.
- Added a NetEase Cloud Music playlist player.
- Kept social links for GitHub, Bilibili, QQ, and WeChat.
- Updated the footer link to point to `nyaovo.com`.
- Weather is no longer a core display item when no API key is configured.

## Background Images

Backgrounds are loaded from local image collections. The logic is in:

```text
src/components/Background.vue
```

They are split by composition:

- Desktop landscape images: `public/images/background/pc`
- Mobile portrait images: `public/images/background/mobile`

## Configuration

Common site information is configured in `.env` and `.env.production`:

```bash
VITE_SITE_NAME="樱落之境"
VITE_SITE_DISPLAY_NAME="Sakuya"
VITE_SITE_URL="nyaovo.com"
VITE_SITE_START="2026-01-09"
VITE_SITE_ICP="赣ICP备2026003508号-2"
```

Site cards are configured in:

```text
src/assets/siteLinks.json
```

Social links are configured in:

```text
src/assets/socialLinks.json
```

Music player settings are configured in `.env`:

```bash
VITE_SONG_API="https://api.injahow.cn/meting/"
VITE_SONG_SERVER="netease"
VITE_SONG_TYPE="playlist"
VITE_SONG_ID="17935143531"
```

## Local Development

Install Node.js and pnpm first.

```bash
pnpm install
pnpm dev
```

The local preview URL is usually:

```text
http://localhost:3000/
```

## Build

```bash
pnpm build
```

The production files will be generated in the `dist` directory.

## Docker Deployment

Docker image: `ghcr.io/charyeahowo/home:latest`

```bash
docker build -t home .
docker run -p 12445:12445 -d home
```

The current production site is deployed in a Docker container. During updates, the generated `dist` directory is copied into `/app/dist` inside the container.

## Tech Stack

- Vue 3
- Vite
- Pinia
- Element Plus
- IconPark
- xicons
- APlayer / Meting

## Original Project

This project is customized from [imsyy/home](https://github.com/imsyy/home). Thanks to the original author for the open-source homepage template.

For the original features, deployment guide, and license information, please refer to the original repository.

---
Code:Codex-GPT-5.5
