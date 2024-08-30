---
title: SVGの色が変わらない？CSSで色を操作する方法
tags:
  - HTML
  - CSS
  - SVG
private: false
updated_at: '2024-08-30T18:30:32+09:00'
id: 99ba6476d550edfead08
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

先日、SVGの色をCSSで変更しようとした際に、なかなか色が変わらなくて困ってしまいました。

この記事では、SVGの色がCSSで変更できない場合の原因と、2つの解決策をわかりやすく解説します。

## SVGの色を変更できない原因

fill属性に直接色が指定されていると、その色が固定されてしまい、CSSのcolorプロパティによる変更が適用されなくなります。

```html
<div class="parent">
  <svg>
    <rect width="100" height="100" fill="#000" />
  </svg>
</div>

<style>
  .parent {
    color: red; // 効きません
  }
</style>
```

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="vYqpXOo" data-pen-title="svg_fill_bad" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/vYqpXOo">
  svg_fill_bad</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## 解決策1：fill属性にcurrentColorを指定する

この問題を解決するには、fill属性に`currentColor`という値を指定します。

`currentColor`は、SVGのfill属性やstroke属性に指定できる特別な値で、**親要素のcolorプロパティに設定された色を継承します。**

https://developer.mozilla.org/ja/docs/Web/SVG/Attribute/color

<br>

例えば、以下のように親要素に`color: red;`を指定した場合、SVGのrect要素は赤色になります。

```html
<div class="parent">
  <svg>
    <rect width="100" height="100" fill="currentColor" />
  </svg>
</div>

<style>
  .parent {
    color: red;
  }
</style>
```

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="WNqdGGp" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/WNqdGGp">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### currentColorを指定して色操作する利点

CSSのcolorプロパティでSVGの色を操作する利点として、併用するテキストなどと一緒に色操作が行える点です。

SVGのアイコンは以下のようにテキストと一緒に使うケースが少なくありません。

そのような場合に、親要素でcolorプロパティを設定することで、子要素の色を連動して操作することができます。

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="yLdpzyR" data-pen-title="svg_in_button" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/yLdpzyR">
  svg_in_button</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## 解決策2：CSSのfillプロパティで色を指定する

CSSのfillプロパティで色を指定する方法もあります。

```html
<svg>
  <rect width="100" height="100" />
</svg>

<style>
  svg rect {
    fill: red;
  }
</style>
```

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="rNEpGoV" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/rNEpGoV">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

<br>

以前は頻繁にfillプロパティを使ってSVGの色指定をしていたのですが、`fill="currentColor"`の存在を知ってからは、あまり使わなくなりました。

この指定方法は、SVG内の個々の要素に異なる色を設定したい場合などに適しています。

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="OJezjeg" data-pen-title="Untitled" data-editable="true" data-user="tasukuwatanabe" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/tasukuwatanabe/pen/OJezjeg">
  Untitled</a> by Tasuku Watanabe (<a href="https://codepen.io/tasukuwatanabe">@tasukuwatanabe</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## まとめ

SVGの色をCSSで操作する場合、以下のような方法が挙げられます。

- `fill="currentColor"`を指定してcolorプロパティで操作する方法
- CSSのfillプロパティで色を操作する方法

特に、currentColorは親要素のcolorプロパティの色を継承するため、テキストと連動して色を変更することができ、非常に便利です。

SVGは色々な属性があるため複雑に感じるかもしれませんが、実際に使ってみて、その便利さを実感してみてください。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
