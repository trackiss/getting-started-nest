# NestJS を始めよう

---

## [NestJS](https://nestjs.com/) ってなに？

TypeScript で書かれた Node.js 向けのバックエンド フレームワーク。

---

## そもそも [TypeScript](https://www.typescriptlang.org/) とは？

JavaScript のスーパーセット (上位互換)。Microsoft 製。最終的に JavaScript へ変換される。

JavaScript は構文が単純なうえ、ブラウザーさえあればどんな環境でも動作するため、Web 2.0 の登場とともに広く使われた。しかし、動的型付けであることなどを筆頭に、バグを生む温床となりやすい仕様が数多く存在している。  
そうした仕様を改善し、より堅牢でメンテナンス性の高い言語を求めて作られたのが TypeScript である。

---

## [Node.js](https://nodejs.org/ja/) とは？

JavaScript はもともとブラウザー上で動作するクライアントサイドの言語である。だが、次第に普通の言語と同じように使用できるようにしたいという考え方が出てきた。

そこで、Chromium (Google Chrome の派生元) に搭載されていた超高速な JavaScript エンジンである V8 Engine をベースに、コンピューター上で動作させられるよう拡張したものが Node.js である。  
軽量なスクリプトとしても使われるほか、サーバーサイドの環境としても利用される。

