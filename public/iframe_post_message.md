---
title: postMessageを使ってクロスオリジンなiframeからデータを受け取る
tags:
  - JavaScript
  - iframe
  - GoogleTagManager
private: false
updated_at: '2024-09-24T18:35:40+09:00'
id: 4774c9a8ef73363f52b9
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディアやランディングページの開発を担当している渡邉です。

SalesforceなどのMAツール製のiframeフォームをウェブサイトに埋め込むことがあります。

この記事では、ウェブサイトとiframeが異なるドメインの場合に、iframeからウェブサイトにデータを受け渡す方法について解説しています。

▼ [HRBrainサービスサイト](https://www.hrbrain.jp/)におけるiframeフォームの事例

![iframe_form.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/acd843cf-63ce-53b2-d9f2-daddb30efa19.png)

## クロスオリジンでのiframe操作の制限

iframeとウェブサイトが異なるドメインに存在している場合、ウェブサイトからiframe内の要素に直接アクセスすることができません。

- ウェブサイトのURL： `example.com` 
- iframeのURL： `example.net`

これは、「同一オリジンポリシー」により、セキュリティの観点から、オリジンが異なるページへのアクセスは制限を受けるためです。（クロスオリジン）

https://developer.mozilla.org/ja/docs/Web/Security/Same-origin_policy

たとえば、iframe内のボタンをウェブサイトから操作しようとすると、以下のようなエラーが発生します。

```javascript:ウェブサイト側：example.com
const iframe = document.getElementById('myIframe');

// iframe内のbutton要素にはウェブサイトからアクセスが許可されない
iframe.contentWindow.document.getElementById('myButton'); // error

// Error: 
// Failed to read a named property 'document' from 'Window': 
// Blocked a frame with origin "http://example.com" from accessing a cross-origin frame.
```

このエラーは、iframeがクロスオリジンのため、ウェブサイトからiframe内のDOMにアクセスできないことを示しています。

## postMessageメソッドについて

クロスオリジンでデータを送信するには、postMessageメソッドを使用します。

このメソッドは、異なるドメイン間で安全にデータをやり取りするための手段です。

https://developer.mozilla.org/ja/docs/Web/API/Window/postMessage

### postMessageメソッドの使い方

postMessageメソッドの第1引数にはウェブサイト側に渡すデータを、第2引数にはターゲットオリジン（メッセージを受け取るドメイン）を渡します。

```javascript:iframe側：example.net
window.parent.postMessage('Hello from iframe!', 'example.com');
```
```javascript:ウェブサイト側：example.com
window.addEventListener('message', (event) => {
  console.log(event.data); // 'Hello from iframe!' が出力される
});
```

### ターゲットオリジンの指定
postMessageメソッドでは、メッセージを送信するターゲットオリジンを第2引数に指定できます。

- `*`：すべてのドメインを許可しますが、セキュリティリスクがあるため、特定のオリジンを指定することを推奨します。

- `https://example.com`：特定のドメインを指定することで、信頼できるドメインだけがメッセージを受信できようになります。

### iframeから受け取ったデータをGTMに渡す事例

ウェブサイトでは、`message` イベントリスナーを使用して、iframeから送信されたデータを受け取ります。

さらに、iframeから受け取ったデータをdataLayerを使ってGTMに渡すことで、マーケティング施策の幅が広がります。

```javascript:iframe側：example.net
const form = document.getElementById('myForm');

form.addEventListener('submit', (event) => {
  // フォームから入力された値を取得してオブジェクトにまとめる
  const formData = {
    name: document.getElementById('name').value, // ユーザー名
    email: document.getElementById('email').value, // メールアドレス
  };

  // ウェブサイトにデータを送信
  window.parent.postMessage({ formData }, 'https://example.com');
});
```
```javascript:ウェブサイト側：example.com
window.addEventListener('message', (event) => {
  // 送信元のオリジンを確認
  if (event.origin !== 'https://example.net') return; // 信頼できるオリジンのみ処理する

  const formData = event.data.formData;

  // 必要に応じて、GTMにデータを送信
  dataLayer.push({
    event: 'formSubmit',
    formData: formData,
  });
});
```

## まとめ

この記事では、postMessageメソッドを使って、クロスオリジンのiframeからウェブサイトにデータを送信する方法を解説しました。

postMessageメソッドを活用すれば、異なるドメイン間でも安全にデータをやり取りすることが可能です。

## PR

HRBrainではフロントエンドエンジニア（コミュニケーションデザイン）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit
