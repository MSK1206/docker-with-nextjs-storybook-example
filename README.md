# Docker で Next.js + Storybook の開発環境を作る

## ディレクトリの作成

```bash
mkdir docker-with-nextjs-storybook-example

cd docker-with-nextjs-storybook-example
```

## Dockerfile & docker-compose.yml を作成

- Linux,Mac をお使いはおなじみの touch で作りましょう。

- Windows で PowerShell をインストールしている場合

```powershell
new-item Dockerfile, docker-compose.yml, docker-compose-storybook.yml
```

## Dockerfile

```dockerfile
FROM node:20.10.0-alpine

WORKDIR /app

RUN npm install -g npm@10.2.5
```

## docker-compose.yml

```yml
version: '3.9'

services:
  app:
    container_name: next-app
    build:
      context: .
    tty: true
    environment:
      - WATCHPACK_POLLING=true
    volumes:
      - ./next-app:/app
    ports:
      - '3000:3000'
    command: sh -c "npm run dev"
```

- Tips: ホットリロードを有効にしておく記述

```yml
environment:
  - WATCHPACK_POLLING=true
```

## docker-compose-storybook.yml

```yml
version: '3.9'

services:
  app:
    container_name: storybook
    build:
      context: .
    tty: true
    environment:
      - WATCHPACK_POLLING=true
    volumes:
      - ./next-app:/app
    ports:
      - '6006:6006'
    command: sh -c "npm run storybook"
```

## image を構築

```bash
docker-compose build
```

## Next.js のインストール

対話形式に沿ってインストール

```bash
docker-compose run --rm app sh -c 'npx create-next-app@latest . --ts --tailwind --eslint --app --src-dir --import-alias @/* --use-npm'
```
※options を指定してインストールする場合は公式の[`APIリファレンス`](https://nextjs.org/docs/pages/api-reference/create-next-app)を参照し適宜自分の合ったものに変更してください。

## Storybook のインストール
```bash
docker-compose run --rm app npx storybook@latest init

docker-compose run --rm app npx storybook@latest add @storybook/addon-styling-webpack
```

## Next.js を動かしてみる

```bash
docker-compose up
```

[`localhost:3000`](http://localhost:3000) でアクセスできれば OK です。

## ついでに Storybook を動かす

```bash
docker-compose -f docker-compose-storybook.yml up
```

[`localhost:6006`](http://localhost:6006) でアクセスできれば OK です。
