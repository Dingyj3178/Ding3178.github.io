---
layout:     post
title:      "SAP 汎用モジュールって何？"
subtitle:   ""
date:       2017-12-11
author:     "Ding"
header-img: "img/aaron-burden-36113.jpg"
catalog: true
visible: 1
tags:
    - SAP
    - BASIS
---
# SAP 汎用モジュールって何？ 

### １．汎用モジュールの概要（Function Module）
汎用モジュールの英語名はFunction Moduleです。和訳はとてもややこしい。。。  SAPはFunction Libraryを用いて汎用モジュールをカプセル化しています。汎用モジュール毎にインポートパラメータとエクスポートパラメータを作成することが可能。
汎用モジュールは独立で他のプログラムにコールされることができ、重複で使用できます。また、汎用モジュールはRFC[^1]をサポートしていますので、外部からとのインタフェースする際にも利用可能となります。

### 汎用グループ（Function Groups） 
機能のカテゴリを区別するために、すべて汎用モジュールは汎用グループを指定する必要があります。一般的に利用目的ごとに分割することが多い。
![](https://farm5.staticflickr.com/4413/36352787164_834caae2c2_o.jpg)
汎用モジュール以外に以下オブジェクトが同じグループに配置することが多い。
1. データオブジェクト
	Function Groupのグローバル変数、内部テーブル、構造、クラスなど
2. サブプログラム
	サブプログラムは該当汎用グループ内にコールできる
3. 画面
	作成された画面は該当汎用グループの汎用モジュールが利用できる

### 汎用モジュールを作成する
T−Code SE37で汎用モジュールビルダーにて初期画面に遷移する
![](https://farm5.staticflickr.com/4380/36375772363_c110e4ff6c_o.jpg)

汎用グループが作成されていない場合、汎用グループを登録します。Goto -\> Function Groups -\> Create Groups
他に変更、照会、削除などもできる。
![](https://farm5.staticflickr.com/4388/37018126732_3bec888d0e_o.jpg)
グループ名、説明を入力する。保存ボタンをクリックすると、どのパッケージに入れるのかを聞かれるが、今回デモですので、ローカルオブジェクトで良い。
![](https://farm5.staticflickr.com/4340/36375933773_68cf543b6c_o.jpg)

### 汎用モジュールのパラメータ
すべての汎用モジュールは以下要素が含まれている
**Import Parameter**
![](https://farm5.staticflickr.com/4413/36384949364_bd7a7e9c8a_o.jpg)
汎用モジュールをコールする際のパラメータとなります。Option（任意）をチェックすると、必須ではなくなります。
パラメータを設定する際、変数を定義する時と同じように、LIKE句を使用することが可能です。

**Export Parameter**
 ![](https://farm5.staticflickr.com/4338/36824359840_d25b404480_o.jpg)

ロジックの実行後、戻り値としてパラメータを返す。値だけではなく、構造でも返せる。

**Changing Parameter**
インポートパラメータを修正して、エクスポートパラメータとして出力する。

**Table**
![](https://farm5.staticflickr.com/4365/36824371080_f7cf8ccb7b_o.jpg)
内部テーブルのデータソースとして使用できる。また、実行結果テーブルとして戻り値として返すことも可能である。

**Exceptions**
異常処理用のパラメータの設定

**注意**
実際汎用モジュールをコールする際に、パラメータのインポートとエクスポートが逆であることを注意してください。
![](DraggedImage.png)

### 汎用モジュールの異常処理
プログラム処理中、例外が発生すると、Raise句を使って例外を出力できる。
	FUNCTION STRING-SPLIT.
	  ...
	  IF ...
	 RAISE NO_DATA.
	  ENDIF.

関数をコースする際、Exceptions句を利用して、例外を出力する。
	CALL FUNCTION 'STRING_SPLIT'
	  EXPORTING
	delimiter = '-'
	string    = text
	  IMPORTING
	head      = head
	tail      = tail
	  EXCEPTIONS
	not_found = 1
	not_valid = 2
	too_long  = 3
	too_small = 4
	OTHERS    = 5.

### RFC (Remote Function Call)
外部からコールできる汎用モジュールを設定するためには汎用モジュールの属性として、イRemote-Enabled-Moduleに設定する必要がある。
![](https://farm5.staticflickr.com/4409/36881166550_3c277e8265_o.jpg)

### BAPI
BAPIは**Business Application Process Interface**の略称である。実は特殊なRFCです。BAPIはよく外部からデータを連携する際に使用されます。（購買依頼を登録など）SAP は多くの標準BAPIを提供しています。トランザクションコードBAPIにて確認できる。

[^1]:	RFC (リモート ファンクション コール) は、SAP インターフェースプロトコルの 1 つです。CPI-C をベースにしていて、システム間の通信プロセスのプログラミングを大幅に簡潔化します。
	RFC を使用すると、事前定義された関数を同一システム内またはリモートシステムで呼び出しと実行を行うことができます。
	出典：http://wiki.genexus.jp/hwiki.aspx?SAP+リモート+ファンクション+コール,