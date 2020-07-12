# HTTP化

<link rel="stylesheet" type="text/css" href="doc/style.less">

## キーワード
| キーワード    | 概要                     |
| :------------ | :----------------------- |
| /var/www/html | apacheのファイルの格納先 |


## 使用ソフト

### メインソフト

| ソフト名 | 選定理由 | リンク先 |
| :------- | :------- | :------- |
|          |          |          |

### apt

| ソフト名  | 選定理由                                                                 | インストールコマンド |
| :-------- | :----------------------------------------------------------------------- | :------------------- |
| apache    | 音声認識のWebSpeechで人の手を介さないようするためhttpsで動作させたいため |                      |
| pi-config | ラズパイで動作させるため                                                 |                      |

### その他

| ソフト名           | 選定理由                           | インストールコマンド |
| :----------------- | :--------------------------------- | :------------------- |
| スクリーンセーバー | 画面を暗転させたくないため         | apt-                 |


## 設定手順

### HTTPSへアクセス可能にする

Web Speech APIはHTTPSでないと毎回マイクの確認をすることになるので、下記のサイトを参考にWeb Server化してHTTPSにアクセスする。

https://variax.wordpress.com/2017/03/18/adding-https-to-the-raspberry-pi-apache-web-server/

- 「sudo a2ensite ssl」ではなくて、「sudo a2enmod ssl」
- 証明書は本来ルートのみ閲覧可能にすべきだが、ROSから触れるようにユーザー権限まで落とす。（セキュリティホール）

``` bash
# apacheのインストール
sudo apt-get update
sudo apt-get install apache2 openssl
```
``` bash
# 証明書の作成
## 証明書は「/etc/ssl/certs」にインストールされるが、専用のパスに生成します。
sudo mkdir -p /etc/ssl/localcerts
## 証明書の作成、必要な設定を質問されるので、自身の情報を記載してください。
sudo openssl req -new -x509 -days 365 -nodes -out /etc/ssl/localcerts/apache.pem -keyout /etc/ssl/localcerts/apache.key
## 本来はrootしかみれない600だが、当バージョンではrosが閲覧できるように権限を持たせる。
sudo chmod 555 /etc/ssl/localcerts/apache*

# SSLの有効化
sudo a2enmod ssl
sudo a2ensite default-ssl

# サイトの有効化
cd /etc/apache2/sites-available
sudo cp default-ssl.conf raspberrypi_orwhatever.conf
sudo nano raspberrypi_orwhatever.conf
```

```txt
# 下記のようにヘッダを変更してください。
<VirtualHost raspberrypi_orwhatever:443>
# 証明書のリンク先を記載してください。
SSLCertificateFile /etc/ssl/localcerts/apache.pem
SSLCertificateKeyFile /etc/ssl/localcerts/apache.key
```

```bash
# サイトの有効化
sudo a2ensite raspberrypi_orwhatever
# apacheの再起動
sudo service apache2 restart
```

