---
title: Keeper Commander CLI を使って環境変数をチームで安全に共有しよう！
tags:
  - Security
  - Keeper
private: false
updated_at: '2024-09-12T10:48:11+09:00'
id: 5569398ed253db3a4f27
organization_url_name: hrbrain
slide: false
ignorePublish: false
---

## はじめに

こんにちは。HRBrainでオウンドメディア・ランディングページの開発を担当している渡邉です。

チーム開発において、APIキーやデータベース接続情報など、機密性の高いデータを安全に共有することは重要な課題です。

HRBrainでは、パスワード管理ツールを「1Password」から「**Keeper**」に切り替えました。

それに伴い、私たちのチームも「1Password CLI」から「**Keeper Commander CLI**」へ移行し、開発に関わる環境変数の共有に活用しています。

この記事では、Keeper Commander CLIを使ってチーム開発で環境変数を安全に管理・共有する方法をご紹介します。

https://docs.keeper.io/jp

## 環境変数をKeeperに保存する方法

### カスタムフィールドを使用

環境変数をKeeperに保存する方法として、2つのアプローチを考えました。

1. **.envファイルをそのままKeeperにアップロードする方法**
   - ファイル自体をKeeperに保存することは可能ですが、ファイル内の情報は**検索対象外**となります。

2. **カスタムフィールドを利用する方法**
   - 環境変数をカスタムフィールドとして保存することで、Keeperの検索機能を活用して必要な情報を迅速に取得可能です。

そのため、検索の利便性を重視し、**2. カスタムフィールドに環境変数を保存する方法**を採用しました。

![keeper_custom_field.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/7149aa77-3a16-5d4a-44fa-cd3ac2551d5a.png)

## Keeper Commander CLIとjqのインストール

### Keeper Commander CLIのインストール

Keeper Commander CLIは、Pythonパッケージとして簡単にインストール可能です。

詳細なインストール手順は以下の公式ドキュメントで紹介されています。

https://docs.keeper.io/jp/v/secrets-manager-jp/commander-cli/commander-installation-setup

<br>

私の環境はmacOSなので、ここではmacOSでのインストール手順を紹介します。

以下、インストールのためのコマンドです。

まずはpip3コマンドがインストールされていることを確認します。

```bash
$ pip3 --version

pip 24.2 from /opt/homebrew/lib/python3.12/site-packages/pip (python 3.12)
```

次に、pip3を使ってKeeper Commanderをインストールします。

ここで、私の環境ではエラーが出てしまい、インストールができませんでした。

```bash
$ pip3 install keepercommander

error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try brew install
    xyz, where xyz is the package you are trying to
    install.

    If you wish to install a Python library that isn't in Homebrew,
    use a virtual environment:

    python3 -m venv path/to/venv
    source path/to/venv/bin/activate
    python3 -m pip install xyz

    If you wish to install a Python application that isn't in Homebrew,
    it may be easiest to use 'pipx install xyz', which will manage a
    virtual environment for you. You can install pipx with

    brew install pipx
```

そのため、指示にある通り、brewでpipxをインストールし、次にpipxコマンドを使ってKeeper Commanderをインストールしました。

2024年11月現在、Python v3.13以上だとエラーになるため、v3.12をオプションで指定しています。

```bash
$ brew install pipx
$ pipx install keepercommander --python python3.12

  installed package keepercommander 16.11.13, installed using Python 3.12.6
  These apps are now globally available
    - keeper
done! ✨ 🌟 ✨
```

Keeper Commanderがインストールできたことを確認します。

```bash
$ keeper version -v

   Commander Version: 16.11.13 (Current version)
  API Client Version: c16.11.0
      Python Version: 3.12.6 (main, Sep  6 2024, 19:03:47) [Clang 15.0.0 (clang-1500.1.0.2.5)]
    Operating System: macOS(22.6.0)
   Working directory: /Users/yourname/Dev/qiita
   Package directory: /Users/yourname/.local/pipx/venvs/keepercommander/lib/python3.12/site-packages/keepercommander
        Config. File: /Users/yourname/.keeper/config.json
          Executable: /Users/yourname/.local/pipx/venvs/keepercommander/bin/python
```

