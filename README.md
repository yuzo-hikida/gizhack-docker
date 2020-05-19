最初に作業用のディレクトリを作成してその中にdockerのリポジトリをクローンする。

```
$ mkdir プロジェクトディレクトリ
$ cd 作成したディレクトリ
$ git clone https://github.com/yuzo-hikida/gizhack-docker.git
```

クローンできたら下記の手順でdocker-compose up -dまで行う。

```
$ cd gizhack-docker/
$ cd docker-config
$ docker-compose build
$ docker-compose up -d
```

ここまでできたら一度上の階層のディレクトリに戻りsrcディレクトリを作成します。  
srcディレクトリにはLaravelとvueのディレクトリを入れる為  
両方クローンをしましょう。

## **①laravel設定**

```
$ cd ../
$cd src
$ git clone -b develop https://github.com/gizumo-inc/gizhack_team_h.git //developをクローン
$ mv gizhack_team_h laravel //名前をlaravelに変更
```

名前の変更が終了したらdocker-compose.yml直下のディレクトリに移動し 下記の手順で.envの変更まで行う。

```
$ cd ../ //docker-compose.yml直下に移動
$ cd docker-config/
$ docker-compose exec php-fpm bash
# cd laravel
# chmod -R 777 bootstrap/cache
# chmod -R 777 storage
# composer install
# cp .env.example .env
# vi .env
```

.envの修正

```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=gizmohack
DB_USERNAME=gizhack
DB_PASSWORD=secret
```

.envにmysqlの設定を行い、keyを設定する

```
# php artisan key:generate
# exit
```

## **②Vueの設定**

```
$ cd ../
$ cd src
$ git clone https://github.com/kan-kan/vue-front.git
```

```
$ cd vue-front/
$ npm install
$ git branch
```

`* master と出ます。`  
作業ブランチを取り込みます。

```
$ git branch -a
```
`* master`  
  remotes/origin/GIZ-123  
  remotes/origin/HEAD -> origin/master  
  remotes/origin/develop  
  remotes/origin/master  

- 通常はdevelopを取り込む ーーーーーーーーーーーーーーーーーーーーーーーー

```
$ git checkout -b develop origin/develop
$ git branch
```
`* develop ブランチが切り替わる`  
master  

```
$ npm run build
```

- ログイン画面出す場合 ーーーーーーーーーーーーーーーーーーーーーーーーーーー
```
$ git checkout -b GIZ-123 origin/GIZ-123
$ git branch
```
`* GIZ-123 ブランチが切り替わる`  
master  

```
$ npm run build
```

## **③確認**

[https://localhost/login](https://localhost/login)画面でます。

## **④開発用フロントサーバーの立ち上げ**

```
$ cd ../../
$ cd docker-config/
$ docker-compose exec nginx sh
# cd /var/www/html/vue-front/
# apk add --update nodejs nodejs-npm
# npm rebuild node-sass
# npm run serve
```

