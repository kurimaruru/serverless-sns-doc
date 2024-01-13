# tweet 一覧画面の作成

## 画面イメージ

今回作成する画面のイメージは以下のとおりです。

![node.js](/2章-tweet一覧画面/tweets.png)

## 画面構成

画面構成は大まかに以下の構成となっています。

- ツイート一覧表示画面（親コンポーネント）
  - ヘッダーコンポーネント（オレンジ）
  - ツイート表示領域コンポーネント（黄色）
  - フッターコンポーネント（紫色）
  - ツイート作成ボタンコンポーネント（緑色）

![node.js](/2章-tweet一覧画面/画面構成.png)

React は分割した部品（コンポーネント）をつなぎ合わせて一つの画面を作り上げていくのが基本となっています。

また、コンポーネントを色々な画面で共通的に利用することができるように実装していくことがとても大切な要素となっています。

それでは実際に画面の方を実装していきましょう。

## 画面実装

まずは vscode で作成したプロジェクトを開きましょう。

```bash
# 作成したプロジェクト配下に移動する。
$ cd twitter-clone/frontend
# vscodeを開く
$ code .
```

vscode をひらいたらローカル環境で next.js アプリをブラウザで開いてみましょう。

```bash
$ npm run dev
```

以下の画面が表示されたら OK です！
![next.js](/2章-tweet一覧画面/nextjs.png)

next.js の初期画面が確認できたら今回作成するアプリに必要なライブラリのインストールを行いましょう。
インストールするライブラリは以下の通りです。

- Material UI
  - UI の実装に便利なコンポーネントライブラリです。
- Recoil
  - グローバルステートを管理するためのライブラリです。
- reset css
  - デフォルトで設定されているスタイルをリセットするライブラリ

```bash
# Material uiのインストール
$ npm install @mui/material @emotion/react @emotion/styled
$ npm install @fontsource/roboto
$ npm install @mui/icons-material
# Recoilのインストール
$ npm install recoil
# reset css
$ npm i the-new-css-reset
```

material UI の設定で`frontend/package.json`を編集する必要があるので、`peerDependencies`のプロパティを追加するように修正してください。

```json
    // 略
  "devDependencies": {
    // 略
  },
  "peerDependencies": {
    "react": "^17.0.0 || ^18.0.0",
    "react-dom": "^17.0.0 || ^18.0.0"
  }
```

ライブラリをインストールしたら`frontend/src/app`配下の layout.tsx と page.tsx （さきほどブラウザで確認したページのソースファイル）を以下のように修正しましょう。

`frontend/src/app/layout.tsx`
layout.tsx は「src/app 配下の page.tsx を自動的にラップする」ができます。
このファイルで実現させているのは以下の内容です。

- material UI 関連の設定
- リセット CSS
- アプリケーション全体で Recoil（グローバルステートの管理）を利用するために RecoilRoot で`children`をラッピングする。(children は page.tsx の内容が入ってくる)

```jsx
// recoilはクライアントコンポーネントでしか使えないためuse clientを記載
"use client";
// MUIのフォント設定まわり
import "@fontsource/roboto/300.css";
import "@fontsource/roboto/400.css";
import "@fontsource/roboto/500.css";
import "@fontsource/roboto/700.css";
import { Inter } from "next/font/google";
// リセットCSS
import "the-new-css-reset/css/reset.css";

import { RecoilRoot } from "recoil";
const inter = Inter({ subsets: ["latin"] });

export default function RootLayout({
  children,
}: {
  children: React.ReactNode,
}) {
  return (
    <html lang="en">
      <body
        className={inter.className}
        style={{ height: "100%", width: "100%" }}
      >
        {/* アプリケーション全体でRecoilを利用するためにラッピング */}
        <RecoilRoot>{children}</RecoilRoot>
      </body>
    </html>
  );
}
```

`frontend/src/app/page.tsx`

このファイルは先ほどブラウザで確認した nextjs の初期画面のページです。`http://localhost:3000/`のルートパスで表示される画面となります。
この画面では tweet 一覧画面へ遷移するための簡易ログイン画面とします。（実際のログイン機能はボーナスレクチャーにて実装します）
`<Link href="/home"`という記載がありますが、こちらはボタンを押下したときに遷移するページのパスとなります。

```jsx
import { Grid } from "@mui/material";
import Button from "@mui/material/Button";
import Link from "next/link";

const _Login = async () => {
  return (
    <Grid
      container
      style={{ backgroundColor: "black", width: "100%", minHeight: "100vh" }}
    >
      <Grid
        item
        xs={12}
        style={{
          display: "flex",
          justifyContent: "center",
          alignItems: "center",
        }}
      >
        <>
          <Button variant="contained">
            <Link
              href="/home"
              style={{ textDecoration: "none", color: "white" }}
            >
              Login
            </Link>
          </Button>
        </>
      </Grid>
    </Grid>
  );
};

export default _Login;
```

tweet 一覧画面を実装する前に next.js の AppRouter について簡単に説明しておきましょう。
next.js の v13 から App Router という仕組みができました。これはルーティングのパスをディレクトリで表現できるようになったという感じですかね。
例えば、`http://localhost:3000/home`にアクセスしたときに表示したいページについては`frontend/src/app/home/`配下に page.tsx を作成するとそのページが表示されるといった感じです。App Router については以下の記事で概要が簡潔にまとめられているので参考にしてみてください。

[AppRouter についての概要](https://zenn.dev/yamadadayo123/articles/6cb4f586de0183)

では、実際にログインボタンを押下した際に遷移する tweet 一覧画面を仮実装してみましょう。`frontend/src/app/`配下に新たに`home`というディレクトリを作成し、その配下に`page.tsx`ファイルを新規作成します。

`frontend/src/app/home/page.tsx`

```jsx
const Home = async () => {
  return (
    <div>
      <h1>Hello World!!!</h1>
    </div>
  );
};

export default Home;
```

ここまで完了したらブラウザで動作確認してみましょう。
`npm run dev`を実行したら`http://localhost:3000/`にアクセスしてみましょう。すると以下のような画面が表示されます。

![next.js](/2章-tweet一覧画面/login.png)

表示されたらログインボタンを押下してみましょう。
`http://localhost:3000/home`に遷移し、「Hello World!!!」が表示されていれば OK です！

![next.js](/2章-tweet一覧画面/helloWorld.png)

それでは`frontend/src/app/home/page.tsx`を本格的に編集していきましょう。
