---
layout: post
title:  "youtube-dl命令"
date:   2020-02-27 15:50:05 +0800
categories: blog
tags:   [mac]
---

## youtube-dl命令记录：

- macOS安装

`brew install youtube-dl`

- youtube-dl帮助

`youtube-dl --help`

- youtube-dl代理下载

`youtube-dl --proxy 127.0.0.1:8087 'https://youtu.be/5wvuWmMcWYI'`

- youtube-dl列出所有视频音频格式

`youtube-dl --proxy 127.0.0.1:8087 --list-formats 'https://youtu.be/5wvuWmMcWYI'`

- youtube-dl选择格式下载

`youtube-dl --proxy 127.0.0.1:8087 -f 22 'https://youtu.be/5wvuWmMcWYI'`

> 22是--list-formats列出的格式编号

- youtube-dl列出所有字幕语言

`youtube-dl --proxy 127.0.0.1:8087 --list-sub 'https://youtu.be/W60KhAapj7s'`

- youtube-dl列出所有字幕语言

`youtube-dl --proxy 127.0.0.1:8087 --list-sub 'https://youtu.be/W60KhAapj7s'`

- youtube-dl下载所有自动生成的字幕， 只下载字幕不下载视频

`youtube-dl --proxy 127.0.0.1:8087 --all-subs --skip-download --write-auto-sub 'https://youtu.be/5wvuWmMcWYI'`

- youtube-dl下载选择语言的字幕， 只下载字幕不下载视频

`youtube-dl --proxy 127.0.0.1:8087 --skip-download --write-auto-sub --sub-lang en,zh-Hans 'https://youtu.be/DBO3ZEfww8I'`


- 从一个文件中读取视频列表下载

`youtube-dl -a video_url.txt`

- 下载最优视频和音频

`youtube-dl -f bestvideo+bestaudio`

- youtube-dl下载选择语言的自动字幕和选择格式的视频, 格式22 （1080）

`youtube-dl --proxy 127.0.0.1:8087 --write-auto-sub --sub-lang en,zh-Hans -f 22 'https://youtu.be/Pe85BH9SiS8'`
