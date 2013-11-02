---
layout: post
title: "FizzBuzzで始めるRuby"
description: ""
category:
tags: [ruby]
---
{% include JB/setup %}

他のプログラミング言語は分かるけどRubyは初心者だという人や、Rubyの本を読んで一通りプログラムは書いてみたけどRubyっぽく書くにはどうすればいいのか悩んでいる人、Ruby特有の機能を知りたいという人向けに、FizzBuzzを書きながらRubyの特徴を覚えていくことを目的とした記事です。

# 第15回　岡山Ruby, Ruby on Rails勉強会
本記事は[「第15回 岡山Ruby, Ruby on Rails勉強会」](http://local.aguuu.com/events/26267)の発表資料として書きました。
[岡山Ruby会議02](http://regional.rubykaigi.org/okayama02)のLTで発表したものを加筆したものです。
ネタの使い回しです。

# FizzBuzzとは

    Fizz Buzz（フィズ・バズ、Bizz Buzz や Buzz とも呼ばれる）は英語圏で長距離ドライブ中や飲み会の時に行われる言葉遊びである。

[Fizz Buzz - Wikipedia](http://ja.wikipedia.org/wiki/Fizz_Buzz)

そうだったのか～。

プログラミングの文脈でいう時には、3の倍数ではFizzを出力して、5の倍数ではFizzBuzzを出力して、
両方の倍数(15の倍数)の場合はFizzBuzzと出力して、それ以外は数字をそのまま出力する処理のことを言います。
本記事では1〜100の範囲で出力するものを扱います。

# 普通のFizzBuzz
とりあえずプログラミングを覚えた人が書くであろうFizzBuzz。

{% highlight ruby %}
for num in 1..100 do
  if num % 15 == 0
    puts "FizzBuzz"
  elsif num % 5 == 0
    puts "Buzz"
  elsif num % 3 == 0
    puts "Fizz"
  else
    puts num
  end
end
{% endhighlight %}

for文で1から100までのループを回しながら、割切れるかどうかをチェックして文字又は数字を出力しています。
処理としてはちゃんと動いてますが、ちょっと書き方がRubyっぽくないですね。

#### 1..100 という書き方について
上記コードのfor文に1..100という表記があります。これは文法ではなくRubyのオブジェクトの表現です。この書き方だと1〜100の範囲を表すRangeクラスのオブジェクトが生成されます。

それと他の言語にもよくある配列ですが、Rubyでは \[1,5,9,100\] の用に書くと配列になります。配列はRubyではArrayクラスのオブジェクトです。irbコマンドでちょっと確認してみましょう。

{% highlight ruby %}
2.0.0-p247 :012 >   1..100
 => 1..100
2.0.0-p247 :013 > (1..100).class
 => Range
2.0.0-p247 :014 > [1,5,9,100]
 => [1, 5, 9, 100]
2.0.0-p247 :015 > [1,5,9,100].class
 => Array
2.0.0-p247 :016 >
{% endhighlight %}

# ちょっとRubyっぽく Range#each を使う
{% highlight ruby %}
(1..100).each do |num|
  if num % 15 == 0
    puts "FizzBuzz"
  elsif num % 5 == 0
    puts "Buzz"
  elsif num % 3 == 0
    puts "Fizz"
  else
    puts num
  end
end
{% endhighlight %}

ArrayやRangeの様に複数の要素を持っているオブジェクトをループさせながら処理するときはeachを使うのがRubyっぽいです。
「なんでfor文が駄目なの?」とか、「じゃあRubyにfor文要らないじゃん、なんであるの？」とか言っては駄目です。


# もうちょっとRubyっぽくシンボルを使う
{% highlight ruby %}
(1..100).each do |num|
  if num % 15 == 0
    puts :FizzBuzz
  elsif num % 5 == 0
    puts :Buzz
  elsif num % 3 == 0
    puts :Fizz
  else
    puts num
  end
end
{% endhighlight %}

FizzBuzzなどの文字列が、シンボルに変わっています。
シンボルとは文字列にとても似ていますが、文字列ではありません。
使い所としては、文字列というデータそのものが必要なのではなく、何かを識別する名前や記号として使いたいときに利用します。

よくあるのは、Hashのキーとしての使われ方です。

{% highlight ruby %}
h = {:name => "Masaya Konishi", :age => "32"}
puts h[:name]
{% endhighlight %}

"name"という文字列が重要なのではなく、データへアクセスするための名前として:nameを使っています。

そういう意味では、今回のFizzBuzzではFizzBuzzという文字を出力する仕様なので、この使い方はなんか違う気もしないでも無いですが・・・。

# メソッドにする
ここまでで、Rubyっぽくない所を直しましたが、まだまだ気に入らないところがあります。
if elsif elsif と連なっているのが汚いですし、ループとFizzBuzzの判定処理と画面出力の処理がごちゃ混ぜなのも嫌ですね。

ちょっとメソッドを作って、役割を分割させましょう。

{% highlight ruby %}
## FizzBuzzの判定をするメソッド
def fizzbuzz(num)
  return :FizzBuzz if num % 15 == 0
  return :Buzz if num % 5 == 0
  return :Fizz if num % 3 == 0

  num
end

## 判定メソッドを使い、判定後の値を持つArrayを作る
fizzbuzz_result = []
(1..100).each do |num|
  fizzbuzz_result << fizzbuzz(num)
end

## 判定後のArrayを画面に出力する
fizzbuzz_result.each do |num|
  puts num
end
{% endhighlight %}

このFizzBuzzの処理は大きく3つの部分にわかれます。
- 数値からFizzBuzz, Fizz, Buzzかどうか判定する
- 1〜100の数字を判定に従って変換する
- 変換された値を画面に表示する

{% highlight ruby %}
## FizzBuzzの判定をするメソッド
def fizzbuzz(num)
  return :FizzBuzz if num % 15 == 0
  return :Buzz if num % 5 == 0
  return :Fizz if num % 3 == 0

  num
end
{% endhighlight %}

1つ目の判定をするメソッドから見ましょう。このメソッドでは引数の数値からどのFizzBuzzになるかを判定しています。
returnが3行続いていて、それぞれの行にif文が付いていますね。これは後置ifという書き方で、if文がtrueの場合のみ実行されます。
3つの条件がいずれも満たされなければ、数字をそのまま返します。
Rubyではメソッドの最後の処理の値がそのままメソッド自体の戻り値になりますので、最後のnumにはreturnが付いていません。これも、こういう文化です。

余談ですが、returnの省略は綺麗に見えるパターンもあってそれ自体は好きなんですが、メソッドの最後なら何が何でも省略するのがRubyだ！という風潮があるっぽいです。return書いた方がいいパターンもある派なので、モヤモヤしますが個人的な趣味の問題なので戦っても仕方ありません。

{% highlight ruby %}
## 判定メソッドを使い、判定後の値を持つArrayを作る
fizzbuzz_result = []
(1..100).each do |num|
  fizzbuzz_result << fizzbuzz(num)
end
{% endhighlight %}

2つ目は、fizzbuzzメソッドを使って数字を変換していっています。変換後の値はfizzbuzz_resultというArrayに格納されていってますね。
ここでわざわざfizzbuzz_resultに入れなくてもputsすればいいじゃないかと思われそうですが、ここでは1〜100の数値をFizzBuzzに変換するというのが目的であって、画面出力が目的ではありません。
ある処理の固まりに名前を付けようとしたときに（メソッド名とか）複数の処理をしていると名前が付けづらかったり、変な名前になるような処理の書き方をしてはいけません。

3つ目は変換後の値を、画面出力しているだけです。

# mapを使う
{% highlight ruby %}
## 判定メソッドを使い、判定後の値を持つArrayを作る
fizzbuzz_result = []
(1..100).each do |num|
  fizzbuzz_result << fizzbuzz(num)
end
{% endhighlight %}

この処理ですが、1〜100の100個の要素をfizzbuzzという変換メソッドを通して、fizzbuzz_resultという同じ100個の結果を得ています。
こういうパターンでは、mapを使うと良いです。

{% highlight ruby %}
## 判定メソッドを使い、判定後の値を持つArrayを作る
fizzbuzz_result = (1..100).map{ |num| fizzbuzz(num) }
{% endhighlight %}

全ての要素にmapに渡したメソッドを適用した、同数の要素を持ったあたらしいオブジェクトが生成されます。
もっとやるとすると、 (1..100).map{ |num| fizzbuzz(num) }  の部分で「FizzBuzzに変換するという処理」を十分表していますので、fizzbuzz_resultという一時変数への代入をやめて以下のようにしてもいいかと思います。

{% highlight ruby %}
(1..100).map{ |num| fizzbuzz(num) }.each do |num|
  puts num
end
{% endhighlight %}

この時点でのFizzBuzzの全容は下のようになります。かなりRubyっぽくなりました。

{% highlight ruby %}
## FizzBuzzの判定をするメソッド
def fizzbuzz(num)
  return :FizzBuzz if num % 15 == 0
  return :Buzz if num % 5 == 0
  return :Fizz if num % 3 == 0

  num
end

(1..100).map{ |num| fizzbuzz(num) }.each do |num|
  puts num
end
{% endhighlight %}


# オープンクラスを使う
この辺りからRubyの特徴的な機能を使って行こうと思います。
いままでのコードではclassを使ってませんでした。
Rubyのclassはこんな感じに使います。

{% highlight ruby %}
class Hoge
  def hello
    puts "hello"
  end
end

h = Hoge.new
h.hello
{% endhighlight %}

よくあるHello World的なプログラムです。
ここでは新しいクラスを作って処理(helloメソッド)を実装していますね。

ところでRubyにはオープンクラスという機能がありまして、新しくクラスを作るだけでなく、既存のクラスをオープンしてメソッドを追加したり、処理を書き換えたり出来ます。動的にもほどがある。

そして、この機能は上記の例のHogeの様な自分で作ったクラスだけではなく、StringとかFixnumとかRubyの組み込みクラスに対しても例外なく出来たりします。

FizzBuzzは数値にたいして行う処理なので、数値のクラスにfizzbuzzメソッドを追加してしまいましょう。

{% highlight ruby %}
class Fixnum
  def fizzbuzz
    return :FizzBuzz if self % 15 == 0
    return :Buzz if self % 5 == 0
    return :Fizz if self % 3 == 0

    self
  end
end
{% endhighlight %}
前に作ったfizzbuzzメソッドによく似てますが少し違います。Fixnumクラスのメソッドとして定義されていますし、fizzbuzzメソッドには引数がありません。
Fixnumクラスは「3」とか「100」とかの数値オブジェクトのクラスです。そのクラスのメソッドなので、メソッド内では数値自体はselfとなります。selfに対してFizzBuzzの判定を行っているわけですね。

これでどうなるか見てみましょう。

{% highlight ruby %}
2.0.0-p247 :018 >   class Fixnum
2.0.0-p247 :019?>     def fizzbuzz
2.0.0-p247 :020?>         return :FizzBuzz if self % 15 == 0
2.0.0-p247 :021?>         return :Buzz if self % 5 == 0
2.0.0-p247 :022?>         return :Fizz if self % 3 == 0
2.0.0-p247 :023?>
2.0.0-p247 :024 >           self
2.0.0-p247 :025?>       end
2.0.0-p247 :026?>   end
 => nil
2.0.0-p247 :027 > 3.fizzbuzz
 => :Fizz
2.0.0-p247 :028 > 10.fizzbuzz
 => :Buzz
2.0.0-p247 :029 > 15.fizzbuzz
 => :FizzBuzz
2.0.0-p247 :030 > 19.fizzbuzz
 => 19
{% endhighlight %}
ちゃんと数値に新しくメソッドが定義されてますね。

このクラスを使ったFizzBuzzの実装の全容が以下です。

{% highlight ruby %}
class Fixnum
  def fizzbuzz
    return :FizzBuzz if self % 15 == 0
    return :Buzz if self % 5 == 0
    return :Fizz if self % 3 == 0

    self
  end
end

(1..100).map {|num| num.fizzbuzz}.each do |num|
  puts num
end
{% endhighlight %}
mapの中身も少し変わっていますね。fizzbuzzメソッドに数値を渡すのではなく、数値自信のfizzbuzzメソッドを呼んでいます。

# Refinementsを使う
便利なような、危ないようなオープンクラスですが、こんなのを実際のプロジェクトでさらっと使われてたら混乱します。
クラスを作った人の意図とは違う動きになるように出来てしまう機能です。
やろうと思えば（割と簡単に）「 5 + 3 」の実行結果を「2」になるようにというのも出来てしまいます。
アプリケーションの初期化処理の中などで、こんなことをされたら色々と不都合が起きますよね。

こういった変更の影響範囲を限定させる必要性が出てきます。Ruby2.0から使えるようになったRefinementsが使えます。

{% highlight ruby %}
## fizzbuzz.rb
module FizzBuzz
  refine Fixnum do
    def fizzbuzz
      return :FizzBuzz if self % 15 == 0
      return :Buzz if self % 5 == 0
      return :Fizz if self % 3 == 0

      self
    end
  end
end
{% endhighlight %}
影響範囲を制御した変更をモジュールの中で行います。
そしてrefineメソッドを呼んで、変更を記述します。

この影響を受けた、つまりfizzbuzzメソッドが追加されたFixnumを使いたい場合は以下のようにusingを使います。

{% highlight ruby %}
## exec_fizzbuzz.rb
require './fizzbuzz'

using FizzBuzz
(1..100).map {|num| num.fizzbuzz}.each do |num|
  puts num
end
{% endhighlight %}
usingでモジュールを指定しています。この指定をしたファイル内ではfizzbuzzメソッドを追加されたFixnumになります。

以下のようにusingを使わない場合はfizzbuzzメソッドの定義されていない通常のFixnumとして処理が動くため、undefined method fizzbuzz for 1:Fixnum (NoMethodError)　が発生します。

{% highlight ruby %}
## no_exec_fizzbuzz.rb
require './fizzbuzz'

(1..100).map {|num| num.fizzbuzz}.each do |num|
  puts num
end
{% endhighlight %}

# gemを使う
最後に、Rubyにはgemというライブラリ管理ツールが付いています。
gemコマンドでライブラリをインストール出来ます。

{% highlight sh %}
 % gem install fizz-buzz
{% endhighlight %}

このライブラリを使うと、以下のコードでFizzBuzz出来てしまいます。

{% highlight ruby %}
require 'fizz-buzz'

(1..100).fizzbuzz.each do |v|
  puts v
end
{% endhighlight %}
詳細な説明は省きますが、オープンクラスの考え方でRangeクラスやArrayクラスをいじっているライブラリになっています。
興味ある人は、[githubにコード](https://github.com/kerrizor/fizz-buzz/blob/master/lib/fizz-buzz.rb)があるので見てみるといいでしょう。
