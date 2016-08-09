
「さくらのクラウドでのDockerハンズオン」および「さくらのクラウドのハンズオン」で使用するyamlファイルです。
<br>
内容は[「Zabbix 3.0をDocker Composeで一度に実行する方法」](http://qiita.com/zembutsu/items/686b99be90d72688aee8)の中にある。Zabbix3.0をDocker composeを利用してインストールするyamlファイルのコピーとなります。
<br>
<br>
以下は、主に「さくらのクラウド」でのハンズオンで使用するコマンドとなります。

## ローカルネットワークを組む



```
ifconfig –a
```
上記コマンドで「eth1」が登録されているか確認する。


```
ifconfig eth1 192.168.0.1/24
```

上記コマンドで「eth1」のIPアドレスを設定する。

```
ping 192.168.0.2
```
上記コマンドで、Pingを打つ。CTRL + Cで止まる。


## WordPressをインストールする。

■Apache、php mysqlのインストール

```
yum install -y httpd php mysql-server php-mysql 
```

■Apacheの起動

```
service httpd start
```

■MySQLの起動

```
service mysqld start
```

■MySQLのユーザ設定
```
/usr/bin/mysqladmin -u root password 'パスワード' 
```
上記のパスワードの部分を半角英数字のパスワードに置き換える。

■mysqlの中に入る
```
mysql -u root -p
```

■データベース「wp」を作る。

```
create database wp;
```

■データベースにwpにアクセスするユーザを作成する。

```
grant all privileges on wp.* to wp@localhost identified by'パスワード'; 
```
上記のパスワードの部分を半角英数字のパスワードに置き換える。


■\qでMySQLを抜ける

```
\q 
```


■PHPの設定

```
vi /etc/php.ini
```

■VIで下記の部分を編集

date.timezone = "Asia/Tokyo" 

■Apacheの再起動


```
service httpd restart

```

■PHPの確認


```
vi /var/www/html/index.php

```
下記を入力する
<?php phpinfo() ?> 


■フォルダを移動

```
cd /var/www/html 
```

■WordPressファイルを持ってくる

```
wget https://ja.wordpress.org/latest-ja.tar.gz
```

■ファイルを解凍する

```
tar zxf latest-ja.tar.gz
```

■ファイルの所有者の変更
```
chown -R apache:apache wordpress
```

■ブラウザの確認
ブラウザのタブを1枚開いて「IPアドレス/wordpress/」を入力





## Dockerのインストールと起動

```
curl -sSL https://get.docker.com/ | sh
```
上記コマンドでDockerをインストールできる。



```
systemctl start docker
```
上記コマンドでDockerを起動する



```
docker run -p 80:2368 -d ghost
```

上記コマンドでghostをスタートする。


```
docker ps
```
Dockerのプロセスを確認する


```
docker stop (CONTANER ID)
```

Dockerのコンテナを止める



## wordpressをDockerで作る

WordPressのイメージを持ってくる
```
docker run wordpress

```

MySQLのイメージを持ってくる。

```
docker pull mysql

```


MySQLの設定
```
docker run --name mysql -e MYSQL_ROOT_PASSWORD=(パスワード) -d mysql
```

WordPressを動かす
```
docker run --name wordpress --link mysql:mysql -p 80:80 wordpress

```

デタッチモードで動かす
```
docker run --name wordpress2 -d --link mysql:mysql -p 80:80 wordpress

```



## Docker composeでzabbixを動かす

```
curl -L https://github.com/docker/compose/releases/download/1.6.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

```
chmod +x /usr/local/bin/docker-compose 
```

上記2つのコマンドでDocker composeをインストール


```
git clone https://github.com/wslash/zabbix3.git
```

上記のコマンドでyamlをこのページから取ってくる。


```
cd zabbix3
```
作成されたzabbix3のフォルダに移動する。


```
docker-compose pull
```

zabbixに必要なファイルを取ってくる


```
docker-compose up -d

```
上記のコマンドで「docker　compose」を利用して zabbixを立ち上げる


