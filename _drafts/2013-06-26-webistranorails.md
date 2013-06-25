---
layout: post
title: "WebistranoでRailsアプリのデプロイを自動化する"
description: ""
category: 
tags: [ruby, rails]
---
{% include JB/setup %}

#capistranoと比べた利点

* コードにデプロイ情報を混ぜなくていい
    * うちは、複数のいろんなサーバーに入れるので特にたすかる
    * ブランチでデプロイ情報が異なる（どのブランチが正かわからない）
  
