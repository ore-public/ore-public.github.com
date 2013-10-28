---
layout: post
title: "FizzBuzzで始めるRuby"
description: ""
category: 
tags: [ruby]
---
{% include JB/setup %}

他のプログラミング言語は分かるけどRubyは初心者だという人や、Rubyの本を読んでひと通りプログラムは書いてみたけどRubyっぽく書くにはどうすればいいのか悩んでいる人、Ruby特有の機能を知りたいという人向けに、FizzBuzzを書きながらRubyの特徴を覚えていくことを目的とした記事です。

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

for文で1から100までのループを回しながら、わり切れるかどうかをチェックして文字又は数字を出力しています。
処理としてはちゃんと動いてますが、ちょっと書き方がRubyっぽくないです。

#### 1..100 という書き方について
TODO:Rangeについて説明する
ついでにArrayも説明する。

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

ArrayとかRangeの様に複数の要素を持っているオブジェクトを、ループさせながら処理するときは each を使うのがRubyっぽいです。
「なんでfor文が駄目なの?」とか、「じゃあRubyにfor文要らないじゃん、なんであるの？」とか言っては駄目です。
そういう文化です。

# もうちょっとRubyっぽく シンボル を使う
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

"name"という文字列が重要なのではなく、データへアクセスするための名前として :name を使っています。

そういう意味では、今回のFizzBuzzではFizzBuzzという文字を出力する仕様なので、この使い方はなんか違う気もしないでも無いですが・・・。

# メソッドにする
ここまでで、パッと見Rubyっぽくない所を直しましたが、まだまだ気に入らないところがあります。
なんか if elsif elsif って連なっているのも汚いですし、ループとFizzBuzzの判定処理と、画面出力の処理がごちゃ混ぜなのも嫌ですね。

ちょっとメソッドを作って、処理の役割を分岐させましょう。

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
return が3行続いていて、それぞれの行にif文が付いていますね。これは後置ifという書き方で、if文がtrueの場合のみ実行されます。
3つの条件がいずれも満たされなければ、数字をそのまま返します。
Rubyではメソッドの最後の処理の値が、そのままメソッド自体の戻り値になりますので、最後のnumにはreturnが付いていません。これも、こういう文化です。

余談ですが、returnの省略は綺麗に見えるパターンもあってそれ自体は好きなんですが、メソッドの最後なら何が何でも省略するのがRubyだ！という風潮があるっぽいです。return書いた方がいいパターンもある派なので、モヤモヤしますが個人的な趣味の問題なので戦っても仕方がないです。

{% highlight ruby %}
## 判定メソッドを使い、判定後の値を持つArrayを作る
fizzbuzz_result = []
(1..100).each do |num|
  fizzbuzz_result << fizzbuzz(num)
end
{% endhighlight %}

2つ目は、fizzbuzzメソッドを使って数字を変換していっています。変換後の値はfizzbuzz_resultというArrayに格納されていってますね。
ここでわざわざfizzbuzz_resultに入れなくてもputsすればいいじゃないかと思われそうですが、ここでは1〜100の数値をFizzBuzzに変換するというのが目的であって、画面出力が目的ではありません。
ある処理のブロックに名前を付けようとしたときに（メソッド名とか）複数の処理をしていて名前が付けづらかったり、変な名前になるような処理の書き方をしてはいけません。

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

この時点でのFizzBuzzの全容は下のようになります。

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


#




{% highlight ruby %}
 
{% endhighlight %}
