---
title: Next.js + Contentfulでブログの目次を実装する
tags:
  - Next.js
  - contentful
  - QiitaEngineerFesta
  - QiitaEngineerFesta2024
private: true
updated_at: '2024-06-20T12:09:36+09:00'
id: 6bea0188e6e574cb2e2b
organization_url_name: null
slide: false
ignorePublish: false
---
## 概要

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

HRBrainでは、Next.js（TypeScript）とContentfulというHeadless CMSを使ってオウンドメディアを運営しています。

本記事では、Contentfulに入力したRich TextデータをNext.js側からAPI経由で取得し、目次を作成する方法をご紹介します。

![hruniv_toc.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/e3d14cfd-fb14-c041-89e3-d1db37c5336b.png)


## 手順

- ContentfulのAPIを使用してRich Textデータを取得（本記事では具体的なAPIリクエストは省略します）
- 必要なライブラリを追加
- Rich TextデータをHTMLに変換
- Rich Textデータから目次を生成
- コンポーネント側から呼び出す

## 必要なライブラリの追加

最初に、実装で必要となるContentful関連のライブラリをインストールします。

```
npm install @contentful/rich-text-types @contentful/rich-text-react-renderer
```

▼ContentfulのRich Textデータ用型ファイル

https://www.npmjs.com/package/@contentful/rich-text-types

▼ContentfulのRich TextデータをReactコンポーネントに変換するライブラリ

https://www.npmjs.com/package/@contentful/rich-text-react-renderer

## Rich TextをHTMLに変換

Contentfulから取得したRich Textデータは、以下のようにnodeデータが配列で提供されます。

![img_richtext_data.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/c09d335d-adaa-5760-acc7-b2ab0bdedfe2.png)

このRich Textデータを`@contentful/rich-text-react-renderer`ライブラリの`documentToReactComponents`関数を使用してHTMLに変換します。

また、目次の項目をクリックした際にページ内リンクでジャンプできるように、h2タグに対して`#heading-1` `#heading-2`という形式のID属性を付与します。

```tsx:articleBody.tsx
import { BLOCKS, Document } from '@contentful/rich-text-types'
import { documentToReactComponents } from '@contentful/rich-text-react-renderer'

type Props = {
  document: Document
}

export default function ArticleBody({ document }: Props): React.ReactElement {
  let headingIndex = 0

  const options = {
    renderNode: {
      [BLOCKS.HEADING_2]: (node) => <h2 id={`heading-${++headingIndex}`}>{node.content[0].value}</h2>
      [BLOCKS.HEADING_3]: (node) => <h3>{node.content[0].value}</h3>
      [BLOCKS.HEADING_4]: (node) => <h4>{node.content[0].value}</h4>
      [BLOCKS.HEADING_5]: (node) => <h5>{node.content[0].value}</h5>
    }
  }

  return <div class="post-content">{documentToReactComponents(document, options)}</div>
}
```

## Rich Textから目次を生成

Propsの`document`にRich Textが渡されるので、このデータを整形してulタグとして出力します。

- Rich Textからh2タグ（`heading-2`）をfilterで取得
- mapを使用してデータを整形
- アンカーリンクはページ内リンクになるように、ID属性へのリンクを付与

```tsx:tableOfContents.tsx
import { Text, Document } from '@contentful/rich-text-types'

type Props = {
  document: Document
}

export default function TableOfContents({ document }: Props): React.ReactElement {
  const headings = document.content
    .filter((content) => content.nodeType === 'heading-2')
    .map((content, index) => ({
        id: `heading-${++index}`,
        text: (content.content[0] as Text).value,
    }))

  return (
    <ul>
      {headings.map((heading) => (
        <li key={heading.id}>
          <a href={`#${heading.id}`}>{heading.text}</a>
        </li>
      ))}
    </ul>
  )
}
```

## 記事側で各コンポーネントを呼び出す

必要な箇所で目次と記事本文を呼び出します。

以下の例では、目次の後に記事本文が配置されるようにしています。

```tsx:Article.tsx
import { Document } from '@contentful/rich-text-types'
import TableOfContents from '@/components/TableOfContents'
import ArticleBody from '@/components/ArticleBody'

type Props = {
  post: {
    fields: {
      rich_text: Document
    }
  }
}

export default function Article({ post }: Props): React.ReactElement {
  return (
    <article>
      <TableOfContents document={post.fields.rich_text} />
      <ArticleBody document={post.fields.rich_text} />
    </article>
  )
}
```

## おまけ: ページ内リンクジャンプ後に見出しがheaderに隠れる場合

ページ内リンクでジャンプした後、見出しがheaderと重なる問題が発生する場合があります。

これを解決するために、遷移先の見出しに対して`scroll-margin-top`プロパティを指定します。

指定した値だけ、ズレた状態で見出しにジャンプすることができます。

```css
h2 {
  scroll-margin-top: 150px;
}
```

![img_scroll_margin.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/818d16d7-bcaa-6e5c-e6de-da311f0b4ff3.png)

ContentfulからRich Textデータを取得し、目次を生成するまでの順序を説明しました。

実装する際の参考にしてください。

## PR

HRBrainでは一緒に働く仲間を募集しています。歴史に残るトライをしよう！

https://www.hrbrain.co.jp/recruit
