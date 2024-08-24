---
title: ファイルのリネームがgitで差分認識されない？git mvを使おう
tags:
  - Git
private: true
updated_at: '2024-08-23T18:35:34+09:00'
id: 3f7324c0b33fe5584f89
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

先日、ファイル名を変更した際に、変更が差分としてGitに認識されないということがありました。

この記事では、Gitが差分を見逃す原因と、その対処法について解説しています。

## ファイル名の大文字と小文字を変更するケース

Gitプロジェクトの中で、`filerename.txt` を `fileRename.txt` にリネームしてみました。

しかし、ローカル環境ではファイル名が変更されていたにもかかわらず、Gitのステージングエリアにはその変更が反映されていません。

```bash
$ mv filerename.txt fileRename.txt
$ ls
fileRename.txt
$ git status
On branch main
nothing to commit, working tree clean
```

### なぜGitがリネームを見逃すのか？

それぞれのOSには何かしらのファイルシステムが使用されていますが、ファイルシステムの種類によっては、Gitがリネームを認識しない場合があります。

たとえば、MacOSで使用されているAPFS（※）やHFS+というファイルシステムがありますが、これは**ケースインセンシティブ**であり、大文字・小文字を区別しません。

※ 設定によりケースセンシティブへの変更が可能ですが、ここでは割愛します。

参考：[Macのディスクユーティリティで利用できるファイルシステムフォーマット - Apple サポート (日本)](https://support.apple.com/ja-jp/guide/disk-utility/dsku19ed921c/mac)

## 解決方法1： `git mv` を利用する

この問題を避けるには、ファイルをリネームする際に `git mv` コマンドを使うことです。

```bash
git mv filerename.txt fileRename.txt
```

このコマンドを使うことで、Gitはファイルのリネームを正しく認識し、差分として記録します。

```bash
$ git mv filerename.txt fileRename.txt
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	renamed:    filerename.txt -> fileRename.txt
```

参考：[Git - git-mv Documentation](https://git-scm.com/docs/git-mv)

## 解決方法2: git configの設定変更

```bash
git config core.ignorecase false
```

このコマンドは、Gitがファイル名の大文字・小文字を区別するように設定を変更します。

```bash
$ mv filerename.txt fileRename.txt
$ gst
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	fileRename.txt
```

この設定により、リネームが検知されるようになります。

ただし、リポジトリ内に`text.txt`と`Text.txt`などのようなファイルが共存する場合には、競合が発生する可能性があります。

また、プロジェクトのメンバー同士で設定を統一するなど、チーム全体での統一が必要になります。

参考：[Git - git-config Documentation](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreignoreCase)

## まとめ

ファイル名を変更するだけの単純な操作でも、ファイルシステムの違いによって差分として検出されないことがあります。

ファイル名の大文字・小文字を変更する場合は、`git mv` コマンドを使用することで、確実にリネームをGitに認識させることができます。

## PR

HRBrainでは一緒に働く仲間を募集しています。歴史に残るトライをしよう！

https://www.hrbrain.co.jp/recruit
