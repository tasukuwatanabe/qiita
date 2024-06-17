---
title: Contentful & Next.jsでブログ記事の目次を作成する
tags:
  - 'Contentful'
  - 'Next.js'
  - 'QiitaEngineerFesta'
  - 'QiitaEngineerFesta2024'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
## 概要

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

HRBrainでは、Next.js(TypeScript) & ContentfulなHeadless CMSでオウンドメディアを運営しています。

Contentfulで入力したRich TextをNext.js側で取得し、目次を生成したので、その時の記録です。

## 

## ライブラリの追加

最初に、実装で必要となるContentful関連のライブラリを2つインストールしておきます。

```
npm install @contentful/rich-text-types @contentful/rich-text-react-renderer
```

▼ContentfulのRich text用型ファイル

https://www.npmjs.com/package/@contentful/rich-text-types

▼ContentfulのRich textをReactプロジェクト上で出力するためのライブラリ

https://www.npmjs.com/package/@contentful/rich-text-react-renderer

## 手順

- フロントエンドのNext.jsからContentfulに対してAPIを叩き、記事データ(Rich text)を受け取る
- Rich textデータをHTMLに変換する

## Rich textをHTMLに変換する

ContentfulのRich textデータは、以下のような形式になっています。

![img_richtext_data.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/c09d335d-adaa-5760-acc7-b2ab0bdedfe2.png)

この配列状にまとまったnodeデータを`@contentful/rich-text-react-renderer`ライブラリの`documentToReactComponents`という関数を通すことにより、HTMLデータに変換することができます。

目次クリックで該当箇所までページ内リンクでジャンプできるよう、`#heading-1` `#heading-2`という形式でID属性を付与しています。

```tsx:richText.tsx
import { BLOCKS } from '@contentful/rich-text-types'
import { documentToReactComponents } from '@contentful/rich-text-react-renderer'

type Props = {
  document: Document
}

export default function RichText({ document }: Props): React.ReactElement {
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

## Rich text形式の記事データから目次を生成する
```tsx:tableOfContents.tsx
import { BLOCKS, Document } from '@contentful/rich-text-types'

type Props = {
  document: Document
}

export default function TableOfContents({ document }: Props): React.ReactElement {
  const headings = document.content
    .filter((content) => content.nodeType === 'heading-2')
    .map((content, index) => (
      {
        id: `heading-${++index}`,
        text: content.content[0].value,
      }
    ))

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
```tsx:Article.tsx
import TableOfContents from '@/components/TableOfContents'
import RichText from '@/components/RichText'

export default function Article({ post }: Props): React.ReactElement {
  return (
    <article>
      <TableOfContents document={post.fields.main_text} />
      <RichText document={post.fields.main_text} />
    </article>
  )
}
```

## ページ内リンクジャンプ後に見出しがheaderに隠れる場合

```css
h2 {
  scroll-margin-top: 150px;
}
```

![img_scroll_margin.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/818d16d7-bcaa-6e5c-e6de-da311f0b4ff3.png)
