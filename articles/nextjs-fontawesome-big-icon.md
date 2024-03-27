---
title: "Next.jsでFontAwesomeを使用する際にアイコンが巨大化する現象と解決方法"
emoji: "🤯"
type: "tech"
topics:
  - "nextjs"
  - "fontawesome"
published: true
---

この記事では、NextjsでFontAwesomeを使用した際にアイコンが巨大化した原因と、解決方法が書かれています。

もし同じ現象で困っているようでしたら、この記事がもしかしたら役に立つかもしれません。

またどのような現象なのかというと、以下の画像の通りです。

## 問題となる現象

![巨大なアイコン](/images/big-icon.png)

本来のFontAwesomeのアイコンでは文字と同じような大きさになるところが、ここでは画面全体にアイコンが表示されています。

## 動作環境

アイコンが巨大化した現象は、次の環境で起きました。

```json
{
    "@fortawesome/fontawesome-svg-core": "^6.4.0",
    "@fortawesome/free-brands-svg-icons": "^6.4.0",
    "@fortawesome/free-regular-svg-icons": "^6.4.0",
    "@fortawesome/free-solid-svg-icons": "^6.4.0",
    "@fortawesome/react-fontawesome": "^0.2.0",
    "next": "^14.1.3",
}
```

## 原因

NextjsのCSSの管理方法が他のフレームワークと異なるからみたいです。

## 解決方法

以下のコードを、`pages/_app.js`、または`app/layout.js`に以下のコードを追加します。

```js:layout.js
import { config } from "@fortawesome/fontawesome-svg-core";
import "@fortawesome/fontawesome-svg-core/styles.css";
config.autoAddCss = false;
```

上記のコードでは、`config.autoAddCss = false`にすることで、`fontawesome-svg-core`が`<head>`に`<style>`要素を挿入しなくなるそうです。

そして`<style>`を挿入しない代わりに、`@fortawesome/fontawesome-svg-core/styles.css`のCSSを使うことで、アイコンの巨大化を防いでるみたいです。多分...

## まとめ

この問題は、Nextjsのスタイルが他のフレームワークと違ってちょっと特殊で、それがFontAwesomeに合わないことで起こるみたいですね。

なのでこちら側でスタイルの無効化や適用をしてやらなくてはいけないみたい。

仕方ないね😎

## 参考文献

https://fontawesome.com/docs/web/use-with/react/use-with
