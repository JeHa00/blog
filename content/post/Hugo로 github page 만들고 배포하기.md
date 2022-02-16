---
title: "Window에서 Hugo로 Github page 만들고 배포하기"
date: 2022-02-16T21:49:13+09:00
draft: true
summary: SSG의 한 종류 'Hugo'와 'git remote, git submodule'로 Github page를 만든다. 그 후, 컨텐츠 생성과 업로드를 한다. 마지막으로 utterances로 댓글 기능 추가, deploy.sh를 사용하여 업로드하는 방법을 다룬다.
tags: ["hugo", "github", "blog", "submodule"]
categories: ["개발-dev"]
---

## Intro

- **We'll be using `yml/yaml` format for all examples down below, I recommend using `yml` over `toml` as it is easier to read.**
- You can find any [YML to TOML](https://www.google.com/search?q=yml+to+toml) converters if necessary.

---
