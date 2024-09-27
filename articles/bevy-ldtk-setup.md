---
title: "【Bevyでゲーム作り】BevyとLDtkで2Dゲームを作る"
emoji: "🎮"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rust", "bevy", "ldtk"]
published: true
---

この記事ではRustで作られたゲームエンジン`Bevy`と、2Dレベルエディタである`LDtk`を組み合わせたゲームの作り方について解説しています。

## この記事は？

`bevy_ecs_ldtk`パッケージを使えばいい感じにBevyでゲームを作れることは書いてたが、
`Bevy`でどうやってゲームの制御面を作れば良いかのことは探してもなかったので、
自分で書いてみました。

## 使用技術

今回ゲーム開発に使用した技術は以下の通り。

https://bevyengine.org/

https://ldtk.io/

https://github.com/Trouv/bevy_ecs_ldtk

## ソースコード

ソースコードはGitHubに保存しています。

https://github.com/ittokunvim/bevy_ldtk_setup

## バージョン

バージョンが違うと多分動作しません。

```toml
bevy = "0.14.2"
bevy_ecs_ldtk = "0.10.0"
```

## ディレクトリ構造

最終的なディレクトリ構造、ファイルは以下の通り。

```
bevy_ldtk_setup
├── Cargo.lock
├── Cargo.toml
├── LICENSE
├── README.md
├── assets
│   ├── bevy_ldtk_setup.ldtk
│   ├── fonts
│   │   ├── FiraMono-Medium.ttf
│   │   └── FiraSans-Bold.ttf
│   └── images
│       ├── player.png
│       ├── thumbnail.png
│       └── tileset.png
└── src
    ├── gameover.rs
    ├── ingame.rs
    ├── main.rs
    └── mainmenu.rs
```

## ゲーム概要

迷路ゲームでプレイヤーを操作してゴールを目指すものとなっています。

ゲーム自体は以下のURLにあるチュートリアルをそのまま使用しています。

読むと`bevy_ecs_ldtk`の基本的な使い方を楽しく学ぶことができるので結構おすすめ。

https://trouv.github.io/bevy_ecs_ldtk/v0.10.0/tutorials/tile-based-game/index.html

## 仕組み

`assets`ディレクトリ下にフォントや画像、LDtkプロジェクトファイルが保存されています。

`src`ディレクトリ下にはゲームを動作させるためのソースコードが保存されており、
ゲームの状態ごとにファイルを分けています。

`gameover.rs`にはゲームがクリアされた時に実行されるコードが保存されています。
ここではゲームクリア画面の描画と、`R`キーを押した時に最初からゲームを始められるようにしています。

`ingame.rs`にはゲームを動作させるためのコードが保存されています。
ここでは`bevy_ecs_ldtk`のチュートリアルに、ゲームがクリアされた時にゲームの状態を遷移させるコードを追加しています。

`main.rs`にはゲームのセットアップなどが書かれています。

`mainmenu.rs`にはゲームのメインメニューを動作させるコードが保存されています。
ここではゲーム開始画面の描画と、画面をクリックするとゲームを開始できるようにしています。

ソースコードは[こちら](https://github.com/ittokunvim/bevy_ldtk_setup)に保存しているので、
詳しく知りたい方はご確認ください。

## まとめ

どうだったでしょうか？あまり記事を書くのは得意ではないので変な事書いてるかもしれませんが...

ソースコードは記事に載せたかったのですが、まとめるのも面倒だし、全部載せると長くなりすぎるので、URLだけにしました。

この記事が何かの役に立てたら嬉しいです。
