---
layout: post
title: "WebistranoでRailsアプリのデプロイを自動化する"
description: ""
category: 
tags: [ruby, rails]
---
{% include JB/setup %}

普段、Webアプリケーションの開発・導入・保守など自社サービスを提供するうえで営業活動以外のことをやっているチームで働いています。
BtoCのサービスでなく、BtoBであり、導入されるお客さんによってサービスを動かすサーバーを指定のLANに置いてあったり、こちらで用意したサーバーであったりと
環境も色々です。
当然、お客様によって動いているソースコードのバージョンにも差があります。

もともとソースコードはgitで管理しており、本番環境へのデプロイはcapistranoでやっていたのでデプロイ作業を自動化するという事について
は実現していました。
ですが、上記の背景のなかで管理が面倒になっていたため解決策を模索していました。

## 解決した課題
1.デプロイの履歴が残らない

1.デプロイの設定がバージョン管理されている
 
#Webistranoとは
CapistranoをWebで使えるようにしたもの。ブラウザでデプロイ情報を設定して、ブラウザ上でデプロイ作業が行い得る。

#capistranoと比べた利点

## コードにデプロイ情報を混ぜなくていい
一つのアプリケーションのデプロイ先が大量にある場合、capistranoのmultistage
* ブランチでデプロイ情報が異なる（どのブランチが正かわからない）件を解消出来る
* デプロイの記録が取れる



#デプロイ可能までの設定
まずはローカルで動かすことを考える。
私はMac OS Xで確認しました。
（個人用で使うWebistranoをUbuntu12.04で動かしているのでUbuntu12.04でも可能だと思います。）

本家のWebistranoは2年以上更新がないらしく、Railsも2系のままとのことだけど、
[git paperboy-all](https://github.com/paperboy-all/webistrano)
にRails3.2系で動くようにしたものがある。(このバージョンが出来た経緯は[このブログ](http://waka.hatenablog.com/entry/2012/11/22/003709)を参照)
ありがたく使わせてもらおう。

## 初期起動
{% highlight bash %}
$ cp config/webistrano_config.rb.sample to config/webistrano_config.rb
$ cp config/database.yml.sample to config/database.ym
$ bundle update
$ 
{% endhighlight %}
    
    
## プロジェクト, ステージ、ホスト、レシピ登録

## 問題点への対応
### bundle installされない
{% highlight ruby %}
require 'bundle/capistrano'
{% endhighlight %}
### rvmに対応してない
### assets:precompileされない
### 鍵認証

# 不満点
* rvm対応のように、webistrano自身のコードに手を入れた(Gemfile)
* gitへのアクセスや、デプロイ先への接続が公開鍵認証方式だとブラウザからだけじゃ設定出来ない?
* もうちょっとWebアプリ上だけで設定出来るようになって欲しい
* webistrano自身はcapistranoでデプロイされることを考慮してないように見える
    * .chef-nor
