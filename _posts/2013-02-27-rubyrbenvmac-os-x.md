---
layout: post
title: "自分でコンパイルしたRubyをrbenvで管理する(Mac OS X)"
description: ""
category: 
tags: [ruby]
---
{% include JB/setup %}

通常はruby-buildもセットで使えば楽でいいのだけど、例えばruby2.0出た！入れてみよう！あ、ruby-buildがまだだ・・・。という理由ですぐに試せないという人が居たとすると悲しいので、自分でやってみた手順をメモしました。

Homebrewは導入済みでかつ、rbenvはHomebrewで導入済みとします。

{% highlight bash  %}
$ brew install openssl
$ brew link openssl --force
$ wget ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p0.tar.gz
$ tar xzvf ruby-2.0.0-p0.tar.gz
$ cd ruby-2.0.0-p0
$ ./configure --prefix=/Users/hoge/.rbenv/versions/2.0.0-p0 --with-openssl-dir=/usr/local
$ make
$ make install
{% endhighlight %}

※hoge にはユーザー名を

これで $HOME/.rbenv/versions 以下にruby2.0がインストールされたはず。

{% highlight bash  %}
$ rbenv versions
  system
  1.9.2-p290
  1.9.3-p194
  1.9.3-p327
* 1.9.3-p374 (set by /Users/hoge/.rbenv/version)
  2.0.0-p0
  2.0.0-preview1
  2.0.0-rc2

$ rbenv global 2.0.0-p0
$ rbenv rehash
$ ruby -v
ruby 2.0.0p0 (2013-02-24) [x86_64-darwin12.2.1]
{% endhighlight %}

これで自分でビルドしたRubyがrbenvで管理出来る状態になったと思います。

あとは、[私がRubyをインストールしたとき確認すること - 絶対R領域](http://d.hatena.ne.jp/tech-kazuhisa/20130116/1358322666)を参考に、ハマリ易そうなところが大丈夫か見ておきます。

{% highlight bash  %}
$ irb
irb(main):001:0> require 'openssl'
true
irb(main):002:0> require 'zlib'
true
irb(main):003:0> require 'psych'
true
{% endhighlight %}

良さそうですね！