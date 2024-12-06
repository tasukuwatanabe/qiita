---
title: Astroでimportエイリアスを設定する方法
tags:
  - astro
  - vite
  - AdventCalendar2024
private: true
updated_at: '2024-12-06T15:45:43+09:00'
id: a76f9100e6f9b3a1e3a1
organization_url_name: null
slide: false
ignorePublish: false
---

この記事はHRBrain Advent Calendar 2024 14日目の記事です。

https://qiita.com/advent-calendar/2024/hrbrain

## はじめに

こんにちは。HRBrainでオウンドメディアやランディングページの開発を担当している渡邉です。

HRBrainでは、[サービスサイト](https://www.hrbrain.jp/)の開発にAstroを採用しています。

Astroは、高速なウェブサイト構築を可能にするフレームワークであり、サーバーサイドレンダリング（SSR）や静的サイト生成（SSG）を行うことができます。

この記事では、Astroプロジェクトでimportエイリアスを設定する方法と、設定する中で遭遇した問題への対処法について解説します。

https://docs.astro.build/ja/guides/imports/

## tsconfig.jsonでのエイリアス設定

TypeScriptでimportエイリアスを使用するには、`tsconfig.json`を編集します。

以下の設定例では、`@/*`形式のエイリアスを使用して、`src`配下のディレクトリを対象にしています。

```jsonc:tsconfig.json
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

`compilerOptions.baseUrl`が設定されていないと、エイリアスのパスが解決できないので注意して下さい。

パスを`"src/*"`のように設定することで、`src`配下に新しいディレクトリが増える場合でも、新たにエイリアス設定を追加する必要がありません。

```typescript:src/pages/index.astro
// 相対パスを使用したimport
import Button from '../../components/Button.astro';
import companyLogo from '../../assets/images/logo.png';
import someFunction from '../../utils/someFunction.ts';

// エイリアスを使用したimport
import Button from '@/components/Button.astro';
import companyLogo from '@/assets/images/logo.png';
import someFunction from '@/utils/someFunction.ts';
```

### エイリアスに関する注意点

`@types`のように、スラッシュを挟まない形式でエイリアス設定をすると、TypeScriptがそれをnpmパッケージの`@types`と誤認識し、以下のようなエラーが発生する場合があります。

```bash
Cannot find module '@types/...' or its corresponding type declarations.
```

この問題を回避するために、エイリアス名を`@/`から始めるように設定しています。

### CSSのurl()で発生するパス解決問題

2024年12月時点では、前述した設定方法だと、CSSの`url()`で画像パスを指定する際にエイリアスが正しく解決されないというバグが潜んでいました。

発生した環境は以下の通りです。

- Astro：v4.5.5
- Node：v20.17.0
- Package Manager：Yarn

https://github.com/withastro/astro/issues/9633

例えば、以下のようにCSSでエイリアスを使用すると、ビルド時にパスが解決されずエラーになりました。

```css
.image {
  background-image: url("@/images/example.png"); /* 解決されない */
}
```

この問題に対しては、`astro.config.mjs`でViteのエイリアスを設定することで、CSS内の`url()`でもエイリアスが解決されるようになりました。

```javascript:astro.config.mjs
import { fileURLToPath } from 'node:url' // 追加
import { defineConfig } from 'astro/config';

export default defineConfig({
  vite: {
    resolve: {
      alias: {
        '@/images': fileURLToPath(new URL('./src/assets/images', import.meta.url)) // 追加
      },
    },
  },
});
```

```css
.image {
  background-image: url("@/images/example.png"); /* 解決される */
}
```

この問題はAstro本体に起因するものであり、今後のアップデートで公式による解消が期待されます。

現時点ではこの方法を利用しつつ、公式の修正を待つことにしました。

## まとめ

Astroでimportエイリアスを設定する際のポイントと、CSSの`url()`におけるパス解決問題への対処方法を紹介しました。

1. `tsconfig.json`の`compilerOptions`にimportエイリアスを設定。
2. CSSの`url()`問題を回避するには、`astro.config.mjs`でViteのエイリアス設定を追加する。

これらを参考に、エイリアスを活用して効率的なプロジェクト運用を目指してください！

## PR

HRBrainではフロントエンドエンジニア（コミュニケーションデザイン）の採用も行なっているので、ぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit
