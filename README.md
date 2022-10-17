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
1. 下記のような構成になるように対象のLaravelプロジェクトをクローン（or作成)

```
- local-development < 
            - infra
            - docker-compose.yml
            - Makefile
            - README.md
            - 対象のプロジェクト


## コマンドラインでは
~/local-development# > git clone ...

```

1. クローンしたプロジェクト(以下仮称: {lara-project} )の名前をそれぞれコピー

```
docker-compose.yml <<<

...

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
        source: ./{lara-project}  #変更
        target: /work/home
...

  web:
    build:
      context: .
      dockerfile: ./infra/docker/nginx/Dockerfile
    ports:
      - target: 80
        published: ${WEB_PORT:-85}
        protocol: tcp
        mode: host
    volumes:
      - type: volume
        source: php-fpm-socket
        target: /var/run/php-fpm
        volume:
          nocopy: true
      - type: bind
        source: ./{lara-project}  #変更
        target: /work/home
...

```
