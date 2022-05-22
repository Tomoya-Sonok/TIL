# 概要

なにかを開発するときに採用してみたい技術スタックや技術選定に関する Tips などを書きなぐるためのメモ。

# ① 技術ブログのリニューアル

Next.js + Tailwind CSS + Vercel で過去に作った Gatsby 製技術ブログをリニューアルしたい。

現在、放置中の技術ブログは[こちら](https://techguy10308blog.netlify.app/ja/)

## 技術要件、使ってみたいもの

- ベースとなるのは[blog-starter-typescript](https://github.com/vercel/next.js/tree/canary/examples/blog-starter-typescript)
  - マークダウンファイルを読み込み、SSG で記事ページをビルドしてくれる
- アイコンは Supabase の[Feather icon library](https://feathericons.com/)を使用する
- とにかく Tailwind CSS を使いたい
- デプロイは、無料でカスタムドメインも使えて Next.js との親和性も高い[Vercel](https://vercel.com/dashboard)を活用

| 要件一覧             |                                                                                        |
| -------------------- | -------------------------------------------------------------------------------------- |
| フロントエンド       | Next.js                                                                                |
| バックエンド         | -                                                                                      |
| CSS フレームワーク   | Tailwind CSS                                                                           |
| ホスティングサービス | Vercel                                                                                 |
| その他               | TypeScript <br> Supabase UI Icon <br> hilight.js <br> (また増えたら、随時更新する予定) |

## デザイン

これから要検討。（既存のブログデザインをそのまま流用もアリ）
