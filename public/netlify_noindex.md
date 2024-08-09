---
title: NetlifyでPDFや画像をnoindexに設定する方法
tags:
  - Netlify
private: false
updated_at: '2024-08-09T18:03:13+09:00'
id: 5c248645e245669e59a8
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

先日、Netlifyでホスティングしている特定のPDFファイルを検索エンジンのインデックスから除外したい、という依頼を受けました。

通常、HTMLファイルであれば、以下のようにmetaタグを設置することで簡単にnoindexに設定できます。

```html
<meta name="robots" content="noindex">
```

しかし、PDFファイルにはmetaタグを設置することができません。

そこでこの記事では、Netlifyで特定のページやリソース（PDF、動画、画像など）をmetaタグを使わずにnoindexに設定する方法を紹介します。

## レスポンスヘッダーのX-Robots-Tagでnoindexを設定する

結論から言うと、X-Robots-Tagというカスタムヘッダーを指定することで、この問題を解決できます。

```
X-Robots-Tag: noindex
```

X-Robots-Tagは、HTTPレスポンスヘッダーに設定することで、検索エンジンに対するインデックス指示を行うためのものです。

これを使うことで、metaタグによるnoindex設定と同様に、ページやリソースを検索エンジンのインデックスから除外することができます。

実は、この方法はGoogleの公式ドキュメントでも紹介されています。

https://developers.google.com/search/docs/crawling-indexing/block-indexing?hl=ja

<br>

ちなみに、Netlifyのdeploy-preview環境が検索エンジンにインデックスされないのは、deploy-preview環境に対して`x-robots-tag: noindex`がデフォルトで設定されているためです。

![netlify_deploy_preview_noindex.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/b1e15b1a-e777-4bbb-2a20-215c3a0f9fd5.png)

## netlify.tomlでの設定方法
Netlifyで特定のディレクトリやファイルにX-Robots-Tagを設定するには、netlify.tomlファイルを使用します。

### 手順1: netlify.tomlファイルの作成
プロジェクトのルートディレクトリにnetlify.tomlという名前のファイルを作成します。

### 手順2: HTTPレスポンスヘッダーの設定
netlify.tomlにカスタムヘッダーを設定するためのセクションを追加します。

以下のように記述することで、特定のディレクトリやファイルに`X-Robots-Tag: noindex`を指定できます。

```toml:netlify.toml
[[headers]]
  path = "/hoge/*"
  [headers.values]
    X-Robots-Tag = "noindex"

[[headers]]
  path = "/fuga/sensitive-file.pdf"
  [headers.values]
    X-Robots-Tag = "noindex"
```

上記の設定では、`/hoge/`ディレクトリ内のすべてのファイルと、`/fuga/sensitive-file.pdf`という特定のファイルにnoindexが適用されます。

### 手順3: デプロイ
設定が完了したら、netlify.tomlを保存してNetlifyにデプロイします。

指定したリソースにアクセスすると、ヘッダー情報に`X-Robots-Tag: noindex`が適用されていることが分かります。

これにより、検索エンジンのインデックスから除外されます。

![netlify_pdf_noindex.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/151f1ebd-72fd-9d22-fd28-c36abbd2b818.png)

もし、すでにページやファイルがGoogleにインデックスされている場合は、Google Search ConsoleからURLの削除を行なってください。

https://support.google.com/webmasters/answer/9689846?hl=ja

## まとめ

X-Robots-Tagというカスタムヘッダーを設定することで、Netlifyにホスティングした特定のページやリソースをnoindexに設定することが可能です。

検索エンジンに対して特定のコンテンツをインデックスしないように指示でき、SEO対策やコンテンツ管理で役に立ちます。

実装する際の参考にしてください。

## PR

HRBrainではコミュニケーションデザインエンジニア（ウェブ制作/フロントエンド）の採用も行なっているのでぜひ！

https://hrmos.co/pages/hrbrain/jobs/2110310

https://www.hrbrain.co.jp/recruit
