---
layout:     post
title:      "グラフDB Neo4jをBluemixのContainerにインストールしてみた"
subtitle:   "グラフDB シリーズ①"
date:       2017-06-30
author:     "Ding"
header-img: "img/home-bg-o.jpg"
catalog: true
visible: 1
tags:
    - Neo4j
    - Bluemix
    - Docker
---
# グラフDB Neo4j をBluemixのContainerにインストールしてみた

最近「会社の知見を管理したい」「職人の知識を会社内で共有したい」というコンセントでグラフDBを流行っているらしいです。その中に使いやすそうなNeo4jをやってみようと思いましたが、後続の連携処理を考慮してやはりCloud Hostingしたほうがいいかと、残念ながらNeo4jは現時点のBluemixのサポートをしていないようです… ただしDockerをサポートされているようですので、BluemixのContainerに入れてみました。

## Cloud Foundry CLI, Bluemix CLI, and Docker CLIのインストール
最終的に紫色のところは追加したNeo4jのイメージとなります。

![](https://farm5.staticflickr.com/4257/34771024844_d354994813_o.jpg)
まず「Upload an Image」をクリックして、表示ページのガイド通り Cloud Foundry CLI, Bluemix CLI, and Docker CLIをインストールします。

![](https://farm5.staticflickr.com/4255/35444191322_4cf3c30fae_o.jpg)

## Log in to Bluemix API

**注意**
> bluemix login -a https://api.au-syd.bluemix.net

**au-syd**はリージョンのため、皆さんはUS-Southを使っている方が多いと思いますので、USにしたい場合、以下のように変えましょう。（後続のau-sydのところも同じ）

> bluemix login -a https://api.ng.bluemix.net

	API endpoint: https://api.ng.bluemix.net
	
	Email> XXXX@XXX.com
	
	Password> 
	Authenticating...
	OK
	
	Select an account (or press enter to skip):
	1. XXXXX's Account (1e84c5193834dae4104118e91605202f)
	2. XXXXX's Account (23bc8e1d84d3f29ca16a3848f41ce5c6)
	Enter a number> 1
	Targeted account XXXXXXX's Account (1e84c5193834dae4104118e91605202f)
	
	Targeted org XXXXXXX
	
	Select a space (or press enter to skip):
	1. Watson TEST
	2. NeGeTA
	Enter a number> 2
	Targeted space NeGeTA
	
	
	                   
	API endpoint:   https://api.ng.bluemix.net (API version: 2.75.0)   
	Region:         us-south   
	User:           XXXX@XXX.com   
	Account:        XXXXXX's Account (1e84c5193834dae4104118e91605202f)   
	Org:            XXXX@XXX.com
	Space:          NeGeTA   
	

## Initialize containers plug-in

次はBluemixのContainerの初期化

> bluemix ic init

初回実行時何故かエラーになった。

	XXXXXMacBook-Pro:~ ding$ bluemix ic init
	Deleting old configuration file...
	Generating client certificates for IBM Containers...
	Client certificates are being stored in /Users/ding/.ice/certs/...
	
	Client certificates are being stored in /Users/ding/.ice/certs/containers-api.ng.bluemix.net/827bc693-bacc-4999-ac60-92fcd223c095...
	
	OK
	Client certificates were retrieved.
	
	Checking local Docker configuration...
	OK
	
	Authenticating with registry at host name registry.ng.bluemix.net
	OK
	Your container was authenticated with the IBM Containers registry.
	FAILED
	
	{
	    "code": "IC5077E", 
	    "description": "The namespace for this space could not be found: 827bc693-bacc-4999-ac60-92fcd223c095", 
	    "environment": "prod-dal09", 
	    "host_id": "132", 
	    "incident_id": "54-1498736452.456-2669", 
	    "name": "NamespaceDNEForSpace", 
	    "rc": "404", 
	    "type": "Infrastructure"
	}
	

Namespaceが長い数字列になっているため、改めてNmaespaceを作成した。

	XXXXXMacBook-Pro:\~ ding$ bx ic namespace-set watsonjoygengraph
	watsonjoygengraph

再度実行すると、正しく初期化できました。

	XXXXXMacBook-Pro:\~ ding$ bx ic init
	Deleting old configuration file...
	Generating client certificates for IBM Containers...
	Client certificates are being stored in /Users/ding/.ice/certs/...
	
	Client certificates are being stored in /Users/ding/.ice/certs/containers-api.ng.bluemix.net/827bc693-bacc-4999-ac60-92fcd223c095...
	
	OK
	Client certificates were retrieved.
	
	Checking local Docker configuration...
	OK
	
	Authenticating with registry at host name registry.ng.bluemix.net
	OK
	Your container was authenticated with the IBM Containers registry.
	Your private Bluemix repository is URL: registry.ng.bluemix.net/watsonjoygengraph
	
	You can choose from two ways to use the Docker CLI with IBM Containers:
	
	
	Option 1: This option allows you to use 'bluemix ic' for managing containers on IBM Containers while still using the Docker CLI directly to manage your local Docker host.
	Use this Cloud Foundry IBM Containers plug-in without affecting the local Docker environment:
	
	
	Example Usage:
	bluemix ic ps
	bluemix ic images
	
	Option 2: Use the Docker CLI directly. In this shell, override the local Docker environment to connect to IBM Containers by setting these variables. Copy and paste the following commands:
	Note: Only Docker commands followed by (Docker) are supported with this option. 
	export DOCKER\_HOST=tcp://containers-api.ng.bluemix.net:8443
	export DOCKER\_CERT\_PATH=/Users/ding/.ice/certs/containers-api.ng.bluemix.net/827bc693-bacc-4999-ac60-92fcd223c095
	export DOCKER\_TLS\_VERIFY=1
	
	Example Usage:
	docker ps
	docker images

この時点で次のステップで実行する

> docker tag image\_id registry.au-syd.bluemix.net/**registryname**/image\_name:image\_tag

**registryname**部分はすでに作成されました。先ほど指定したnamespaceに基づいて作成されています。

> registry.ng.bluemix.net/watsonjoygengraph

## Push your Image

ここまできたら残りは楽勝です。
まずNeo4jのイメージをローカルに持ってきます。

	XXXXXMacBook-Pro:\~ ding$ docker pull neo4j
	Using default tag: latest
	latest: Pulling from library/neo4j
	Digest: sha256:4a675163357c0a310d24ee4c0625cf3a9f627a809e017fdd1b6a0cf75f7e965f
	Status: Image is up to date for neo4j:latest

最後はIBMのレポジトリへコピーする。

	XXXXXMacBook-Pro:\~ ding$ docker tag neo4j registry.ng.bluemix.net/watsonjoygengraph/neo4j
	XXXXXMacBook-Pro:\~ ding$ docker push registry.ng.bluemix.net/watsonjoygengraph/neo4j
	The push refers to a repository \[registry.ng.bluemix.net/watsonjoygengraph/neo4j]
	c9892aae952a: Pushed 
	c72582e0e0c9: Pushed 
	a1449494a20a: Pushed 
	798528302fdf: Pushed 
	ac8157ab87b1: Pushed 
	a4bd196655cf: Pushed 
	404361ced64e: Pushed 
	latest: digest: sha256:4a675163357c0a310d24ee4c0625cf3a9f627a809e017fdd1b6a0cf75f7e965f size: 1785
	XXXXXMacBook-Pro:\~ ding$ 

## Containerを起動する
作成したContainerが追加できるようになります。

![](https://farm5.staticflickr.com/4257/34771024844_d354994813_o.jpg)

Containerを作成する際に、後で普通にブラウザからアクセスしたいので、Public IP address欄を「**Request and Bind Public IP**」にします。

![](https://farm5.staticflickr.com/4193/35612608715_9daeb8f23a_o.jpg)

Containerが作成成功すると、パブリックのIDが発行されます。

![](https://farm5.staticflickr.com/4036/35226180690_004b1dfdf3_o.jpg)

7474のポートでブラウザからアクセスできます。初期パスワードはneo4j

![](https://farm5.staticflickr.com/4282/35612782295_666323abfd_o.jpg)
