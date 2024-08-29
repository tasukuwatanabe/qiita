---
title: iframeを使った親子間のデータのやり取り方法
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

こんにちは。HRBrainでオウンドメディアやランディングページの開発を担当している渡邉です。

ウェブサイトにMarketoやSalesforceなどのツールのフォームをiframeで埋め込むことがありますよね。

例えば、iframe内で入力された値を親サイトに送り、GTMに渡してマーケティングに活用する、といったケースです。

この記事では、そんなときに使える、iframeから親サイトにデータを渡す方法についてお話しします。

## iframe内の要素はイベントリスナーでは操作できない

iframeは、親ページとは異なるドメインで動作することがよくあります。そのため、通常のイベントリスナーを使っても、iframe内の要素にはアクセスできません。

例えば、iframe内のボタンをクリックしたときに、親ページの要素を操作しようとすると、セキュリティ上の理由でエラーが発生します。

```javascript
// iframe内のボタンをクリックしたときに親ページの要素を操作しようとする例
const iframe = document.getElementById('myIframe');
const button = iframe.contentWindow.document.getElementById('myButton');

button.addEventListener('click', () => {
  // 親ページの要素を操作しようとするとエラーが発生する
  document.getElementById('myElement').style.backgroundColor = 'red';
});
```

## window.postMessage()でデータを渡す

iframe内のフォームのデータを親ページに送るときは、`window.postMessage()` を使います。

このメソッドを使えば、異なるドメイン間でもデータを送ることができます。

```javascript
// iframe内のコード
const form = document.getElementById('myForm');

form.addEventListener('submit', (event) => {
  event.preventDefault(); // フォームのデフォルト動作をキャンセル

  const formData = {
    name: document.getElementById('name').value,
    email: document.getElementById('email').value,
  };

  // 親ページにデータを送信
  window.parent.postMessage({ formData }, '*'); // '*'はすべてのドメインを許可する
});
```

## 親ページでデータを受け取る

親ページでは、messageイベントリスナーを使って、iframeから送信されたデータを受け取ります。

```javascript
// 親ページのコード
window.addEventListener('message', (event) => {
  const formData = event.data.formData;
  
  // コンソールにデータを表示
  console.log(formData);

  // 必要に応じて、GTMにデータを送信する処理など
  dataLayer.push({
    event: 'formSubmit',
    formData: formData,
  });
});
```

## window.postMessage()の使い方

`window.postMessage()` は、異なるオリジン（ドメイン）のwindow間でメッセージを送信するために使います。

これによって、通常の「同一オリジンポリシー」というセキュリティ制限を回避できます。

### 同一オリジンポリシーとは？

同一オリジンポリシーは、異なるドメインのスクリプトが別のドメインのページのコンテンツにアクセスするのを防ぐためのセキュリティ対策です。

例えば、`https://child.site` から `https://parent.site` のページにアクセスしようとすると、同一オリジンポリシーによってブロックされます。

### window.postMessage()の使い方

`window.postMessage()` は、メッセージデータとターゲットオリジン（メッセージを受け取るドメイン）という2つの引数を取ります。

```javascript
// iframe内のコード
window.parent.postMessage('Hello from iframe!', '*'); // '*'はすべてのドメインを許可

// 親ページのコード
window.addEventListener('message', (event) => {
  console.log(event.data); // 'Hello from iframe!' が出力される
});
```

### ターゲットオリジンの指定

第2引数には、メッセージを受け取るwindowのオリジンを指定します。

- `*`：すべてのドメインを許可します。ただし、セキュリティリスクがあるので、できるだけ避けましょう。

- `https://child.site`：特定のドメインだけを許可します。

```javascript
// iframe内のコード
window.parent.postMessage('Hello from iframe!', 'https://parent.site'); // 特定のドメインを許可

// 親ページのコード
window.addEventListener('message', (event) => {
  if (event.origin === 'https://child.site') {
    console.log(event.data); // 'Hello from iframe!' が出力される
  }
});
```

## まとめ

この記事では、`postMessage` メソッドを使ってiframe内のデータを親ページに送信する方法について解説しました。

このメソッドを使うと、異なるドメイン間でも安全にデータをやり取りできます。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310
