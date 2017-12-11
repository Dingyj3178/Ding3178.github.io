---
layout:     post
title:      "MacbookでPowerpointのスライドショーを見せながら内容を修正する方法"
subtitle:   "MacbookでもOfficeを使わないといけない人達"
date:       2017-05-29
author:     "Ding"
header-img: "img/aaron-burden-36113.jpg"
catalog: true
tags:
    - Macbook
    - Powerpoint
---
# SAP HANAの自動採番方法

SAP HANAの自動採番の方法を紹介します。

すべての内容は[この動画](https://www.youtube.com/watch?v=xnMSAPujJts)にあります。動画が使用しているソースコードは[Github](https://github.com/saphanaacademy/SQL/blob/master/IDENTITY%20Column%20INSERT%20with%20OVERRIDING%20%20VALUE.sql)に公開されています。

もう少しSQLの中身を説明します。
[公式ドキュメント](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20d58a5f75191014b2fe92141b7df228.html)でも参考してください。

 HANAの自動採番はIdentityに基づいて実現される。

CREATE COLUMN TABLE EMPLOYEES 
(
EMPID INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY (start with 100),
EMP VARCHAR(20),
ROLE VARCHAR(20),
SALARY INTEGER
);

上記コードに記載されている通り、項目定義する際に**GENERATED ALWAYS AS IDENTITY** で定義する。

**ALWAYS AS**以外に、**BY DEFAULT**というOptionもあります。違いは
- ALWAYSの場合、生成された列は自動採番以外、ユーザーからデータをInsertできない。
- BY DEFAULTの場合、Insertしなかったら自動採番します。ユーザーから直接データをInsertすることもできる。ただ、ユーザーが入力した数字は採番履歴を上書きしますので、次の自動採番はユーザーの入力値の次から採番します。
特別な要件がなければALWAYSを使用することを推奨します。BY DEFAULTを使うと、ユーザーが間違って採番列を更新すると、番号が狂う可能性があります。
例：
> 1   商品A → 自動採番
> 2   商品B → 自動採番
> 999 商品C → ユーザー入力
> **1000 商品D → 自動採番**

また、下記パラメータで採番の間隔、初期値を設定することも可能です。
>  | INCREMENT BY \<increment_value\>
>  | MAXVALUE \<max_value\>
>  | NO MAXVALUE
>  | MINVALUE \<min_value\>
>  | NO MINVALUE
>  | CYCLE
>  | NO CYCLE
>  | CACHE \<cache_size\>
>  | NO CACHE
>  | RESET BY \<subquery\>

### ※補足
1. IdentityはHANAを再起動しても、元の採番履歴を保持しますので、リセットしない限り、採番をし続ける仕様となります。
2. 採番をリセットしたい場合、 ALTER SEQUENCE seq RESTART WITH 1; を利用する。
