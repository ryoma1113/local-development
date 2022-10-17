# 前提要素
- php7.2.5 (7.1〜7.3) (composer:2.1)
- mysql5.7
- nginx1.20 (npm nodejs)
- docker構成
    - web
    - app
    - db

# import手順

1. このプロジェクトをクローン
1. クローンしたファイル名を好きに変更（local-development→{change-project}）
1. 下記のような構成になるように対象のLaravelプロジェクトをクローン（or作成)

```
- local-development(変更した名称:{change-project}) < 
            - infra
            - docker-compose.yml
            - Makefile
            - README.md
            - 対象のプロジェクト


## コマンドラインでは
~/local-development# > git clone ...

```

1. docker-compose.ymlをプロジェクトに合わせるために変更
`#変更`となっている行の『...』をそれぞれ指示通りに変更

```

...

version: "3"
volumes:
  php-fpm-socket:
  db-store:
  psysh-store:
services:
  app:
    build:
      context: .
      dockerfile: ./infra/docker/php/Dockerfile
    volumes:
      - type: volume
        source: php-fpm-socket
        target: /var/run/php-fpm
        volume:
          nocopy: true
      - type: bind
        source: ./『lara-project』 #変更(対象プロジェクト名へ)
        target: /work/home
      - type: volume
        source: psysh-store
        target: /root/.config/psysh
        volume:
          nocopy: true
    environment:
      - DB_CONNECTION=mysql
      - DB_HOST=db
      - DB_PORT=『3326』#変更(対象プロジェクトの.envに記載されたDB_PORT(他のプロジェクトと被る場合は別の番号を.envにも反映))
      - DB_DATABASE=${DB_NAME:-『local_db』} #変更(対象プロジェクトの.envに記載されたDB_NAME)
      - DB_USERNAME=${DB_USER:-『localuser』} #変更(対象プロジェクトの.envに記載されたDB_USER)
      - DB_PASSWORD=${DB_PASS:-『secret』} #変更(対象プロジェクトの.envに記載されたDB_PASS)

  web:
    build:
      context: .
      dockerfile: ./infra/docker/nginx/Dockerfile
    ports:
      - target: 80
        published: ${WEB_PORT:-『85』} #変更(既に使っていたら別のPORT番号)
        protocol: tcp
        mode: host
    volumes:
      - type: volume
        source: php-fpm-socket
        target: /var/run/php-fpm
        volume:
          nocopy: true
      - type: bind
        source: ./『lara-project』 #変更(対象プロジェクト名へ)
        target: /work/home

  db:
    image: mysql:5.7
    build:
      context: .
      dockerfile: ./infra/docker/mysql/Dockerfile
    ports:
      - target: 3306
        published: ${DB_PORT:-『3326』} #変更(対象プロジェクトの.envに記載されたDB_PORT(他のプロジェクトと被る場合は別の番号を.envにも反映))
        protocol: tcp
        mode: host
    volumes:
      - type: volume
        source: db-store
        target: /var/lib/mysql
        volume:
          nocopy: true
    environment:
      - MYSQL_DATABASE=${DB_NAME:-『local_db』} #変更(対象プロジェクトの.envに記載されたDB_NAME)
      - MYSQL_USER=${DB_USER:-『localuser』} #変更(対象プロジェクトの.envに記載されたDB_USER)
      - MYSQL_PASSWORD=${DB_PASS:-『secret』} #変更(対象プロジェクトの.envに記載されたDB_PASS)
      - MYSQL_ROOT_PASSWORD=${DB_PASS:-『secret』} #変更(対象プロジェクトの.envに記載されたDB_PASS)

```
