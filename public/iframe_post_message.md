---
title: iframe埋め込みをした親子間でpostMessageメソッドを使ってデータをやり取りする
tags:
  - 'javascript'
  - 'iframe'
  - 'GoogleTagManager'
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

MarketoやSalesforceなどのMA製フォームをウェブサイトにiframeで埋め込むケースがよくあります。

## iframeから埋め込みサイトにフォーム入力値を渡す

iframe側（子）からサイト側（親）に対してフォーム入力値を送る場合、JavaScriptのwindow.postMessage()を使用します。

window.postMessage()メソッドは、iframeの親ページに対してメッセージを送信する際に使用します。

```javascript
// iframe内（子）のコード
const form = document.getElementById('myForm');

form.addEventListener('submit', (event) => {
  event.preventDefault(); // フォームのデフォルト動作をキャンセル

  const formData = {
    name: document.getElementById('name').value,
    email: document.getElementById('email').value,
  };

  // 親ページにメッセージを送信
  window.parent.postMessage({ formData }, '*'); // '*'はすべてのドメインを許可する
});
```

### eventListenerでのイベント操作はiframeだとできない

iframeは、親ページとは異なるドメインで動作することが多いため、通常のイベントリスナーではiframe内の要素にアクセスできません。

例えば、iframe内のボタンをクリックした際に親ページの要素を操作しようとすると、セキュリティ上の理由から動作しません。

```javascript
// iframe内のボタンをクリックした際に親ページの要素を操作しようとする例
const iframe = document.getElementById('myIframe');
const button = iframe.contentWindow.document.getElementById('myButton');

button.addEventListener('click', () => {
  // 親ページの要素を操作しようとするとエラーが発生する
  document.getElementById('myElement').style.backgroundColor = 'red';
});
```

## 親側ではmessageイベントを使ってデータを受け取る

親ページでは、messageイベントリスナーを使用して、iframeから送信されたメッセージを受け取ります。

```javascript
// 親ページのコード
window.addEventListener('message', (event) => {
  const formData = event.data.formData;
  console.log(formData); // 子から受け取ったデータを親側でコンソールに出力する

  // GTMにデータを送信
  dataLayer.push({
    event: 'formSubmit',
    formData: formData,
  });
});
```

## window.postMessage() メソッドについて

window.postMessage() メソッドは、iframeを含む異なるオリジンのウィンドウ間でメッセージを送信するために使用されます。

これは、ブラウザのセキュリティ制限である「同一オリジンポリシー」を回避するために設計されています。

### 同一オリジンポリシーとは

同一オリジンポリシーとは、異なるドメインのスクリプトが、別のドメインのページのコンテンツにアクセスすることを制限するセキュリティ対策です。

例えば、`https://example.com` のページから `https://another.com` のページのコンテンツにアクセスしようとすると、同一オリジンポリシーによってアクセスが制限されます。

### window.postMessage() メソッドの使用方法

window.postMessage() メソッドは、以下の2つの引数を取ります。

メッセージデータ: 送信するメッセージデータ。文字列、数値、オブジェクトなど、任意のデータ型を指定できます。
ターゲットオリジン: メッセージを受け取るウィンドウのオリジン。

```javascript
// iframe内（子）のコード
window.parent.postMessage('Hello from iframe!', '*'); // '*'はすべてのドメインを許可する

// 親ページのコード
window.addEventListener('message', (event) => {
  console.log(event.data); // 'Hello from iframe!' が出力される
});
```

上記の例では、iframe内から親ページに 'Hello from iframe!' というメッセージを送信しています。

### ターゲットオリジンの指定

window.postMessage() メソッドの第2引数には、メッセージを受け取るウィンドウのオリジンを指定します。

'*'：すべてのドメインを許可します。セキュリティ上のリスクがあるため、可能な限り使用は避けましょう。
'https://example.com'：特定のドメインのみを許可します。

```javascript
// iframe内（子）のコード
window.parent.postMessage('Hello from iframe!', 'https://example.com'); // 特定のドメインのみを許可

// 親ページのコード
window.addEventListener('message', (event) => {
  if (event.origin === 'https://example.com') {
    console.log(event.data); // 'Hello from iframe!' が出力される
  }
});
```

上記の例では、iframe内から親ページにメッセージを送信する際に、ターゲットオリジンを 'https://example.com' に指定しています。親ページでは、event.origin プロパティを使用して、メッセージの送信元を確認しています。

## バリデーションは送信ボタンを押した時にDOMのエラーメッセージの有無で判断する

フォームのバリデーションは、送信ボタンを押した際にDOMのエラーメッセージの有無で判断することができます。

```javascript
// iframe内（子）のコード
const form = document.getElementById('myForm');

form.addEventListener('submit', (event) => {
  event.preventDefault(); // フォームのデフォルト動作をキャンセル

  // バリデーションチェック
  if (document.getElementById('name').value === '') {
    // エラーメッセージを表示
    document.getElementById('nameError').textContent = '名前を入力してください';
    return;
  }

  // バリデーションが成功した場合、親ページにメッセージを送信
  const formData = {
    name: document.getElementById('name').value,
    email: document.getElementById('email').value,
  };
  window.parent.postMessage({ formData }, '*');
});
```

## dataLayer.push()でGTMに対して子から受け取った値を送る

親ページでmessageイベントリスナーで受け取ったデータは、dataLayer.push()メソッドを使用してGTMに送信することができます。

```javascript
// 親ページのコード
window.addEventListener('message', (event) => {
  const formData = event.data.formData;

  // GTMにデータを送信
  dataLayer.push({
    event: 'formSubmit',
    formData: formData,
  });
});
```

## まとめ

iframe埋め込みをした親子間でpostMessageメソッドを使ってデータをやり取りする方法について解説しました。

`window.postMessage()`メソッドを使用することで、iframe間でのデータ通信を実現し、GTMなどのツールにデータを送信することができます。

セキュリティ上のリスクを考慮し、適切なドメインを指定して使用しましょう。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://hrmos.co/pages/hrbrain/jobs/23020910