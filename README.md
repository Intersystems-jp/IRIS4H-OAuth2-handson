# IRIS4H-OAuth2-ハンズオン　サンプル
このサンプルは、[InterSystems 開発者コミュニティ](https://jp.intersystems.com)に掲載している[記事](https://jp.community.intersystems.com/node/481066/)のサンプルです。

詳細はコミュニティの記事をご参照ください。

このサンプルでは、[Dockerfile](/Dockerfile)と[docker-compose.yml](/docker-compose.yml)を使用して、IRIS for Health 2020.3 Preview versionとApacheのSSL設定を行います。

## 事前準備

[Dockerfile](/Dockerfile)を使用してApache用コンテナを作成します。

コンテナをビルドする前に、Dockerfileがあるディレクトリと同じディレクトリにSSL証明書用ファイルをご準備ください。

ファイル名は server.crt と server.key　とします。
（[Dockerfile](/Dockerfile)の36-37行目に以下の記載があります。）

```
COPY server.crt /usr/local/apache2/conf/
COPY server.key /usr/local/apache2/conf/
```
Dockerfile内でコピーしているserver.crt server.keyファイルはお使いの環境のホスト名に合わせる必要があります。

なお、**このホスト名はDockerコンテナのホスト名ではなく、コンテナが動いているホストのホスト名を指定します。**

OAuth2で接続する際、SSL証明書がホスト名と一致しているか検証しているためです。


## 証明書ファイルの作成方法
2020.1以前のIRISのPKI機能でキーファイルを作成するかopensslなどをお使いください。

作成したkeyは以下のコマンドでパスフレーズ入力が不要なタイプに変更しておく必要があります。

```
$ openssl rsa -in cert.key.org -out cert.key
```

## OAuth2のサーバ／クライアント／リソースサーバ用の証明書ファイルの準備
Apacheコンテナ用SSL証明書ファイルの他に、3セット（OAuth2のサーバ／クライアント／リソースサーバ用）準備してください。

Apacheコンテナ用SSL証明書と同様の手順で作成してください。


## コンテナの内容
Apache用コンテナとIRIS for Health用コンテナの2つコンテナが作成されます。

（Apache用コンテナの作成詳細は、[Dockerfile](/Dockerfile)をご参照ください）

IRIS for Health用コンテナでは、コンテナ開始時に[永続的な%SYS(Durable %SYS)](https://docs.intersystems.com/irislatest/csp/docbookj/DocBook.UI.Page.cls?KEY=ADOCK#ADOCK_isc)を指定しています。

この設定により永続データはコンテナ内ではなく、ホスト側に配置できるように設定できます。

[docker-compose.yml](/docker-compose.yml)ファイルの以下の指定が永続的な%SYSの指定を行っています。（ISC_DATA_DIRECTORY に指定されているパスがコンテナ内のパスです）
>    environment:
>      - ISC_DATA_DIRECTORY=/ISC/dur

また、以下の指定により、docker-compose.ymlのあるカレントディレクトリをコンテナ内の /ISC ディレクトリをマウントしています。
（ホストからコンテナの /ISC ディレクトリ以下の操作が行えます）
>    volumes:
>      - .:/ISC



## コンテナ開始手順

コンテナのビルド
```
docker-compose build
```

コンテナの開始
```
docker-compose up
```
(docker-compose up -d でも起動できますが、エラーが出ていないかどうか確認のため、-d無しで実行してください)


コンテナの停止
```
docker-compose stop
```

コンテナの破棄
```
docker-compose down
```

