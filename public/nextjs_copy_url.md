---
title: URLコピーボタンと吹き出しをNext.jsで実装する
tags:
  - Next.js
  - QiitaEngineerFesta
  - QiitaEngineerFesta2024
private: false
updated_at: '2024-07-20T10:19:57+09:00'
id: bb4c97da0891da3e3da9
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

この記事では、Next.jsプロジェクトにURLコピーボタンを実装し、さらにユーザー体験を向上させるために吹き出し（ツールチップ）を追加する方法を紹介します。

![copy_url.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/837d779f-7064-78a5-3af1-98b5689d5c06.gif)

:::note info
吹き出し（ツールチップ）について
ボタンをクリックした際に表示される吹き出しを「ツールチップ」と呼んでいます。
一般的には、ボタンなどの要素にホバーやクリックした時に出現する脚注や説明を指すそうです。
:::

## 実装手順

### コンポーネントの作成

`CopyUrlButton`コンポーネントを作成します。

```react
import React, { useState } from 'react';
import { useRouter } from 'next/router';

const CopyUrlButton = () => {
  // ツールチップの表示・非表示を制御するstate
  const [displayTooltip, setDisplayTooltip] = useState(false);
  const router = useRouter();

  const currentUrl = `${process.env.NEXT_PUBLIC_BASE_URL}${router.pathname}`

  const handleCopy = async () => {
    try {
      // 現在のURLをクリップボードにコピー
      await navigator.clipboard.writeText(currentUrl);

      setDisplayTooltip(true);
      setTimeout(() => {
        setDisplayTooltip(false);
      }, 2000); // 2秒後にメッセージを消す
    } catch (err) {
      console.error('URLのコピーに失敗しました');
    }
  };

  return (
    <div class="wrap">
      <div className='tooltip' style={{ opacity: displayTooltip ? 1 : 0 }}>URLをコピーしました</div>
      <button onClick={handleCopy} class="button">URLをコピーする</button>
    </div>
  )
};

export default CopyUrlButton;
```

### ツールチップのスタイル実装

ツールチップのスタイルを実装します。

```scss
.wrap {
  position: relative;

  .tooltip {
    color: #fff;
    font-size: 12px;
    padding: 8px 8px 9px;
    background-color: #000;
    border-radius: 6px;
    white-space: nowrap; // テキストを改行しない
    transition-duration: 0.2s; // ふわっと表示

    // 以下は親要素に対する配置
    position: absolute;
    top: -45px;
    left: 50%;
    transform: translateX(-50%);

    // ツールチップ下部の三角形部分
    &::after {
      content: '';
      width: 12px;
      height: 8px;
      background-color: #000;
      clip-path: polygon(0 0, 100% 0%, 50% 100%);

      // ツールチップに対する配置
      position: absolute;
      bottom: -7px;
      left: 50%;
      transform: translateX(-50%);
    }
  }

  .button {
    // ボタンのCSSはお好みで
  }
}
```

### clip-pathを使った三角形の作成

ツールチップ下部の三角形部分には、`clip-path`を使用しています。

`clip-path`は、要素の形状を定義するためのCSSプロパティです。`polygon()`関数を用いることで、任意の多角形を定義できます。

`polygon(0% 0%, 100% 0%, 50% 100%)`は、左上、右上、中央下の3点を結んだ三角形を表します。

```css
.triangle {
  width: 100px;
  height: 70px;
  background-color: #000;
  clip-path: polygon(0% 0%, 100% 0%, 50% 100%);
}
```

![clip_path_triangle.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/1d8276b3-0742-fd98-def3-f074cf58d8db.png)


▼MDN

https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape/polygon

▼caniuse

IE以外のブラウザでサポートされています。

https://caniuse.com/css-clip-path

## まとめ

Next.jsプロジェクトにURLコピーボタンを実装することができました。

ユーザーが簡単に現在のページのURLをコピーできるようにすることで、ソーシャルメディアへの共有やブックマークを促進することができます。

実装する際の参考にしてください。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっています。

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit
