# フロントエンド環境構築

## node.js のインストール

念のため、筆者のバージョンと同じにしておくと環境際によるエラーなどが発生する可能性が下がるのでできれば同じバージョンをインストールして頂けると助かります。もともと筆者より上のバージョンを利用されている場合はそのままでも問題ございません。

```bash
# 筆者のバージョン
$ node --version
v20.11.0
$ npm --version
10.2.4
```

以下のリンクから自分の環境に応じた node.js をインストールしてください。LTS の`v20.11.0`をインストールしてください。

https://nodejs.org/en

バージョンは異なりますが、progate の環境構築説明が易しく説明してくれています。

- windows
  - https://prog-8.com/docs/nodejs-env-win
- mac
  - https://prog-8.com/docs/nodejs-env

以下のコマンドを実行して、バージョンが表示されたらインストールは成功です。

```bash
$ node --version
v20.11.0
$ npm --version
10.2.4
```

## vscode のインストール

https://code.visualstudio.com/download

適宜、自分好みの拡張機能などをインストールすると快適にコーディングできると思います。

[参考](https://ugo.tokyo/react-vsc/)

## next.js でプロジェクトを作成

任意のフォルダを作成して、その中に作成してみましょう。

```bash
$ mkdir twitter-clone
$ cd twitter-clone
$ npx create-next-app@14.0.4
# アプリケーション構成について質問されるので、以下のように回答する
Need to install the following packages:
  create-next-app@14.0.4
Ok to proceed? (y) y
✔ What is your project named? … frontend
✔ Would you like to use TypeScript? … Yes
✔ Would you like to use ESLint? … Yes
✔ Would you like to use Tailwind CSS? … No
✔ Would you like to use `src/` directory? … Yes
✔ Would you like to use App Router? (recommended) … Yes
✔ Would you like to customize the default import alias (@/*)? … No
```

## まとめ

フロントエンド環境構築については以上となります。次の章から実際に画面開発の方に進んでいきます。