ごく最近になって Node.js の後継として開発された [Deno](https://deno.land/) がリリースされたが、完全な移行にはまだしばらく時間がかかると思われる。

---

## NestJS があるとなにが嬉しいのか？

NestJS は、バックエンド、すなわちサーバー側の処理を行うのに適している。  
主に以下のような特徴を持っている。

### TypeScript-friendly

NestJS そのものが TypeScript で書かれており、TypeScript の機能を活かしたスマートなコーディング体験を可能にしている。  
Express.js などの従来のフレームワークでも TypeScript をサポートしていることは多いが、その多くは JavaScript を前提に設計されており、最新の機能を活かしきれていない。

### 明確で堅牢なアーキテクチャー

NestJS は、**Repository** (無理に導入する必要はないが推奨)、**Service**、**Controller** と、これらを管理する **Module** によって構成される。

軽く各モジュールの説明をしておく。

#### Repository

データベースの操作を提供するモジュールである。  
Repository は、ビジネス ロジック (要件を実現させるための本質的なロジック) とデータベース アクセスを分離するためによく採られる手法であるリポジトリー パターンにおける概念のひとつ。

ビジネス ロジックが直接データベースで依存してしまうと、本来の関心事ではないロジックが混ざってしまううえ、別のデータベースへ移行する際に問題が生じやすい。  
そこで、中間層として Repository を実装し、ビジネス ロジックとデータベースを分離させる。

#### Service

いわゆるビジネス ロジックに相当するモジュールである。Repository とやりとりし、Controller へ対して機能 (usecase) を提供する。

#### Controller

クライアントとやりとりをするモジュールである。Service が提供する機能を外部へ公開する。  
Provider とも呼ばれる。

#### Module

上記 3 つのモジュール間の依存関係を解決するモジュールである。  
一種の DI (dependency injection, 依存性の注入) コンテナーとして動作する。

<br>

ここで、依存関係は以下のようになる。

```txt
DB <-依存-- Repository --実装-> RepositoryInterface <-依存-- Service --実装-> ServiceInterface <-依存-- Controller

(Module はすべての実装へ依存する)
```

Repository、Service、Controller 間は Interface を使用して契約に依存するように設計する。

![概念図](img/nest-class.png)  
(↑これは MS ペイントで書いた概念図。さらにわかりやすくなったね)

### 統合されたバリデーション

ユーザー名やパスワードなど、入力されてくるデータが適切であるか (文字数が正常範囲内であるか、形式が間違っていないか、使用できない文字が混ざっていないか) を検証することをバリデーション (検証) という。  
NestJS では、TypeScript のデコレーターという機能を使って、組み込みのバリデーション機能を提供している。

### TypeORM のサポート

データベースのテーブル構成とプログラミングにおけるオブジェクトとを相互にマッピングするミドルウェアを ORM (object-relational mapper) という。  
Node.js の同種の ORM の中でも評判のよい [TypeORM](https://typeorm.io/) をサポートしており、Web 開発でよく使用される NoSQL の一種である MongoDB だけでなく、MySQL や PostgreSQL などの RDB を使用することもできる。

### Passport のサポート

ユーザー名とパスワードの認証として、Node.js でよく使われる [Passport](http://www.passportjs.org/) をサポートしている。

### カスタマイズ可能なコントローラー

デフォルトでは REST API をサポートしている。これに加え、WebSocket や gRPC、GraphQL などの通信プロトコルもサポートする。

### 統合されたルーティング

多くのバックエンド フレームワークでは、Controller と ルーティング (URL と公開する機能の対応付け) とを分離していることが多い。  
これは、ルートを俯瞰的に理解しやすいメリットはあるが、現在開発している Controller が実際のところどの部分を担当しているコントローラーなのかをうかがい知ることが難しい。

NestJS では、Controller とルーティングを統合している。

### API ドキュメントの自動生成機能

API ドキュメントを自動生成するだけでなく、Swagger UI と連携して API テストを容易に行えるしくみになっている。

<br>

etc.

---

## 環境構築

環境構築のハウツー。すでにインストールしてあるのなら適宜飛ばしてもらってかまわない。まったくゼロの状態からだとそこそこ大変なので覚悟しておこう。  
ほかのやり方もいくらでもあるので、基本的に好きなようにしてもらってかまわないが、バージョンはそろえること。

なお、全体的に Windows での操作を想定している。それ以外の OS を使用している場合は適切な方法を選択すること。

### Nodist のインストール

Node.js にはさまざまなバージョンがあり、それらを共存させるためには [Nodist](https://github.com/nullivex/nodist) というツールを使用する。  
「自分はひとつのバージョンしか使わない」という絶対の自信がある場合は [Node.js のインストーラー](https://nodejs.org/ja/) を使うなり Chocolatey を使ってインストールするなりしてもらってかまわない。

[ここから](https://github.com/nullivex/nodist/releases/latest) インストーラーをダウンロードし、インストールする。  
インストール後、PowerShell から次のコマンドを実行する。

```pwsh
node -v
```

バージョンが表示されれば、インストールに成功している。

...ちなみに、Windows 10 標準の PowerShell は非常に古いため、この機会に最新のバージョンへ更新しておくと幸せになれる。最新の安定版は [ここから](https://github.com/PowerShell/powershell/releases/latest) ダウンロードできる。  
[Microsoft のデベロッパーによるツイート](https://twitter.com/richturn_ms/status/1265155820083240962) からもわかるように、コマンドプロンプト (cmd.exe) は必要のないかぎり使用しないこと。

### Node.js のインストール

次に、Nodist を使って Node.js をインストールする。
2020年6月現在、最新の LTS 版である **12.17.0** をインストールしよう。

PowerShell から次のコマンドを実行する。

```pwsh
nodist + 12.17.0
```

インストール後、次のコマンドを実行する。

```pwsh
node -v
```

バージョンが表示されれば、インストールに成功している。

### npm のアップデート

次に npm をアップデートしておく。  
npm (node package manager) というのは、Node.js のパッケージ (ライブラリのようなもの) を管理するツールのことである。

PowerShell から次のコマンドを実行する。  
`npm install -g xxx` は、`xxx` をグローバルにインストールするよ、という意味。対して、各プロジェクト内だけ (ローカル) にインストールするときは `-g` オプションをなくす。

```pwsh
npm i -g npm
```

npm じゃなくて [yarn](https://classic.yarnpkg.com/ja/) がいい！  
という人もいるかもしれないが、最近の npm は yarn 並に速度が向上してきているうえ、NestJS のドキュメントではすべて npm コマンドになっているので、ここは統一しておいたほうがいいだろう。

### Nest CLI のインストール

次に、NestJS 独自のコマンドライン ツールである [Nest CLI](https://github.com/nestjs/nest-cli) をインストールする。  
これ以降、Nest に関わるコマンドはこの Nest CLI から実行することになる。

PowerShell から次のコマンドを実行する。  

```pwsh
npm i -g @nestjs/cli
```

インストールが完了したら、PowerShell から次のコマンドを実行して確認しよう。

```pwsh
nest -v
```

---

## ひな形の作成

いよいよ、NestJS を使って Hello World! をしてみる。

プロジェクトを作成するフォルダーへ移動し (つまり、プロジェクト ディレクトリーのひとつ上のディレクトリー)、PowerShell から次のコマンドを実行する。

```pwsh
nest new nest-sample
```

`nest new xxx` で `xxx` というプロジェクトが作成される。プロジェクト名は任意に決めてよい。  
すると、次のように、どのパッケージ マネージャーを使用するか訊かれる。チュートリアル的には npm を選んでほしいところだが、あなたが真に ❤️ なほうを選ぼう。

```
? Which package manager would you ❤️  to use? (Use arrow keys)
> npm
  yarn
```

しばらく待つと (初回はそこそこ速いマシンでもそこそこ時間がかかる)、数多くのファイルが作成される。

細かい説明は抜きにして、PowerShell から次のコマンドを実行してみよう。  
`cd xxx` についての説明はいらないと思う。`npm tun start` でアプリケーションがビルドされ、サーバーが起動する。

```pwsh
cd nest-sample
npm run start
```

少し待って、` [NestApplication] Nest application successfully started` と表示されれば準備完了。  
途中で “Windows セキュリティの重要な警告” が表示されたら、“アクセスを許可” を選択する。

さて、あとはお使いのブラウザーで `http://localhost:3000/` にアクセスしてみれば、`Hello World!` が表示されるはずだ。

---
