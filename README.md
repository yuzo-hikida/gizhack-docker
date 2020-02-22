# **Laravel vue docker 環境構築**

![]
## **手順**
最初に作業用のディレクトリを作成してその中にdockerのリポジトリをクローンする。
```
$ mkdir プロジェクトディレクトリ
$ cd 作成したディレクトリ
$ git clone https://github.com/yuzo-hikida/docker-hack.git
```

クローンできたら下記の手順で`docker-compose up -d`まで行う。
```
$ cd docker-hack
$ cd docker-config
$ docker-compose build
$ docker-compose up -d
```

ここまでできたら一度上の階層のディレクトリに戻りlaravelのリポジトリをクローンする。
```
$ cd ../
$ git clone https://github.com/yuzo-hikida/gizhack_team_h.git
```

クローンが完了したら中のlaravelというディレクトリがあるが中が空なので下記のコマンドで削除。
最初からgizhack_tean_hしかなかったら下のコマンドだけで良い。
その後に、クローンで作成された`gizhack_tean_h`の名前をlaravelに変更する

```
$ rm -rf laravel
$ mv gizhack_team_h laravel
```

名前の変更が終了したら`docker-compose.yml`直下のディレクトリに移動し
下記の手順で`.env`の変更まで行う。
```
$ cd docker-config
$ docker-compose exec php-fpm bash
# cd laravel
# chmod 777 storage -R
# composer install
# cp .env.example .env
```

.envにmysqlの設定を行い、keyを設定する
```
# php artisan key:generate
# exit
```

docker-compose.ymlを下記に変更して`docker-compose up -d`をもう一度行い  
`docker-compose exec php-fpm bash`の中に入り  
[https://localhost](https://localhost)にアクセスするとLaravelのデフォルト画面が開く

```docker-compose.yml
version: '3'
services:
  nginx:
    image: nginx:alpine
    depends_on:
      - php-fpm
    ports:
      - 80:80
      - 443:443
    volumes:
      - ../:/var/www/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./nginx/cert-key:/etc/nginx/ssl
  php-fpm:
    build: ./php-fpm
    depends_on:
      - mysql
    ports:
      - 9000:9000
    volumes:
      - ../laravel:/var/www/html
  mysql:
    image: mysql:5.7
    ports:
      - 3306:3306
    environment:
      - MYSQL_USER=root
      - MYSQL_ROOT_PASSWORD=secret
      - MYSQL_DATABASE=gizmohack
    volumes:
      - ./data-mysql:/var/lib/mysql
    container_name: mysql57
  selenium:
    image: selenium/standalone-chrome-debug
    ports:
      - 4444:4444
      - 5900:5900
    depends_on:
      - nginx
    privileged: true
    container_name: selenium
```

## **Vueのインストール**

docker-compose.ymlの変更
```
version: '3'
services:
  nginx:
    image: nginx:alpine
    depends_on:
      - php-fpm
    ports:
      - 80:80
      - 443:443
    volumes:
      - ../:/var/www/html
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./nginx/cert-key:/etc/nginx/ssl
  php-fpm:
    build: ./php-fpm
    depends_on:
      - mysql
    ports:
      - 9000:9000
      - 8001:8001
      - 3000:3000
    environment:
      - HOST=0.0.0.0
    working_dir: /var/www/html
    volumes:
      - ../laravel:/var/www/html
  mysql:
    image: mysql:5.7
    ports:
      - 3306:3306
    environment:
      - MYSQL_USER=root
      - MYSQL_ROOT_PASSWORD=secret
      - MYSQL_DATABASE=gizmohack
    volumes:
      - ./data-mysql:/var/lib/mysql
    container_name: mysql57
  selenium:
    image: selenium/standalone-chrome-debug
    ports:
      - 4444:4444
      - 5900:5900
    depends_on:
      - nginx
    privileged: true
    container_name: selenium

```

変更が完了したら下記のコマンド

```
$ docker-compose build
$ docker-compose up -d
$ docker-compose exec php-fpm sh
```
