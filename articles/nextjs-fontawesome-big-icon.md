---
title: "Next.jsでFontAwesomeを使用する際にアイコンが巨大化する現象と解決方法"
emoji: "🤯"
type: "tech"
topics:
  - "nextjs"
  - "fontawesome"
published: true
---

この記事では、NextjsでFontAwesomeを使おうとした際にアイコンが巨大化した原因と、この現象を治す方法が書かれています。

もし同じ現象で直し方を探していて、この記事を見て直すことがかなったら幸いです。

ちなみに以下のような感じになります。

![巨大なアイコン](/images/big-icon.png)

またすでにFontAwesomeのサイトにこの記事と同じことが書かれています。
英語が読める方なら以下のURLから参照したほうが早いかもしれません。

> https://fontawesome.com/docs/web/use-with/react/use-with

### 動作環境

アイコンが巨大化した現象は、次の環境で起きました。

```
package (version)

Nextjs (13.4.9)
@fortawesome/fontawesome-svg-core (^6.4.0)
@fortawesome/free-brands-svg-icons (^6.4.0),
@fortawesome/react-fontawesome (^0.2.0),
```

### 原因と解決方法

アイコンが巨大化する原因はNextjsのCSSの管理方法が他のフレームワークと異なるため、とFontAwesome公式サイトの記事で書かれていました。

解決する方法は以下のコードを、`pages/_app.js`、または`app/layout.js`に以下のコードを追加します。

```javascript
import { config } from "@fortawesome/fontawesome-svg-core";
import "@fortawesome/fontawesome-svg-core/styles.css";
config.autoAddCss = false;
```

上記のコードでは、`config.autoAddCss = false`にすることで、`@fortawesome/fontawesome-svg-core`が`<head>`に`<style>`要素を挿入しなくなるそうです。

そして`<style>`要素を挿入しない代わりに、`@fortawesome/fontawesome-svg-core/styles.css`のCSSを使うことで、この現象を抑えてるってことなのかも。

この記事は以上です！見ていただきありがとうございました😎