`keeper shell` コマンドで、Keeper Commanderを対話モードで起動します。

```bash
$ keeper shell

(省略)

Navigate to SSO Login URL with your browser and complete login.
Copy a returned SSO Token into clipboard.
Paste that token into Commander
NOTE: To copy SSO Token please click "Copy login token" button on "SSO Connect" page.

  a. SSO User with a Master Password
  c. Copy SSO Login URL to clipboard
  o. Navigate to SSO Login URL with the default web browser
  p. Paste SSO Token from clipboard
  q. Quit SSO login attempt and return to Commander prompt
Selection:
```

ログイン方法の選択が求められるので、自分の環境に応じた方法を選択してください。

私は法人SSOログインを使用しているため `o` を選択し、`SSO Token`をコピーした上で、`p`でトークンを入力しています。

![sso_connect.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/b5b35f8e-bc95-d370-e30b-848d47c94611.png)

```
Selection: p
Successfully authenticated with SSO Login
Syncing...
Decrypted [81] record(s)
My Vault>
```

保管庫にログインが成功したら、`ls` を叩いてみてください。あなたが保存しておいた認証アイテムが表示されれば成功です！

```
My Vault> ls
（ここにあなたが保存した認証アイテムが表示されれば成功です）
```

### jqのインストール

環境変数を整形して出力するために、JSON形式のデータを処理できる **jq** が必要です。

jqがインストールされていない場合、以下のページを参考にインストールを行なってください。

https://jqlang.github.io/jq/download/

<br>

ちなみにmacOSでHomebrewをお使いの方であれば、以下のコマンドなどでインストールできます。

```bash
brew install jq
```

## 環境変数を取得する方法

Keeperに保存した環境変数を取得するために、以下のコマンドを実行します。

```bash
keeper get [UID] --format json | jq -r '.custom[] | .label + "=" + .value[]' > .env
```

|コマンド|意味|
|--|--|
|**keeper get [UID]**|Keeperに保存された指定のレコードを取得します。UIDは各レコードに割り当てられた一意の識別子です。|
|**--format json**|出力をJSON形式で整形します。|
|**jq -r '.custom[] \| .label + "=" + .value[]'**|JSONデータを`label=value` 形式で整形します。|
|**> .env**|jqコマンドにより出力した値を `.env` ファイルに書き込みます。|

UIDはKeeperアイテムの `i` マークから確認ができます。

![keeper_uid.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/b88bacbd-8e2e-5ecb-475d-2e03ff0a85b0.png)

### 実行例

UIDが `abcde12345` のアイテムであれば、以下のようなコマンドになります。

```bash
keeper get abcde12345 --format json | jq -r '.custom[] | .label + "=" + .value[]' > .env
```

環境変数がカスタムフィールドに保存されている場合、JSONは以下の形式で取得されます。

```json
{
  "custom": [
    {
      "label": "DB_USERNAME",
      "value": ["dbusername"]
    },
    {
      "label": "DB_HOST",
      "value": ["dbhost"]
    }
    {
      "label": "DB_PASSWORD",
      "value": ["dbpassword"]
    }
  ]
}
```

これに対して、jqコマンドで整形した上で、`.env` に書き込みを行うと、以下のような形式で出力されます。

```.env
DB_USERNAME=dbusername
DB_HOST=dbhost
DB_PASSWORD=dbpassword
```

### Keeper Commanderへのログインを何度も求められる場合

Keeper Commander CLIで毎回ログイン情報が求められてしまう方は、KeeperのWebサイトで「**ログイン状態を保持**」をオンに設定すると、この問題が解消できます。

![keeper_security_option.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/681000/be507703-57bf-f312-3556-1d16e9fc2b00.png)

## まとめ

Keeperを活用することで、チーム開発における**機密情報の安全な共有**が実現します。

とくに、CLIツールとjqコマンドを組み合わせることで、**環境変数を効率的かつ安全に管理**できるため、セキュリティリスクを大幅に低減可能です。

今回紹介した方法を活用して、より安全で効率的なチーム開発環境を整えてみてください。

## PR

HRBrainでは積極的にエンジニア採用を行なっているので、ぜひ！

https://www.hrbrain.co.jp/recruit
