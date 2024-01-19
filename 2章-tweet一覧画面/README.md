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

まずはヘッダーから作成します。
`rontend/src/app/`配下に`components`フォルダを新規作成します。そして`components`配下に`elements`フォルダと`header`フォルダを作成し、`header.tsx`をというファイルを新規作成します。

`rontend/src/app/components/elements/header/header.tsx`
ここからは Material UI を有効活用していきます。
MUI の公式ドキュメントからたくさんのサンプルが見られるので一度全体に目を通しておくと概要をつかむことができると思います。今回のアプリはスタイリングは MUI に大部分を任せて React や typescript などの概要をつかむことに集中しましょう！

https://mui.com/material-ui/all-components/

```jsx
"use client";

import TwitterIcon from "@mui/icons-material/Twitter";
import AppBar from "@mui/material/AppBar";
import Avatar from "@mui/material/Avatar";
import Box from "@mui/material/Box";
import Container from "@mui/material/Container";
import IconButton from "@mui/material/IconButton";
import Toolbar from "@mui/material/Toolbar";
import Tooltip from "@mui/material/Tooltip";
import Typography from "@mui/material/Typography";

export const Header = () => {
  return (
    <>
      <AppBar component="nav">
        <Container maxWidth="xl">
          <Toolbar disableGutters>
            <Box sx={{ flexGrow: 1, display: { xs: "flex", md: "none" } }}>
              <Tooltip title="Open settings">
                <IconButton sx={{ p: 0 }}>
                  <Avatar
                    alt="Remy Sharp"
                    src={
                      "https://kotonohaworks.com/free-icons/wp-content/uploads/kkrn_icon_user_1-768x768.png"
                    }
                  />
                </IconButton>
              </Tooltip>
            </Box>
            <TwitterIcon />
            <Typography
              variant="h5"
              noWrap
              sx={{
                ml: 1,
                display: { xs: "flex", md: "none" },
                flexGrow: 2,
                fontFamily: "monospace",
                fontWeight: 700,
                letterSpacing: ".3rem",
                color: "inherit",
                textDecoration: "none",
              }}
            >
              sns
            </Typography>
          </Toolbar>
        </Container>
      </AppBar>
    </>
  );
};
```

ヘッダーが作成できたので、`frontend/src/app/home/page.tsx`から読み込んでみましょう。
`frontend/src/app/home/page.tsx`

```jsx
import { Grid } from "@mui/material";
// 作成したHeaderコンポーネントの読み込み
import { Header } from "../components/elements/header/header";

const Home = async () => {
  return (
    <>
      <Grid container>
        <Grid item xs={12}>
          {/* Headerコンポーネントの利用 */}
          <Header />
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "60px" }}>
        <Grid item xs={12}>
          <div>
            <h1>Hello World!!!</h1>
          </div>
        </Grid>
      </Grid>
    </>
  );
};

export default Home;
```

ここまで編集できたらブラウザで確認してみましょう。
ちなみに今回のアプリはスマホサイズに合わせて作成しているので、検証ツールを開いて（Windows の場合 ctrl + shift + I , Mac は Command + Option + I）画面サイズをスマホサイズに合わせてください。
![next.js](/2章-tweet一覧画面/header.png)

次はフッターコンポーネントを作成しましょう。
`rontend/src/app/components/elements/footer/footer.tsx`
フッターは特にロジックもないので画面側の実装をメインに行います。
フッターコンポーネントで重要なことは`StyledFab`ボタン（プラスボタン）を押下したときに`<Link href="/tweet"`でツイート作成画面へ遷移できるようにしているところぐらいですかね。ツイート作成画面は`3章`で作成します。

```jsx
"use client";

import AddIcon from "@mui/icons-material/Add";
import HomeIcon from "@mui/icons-material/Home";
import ModeCommentIcon from "@mui/icons-material/ModeComment";
import NotificationsActiveIcon from "@mui/icons-material/NotificationsActive";
import SearchIcon from "@mui/icons-material/Search";
import { Grid } from "@mui/material";
import AppBar from "@mui/material/AppBar";
import CssBaseline from "@mui/material/CssBaseline";
import Fab from "@mui/material/Fab";
import IconButton from "@mui/material/IconButton";
import { styled } from "@mui/material/styles";
import Toolbar from "@mui/material/Toolbar";
import Link from "next/link";

const StyledFab = styled(Fab)({
  position: "absolute",
  zIndex: 1,
  top: -60,
  left: "85%",
  right: 0,
});

export const Footer = () => {
  return (
    <>
      <CssBaseline />
      <AppBar position="fixed" color="primary" sx={{ top: "auto", bottom: 0 }}>
        <Toolbar>
          <Grid container>
            <Grid item xs={3} style={{ paddingLeft: "6%" }}>
              <IconButton
                color="inherit"
                aria-label="open drawer"
                style={{ color: "white" }}
              >
                <HomeIcon />
              </IconButton>
            </Grid>
            <Grid item xs={3} style={{ paddingLeft: "6%" }}>
              <IconButton color="inherit">
                <SearchIcon />
              </IconButton>
            </Grid>
            <Grid item xs={3} style={{ paddingLeft: "6%" }}>
              <IconButton color="inherit">
                <NotificationsActiveIcon />
              </IconButton>
            </Grid>
            <Grid item xs={3} style={{ paddingLeft: "6%" }}>
              <IconButton color="inherit" style={{ color: "white" }}>
                <ModeCommentIcon />
              </IconButton>
            </Grid>
          </Grid>
        </Toolbar>
        <StyledFab color="primary" aria-label="add">
          <Link href="/tweet" style={{ textDecoration: "none" }}>
            <AddIcon sx={{ mt: 1 }} />
          </Link>
        </StyledFab>
      </AppBar>
    </>
  );
};
```

フッターが作成できたので、`frontend/src/app/home/page.tsx`から読み込んでみましょう。
`frontend/src/app/home/page.tsx`

```jsx
import { Grid } from "@mui/material";
// 作成したFooterコンポーネントの読み込み
import { Footer } from "../components/elements/footer/footer";
import { Header } from "../components/elements/header/header";

const Home = async () => {
  return (
    <>
      <Grid container>
        <Grid item xs={12}>
          <Header />
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "60px" }}>
        <Grid item xs={12}>
          <div>
            <h1>Hello World!!!</h1>
          </div>
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "50px" }}>
        <Grid item xs={12}>
          {/* Footerコンポーネントの利用 */}
          <Footer />
        </Grid>
      </Grid>
    </>
  );
};

export default Home;
```

Footer コンポーネントを読み込んだのでブラウザで画面を開いてみましょう。
以下のように表示されていれば OK です！
![next.js](/2章-tweet一覧画面/footer.png)

ここまでできたらこの画面で一番重要な tweet 一覧を表示できるようにしていきましょう！
tweet 一覧表示のために必要なことは以下の通りです。

- api から tweet 一覧情報を取得する。
- 取得した値をもとに画面を描画する。

これらを順に実装していきましょう。

### api から tweet 一覧情報を取得する。

typescript はそれぞれの値に型情報を持たせる必要があります。型を持たせるメリットとしては以下のようなことが挙げられます。

- 静的な型付けによる安全性向上:
  - TypeScript は変数や関数の型を指定できるため、コードがより安全で信頼性が高まります。型エラーが早い段階で検出され、実行時エラーを減少させます。

なのでまずは、tweet 情報の型定義を行いましょう。
`frontend/src/app/`配下に`type`フォルダを作成し、`types.ts`ファイルを新規で作成します。
`frontend/src/app/type/types.ts`
それぞれのプロパティと型の説明は以下のコード中に記載します。

```typescript
// tweet一覧の型情報
export type tweetsData = { tweets: tweetData[] };
// tweetの型情報
export type tweetData = {
  id: string; // tweetを一意に識別するためのid
  tweetInfo: tweetInfo; // tweetの情報の型
  tweetContent: tweetContent; // tweetの内容の型
  tweetUserAction: userAction; // tweetに対するユーザーのアクションの型
  userId: string; // tweet作成したユーザーのID
};

export type tweetInfo = {
  userName: string; // ユーザー名
  createdAt: string; // ツイート作成日時
};

export type tweetContent = {
  message: string; // ツイートのメッセージ
  imgName: string; // ツイートに添付した画像の名称
};

export type userAction = {
  good: string; // いいね
  comments: string[]; // ツイートに対するコメント
};
```

型の準備ができたので、つぎに api から実際にデータを取得できるようにしていきたいと思います。
まずは api を呼び出すための URL をまとめたファイルを作成しましょう。
`frontend/src/app`配下に`constants`フォルダを作成し`url.ts`ファイルを新規作成しましょう

`frontend/src/app/constants/url.ts`
このファイルでは ApiUrl というオブジェクトを作成し、ローカル環境と本番環境の api の URL を環境変数によって取得することができるようにしています。

`hoge === true ? HOGE : HOGEHOGE;`のような記載が多くみられると思いますがこれは三項演算子という条件式で、`?`の後ろが条件が true の場合に返す値で`:`の後ろが false の場合に返す値となっています。
以下の場合は local 環境ではない場合は本番環境の api を呼び出し、local の場合は localhost の api を呼び出すといった処理になっています。

```typescript
const env = process.env.NEXT_PUBLIC__ENV;

export const ApiUrl = {
  BASE_API_URL:
    env !== "local"
      ? process.env.NEXT_PUBLIC_BASE_API_URL
      : "http://localhost:3000",
  API_UPLOAD_URL: env !== "local" ? "/v1/api/upload_url" : "/api/upload_url",
  API_DOWNLOAD_URL:
    env !== "local" ? "/v1/api/download_url" : "/api/download_url",
  API_CREATE_TWEET:
    env !== "local" ? "/v1/api/create_tweet" : "/api/create_tweet",
  API_FETCH_TWEET: env !== "local" ? "/v1/api/fetch_tweet" : "/api/fetch_tweet",
};
```

まだ環境変数を設定していなかったと思うので、`frontend/`配下に`.env.local`ファイルを作成して以下のように環境変数を設定してあげてください。

```
NEXT_PUBLIC__ENV=local
# バックエンドのAPIを作成した後に設定する
NEXT_PUBLIC_BASE_API_URL=https://hogehoge/api
```

次に api を呼び出す共通関数を定義してあげましょう。
`frontend/src/app/`配下に`utils`フォルダを作成し、`baseApi.ts`ファイルを新規作成しましょう

apiClient メソッドでは引数で引き受けた設定条件をもとに fetch 関数で通信リクエストを行います。

また、async および await は、非同期処理をより直感的でシンプルに記述するためのものであり、async は非同期関数を宣言し、await は非同期処理が完了するまで待機するために使用されます。

```typescript
import { ApiUrl } from "../constants/url";

/**
 * @param urlSuffix - 呼び出したいAPIのパス（/api/hogehoge）
 * @param method - apiのhttpメソッド
 * @param cache - キャッシュの設定
 * @param body -リクエストボディに含める値
 */
export const apiClient = async (
  urlSuffix: string,
  method: string,
  cache: RequestCache | undefined,
  body?: BodyInit | null | undefined
) => {
  const response = await fetch(ApiUrl.BASE_API_URL + urlSuffix, {
    method: method,
    headers: {
      "Content-Type": "application/json",
      "Access-Control-Allow-Origin": "*",
    },
    cache: cache,
    body: body,
  });

  return response.json();
};
```

`frontend/src/app/home/`配下に`home.hooks.ts`というファイルを新規作成します。

`frontend/src/app/home/home.hooks.ts`

この hooks ファイルは React のカスタムフックという仕組みを利用しています。カスタムフックには以下のような特徴があります。

- 自分独自のフックを作成することで、コンポーネントからロジックを抽出して再利用可能な関数を作ることが可能
  - コンポーネントの複雑化を防ぐことができ、View とロジックを分離することができます。（現場では必須の知識となるので覚えておきましょう！！！）
- 命名は必ず "use" で始まる
  - React hooks には useState や useEffect などがありますが、それぞれ`use`で必ず始まります。カスタムフックもそれらと同じように命名しようということですね。

```typescript
// 定義した型情報の読み込み
import { tweetData, tweetsData } from "../type/types";
// APIを呼び出すための共通関数の読み込み
import { apiClient } from "../utils/baseApi";

export const useFetchTweetsData = (userIds: string[]) => {
  const fetchTweetsData: tweetsData = async (): Promise<tweetData[]> => {
    const tweetsData = await apiClient(
      "/api/fetch_tweet",
      "POST",
      "no-store",
      JSON.stringify({ userIds: userIds })
    );
    return tweetsData.tweets;
  };

  return { fetchTweetsData };
};
```

ここまで実装した`useFetchTweetsData`の`fetchTweetsData`を呼び出すと実際に api の処理が呼ばれるわけですが、ローカル環境の api をまだ実装していないため、このままだとデータが返ってきません。
そのため、ローカル環境用のモック API を作成しましょう。
`frontend/src/app/`配下に`api`フォルダを作成し、`fetch_tweet`フォルダを作成しその中に`route.ts`ファイルを追加します。

```typescript
import { NextResponse } from "next/server";

export function POST() {
  console.log("fetch_tweet");
  return NextResponse.json({
    tweets: [
      {
        id: "test1",
        tweetInfo: {
          userName: "test1",
          createdAt: "2022/10/1",
        },
        tweetContent: {
          message:
            "This impressive paella is a perfect party dish and a fun meal to cook together with your guests. Add 1 cup of frozen peas along with themussels, if you like.",
          imgName: "cat.jpg",
        },
        tweetUserAction: {
          good: 3,
        },
        userId: "test111",
      },
      {
        id: "test2",
        tweetInfo: {
          userName: "test2",
          createdAt: "2022/10/5",
        },
        tweetContent: {
          message:
            "This impressive paella is a perfect party dish and a fun meal to cook together with your guests. Add 1 cup of frozen peas along with themussels, if you like.",
          imgName: "cat2.jpg",
        },
        tweetUserAction: {
          good: 2,
        },
        userId: "test222",
      },
      {
        id: "test3",
        tweetInfo: {
          userName: "test3",
          createdAt: "2022/10/10",
        },
        tweetContent: {
          message:
            "This impressive paella is a perfect party dish and a fun meal to cook together with your guests. Add 1 cup of frozen peas along with themussels, if you like.",
          imgName: "flower.jpeg",
        },
        tweetUserAction: {
          good: 1,
        },
        userId: "test333",
      },
      {
        id: "test4",
        tweetInfo: {
          userName: "test4",
          createdAt: "2022/10/15",
        },
        tweetContent: {
          message:
            "This impressive paella is a perfect party dish and a fun meal to cook together with your guests. Add 1 cup of frozen peas along with themussels, if you like.",
          imgName: "flower2.jpeg",
        },
        tweetUserAction: {
          good: 0,
        },
        userId: "test444",
      },
    ],
  });
}
```

ここまで実装できたらローカル環境で api を呼び出すことができます。
`frontend/src/app/home/page.tsx`から先ほど作成したカスタムフックを呼んでみましょう。
`frontend/src/app/home/page.tsx`

```jsx
import { Grid } from "@mui/material";
import { Footer } from "../components/elements/footer/footer";
import { Header } from "../components/elements/header/header";
import { useFetchTweetsData } from "./home.hooks"; // 追加

const Home = async () => {
  // =========追加：ここから============
  // TODO: useFetchTweetsDataの引数はログイン機能実行あとにuserのメアドを引数に渡せるように更新する
  const { fetchTweetsData } = useFetchTweetsData(["test@mail.com"]);
  const tweetsData = await fetchTweetsData();
  // 一旦console.logでコマンドプロンプトやbashなどの`npm run dev`を実行しているところでレスポンスデータを確認してみましょう
  console.log(tweetsData);
  // =========追加：ここまで============

  return (
    <>
      <Grid container>
        <Grid item xs={12}>
          <Header />
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "60px" }}>
        <Grid item xs={12}>
          <div>
            <h1>Hello World!!!</h1>
          </div>
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "50px" }}>
        <Grid item xs={12}>
          <Footer />
        </Grid>
      </Grid>
    </>
  );
};

export default Home;
```

`npm run dev`しているところでレスポンスのログを確認できたら OK です！

### 取得した値をもとに画面を描画する。

先ほどまでの実装で api からデータを受け取ることができたので、tweet を表示できるようにしていきましょう！

`frontend/src/app/components`配下に`container`フォルダを追加し、`TweetCard`フォルダを作成し`/TweetCard.tsx`ファイルを新規作成します。ちなみにこの時点で`components`配下に`elements`と`container`フォルダが存在するようになったと思いますがそれぞれのフォルダには以下のような役割があります。

- elements
  - Props の値をうけとって単純に表示するだけのような最小単位のコンポーネントを格納するフォルダ
- container
  - 複数の elements 配下のコンポーネントを利用したり、ロジックがふくまれているようなコンポーネントを格納するためのフォルダ

その他、複数の containe を含むコンポーネントを格納するための features フォルダというのもプロジェクトによっては存在しますが、今回のアプリではこちらは含みません。

`frontend/src/app/container/TweetCard/TweetCard.tsx`

以下のファイルでは、これまでに出ていなかった仕組みが出てくるので説明します。

- useState
  - useState は React で状態を管理するためのフック（hook）の一つです。これを使用することで、関数コンポーネント内で状態（ローカルステートとよく呼ばれる）を作成し、その状態を更新できます。useState は、現在の状態の値とその値を更新するための関数を提供します。
- onClick
  - onClick は React で使用されるイベントハンドラの一つで、ユーザーが要素（通常はボタンやリンクなど）をクリックしたときに実行される関数を指定します。
- onClose
  - onClose もイベントハンドラの一種で、通常はウィンドウやモーダル、アラートなどが閉じられたときに呼び出される関数を指定します。
- Props
  - Props（プロパティ）は、親コンポーネントから子コンポーネントにデータを渡すためのメカニズムであり、コンポーネント間の情報の受け渡しを可能にします。

```jsx
"use client";

import { tweetData } from "@/app/type/types";
import { ChatBubbleOutline, Favorite } from "@mui/icons-material";
import MoreVertIcon from "@mui/icons-material/MoreVert";
import Avatar from "@mui/material/Avatar";
import Card from "@mui/material/Card";
import CardActions from "@mui/material/CardActions";
import CardContent from "@mui/material/CardContent";
import CardHeader from "@mui/material/CardHeader";
import CardMedia from "@mui/material/CardMedia";
import IconButton from "@mui/material/IconButton";
import MenuItem from "@mui/material/MenuItem";
import Typography from "@mui/material/Typography";
import { Box } from "@mui/system";
import { useState } from "react";
import { StyledMenu } from "../../elements/styledMenu/styledMenu";

type Props = {
  // 親コンポーネントから受け取ったツイートデータ
  tweet: tweetData,
};

export const TweetCard = (props: Props) => {
  // useStateの1つ目が状態を表すステートで2つ目が状態を更新する関数
  const [anchorEl, setAnchorEl] = (useState < null) | (HTMLElement > null);
  const open = Boolean(anchorEl);
  const handleClick = (event: React.MouseEvent<HTMLElement>) => {
    setAnchorEl(event.currentTarget);
  };
  const handleClose = () => {
    setAnchorEl(null);
  };

  return (
    <Card>
      <CardHeader
        avatar={
          <Avatar
            alt="Remy Sharp"
            // TODO: ログイン機能を実装したらユーザーの画像が表示されるように更新する
            src={
              "https://kotonohaworks.com/free-icons/wp-content/uploads/kkrn_icon_user_1-768x768.png"
            }
          />
        }
        action={
          <>
            <IconButton aria-label="settings" onClick={handleClick}>
              <MoreVertIcon />
            </IconButton>
            <StyledMenu
              id="demo-customized-menu"
              MenuListProps={{
                "aria-labelledby": "demo-customized-button",
              }}
              anchorEl={anchorEl}
              open={open}
              onClose={handleClose}
            >
              <MenuItem style={{ color: "red" }}>削除</MenuItem>
            </StyledMenu>
          </>
        }
        title={props.tweet.tweetInfo.userName}
        subheader={props.tweet.tweetInfo.createdAt}
      />
      <CardContent sx={{ mr: 3, ml: 3 }}>
        <Typography variant="body2" color="text.secondary">
          {props.tweet.tweetContent.message}
        </Typography>
      </CardContent>
      <CardMedia
        component="img"
        height="200"
        // TODO: ダウンロードURL取得APIから取得したURLを設定するように
        image="https://news.walkerplus.com/article/1023800/10210444_615.jpg"
        alt="image"
        style={{ objectFit: "contain" }}
      />
      <CardActions sx={{ mr: 3, ml: 3 }}>
        <IconButton aria-label="add to favorites">
          <ChatBubbleOutline />
        </IconButton>
        <IconButton aria-label="add to favorites">
          <Favorite
            style={{
              color: props.tweet.tweetUserAction.good !== "0" ? "red" : "gray",
            }}
          />
        </IconButton>
        <Box>{props.tweet.tweetUserAction.good}</Box>
      </CardActions>
    </Card>
  );
};
```

StyledMenu コンポーネントがまだ準備できていなかったので`frontend/src/app/components`配下に`styledMenu/styledMenu.tsx`を追加します。

```jsx
import Menu, { MenuProps } from "@mui/material/Menu";
import { alpha, styled } from "@mui/material/styles";

export const StyledMenu = styled((props: MenuProps) => (
  <Menu
    elevation={0}
    anchorOrigin={{
      vertical: "bottom",
      horizontal: "right",
    }}
    transformOrigin={{
      vertical: "top",
      horizontal: "right",
    }}
    {...props}
  />
))(({ theme }) => ({
  "& .MuiPaper-root": {
    borderRadius: 6,
    marginTop: theme.spacing(1),
    minWidth: 180,
    color:
      theme.palette.mode === "light"
        ? "rgb(55, 65, 81)"
        : theme.palette.grey[300],
    boxShadow:
      "rgb(255, 255, 255) 0px 0px 0px 0px, rgba(0, 0, 0, 0.05) 0px 0px 0px 1px, rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px",
    "& .MuiMenu-list": {
      padding: "4px 0",
    },
    "& .MuiMenuItem-root": {
      "& .MuiSvgIcon-root": {
        fontSize: 18,
        color: theme.palette.text.secondary,
        marginRight: theme.spacing(1.5),
      },
      "&:active": {
        backgroundColor: alpha(
          theme.palette.primary.main,
          theme.palette.action.selectedOpacity
        ),
      },
    },
  },
}));
```

ここまで実装できたら tweet 一覧データを表示することができます。
`frontend/src/app/home/page.tsx`から先ほど作成した`TweetCardコンポーネント`を読み込んでみましょう。

`frontend/src/app/home/page.tsx`

aip から取得した tweetsData が 1 件以上ある場合のみ、`TweetCardコンポーネント`を読み込むようにしています。また、`tweetsData.map`の箇所で取得した tweet の配列数分だけ`TweetCardコンポーネント`を繰り返し表示するようにしています。この JSX の中で map を使うのは現場で頻出なので使い慣れておくよ良いでしょう！

```jsx
import { Grid } from "@mui/material";
import { Footer } from "../components/elements/footer/footer";
import { Header } from "../components/elements/header/header";
import { useFetchTweetsData } from "./home.hooks";

const Home = async () => {
  // TODO: useFetchTweetsDataの引数はログイン機能実行あとにuserのメアドを引数に渡せるように更新する
  const { fetchTweetsData } = useFetchTweetsData(["testUserId"]);
  const tweetsData = await fetchTweetsData();

  return (
    <>
      <Grid container>
        <Grid item xs={12}>
          <Header />
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "60px" }}>
        <Grid item xs={12}>
          {/* ここを修正 */}
          {tweetsData.length !== 0 &&
            tweetsData.map((data) => <TweetCard key={data.id} tweet={data} />)}
        </Grid>
      </Grid>
      <Grid container style={{ marginTop: "50px" }}>
        <Grid item xs={12}>
          <Footer />
        </Grid>
      </Grid>
    </>
  );
};

export default Home;
```

お疲れ様でした！ここまで実装したら tweet 一覧が画面から確認することができます！

![dispTweets](/2章-tweet一覧画面/displayTweets.png)

tweet 一覧画面についての実装は一旦以上となりますが、next.js の重要な要素を最後に説明してこの章を終わりにしたいと思います。
App Router が実現されてから、デフォルトでコンポーネントはすべてサーバーコンポーネントになるようになりました。要するにデフォルトではサーバー側でコンポーネントを生成してからクライアントの画面に表示するようになっているということです。（これが SSR（サーバーサイドレンダリング）というもの）

サーバーコンポーネントでは、React フックスなどが利用できないため、クライアントコンポーネントを作成したい場合はファイルの先頭で`"use client";`と宣言してあげる必要があります。

クライアントコンポーネントやサーバーコンポーネント、CSR や SSR については以下の記事を見てみると概要が分かると思いますので是非読んでみてください！！！

https://zenn.dev/akino/articles/78479998efef55
