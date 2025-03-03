---
title: "Rubyの組み込み変数まとめ"
emoji: "😑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ruby"]
published: true
---
この記事では、プログラミング言語`Ruby`の組み込み変数がまとめられています。

## 参考URL

**定数**

https://docs.ruby-lang.org/ja/latest/class/Object.html

**特殊変数**

https://docs.ruby-lang.org/ja/latest/class/Kernel.html

**擬似変数**

https://docs.ruby-lang.org/ja/latest/doc/spec=2fvariables.html#pseudo

**環境変数**

https://docs.ruby-lang.org/ja/latest/doc/spec=2fenvvars.html

## 定数

この項目では[class Object](https://docs.ruby-lang.org/ja/latest/class/Object.html)で定義されている
定数を紹介します。

### ARGF -> Object

引数（なければ標準入力）で構成される仮想ファイル。

### ARGV -> Array

Rubyスクリプトに与えられた引数を表す配列。

```ruby
p ARGV

# $ ruby index.rb hoge bar
# ["hoge", "bar"]
```

### DATA -> File

スクリプトの`__END__`プログラムの終わり以降をアクセスする`File`オブジェクト。

```ruby
puts DATA.gets
puts DATA.gets

__END__
やっはろー
プログラムはここで終わりです。

# $ ruby index.rb
# やっはろー
# プログラムはここで終わりです。
```

### ENV -> Object

環境変数を表す（擬似）連想配列。

```ruby
pp ENV

# $ ruby index.rb
# {"HOME" => "/Users/home",
#  "LANG" => "ja_JP.UTF-8",
#  "LC_TERMINAL" => "iTerm2",
#  "PATH" => "/ruby/path",
#  "PWD" => "/Users/home/current/directory",
#  "SHELL" => "/usr/bin/bash",
#  "USER" => "ittokunvim",
#  "..." => "..."}
```

### RUBY_COPYRIGHT -> String

`Ruby`のコピーライトを表す文字列。

```ruby
puts RUBY_COPYRIGHT

# $ ruby index.rb
# ruby - Copyright (C) 1993-2025 Yukihiro Matsumoto
```

### RUBY_DESCRIPTION -> String

`Ruby`の詳細を表す文字列。 ruby -vで表示される。

```ruby
puts RUBY_DESCRIPTION

# $ ruby index.rb
# ruby 3.4.2 (2025-02-15 revision d2930f8e7a) +PRISM [arm64-darwin24]
```

### RUBY_ENGINE -> String

`Ruby`処理系実装のバージョンを表す文字列。

```ruby
puts RUBY_ENGINE

# $ ruby index.rb
# ruby
```

### RUBY_PATCHLEVEL -> String

`Ruby`のパッチレベルを表す`Integer`オブジェクト。
パッチレベルとは`Ruby`の各バージョンに対するバグ修正パッチの適用がカウントされている。

```ruby
puts RUBY_PATCHLEVEL

# $ ruby index.rb
# 28
```

### RUBY_PLATFORM -> String

プラットフォームを表す文字列。

```ruby
puts RUBY_PLATFORM

# $ ruby index.rb
# arm64-darwin24
```

### RUBY_RELEASE_DATE -> String

`Ruby`のリリース日を表す文字列。

```ruby
puts RUBY_RELEASE_DATE

# $ ruby index.rb
# 2025-02-15
```

### RUBY_REVISION -> String

`Ruby`のGITコミットハッシュを表す`String`オブジェクト。

```ruby
puts RUBY_REVISION

# $ ruby index.rb
# d2930f8e7a5db8a7337fa43370940381b420cc3e
```

### RUBY_VERSION -> String

`Ruby`のバージョンを表す文字列。

```ruby
puts RUBY_VERSION

# $ ruby index.rb
# 3.4.2
```

### SCRIPT_LINES__ -> Hash

ソースファイル別にまとめられたソースコードの各行。
この定数はデフォルトでは定義されておらず、この定数がハッシュとして定義された後に設定される。

```ruby
SCRIPT_LINES__ = {}
require 'English'
pp SCRIPT_LINES__

# $ ruby index.rb
# {"/opt/homebrew/Cellar/ruby/3.4.2/lib/ruby/3.4.0/English.rb" =>
#   ["# frozen_string_literal: true\n",
#    "#  Include the English library file in a Ruby script, and you can\n",
#    "#  reference the global variables such as <code>$_</code> using less\n",
#    "#  cryptic names, listed below.\n",
#    ...
```

### STDERR -> IO

標準エラー出力。

```ruby
# 標準エラー出力先をhoge.txtに変更
$stderr = File.open('./hoge.txt', 'w')
puts hoge # エラー
$stderr = STDERR # 元に戻す

# $ ruby index.rb
#
# hoge.txt
# index.rb:2:in '<main>': undefined local variable or method 'hoge' for main (NameError)
#
# puts hoge
#      ^^^^
```

### STDIN -> IO

標準入力。

### STDOUT -> IO

標準出力。

### TOPLEVEL_BINDING

トップレベルでの`Binding`オブジェクト。

## 特殊変数

| 特殊変数 | 型                     | 説明                                                       |
| -------- | ----------------       | ---------------------------------------------------------- |
| $!       | Exception \| nil       | 最後に例外が発生した時の`Exception`オブジェクト            |
| $"       | [String]               | `Kernel.#require`でロードされたファイル名を含む配列        |
| $$       | Integer                | 現在実行中の`Ruby`プロセスのプロセスID                     |
| $&       | String \| nil          | 正規表現でマッチした文字列                                 |
| $'       | String \| nil          | 正規表現でマッチした文字列より後ろの文字列                 |
| $*       | String \| nil          | `Ruby`スクリプトに与えられた引数を表す配列                 |
| $+       | String \| nil          | 正規表現でマッチした最後の括弧に対応する文字列             |
| $,       | String \| nil          | デフォルトの出力フィールド区切り文字列                     |
| $/       | String \| nil          | 入力レコード区切りを表す文字列                             |
| $;       | String \| nil          | `String#split`で引数を省略した場合の区切り文字列           |
| $:, $-I  | [String]               | `Ruby`ライブラリをロードする時の検索パス                   |
| $-K      | object                 | 通常のグローバル変数、`Ruby2.7`までは特殊変数              |
| $-W      | 0 \| 1 \| 2            | コマンドラインオプション`-W`指定時に、引数の値が設定       |
| $-a      | bool                   | 自動`split`モードを表すフラグ                              |
| $-d      | bool                   | `true`時にインタプリタがデバッグモードになる               |
| $-i      | String \| nil          | in-place置き換えモードで用いられる                         |
| $-l      | bool                   | コマンドラインオプション`-l`指定時に、`true`が設定         |
| $-p      | bool                   | コマンドラインオプション`-p`指定時に、`true`が設定         |
| $-v, $-w | bool \| nil            | 冗長メッセージフラグ、コマンドラインオプション`\v`でセット |
| $.       | Integer                | `IO`オブジェクトが最後に読んだ行の行番号                   |
| $0       | String                 | 現在実行中の`Ruby`スクリプト名を表す文字列                 |
| $1 ~ $11 | String \| nil          | 正規表現でマッチした`n`番目の括弧にマッチした値            |
| $<       | IO                     | 全ての引数または標準入力で構成される仮想ファイル           |
| $=       | bool                   | 常に値が`false`                                            |
| $>       | object                 | 標準出力                                                   |
| $?       | Process::Status \| nil | 最後に終了した子プロセスのステータス                       |
| $@       | [String] \| nil        | 最後に例外が発生した時のバックトレース                     |
| $\\      | String \| nil          | 出力レコード区切りを表す文字列                             |
| $_       | String \| nil          | 最後に`Kernil.#gets, Kernel.#readline`で読み込んだ文字列   |
| $`       | String \| nil          | 正規表現でマッチした部分より前の文字列                     |
| $~       | MatchData \| nil       | 正規表現でマッチに関する`MatchData`                        |

### 特殊変数その他

| 特殊変数  | 型     | 説明                       |
| --------- | ------ | -------------------------- |
| $stdout   | object | 標準出力                   |
| $FILENAME | String | 現在読み込み中のファイル名 |
| $SAFE     | object | 通常のグローバル変数       |
| $stderr   | object | 標準エラー出力             |
| $stdin    | object | 標準入力                   |

### 同じ意味の特殊変数

| 特殊変数         | 特殊変数 |
| ---------------- | -------- |
| $LOADED_FEATURES | $"       |
| $-0              | $/       |
| $-F              | $;       |
| $LOAD_PATH       | $:       |
| $-I              | $:       |
| $KCODE           | $-K      |
| $DEBUG           | $-d      |
| $VERBOSE         | $-v, $-w |
| $PROGRAM_NAME    | $0       |
| $stdout          | $>       |

## 擬似変数

擬似変数とは以下のような変数となります。

| 擬似変数      | 説明                                             |
| ------------- | ------------------------------------------------ |
| self          | 現在のメソッドの実行主体                         |
| nil           | `NilClass`唯一のインスタンス                     |
| true          | `TrueClass`唯一のインスタンス                    |
| false         | `FalseClass`唯一のインスタンス                   |
| \__FILE__     | 現在のソースファイル名                           |
| \__LINE__     | 現在のソースファイル中の行番号                   |
| \__ENCODING__ | 現在のソースファイルのスクリプトエンコーディング |

## 環境変数

`Ruby`インタプリタは以下の環境変数を参照します。

| 環境変数  | 説明                                                                           |
| --------- | ------------------------------------------------------------------------------ |
| RUBYOPT   | `Ruby`インタプリタでデフォルトで渡すオプションを指定                           |
| RUBYPATH  | `-S`オプション指定時、一番後ろに検索パスを追加                                 |
| RUBYLIB   | 一番前に検索パスを追加                                                         |
| RUBYSHELL | `Kernel.#system`でコマンド実行するシェル（`mswin32, mingw32`の`Ruby`のみ有効） |
| PATH      | `Kernel.#system`などでコマンド実行する検索パス                                 |

## まとめ

この記事では、`Ruby`の組み込まれている変数の一覧をざっくり説明してみました。

詳細が知りたい場合は上記の参考URLから飛んでみてみることをお勧めします。

ここでは「こういったものがあるんだよ」ということをこの記事を見た人に知ってもらうことが目的なので、
何かの参考になっていただけたら幸いです。

