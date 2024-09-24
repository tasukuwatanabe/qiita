---
title: '【CSS】position: sticky で固定ヘッダーを実装するメリットと実装例'
tags:
  - 'HTML'
  - 'CSS'
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: true
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

Webサイト制作でよく使う固定ヘッダー。`position: fixed` で実装することが多いですが、他の要素と重なってしまう問題が発生することがあります。

そこで今回は、 `position: sticky` を使った固定ヘッダー実装と、そのメリットについて解説します。

https://developer.mozilla.org/ja/docs/Web/CSS/position

## position: fixed の問題点

`position: fixed` は、要素をビューポート（ブラウザの表示領域）に対して固定表示する際に便利なプロパティです。

しかし、 `position: fixed` を使用すると、固定された要素が他の要素の上に重なって表示されてしまうため、レイアウトが崩れたり、コンテンツが隠れてしまうといった問題が発生する可能性があります。

例えば、以下のようなHTML構造とCSSスタイルシートを考えてみましょう。

<p class="codepen" data-height="500" data-theme-id="dark" data-default-tab="css,result" data-slug-hash="MWNWqMx" data-pen-title="Header position fixed" data-editable="true" data-user="tasukuwatanabe" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/MWNWqMx">
  Header position fixed</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

上記の例では、ヘッダーを画面上部に固定表示するために `position: fixed` を使用しています。

しかし、ヘッダーが固定されたことにより、メインコンテンツがヘッダーの下に隠れてしまうため、 `margin-top` でコンテンツを下にずらす調整が必要になります。

<p class="codepen" data-height="500" data-theme-id="dark" data-default-tab="css,result" data-slug-hash="mdNdzmp" data-pen-title="Header position fixed - margin" data-editable="true" data-user="tasukuwatanabe" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/mdNdzmp">
  Header position fixed - margin</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

この `margin-top` の値は、ヘッダーの高さに合わせて調整する必要があり、ヘッダーの高さが変更された場合は、 `margin-top` の値も変更する必要があり、メンテナンスが煩雑になる可能性があります。

例えば、以下のように、ページ最上部に他の要素を追加する場合なども、`margin-top` を再調整するきっかけになります。

![header_position_fixed_trouble.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/0767d803-1a55-8319-2698-9e53a5cb40ab.png)

## position: sticky で実現する固定ヘッダー

`position: sticky` を使用すると、スクロール位置に応じて要素を固定するかどうかの挙動を制御できます。

初期状態では通常の要素と同じように配置されますが、スクロール位置が指定した閾値に達すると、その位置で固定表示されるようになります。

`position: sticky` を使用することで、 `position: fixed` のように要素が他の要素の上に重なって表示されることを防ぎ、自然なスクロール動作を実現できます。

先ほどのHTML構造を `position: sticky` を使用して書き換えてみましょう。

<p class="codepen" data-height="500" data-theme-id="dark" data-default-tab="css,result" data-slug-hash="abebRbz" data-pen-title="Header position sticky" data-user="tasukuwatanabe" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/abebRbz">
  Header position sticky</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

上記の例では、ヘッダーに `position: sticky` と `top: 0` を指定することで、ヘッダーが画面上部に固定表示されるようになります。

`position: sticky` を使用することで、 `position: fixed` のようにコンテンツを下にずらすための `margin-top` の設定が不要になり、コードが簡潔になるだけでなく、レイアウトの崩れも防ぐことができます。

## まとめ

本記事では、 `position: sticky` を使用した固定ヘッダーの実装方法を紹介しました。

`position: sticky` は `position: fixed` よりも柔軟性があり、固定ヘッダーの実装に最適なので、ぜひ使ってみてください。

## PR

HRBrainではフロントエンドエンジニア（コミュニケーションデザイン）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>