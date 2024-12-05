---
title: Astroでimportエイリアスを設定する方法
tags:
  - 'Astro'
private: true
updated_at: ''
id: null
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディアやランディングページの開発を担当している渡邉です。

HRBrainでは、[サービスサイト](https://www.hrbrain.jp/)の開発にAstroを採用しています。

Astroは、高速なウェブサイト構築を可能にするフレームワークであり、サーバーサイドレンダリング（SSR）や静的サイト生成（SSG）を行うことができます。

この記事では、Astroプロジェクトでimportエイリアスを設定する方法について解説します。

https://docs.astro.build/ja/guides/imports/

## tsconfig.jsonでのエイリアス設定

TypeScriptでimportエイリアスを使用するには、`tsconfig.json`を編集します。以下の設定例では、`@/*`形式のエイリアスを使用して、`src`ディレクトリ全体を対象にしています。

```json:tsconfig.json
{
  "extends": "astro/tsconfigs/strict",
  "compilerOptions": {
    "baseUrl": ".", // 追加
    "paths": {
      "@/*": ["src/*"] // 追加
    }
  }
}
```

この設定により、src配下の任意のファイルを簡潔に参照できるようになります。

```tsx
// 相対パスを使用したimport
import Button from "../../components/Button";

// エイリアスを使用したimport
import Button from "@/components/Button";
```

## CSSでエイリアスを使用する

CSSやSassでも同じエイリアスを使用するには、Astroの`astro.config.mjs`でViteのエイリアス設定を追加します。


```javascript:astro.config.mjs
import { defineConfig } from 'astro/config';

export default defineConfig({
  vite: {
    resolve: {
      alias: {
        '@/*': '/src/*',
      },
    },
  },
});
```

```scss
@import "@/styles/variables.scss";

.button {
  color: $primary-color;
}
```

### エイリアスに関する注意点

エイリアスを`@types`のような名前を設定すると、TypeScriptがそれをnpmパッケージの`@types`と誤認識し、以下のようなエラーが発生する場合があります。

```bash
Cannot find module '@types/...' or its corresponding type declarations.
```

この問題を回避するためには、エイリアス名を`@/`から始めるように設定するのがおすすめです。たとえば、`@/types`や`@/assets`のように命名すれば、衝突を防ぐことができます。

## まとめ

Astroでimportエイリアスを設定する際は、以下のポイントを押さえましょう。

1. `tsconfig.json`で`@/*`形式のエイリアスを設定。
2. `astro.config.mjs`でViteのエイリアスを設定し、CSSやSassでも利用可能に。
3. `@/types`のような形式にすることでnpmパッケージとの衝突を避ける。

これらの設定により、JavaScript/TypeScriptとCSSの両方で統一的なエイリアスが利用でき、開発効率が向上します。ぜひプロジェクトに導入してみてください！

## PR

HRBrainではフロントエンドエンジニア（コミュニケーションデザイン）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit