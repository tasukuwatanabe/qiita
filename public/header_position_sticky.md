---
title: 'position: stickyでヘッダーを固定して要素の重なりを防ぐ方法'
tags:
  - 'HTML'
  - 'CSS'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

ウェブサイトにおけるナビゲーションバーやリンクバーの固定は、ユーザーがスクロールしても常にアクセス可能な位置に配置するための重要な手法です。

しかし、これを実装する際に意図せぬレイアウト崩れが発生することがあります。

特に、position: fixedを使ってヘッダーやリンクバーを固定しようとした場合、後続するコンテンツがヘッダーの下に「潜り込む」問題に直面し、大規模な修正が必要になることもあります。

この記事では、私が実際にリンクバーをページ最上部に固定しようとしたときに、position: fixedの落とし穴に遭遇した体験を共有しつつ、position: stickyの優位性について説明します。

## position: fixed で起きた問題

position: fixedは、ビューポート（画面）に対して要素を固定する方法として非常にシンプルに見えますが、実際には以下のような問題が発生することがあります。

ヘッダーをposition: fixedで固定すると、他のページ要素がそのヘッダーの「下に潜り込む」現象が発生します。

これを防ぐためには、ページ全体のレイアウトを大幅に修正しなければならず、大工事になることが多いです。

具体的には、ヘッダーの高さ分だけmarginやpaddingを調整して、後続する要素がヘッダーに重ならないようにする必要があります。


```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fixed Header Issue</title>
  <style>
    header {
      position: fixed;
      top: 0;
      width: 100%;
      background-color: #333;
      color: white;
      padding: 15px;
      z-index: 1000;
    }
    section {
      padding: 20px;
      height: 1500px;
      background-color: lightgray;
    }
  </style>
</head>
<body>
  <header>これは固定ヘッダーです</header>
  <section>
    <p>スクロールしてみてください。コンテンツがヘッダーの下に潜り込んでいます。</p>
  </section>
</body>
</html>
```

このコードでは、headerが画面上部に固定されていますが、後続するsection要素がヘッダーに隠れてしまっています。

margin-topなどで調整することは可能ですが、レスポンシブデザインを考慮する場合、各画面サイズごとに余白を設定する必要があり、非常に手間がかかります。

## position: sticky で問題解決

このような問題を解決するために、position: stickyを利用することを検討しました。

position: stickyを使うことで、スクロール時にヘッダーが特定の位置に達したときだけ固定され、他の要素が自然にその位置を避けて配置されるため、後続する要素が潜り込む問題が発生しません。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sticky Header Example</title>
  <style>
    header {
      position: sticky;
      top: 0;
      background-color: #333;
      color: white;
      padding: 15px;
      z-index: 1000;
    }
    section {
      padding: 20px;
      height: 1500px;
      background-color: lightgray;
    }
  </style>
</head>
<body>
  <header>これはStickyヘッダーです</header>
  <section>
    <p>スクロールしてみてください。Stickyヘッダーが自然に固定されます。</p>
  </section>
</body>
</html>
```

このコードでは、headerはposition: stickyを使用しています。

ページをスクロールすると、ヘッダーが親要素内で指定した位置に達したときに固定され、後続するsection要素はヘッダーに隠れたり潜り込んだりすることなく正常に表示されます。

## position: sticky のメリット

position: stickyは、スクロールに応じて要素を特定の位置に固定するという挙動を実現しつつ、レイアウトの崩れを最小限に抑えることができるため、position: fixedよりも柔軟かつシンプルです。

### メリット

レイアウト調整が不要: fixedのようにコンテンツが重なったり、潜り込んだりする問題が発生しません。後続要素とのレイアウト調整がほとんど不要です。
親要素に依存した自然な挙動: 親要素の範囲内でのみ固定されるため、ビューポート全体を対象にしない分、意図しないレイアウト崩れが発生しにくいです。
ブラウザ互換性: ほとんどのモダンブラウザでサポートされており、レガシーブラウザを除けば安定して動作します。

### 落とし穴

ただし、position: stickyにも注意点があります。以下のようなケースでは、うまく動作しないことがあります。

親要素のoverflow設定: 親要素にoverflow: hiddenやoverflow: autoが指定されている場合、stickyは意図した通りに動作しません。このような場合は親要素のスタイルを確認し、必要に応じて修正する必要があります。
古いブラウザ対応: Internet Explorerなどの古いブラウザでは、position: stickyはサポートされていないため、別の解決策が必要になります。

## まとめ

position: fixedはシンプルに要素を固定できる一方、他の要素とのレイアウト調整が非常に手間になる場合があります。特に、隣接する要素がヘッダーやリンクバーの下に潜り込んでしまう問題は、stickyを使うことで回避できます。

position: stickyは親要素内でのみ固定されるため、スクロールに対して自然な動作を提供し、レイアウトの崩れを最小限に抑えることができる便利な手法です。新しいプロジェクトやリンクバーの実装時には、ぜひstickyを第一選択肢として検討してみてください。
