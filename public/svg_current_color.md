---
title: SVGに対してCSSのcolor指定で色が変わらない時の解決策
tags:
  - HTML
  - CSS
  - SVG
private: true
updated_at: '2024-08-15T23:45:06+09:00'
id: 99ba6476d550edfead08
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

SVGは、ベクター形式の画像データで、拡大縮小しても画質が劣化しないため、Webサイトで頻繁に使用されています。

SVGに色を指定する際には、fill属性を使用しますが、CSSから色を変更しようとした際に、なかなか色が変わらなくてハマりかけました。

この記事では、その解決策をご紹介します。

## CSSのfillプロパティが効かない問題

以下のように`#000`などのカラーコードを直接fill属性に記述した場合、CSSのcolorプロパティで色を変更しても、`#000`で上書きされてしまい、図形の色が変わりません。

```html
<div class="square">
  <svg>
    <rect width="100" height="100" fill="#000" />
  </svg>
</div>

<style>
  .square {
    color: red; // 効きません
  }
</style>
```

<p class="codepen" data-height="350" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="vYqpXOo" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 350px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/vYqpXOo">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

これは、fill属性に直接色を指定することで、その色が固定されてしまい、CSSのcolorプロパティによる変更が適用されなくなるためです。

## 解決策：fill属性に対してcurrentColorを指定する

この問題を解決するには、fill属性に`currentColor`という値を指定します。

`currentColor`は、SVGのfill属性やstroke属性に指定できる特別な値で、**要素の親要素のcolorプロパティで設定された色を継承します。**

例えば、以下のように親要素に`color: red;`を指定した場合、SVGの`rect`要素は赤色になります。

```html
<div class="square">
  <svg>
    <rect width="100" height="100" fill="currentColor" />
  </svg>
</div>

<style>
  .square {
    color: red;
  }
</style>
```

<p class="codepen" data-height="350" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="WNqdGGp" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 350px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/WNqdGGp">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### currentColorの挙動

`currentColor`は、要素の親要素のcolorプロパティの色を継承します。

ただし、親要素にcolorプロパティが指定されていない場合は、ブラウザがデフォルトで設定している色が使用されます。

https://developer.mozilla.org/ja/docs/Web/SVG/Attribute/color

<br>

また、`currentColor`は、fill属性だけでなく、stroke属性にも使用できます。

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="jOjYMyL" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/jOjYMyL">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## まとめ

CSSのcolorプロパティを変えてもSVGの色が変わらない場合は、fill属性に`currentColor`を指定してみてください。

`currentColor`は、親要素のcolorプロパティの色を継承するため、CSSで柔軟に色を変更することができます。

SVGは色々な属性があるため複雑に感じるかもしれませんが、実際に使って、その便利さを実感してみてください。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっています。

https://hrmos.co/pages/hrbrain/jobs/2110310

https://hrmos.co/pages/hrbrain/jobs/23020910

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
