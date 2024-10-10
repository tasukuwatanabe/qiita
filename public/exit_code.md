---
title: プログラムにおける終了コードとは？その役割とNode.jsでの活用例
tags:
  - ShellScript
  - Node.js
  - shell
private: false
updated_at: '2024-10-10T10:08:18+09:00'
id: da8e71e3427c9febddc4
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

先日、終了コードを真偽値と誤認したことが原因で、ハマりかけました。

終了コードは、プログラムが終了する際にOSへ返す数値であり、その値によってプログラムの実行結果を表しています。

この記事では、終了コードの役割と、Node.jsでの具体的な活用例を解説します。

## 終了コードを真偽値と混同してはいけない

プログラムの終了コードは `0` が成功、`0以外` がエラーや失敗を表します。

これは多くのプログラミング言語やOSで採用されている標準的な仕様です。

しかし、直感的には `0` はfalseや失敗を示し、`1` はtrueや成功を意味すると思いがちです。これが誤認の原因となります。

- 終了コード
  - `0` → 成功
  - `1` やそれ以外 → 失敗
- 真偽値（一般的な解釈）
  - `0` → false
  - `1` → true

## 終了コードの特徴と一般的な値

終了コードには、以下のような特徴があります。

1. 終了コード `0` はプログラムの正常終了を、`0` 以外の値はエラーや異常終了を表す
1. 異常終了を表す終了コードは、エラーの種類に応じて使い分けられる
1. 終了コードによって後続の処理を分岐させるなどの使い方がある

広く使われている、終了コードの一般的な値は以下のとおりです。

|終了コード|意味|
|--|--|
|0|正常終了|
|1|一般的なエラー|
|2|シェル組み込み関数の誤用|
|126|コマンドの実行権限がない場合|
|127|コマンドが見つからない場合|
|128|exitへの無効な引数|
|130|Ctrl+Cによる中断で返される|
|255|終了コードが範囲外の値|

https://tldp.org/LDP/abs/html/exitcodes.html

## Node.jsでの終了コードの扱い方

Node.jsでは、`process.exit()` メソッドを使って明示的に終了コードを指定できます。

```javascript
// 正常終了
process.exit(0);

// 一般的なエラー
process.exit(1);
```

## Node.jsでの活用例

### ファイル操作

ファイル操作の成功・失敗に応じて終了コードを返す例です。

```javascript:file_operation.js
const fs = require('fs');

fs.readFile('somefile.txt', 'utf8', (err, data) => {
  if (err) {
    console.error("ファイルの読み込みに失敗しました:", err);
    process.exit(1);
  } else {
    console.log("ファイルの内容:", data);
    process.exit(0);
  }
});
```

### HTTPリクエスト

HTTPリクエストの成否に応じて終了コードを返す例です。

```javascript:http_request.js
const https = require('https');

https.get('https://www.hrbrain.jp', (res) => {
  if (res.statusCode === 200) {
    console.log("HTTPリクエスト成功");
    process.exit(0);
  } else {
    console.error("HTTPリクエスト失敗:", res.statusCode);
    process.exit(1);
  }
}).on('error', (err) => {
  console.error("HTTPリクエストエラー:", err);
  process.exit(1);
});
```

### シェルスクリプトとの連携

Node.jsのプログラムをシェルスクリプトから実行し、終了コードによって後続処理を分岐させる例です。

```bash:exit_code.sh
#!/bin/bash

node http_request.js

if [ $? -eq 0 ]; then
  echo "プログラムは正常に終了しました"
else
  echo "プログラムはエラーで終了しました"
fi
```

上記の例では、シェルスクリプト内で `http_request.js` を実行しています。

URL設定を書き換えてHTTPリクエストの成否を操作することで、出力結果を変化させることができます。

```bash
# 設定したURLがレスポンスコード200を返す場合
$ sh exit_code.sh
HTTPリクエスト成功
プログラムは正常に終了しました

# 無効なURLを指定した場合や、レスポンスコード200以外が返る場合
$ sh exit_code.sh
HTTPリクエストエラー
プログラムはエラーで終了しました
```

## まとめ

終了コードは、`0`~`255`までの数値で表され、その番号に応じてエラーの内容を表現しています。

頻繁に遭遇する、真偽値としての `1` や `0` とは意味合いが異なるので、混同しないように注意しましょう！

## PR

HRBrainでは積極的に採用を行なっているのでぜひ！

https://www.hrbrain.co.jp/recruit
