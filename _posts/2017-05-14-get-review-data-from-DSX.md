---
layout:     post
title:      "IBM Data Science Experienceを使ってぐるなびレビューデータを5万を取得する"
subtitle:   "日本語レビューデータセットをゲット"
date:       2017-05-14
author:     "Ding"
header-img: "img/aaron-burden-36113.jpg"
catalog: true
tags:
    - Data Science
    - IBM Data Science Experience
---

## 何故この記事

1. 機械学習・テキストマイニングのための日本語のレビューデータを取得する手段は少なく、民間の人はデータを取得するのは困難である
2. ぐるなびはAPIを公開しているが、使い勝手が悪い（一回の最大取得件数が50件）
2. DSXを使用する情報が未だ少い

## 使うモノ
[IBM Data Science Experience（DSX）](https://apsportal.ibm.com/analytics)

[ぐるなびAPIとAPIキー](http://api.gnavi.co.jp/api/manual/photosearch/)
> ぐるなびのAPIキーは申請する必要がある

## 実行手順
- DSX上にプロジェクトとノートブックを作成する。
![](https://farm5.staticflickr.com/4181/34520350641_d0811f77da_o.png) 
![](https://farm5.staticflickr.com/4194/34520353421_d2a0d82e02_o.png)
> ぐるなびのAPIのサンプルコードはPython2.xに基づいて書いてありますので、KernalもPython2にしましょう。

- ぐるなびのサンプルコードを実装
![](https://farm5.staticflickr.com/4172/34265881510_edc0e11d22_o.jpg)
15件しか表示しないため、使えるモノにならない。

- APIを関数にして、リクエストパラメータを修正する。
![](https://farm5.staticflickr.com/4167/34265937390_71bc58bf62_o.jpg)

- 最大50件しか出力出来ないが、検索結果は全体ヒット件数とページ数があり、同じリクエストで、**offsetpage**を変更すれば次のページの50件を表示してくれる。これで繰り返してAPIのFunctionをリクエストする。（1000回をコールするには1時間ぐらい掛かった）
- Responsパラメータを参照しながら抽出項目を設定する。
![](https://farm5.staticflickr.com/4175/34489343042_dc98cff3bc_o.jpg)
 - 出力したリストをCSVに保存する。
![](https://farm5.staticflickr.com/4163/34651521445_0fb4552c79_o.jpg)
- DSXのサーバー上出実装しているため、実際のファイルはローカルPCにない。実際にのPCダウンロードするためにもう一つの手間が掛かる。DSXのObject Storageに保存する必要がある。
具体的な操作は下記文書を参考した。[Working with Object Storage in Data Science Experience - Python Edition](https://datascience.ibm.com/blog/working-with-object-storage-in-data-science-experience-python-edition/)
- 空のCSVファイルをまずアップロードし、該当ファイルの定義を実際のノートブックにインサートする。
![](https://farm5.staticflickr.com/4155/34651642495_d112834529_o.jpg)
- 参考文書にあるのFunctionを実装し、データをObject Storageに蓄積する。
![](https://farm5.staticflickr.com/4170/34489588972_fc58d22dc9_o.jpg)
- 直接プロジェクト画面でData Assetsのファイルをクリックしても反応しない、そして、…のバーのところクリックしても「Remove」しかない… どんな設計だよ！色々試した結果、Data Serviceのところに移動し、やっとダウンロードできた！
![](https://farm5.staticflickr.com/4179/33842104143_ba560eb855_o.jpg)
![](https://farm5.staticflickr.com/4167/33808955164_ba47c791d3_o.jpg)
![](https://farm5.staticflickr.com/4188/33842063783_1891a29ece_o.jpg)

