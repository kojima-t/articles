+++
date = "2017-02-11T16:30:13+09:00"
title = "rbenvを用いたRubyのインストール"
draft = false
tags = ["Ruby", "rbenv"]
+++

普段は Python ばかり使っているのだが、最近 Python にも飽きてきて、休日くらい違う言語で動くプログラムをいじりたいなと思った。そこでとりあえず Ruby の仮想環境を [pyenv](https://github.com/yyuu/pyenv) みたく作れる[^1] [rbenv](https://github.com/rbenv/rbenv) を使ってみることにした。

非常に今更感のある記事であるが、まぁあまり気にしないで書いていこうかと思う。ちなみに今回は Windows は想定していない[^3]。

[^1]: どちらが先なのかは知らない。なんとなく rbenv のほうが先な気がするが。

[^3]: Windows で `make` するとか僕には考えられない。

ところで、"rbenv" というワードのみで Google 検索すると GitHub よりも上に某技術系記事を投稿するWebサービスとか、個人ブログとかが引っかかるのはいかがなものかと思う。1 番上には GitHub というよりも公式の情報が来て欲しい。今はしかたなく "rbenv github" などと調べているが、日本人向けに最適化されているのだろうか、余計なお世話だと思うのだが。

## 環境など

* Arch Linux

## rbenv のインストール

公式のREADME.mdに従ってインストールをする。rbenv のみでは Ruby のインストールはできないので、 [ruby-build](https://github.com/rbenv/ruby-build) というプラグインが必要だ[^2]。

[^2]: この点、pyenvはpyenvリポジトリの中にあらかじめ `plugins/pyenv-build` というプラグインが入っているので楽だ。

なにはともあれ、必要なものをインストールしよう。

```
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ cd ~/.rbenv && src/configure && make -C src
```

次にパスを通す。

```
# bash
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
# fish shell
$ echo 'set -x PATH $PATH "$HOME/.rbenv/bin"' >> $XDG_CONFIG/fish/config.fish
```

Ubuntu ユーザーは `~/.bash_profile` を `~/.bashrc` に。 zsh ユーザーは `~/.zshrc` に適宜読みかえる。

そして、次のコマンドを実行する。

```
$ ~/.rbenv/bin/rbenv init -
```

実行語、標準出力にシェル毎に rbenv の初期化方法が出力されるので、指示通りに設定ファイルに書き込む。

シェルを再起動し、以下の出力になればインストールは成功である。

```
$ type rbenv # => "rbenv is a function"
```

続いて、 `ruby-build` をインストールする。

```
$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

これで、 `rbenv install -l` とすれば、インストールできる Ruby の version が表示されるはずである。

## 困ったこと

ただ単に `rbenv install 2.3.1` などとするだけでは僕の環境ではインストールすることができなかった。よくわからないエラーが出て、インストールできなかったのである。

```
# エラー内容
/home/user/.rbenv/versions/2.3.1/lib/ruby/2.3.0/yaml.rb:5:in `<top (required)>':
It seems your ruby installation is missing psych (for YAML output).
To eliminate this warning, please install libyaml and reinstall your ruby.
The Ruby yaml extension was not compiled.
ERROR: Ruby install aborted due to missing extensions
```

原因は結局わからなかったのだが、[ruby-build の wiki](https://github.com/rbenv/ruby-build/wiki#build-failure-of-fiddle-with-ruby-220) に書いてある環境変数を設定してインストールしたら、僕の環境ではインストールできた。

```
# fish shell
$ env RUBY_CONFIGURE_OPTS=--enable-shared rbenv install 2.3.1
```

これでインストールできた。

## 使い方

`~/myapp` があるとして、

```
$ cd ~/myapp
$ rbenv local 2.3.0
```

などとすると、 `myapp` 下では Ruby の version は 2.3.0 が使用されることになる。`rbenv`で設定した Ruby を使用したいと明示する場合には、 `rbenv exec` を使用すれば良い。

```
$ rbenv exec gem install bundler # rbenv 環境下での bundler をインストール
$ rbenv exec bundle install # rbenv 環境下での bundler を用いて bundle install
$ rbenv exec bundle exec ruby app.rb
```

とこんな感じで使える。

## おわりに

Ruby のインストールは結構ハマるようなので、ruby-build の GitHub は結構使えるかもしれない。

そのうち Rails アプリを自分の PC で動かしてみようか。
