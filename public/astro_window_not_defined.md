---
title: Astroで「window is not defined」エラーを解消する方法
tags:
  - "Astro"
  - "React"
  - "Vue"
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Astro は、静的サイト生成やサーバーレンダリングを簡単に行えるモダンなフレームワークです。

しかし、Astro を使って開発を進めると、「window is not defined」というエラーに遭遇することがあります。

この記事では、このエラーの原因とその解消法について詳しく解説します。

## 「window is not defined」の意味

「window is not defined」というエラーは、`window`オブジェクトがサーバーサイドでは存在しないために発生します。

`window`オブジェクトはブラウザ環境（クライアントサイド）でのみ利用可能であり、サーバーサイドでは存在しません。

そのため、サーバーサイドで実行されるコードで`window`を参照しようとすると、このエラーが発生します。

## Astro はサーバーサイドで実行される

Astro は、初期描画をサーバーサイドで行い、必要な部分のみクライアントサイドで再度レンダリング（ハイドレーション）します。

この仕組みにより、パフォーマンス向上や SEO の最適化が期待できますが、クライアントサイドに依存するコードがサーバーサイドで実行されるとエラーを引き起こす可能性があります。

## 「window is not defined」の解消法

### 1. クライアントサイド限定で`window`を使用する

クライアントサイドでのみ`window`を使用するために、`typeof window !== 'undefined'`を使って、`window`が存在する場合のみコードを実行するようにします。

```js
if (typeof window !== "undefined") {
  console.log(window.innerWidth);
}
```

ただし、この方法ではサーバーサイドでのエラーを回避することはできますが、クライアントサイドで再度この処理が実行されるわけではありません。

根本的な解決をするためには他の手法が必要です。

### 2. Astro の`client:only`ディレクティブを使用する

Astro では、特定のコンポーネントをクライアントサイドでのみレンダリングするために、`client:only`ディレクティブを使用します。

これにより、サーバーサイドでは無視され、クライアントサイドでのみ実行されるようになります。

```astro:main.astro
<ParentComponent client:only="react" />
```

#### 子コンポーネントへの影響

`client:only`ディレクティブを使用すると、指定された親コンポーネントはサーバーサイドではレンダリングされず、クライアントサイドでのみ実行されます。

このとき、親コンポーネント内で使用している子コンポーネントも、クライアントサイドでレンダリングされます。

次のような React コンポーネントの構成の場合、`ParentComponent`とその中に含まれる`ChildComponent`はクライアントサイドでのみ実行されます。

```astro:main.astro
<!-- ParentComponentをクライアントサイドでのみ実行 -->
<ParentComponent client:only="react" />
```

```jsx:ParentComponent.jsx
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

```jsx:ChildComponent.jsx
import React from "react";

function ChildComponent() {
  return <p>Child Component</p>;
}

export default ChildComponent;
```

この場合、`ParentComponent`と`ChildComponent`はどちらもサーバーサイドではレンダリングされず、クライアントサイドでのみ実行されます。

### 3. Vue の`mounted`や React の`useEffect`を使って解消する方法

もう一つの解決策として、クライアントサイドでのみ実行されるライフサイクルフックを利用する方法があります。

Vue の`mounted`フックや React の`useEffect`を使えば、サーバーサイドでのエラーを回避しつつ、クライアントサイドで`window`を安全に参照することができます。

#### Vue での解消方法 (`mounted`フック)

Vue では、`mounted`ライフサイクルフックを使ってコンポーネントがクライアントサイドでレンダリングされた後に`window`を参照できます。

```vue
<template>
  <div>
    {{ windowWidth }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      windowWidth: 0,
    };
  },
  mounted() {
    // クライアントサイドでのみwindowを参照
    this.windowWidth = window.innerWidth;
  },
};
</script>
```

#### React での解消方法 (`useEffect`フック)

React では、`useEffect`フックを使ってクライアントサイドでのみ実行される処理を記述します。

これにより、サーバーサイドでのエラーを防ぎ、クライアントサイドで`window`を使用できます。

```jsx
import React, { useState, useEffect } from "react";

function WindowWidthComponent() {
  const [windowWidth, setWindowWidth] = useState(0);

  useEffect(() => {
    // クライアントサイドでのみwindowを参照
    setWindowWidth(window.innerWidth);
  }, []);

  return <div>{windowWidth}</div>;
}

export default WindowWidthComponent;
```

## まとめ

Astro で「window is not defined」というエラーが発生するのは、サーバーサイドで`window`オブジェクトが存在しないためです。

この問題を解決するためには、クライアントサイドでのみ`window`オブジェクトを参照する必要があります。

解決策として、`client:only`ディレクティブを使用してクライアントサイドでのみコンポーネントを実行するか、Vue の`mounted`や React の`useEffect`を利用して、クライアントサイドでのみ`window`を参照する方法があります。

特に、`client:only`を使用すると親コンポーネントと子コンポーネントの両方がクライアントサイドでのみ実行され、サーバーサイドでのエラーを完全に回避できます。
