---
title: astroで window is not defined エラーを解消する方法
tags:
  - Vue.js
  - React
  - astro
private: true
updated_at: '2024-09-05T18:02:56+09:00'
id: 44f6ad50efeae40267cd
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディアやランディングページの開発を担当している渡邉です。

HRBrainでは、[サービスサイト](https://www.hrbrain.jp/)の開発にastroを採用しています。

astroは、高速なウェブサイト構築を可能にするフレームワークであり、サーバーサイドレンダリング（SSR）や静的サイト生成（SSG）を行うことができます。

この記事では、astro開発で遭遇した `window is not defined` というエラーの解決策を分かりやすく解説します。

https://astro.build/

## astroはサーバーサイドで実行される

astroは、まずサーバーサイドでHTMLを生成し、それをブラウザに送信します。

その後、ブラウザ上でJavaScriptが実行され、イベントリスナーの登録やコンポーネントの状態管理などを行います。

これを「ハイドレーション」と呼びます。


## window is not defined エラーの原因

ブラウザには `window` というオブジェクトが用意されており、ブラウザのウィンドウに関する情報を持っています。

しかし、astroはサーバーサイドレンダリングを行うため、コードの一部はサーバー側で実行されます。

サーバーにはブラウザのウィンドウは存在しないため、windowオブジェクトも存在しません。

そのため、サーバー側で実行されるコードの中でwindowオブジェクトを使おうとすると、`window is not defined` エラーが発生します。

## window is not defined エラーの解消

以下のastroドキュメントにも記載されている方法ではありますが、コードを用いてより詳しく解説します。

https://docs.astro.build/ja/guides/troubleshooting/#%E3%82%88%E3%81%8F%E3%81%82%E3%82%8B%E5%8E%9F%E5%9B%A0

### client:only ディレクティブを使う

astroは、React、Vue、Svelteなど、様々なフレームワークとの連携（インテグレーション）をサポートしています。 

`client:only` ディレクティブを使用することで、これらのフレームワークをクライアントサイドでのみ実行することができます。 

例えば、以下のようにコンポーネントを `client:only` ディレクティブを使ってレンダリングすることができます。

```astro:src/pages/index.astro
<Component client:only="react" />   // React
<Component client:only="vue" />     // Vue
<Component client:only="svelte" />  // Svelte
```
<br>

https://docs.astro.build/ja/reference/directives-reference/#clientonly

#### client:only ディレクティブの効果

`client:only` ディレクティブを指定したコンポーネントは、そのコンポーネント自身だけでなく、そのコンポーネント内で使用されている子コンポーネントも、すべてクライアントサイドでのみ実行されるようになります。

例えば、以下のような構成の場合、`ParentComponent` と `ChildComponent` はどちらもクライアントサイドでのみ実行されます。

```astro:src/pages/index.astro
<ParentComponent client:only="react" />
```

```jsx:src/components/ParentComponent.jsx
import React from "react";
import ChildComponent from "./ChildComponent";

function ParentComponent() {
  return (
    <div>
      <h1>Parent Component</h1>
      <ChildComponent />
    </div>
  );
}

export default ParentComponent;
```

```jsx:src/components/ParentComponent.jsx
import React from "react";

function ChildComponent() {
  return <p>Child Component</p>;
}

export default ChildComponent;
```

### VueのonMountedやReactのuseEffectを使う

クライアントサイドでのみ実行されるライフサイクルイベントを利用する方法もあります。

Vue.jsでは `onMounted` 、Reactでは `useEffect` というライフサイクルイベントが用意されています。

- `onMounted` は、コンポーネントがブラウザにマウントされた後に実行されます。
- `useEffect` は、コンポーネントがブラウザにマウントされた後、そして必要に応じて更新後にも実行されます。

これらのイベントを使うことで、サーバー側でのエラーを防ぎつつ、クライアントサイドでwindowオブジェクトを安全に利用することができます。

#### Vueでの例 (onMountedフック)

```vue
<script setup>
import { ref, onMounted } from 'vue'

// ref()を使ってリアクティブな変数 currentPath を作成
const currentPath = ref(null)

onMounted(() => {
  // ref()で作成した変数の値を更新するには .value を使用
  currentPath.value = window.location.pathname;
})
</script>

<template>
  <div>
    現在のパス: {{ currentPath }}
  </div>
</template>
```

#### Reactでの例 (useEffectフック)

```jsx
import React, { useState, useEffect } from "react";

function CurrentPathComponent() {
  const [currentPath, setCurrentPath] = useState(null);

  useEffect(() => {
    setCurrentPath(window.location.pathname);
  }, []);

  return <div>現在のパス: {currentPath}</div>;
}

export default CurrentPathComponent;
```

## まとめ

astroで `window is not defined` というエラーが発生する原因は、サーバーサイドで windowオブジェクトが存在しないためです。

以下のいずれかの方法で解消することができます。

- `client:only` ディレクティブを使って、コンポーネントをクライアントサイドでのみ実行する
- Vue.jsの `onMounted` やReact `useEffect` を使って、コンポーネントがクライアントサイドでマウントされた後にwindowオブジェクトにアクセスする

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit
