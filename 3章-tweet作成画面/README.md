# tweet 一覧画面の作成

## 画面イメージ

今回作成する画面のイメージは以下のとおりです。

![node.js](/3章-tweet作成画面/createTweet.png)

tweet したいメッセージと画像をサーバーにアップロードする画面となっています。

## 画面実装

では、ツイート作成画面を作成していきましょう。
`frontend/src/app/`配下に`tweet`フォルダを作成し、`page.tsx`を作成しましょう。

`frontend/src/app/tweet/page.tsx`

このコンポーネントではフォームの制御を楽に実装するためのライブラリとして`react-hook-form`を利用します。
そのため、以下のコマンドを実行してライブラリをインストールしましょう。

```bash
# package.jsonファイルがある階層で実行
$ npm install react-hook-form
```

インストールが完了したら`frontend/src/app/tweet/page.tsx`の編集を行いましょう。

```jsx
"use client";

import { Avatar, Button, Grid, TextareaAutosize } from "@mui/material";
import Link from "next/link";
import { useEffect, useState } from "react";
import { useForm } from "react-hook-form";
import { tweetData } from "../type/types";

const _CreateTweet = ({}) => {
  // 解説ポイント1 下で解説します。
  const {
    register,
    handleSubmit,
    watch,
  } = useForm<tweetData>();
  const [disable, setDisable] = useState(true);

  // 解説ポイント2 下で解説します。
  useEffect(() => {
    let len = watch("tweetContent.message").length;
    if (len === 0) {
      setDisable(true);
    } else {
      setDisable(false);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [watch("tweetContent.message")]);

  return (
    <>
      <form onSubmit={handleSubmit((e) => console.log(e))}>
        <Grid container mt={1}>
          <Grid item xs={3}>
            <Link href="/home" style={{ textDecoration: "none" }}>
              <Button variant="text">キャンセル</Button>
            </Link>
          </Grid>

          <Grid item xs={9} sx={{ display: "flex", justifyContent: "end" }}>
            <Button
              variant="contained"
              type="submit"
              style={{ borderRadius: "20px" }}
              disabled={disable}
            >
              投稿する
            </Button>
          </Grid>
        </Grid>
        <Avatar
          alt="Remy Sharp"
          src={
            "https://kotonohaworks.com/free-icons/wp-content/uploads/kkrn_icon_user_1-768x768.png"
          }
        />
        <TextareaAutosize
          maxRows={4}
          placeholder="いまどうしてる？"
          style={{
            width: "100%",
            height: "20vh",
            outline: "none",
            border: "none",
            fontSize: "15pt",
          }}
          {...register("tweetContent.message", { required: true })}
        />
      </form>
    </>
  );
};

export default _CreateTweet;

```

上記のコードで重要なところについて以下で解説します。

- 解説ポイント 1 　`useForm`

  - useForm フックは、フォームの状態を管理するために使用される。

  - `<tweetData>`は、フォームのデータ型を指定しており、この型は、フォームで収集するデータの構造を表します。

  - `register` 関数は、フォーム内の入力要素を react-hook-form に登録します。これにより、フォームの各入力項目がフォームステートに関連付けられ、フォームの値が取得できるようになります。

  - handleSubmit 関数は、フォームが送信されたときに実行されるコールバック関数です。フォームの送信時に実行するべき処理をこの関数に定義します。通常、この中でフォームデータを送信したり、他のアクションを実行したりします。

  - watch 関数は、指定したフォームの入力項目の値を監視するために使用されます。これにより、特定の入力項目の値が変更されたときにリアクションを起こすことができます。

- 解説ポイント 2 　`useEffect`
  - `useEffect`の第一引数には。副作用の関数（DOM の書き換え、state の更新、API 通信など UI 構築以外の処理など）を指定します。
  - `useEffect`は第二引数（上記のコードでいう`[watch("tweetContent.message")]`のところ）に指定した値が更新されたときに第一引数に指定した副作用を実行します。
  - 上記より、`frontend/src/app/tweet/page.tsx`の`useEffect`は、ツイートのメッセージを監視して、ボタンの活性・非活性を制御するための`disable` state を更新しています。ボタンが活性のときしかツイートできないような制御となっています。

ここまでできたら画面で tweet 作成画面を確認してみましょう！

ツイート一覧画面から、2 章で作成したツイート作成画面へ遷移するためのボタンを押下すると以下の画面が開くはずです。

![tweet1](/3章-tweet作成画面/tweet1.png)

開けたら実際に文字を入力してみましょう。

文字を入力すると投稿ボタンが活性化したと思います。ボタンが活性化したら投稿ボタンを押下してみましょう。
すると、フォームに入力された値が`submit`によって送信され`onSubmit={handleSubmit((e) => console.log(e))}`の箇所でログ出力した情報がコンソール画面に表示されたと思います。

では、次にこれらのツイートデータを送信したり、画像をアップロードできたりするようにソースコードを更新していきましょう。

![tweet1](/3章-tweet作成画面/tweet2.png)

`frontend/src/app/tweet/tweet.hooks.ts`

```typescript

```
