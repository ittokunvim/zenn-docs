---
title: "Bevyでクリックゲームを作る"
emoji: "🎮"
type: "tech"
topics:
  - "rust"
  - "game"
  - "bevy"
published: false
---

ここでのクリックゲームとは、画面内を跳ね返っているボールをクリックしてボールを消してゆき、ゼロになったらOKというゲームです。

![Bevy Click Game](https://storage.googleapis.com/zenn-user-upload/40f49042630b-20230619.gif)

ゲームを作るに当たって以下のURLを参考にしております。

> [Break Out (Bevy Examples)](https://github.com/bevyengine/bevy/blob/main/examples/games/breakout.rs)

完成したゲームのソースコードはこちら

> [Click Game](https://github.com/ittokun/bevy-games/blob/main/examples/click.rs)

ゲームを正常に動作させるためにアセットをダウンロードしておく必要があります。
以下のURLから2つのフォントをダウンロードしておいてください。

> - [FiraSans-Bold.ttf](https://github.com/ittokun/bevy-games/blob/main/assets/fonts/FiraSans-Bold.ttf)
> - [FiraMono-Medium.ttf](https://github.com/ittokun/bevy-games/blob/main/assets/fonts/FiraMono-Medium.ttf)

### 始める前に

ゲームを作成する手順を説明する前に、まずは開発環境がきちんと用意されているか重要になります。

この記事でゲームを作成するには、Rustがインストールされている必要があります。

> [Rust Programming Language](https://www.rust-lang.org/ja)

また注意点として、ここで説明するゲームエンジンであるBevyのバージョンは`0.10.1`です。
他のバージョンでは動作しないこともあるので注意してください。

では始めます。

### プロジェクトのセットアップ



### Bevyを動かす



### カメラと壁を追加



### ボールを追加



### ボールの動きを実装



### ボールと壁の衝突判定を実装



### カーソル位置を取得



### ボールを削除する機能を追加



### スコアボードを追加
