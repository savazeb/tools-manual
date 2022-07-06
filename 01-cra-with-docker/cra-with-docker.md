# CRA with docker (開発)

[Create React App](https://create-react-app.dev/) とは Facebook が提供している CLI ツールで用意されたテンプレートを元にアプリケーションの雛形を生成してくれるものです。

雛形を提供するだけでなくオプションを指定することで TypeScript や PWA など色々な用途に合わせたアプリケーションの雛形を作成してくれます。

[ [see more ...] ](https://enjoyworks.jp/tech-blog/6889#:~:text=Create%20React%20App%20%E3%81%A8%E3%81%AF%20Facrbook%20%E3%81%8C%E6%8F%90%E4%BE%9B%E3%81%97%E3%81%A6,%E3%81%97%E3%81%A6%E3%81%8F%E3%82%8C%E3%82%8B%E3%82%82%E3%81%AE%E3%81%A7%E3%81%99%E3%80%82&text=%E9%9B%9B%E5%BD%A2%E3%82%92%E6%8F%90%E4%BE%9B%E3%81%99%E3%82%8B%E3%81%A0%E3%81%91,%E4%BD%9C%E6%88%90%E3%81%97%E3%81%A6%E3%81%8F%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82)

---

## 構築方法

### テスト環境

- Windows 10 Pro (19046.1766)
- Docker version 20.10.14, build a224086
- docker-compose version 1.29.2, build 5becea4c
- node v16.15.0

### Create React App

以下のコマンドで新しいプロジェクトを作成する

```bash=1
npx create-react-app my-app
cd my-app
npm start #アプリを起動
```

### Docker

- Docker の自作イメージの立ち上げ方
- ローカルにあるデータをコンテナにコピーする方法

初期のディレクトリ構成

```
.
├── docker
│   ├── Dockerfile
│   └── docker-compose.yaml
└── my-app
    ├── README.md
    ├── node_modules
    ├── package-lock.json
    ├── package.json
    ├── public
    └── src.
```

Docker の自作イメージを作成するには、二つのファイル(Dockerfile・docker-compose.ymal)が必要。Dockerfile と docker-compose が 最も効力を発揮する場面は、チームメンバーに同じ Docker 環境を構築してもらうとき。

- dockerfile
  これができると（Dockerfile を書けるようになると）自分の好きなようにカスタマイズした Docker Image をコマンド 1 行で作成できるようになる
- docker-compose
  これができると（コンテナ管理を docker-compose による管理で行うと）複数のコンテナで構成されるアプリケーションについて、Docker Image のビルドや各コンテナの起動・停止、ネットワーク接続をコマンド 1 行で実行できるようになる。

Dockerfile に記述する。

```dockerfile=1
# 自作イメージの作成に使うDocker Image
FROM node:16.15-alpine

# Docker環境のダイレクトリー
WORKDIR /my-app

# Docker環境にインストールしたアプリをコピーする
COPY ../my-app/. /my-app

# アプリケーションのポート
EXPOSE 3000

# アプリケーションを起動させる
CMD ["npm", "start"]
```

docker-compose.yaml に記述する。

```dockerfile=1
version: "3.7"
services:
  react-ui:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: react-ui
    ports:
      - "3000:3000"
```
