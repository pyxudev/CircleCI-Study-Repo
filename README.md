# Descripttion
This is a repository for studying CircleCI personaly.<br>
If you have any suggestions, please raise an issue to discuss.

# Study plan(Japanese)
## 🎯 ゴール設定

1. **CircleCI の基本構造と概念**を理解する（ワークフロー、ジョブ、ステップ、オーブなど）
2. **`.circleci/config.yml` を自力で書ける**
3. **GitHub リポジトリと連携し、CI パイプラインを動かす**
4. 実務で使えるレベルの設定（テスト・ビルド・デプロイ）を作成
5. よくあるエラーとデバッグ方法を理解

---

## 📅 1週間の学習スケジュール

| 日 | 達成目標 | 勉強内容 | 参考資料 | Done |
| --- | --- | --- | --- | --- |
| **Day 1** | CircleCI の全体像と用語を理解 | - CircleCI の役割とCI/CDの基礎 - ワークフロー・ジョブ・ステップ・オーブの概念 - GitHub連携の流れ | 公式概要 公式用語集 | ✅ |
| **Day 2** | 最小構成の `.circleci/config.yml` を書く | - `version`, `jobs`, `steps` の基本構造- Hello World ビルドを作成し、GitHub に push → 実行- Web UI のジョブ結果の見方 | Config の書き方 | ✅ |
| **Day 3** | 実務的なテストパイプライン作成 | - Node.js / Python などのプロジェクトをテストするパイプライン作成 - キャッシュの使い方 - `.env` や secrets の設定 | キャッシュ設定 環境変数 | ✅ |
| **Day 4** | 並列実行とワークフロー制御を学ぶ | - 複数ジョブの並列実行 - `requires` による依存制御 - 条件分岐（filters / branch） | ワークフロー設定 | ✅ |
| **Day 5** | デプロイ設定の追加 | - デプロイジョブの作成（例: GitHub Pages / AWS S3 / Vercel） - オーブを使ったデプロイ簡略化 | Orbsの使い方 | ✅ |
| **Day 6** | トラブルシューティング・最適化 | - よくあるエラー（パーミッション、キャッシュ、パス問題） - 再実行・SSHデバッグ方法- 実行時間短縮のベストプラクティス | デバッグ方法 | ✅ |
| **Day 7** | 総合演習 | - 小規模プロジェクトをGitHubに作り、テスト・ビルド・デプロイまでを自動化 - プルリク作成 → 自動テスト → デプロイの一連の流れを完成 | サンプルプロジェクト集 | ✅ |

## Day 1✅

## CI/CD とは？

CI/CD とは、ソフトウェアやアプリ開発には欠かせない 開発手法の一つであり日本語では「継続的インテグレーション＆継続的デリバリー」、英語では「**Continuous Integration・CI / Continuous Delivery・CD**」二つの言葉を一つに略して「**CI/CD**」と略します。CI/CDを開発プロセスに導入することにより、コードの品質チェックはもちろん、バグの発見、テストの自動化、自動化デリバリー、デプロイメントが実現し、開発者やチーム全体の生産性の向上にも非常に効果があると知られている開発手法です。
Reference: https://circleci.com/ja/blog/what-is-ci-cd/

### CircleCI の役割とCI/CDの基礎- ワークフロー・ジョブ・ステップ・オーブの概念

1. **パイプライン (Pipeline)**
    
    パイプラインは、CircleCIにおける**一連のプロセスの全体像**を表します。通常、GitHubやBitbucketのリポジトリへのコードの`push`などをトリガーとして開始されます。1つのパイプラインは、1つ以上のワークフローで構成されます。
    
2. **ワークフロー (Workflow)**
    
    ワークフローは、パイプライン内で実行される**ジョブの実行順序や依存関係を定義**するものです。例えば、「ビルドジョブが成功したら、テストジョブを実行し、テストジョブも成功したらデプロイジョブを実行する」といった流れを定義できます。ジョブを並列に実行したり、特定のブランチでのみ実行したりといった制御も可能です。これにより、複雑なプロセスも効率的に管理できます。
    
3. **ジョブ (Job)**
    
    ジョブは、**特定のタスクを実行する単位**です。例えば、「コードのビルド」「単体テストの実行」「Dockerイメージの作成」「サーバーへのデプロイ」などがそれぞれジョブとして定義されます。各ジョブは、指定されたExecutor（実行環境）上で、一連のステップを実行します。
    
4. **ステップ (Step)**
    
    ステップは、ジョブ内で実行される**個々のコマンドやアクション**です。シェルのコマンドを実行する`run`ステップ、ソースコードをチェックアウトする`checkout`ステップ、キャッシュを保存・復元する`save_cache`/`restore_cache`ステップなど、様々な種類のステップが用意されています。
    
5. **Executor (実行環境)**
    
    Executorは、**ジョブが実行される環境**を指定します。CircleCIでは、主に以下のExecutorが利用可能です。
    
    - `docker`: 指定したDockerイメージ内でジョブを実行します。最も一般的に利用されます。
    - `machine`: Linux, Windows, macOS の仮想マシン上でジョブを実行します。Dockerが利用できない場合や、より多くのリソースが必要な場合に使用します。
    - `macos`: macOSアプリの開発など、macOS環境が必要な場合に使用します。
    
    プロジェクトの要件に合わせて適切なExecutorを選択することが重要です。
    
6. **オーブ (Orb)**
    
    オーブは、**再利用可能な設定のパッケージ**です。よく使われるタスク（例: AWSへのデプロイ、Slackへの通知、特定のフレームワークのテスト設定）がオーブとして提供されており、`config.yml`に数行追加するだけで簡単に利用できます。自分でオーブを作成して共有することも可能です。これにより、設定ファイルの記述量を削減し、メンテナンス性を向上させることができます。
    Reference: https://omomuki-tech.com/archives/182
    

## Day2✅

## Config の書き方

```yaml
version: 2.1

jobs:
  say-hello:
    docker:
      - image: cimg/base:stable
    steps:
      - run: echo "Hello, CircleCI!"

workflows:
  main:
    jobs:
      - say-hello
```

- `version: 2.1`: CircleCIの設定ファイルフォーマットのバージョンを指定しています。最新の機能を利用するために`2.1`が推奨されています。
- `jobs`: 実行される可能性のあるジョブのリストを定義します。
- `say-hello`: `jobs`の中に定義されたジョブの名前です。
- `docker: - image: cimg/base:stable`: このジョブが`cimg/base:stable`というDockerイメージ上で実行されることを指定しています。これはCircleCIが提供する汎用のLinux環境イメージです。
- `steps`: ジョブ内で実行される具体的な手順のリストです。
- `run: echo "Hello, CircleCI! "`: シェルで`echo`コマンドを実行し、”Hello, CircleCI! ”という文字列を出力するステップです。
- `workflows`: どのジョブをどの順序で実行するかを定義します。
- `main`: ワークフローの名前です。
- `jobs: - say-hello`: `main`ワークフローでは、`say-hello`というジョブを実行することを指定しています。

## Day3✅

### Node.js / Python などのプロジェクトをテストするパイプライン作成

Node.js

`npx create-next-app circlecitestapp` 

`npm run lint`

```yaml
version: 2.2

jobs:
  build:
    docker:
      - image: cimg/node:20.10
      # Node.js 公式 CircleCI イメージ（バージョンはプロジェクトに合わせる）
    working_directory: ~/repo

    steps:
      - checkout
      # リポジトリをクローン
      - restore_cache:
          keys:
            - v1-deps-{{ checksum "circlecitestapp/package-lock.json" }}
            - v1-deps-
      - run:
          name: Install dependencies
          command: |
            cd circlecitestapp
            npm ci
      - save_cache:
          paths:
            - ~/.npm
          key: v1-deps-{{ checksum "circlecitestapp/package-lock.json" }}
      - run:
          name: Run lint
          command: |
            cd circlecitestapp
            npm run lint
      - run:
          name: Build Next.js app
          command: |
            cd circlecitestapp
            npm run build

workflows:
  version: 2
  build_and_test:
    jobs:
      - build
```

- **`cimg/node:20.10`**
    - CircleCI が提供する公式 Node.js イメージを利用。
    - プロジェクトの `package.json` に合わせて Node のバージョンを指定するとよいです。
- **キャッシュ (`restore_cache` / `save_cache`)**
    - `package-lock.json` の checksum をキーにしてキャッシュすることで、依存関係インストールを高速化できます。
- **`npm ci`**
    - 再現性のあるクリーンインストール。CI/CD では `npm install` より推奨。
- **テスト → ビルドの順序**
    - `npm test` はデフォルトでは Next.js プロジェクトにテストがないので失敗する可能性あり。
    - もしまだテストを用意していなければ、`npm test` のステップは一旦コメントアウトして OK。
- **ワークフロー (`workflows`)**
    - CircleCI はジョブ単体でも動かせますが、実務では `workflows` で制御するのが一般的。

### キャッシュの使い方

- **キャッシュ (`restore_cache` / `save_cache`)**
    - `package-lock.json` の checksum をキーにしてキャッシュすることで、依存関係インストールを高速化できます。
    - `v1-deps` : キャッシュのバージョン
    `v1` : バージョン、 `v2` などに書き換えると上書きされる
    `deps` : dependencies（依存関係） の略

Reference: [Caching dependencies :: CircleCI Documentation](https://circleci.com/docs/guides/optimize/caching/)

## Day4✅

## 複数ジョブの並列実行

### `requires`

```yaml
workflows:
  version: 2
  build-deploy:
    jobs:
      - build-python
      - build-next
      - deploy:
          requires:
            - build-python
            - build-next
```

`build-python` と `build-next` は **並列実行**され、両方が終わってから `deploy` が実行される。

### 条件分岐（filters / branch）

```yaml
workflows:
  version: 2
  build-deploy:
    jobs:
      - build
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: main
```

- `build` は全ブランチで実行
- `deploy` は **main ブランチに push されたときだけ**実行

## Day5✅

## デプロイジョブの作成

### GitHub Pages にデプロイ

```yaml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/node:20.12
    steps:
      - checkout
      - run: npm install
      - run: npm run build
      - run: npm run export
      - persist_to_workspace:
          root: .
          paths:
            - out

  deploy-ghpages:
    docker:
      - image: cimg/node:20.12
    steps:
      - attach_workspace:
          at: .
      - run:
          name: Install gh-pages
          command: npm install -g gh-pages
      - run:
          name: Deploy to GitHub Pages
          command: gh-pages -d out -r https://$GITHUB_TOKEN@github.com/USERNAME/REPO.git

workflows:
  version: 2
  deploy:
    jobs:
      - build
      - deploy-ghpages:
          requires:
            - build
          filters:
            branches:
              only: main
```

- `$GITHUB_TOKEN` は **CircleCI の Project Settings → Environment Variables** で設定する
- `gh-pages` パッケージを使って `out/` ディレクトリを `gh-pages` ブランチへ push

### Vercel にデプロイ

```yaml
jobs:
  deploy-vercel:
    docker:
      - image: cimg/node:20.12
    steps:
      - checkout
      - run: npm install -g vercel
      - run:
          name: Deploy to Vercel
          command: vercel --prod --token $VERCEL_TOKEN
```

- `$VERCEL_TOKEN` を Vercel アカウントから発行 → CircleCI の環境変数に登録
- `vercel.json` をリポジトリに置いておくと設定が安定する

## オーブを使ったデプロイ簡略化

### GitHub Pages Orb を使ったデプロイ

```yaml
version: 2.1

orbs:
  gh-pages: circleci/gh-pages@1.2.0

workflows:
  deploy:
    jobs:
      - gh-pages/deploy:
          setup: npm ci && npm run build && npm run export
          publish-dir: out
          repo: USERNAME/REPO
          git-username: CircleCI
          git-email: ci@example.com
          token: GITHUB_TOKEN
          filters:
            branches:
              only: main
```

- Orb が自動的に `gh-pages` ブランチを作成してデプロイしてくれる
- `publish-dir: out` を Next.js の export 出力先に指定
- `GITHUB_TOKEN` を CircleCI の環境変数に登録しておく

### Vercel Orb は存在しない（が代替は可能）

シンプルに `Vercel CLI` を呼ぶ

もし Orb っぽく簡略化したいなら `commands:` を自作して再利用

```yaml
commands:
  deploy-vercel:
    steps:
      - run: npm install -g vercel
      - run: vercel --prod --token $VERCEL_TOKEN
```

## Day 6✅

## よくあるエラー

### インデント崩れ

```yaml
      - run:
          name: Run script
```

### CircleCI の YAML スキーマに合わない書き方

エラー：

```
ERROR IN CONFIG FILE: 
[#/jobs/build] 0 subschemas matched instead of one 1. 
[#/jobs/build] only 1 subschema matches out of 2
```

問題発生箇所：

```yaml
workflows:
  version: 2
  build:
    jobs:
      - build
```

✅ 修正した正しい形：

```yaml
workflows:
  version: 2
  ci_pipeline:
    jobs:
      - build
```

### next lint が初回実行時に対話式で ESLint 設定を作ろうとする

エラー：

```
> circlecitestapp@0.1.0 lint 
> next lint 

? How would you like to configure ESLint? 
https://nextjs.org/docs/app/api-reference/config/eslint 
❯ Strict (recommended) 
Base 
Cancel 

Too long with no output (exceeded 10m0s): context deadline exceeded
```

✅ 解決策: 

ローカルで ESLint 設定を生成してコミット

```bash
npm run lint
```

### Python 仮想環境の作り方

- CircleCI の Linux コンテナでは Windows の `Scripts/activate` は存在しません（それは Windows 環境専用のパスです）。
- CircleCI の Linux 環境では `venv/bin/activate` を使うのが正解です。

### キャッシュの対象

- `~/.pip` ではなく、pip のダウンロードキャッシュは `~/.cache/pip` に保存されます。
- 依存関係の再利用をしたいならこっちをキャッシュするのがおすすめです。

### `version:` の値

- CircleCI の `version:` は **2.1 か 2.0** が推奨です（2.3 や 3 は存在しません）。
- ワークフローの `version:` も同様に `2` を書きます。

## Day7✅

## 再実行・SSHデバッグ方法- 実行時間短縮のベストプラクティス

### ✅ 再実行（Rerun）

- **CircleCI の Web UI** → `Workflows` → 該当のジョブで「Rerun」を選択可能
- オプション:
    - **Rerun Job**: そのジョブだけ再実行
    - **Rerun Workflow from failed**: 失敗したジョブ以降を再実行
    - **Rerun from beginning**: 全ジョブを再実行

💡 特に依存関係がある場合は `from failed` が便利です。

### ✅ SSH デバッグ

CircleCI ではジョブを **SSH 付きで再実行**できます。

- UI から「Rerun job with SSH」を選択
- 数分後に SSH 接続情報が表示される
- ローカルで:
    
    ```bash
    ssh -p <PORT> <USER>@<IP>
    ```
    
- その環境に入って `ls`, `cat config.yml`, `npm run build` などを直接実行して調査可能

💡 `~/project` ディレクトリ配下がソースコード、`~/` にキャッシュなどがあります。

# 🔹 実行時間短縮のベストプラクティス

1. **キャッシュの適切利用**
    - `restore_cache` / `save_cache` を使い、依存関係を再利用
    - 例（Python/Pip）:
        
        ```yaml
        - restore_cache:
            keys:
              - deps-{{ checksum "requirements.txt" }}
              - deps-
        - run: pip install -r requirements.txt
        - save_cache:
            key: deps-{{ checksum "requirements.txt" }}
            paths:
              - ~/.cache/pip
        ```
        
    - 例（Node/NPM）:
        
        ```yaml
        - restore_cache:
            keys:
              - npm-deps-{{ checksum "package-lock.json" }}
              - npm-deps-
        - run: npm ci
        - save_cache:
            key: npm-deps-{{ checksum "package-lock.json" }}
            paths:
              - ~/.npm
        ```
        
2. **依存関係の固定**
    - `pip install -r requirements.txt` の代わりに **lock ファイル**を使う
    - Node なら `npm ci` を推奨（`npm install` より高速で再現性あり）
3. **ジョブの並列化**
    - `parallelism: 4` のように指定するとテストを分割できる
    - 例:
        
        ```yaml
        jobs:
          test:
            docker:
              - image: cimg/python:3.11
            parallelism: 4
            steps:
              - checkout
              - run: pytest --maxfail=1 --disable-warnings -q
        ```
        
4. **ワークスペースの活用**
    - あるジョブでビルドした成果物を、次のジョブで再利用可能
    - 例:
        
        ```yaml
        - persist_to_workspace:
            root: .
            paths:
              - build
        - attach_workspace:
            at: .
        ```
        
5. **不要なステップを削る**
    - `npm run lint` や `pytest` を main ブランチのみ実行
    - `filters:` を活用して PR 時は最小限のチェックだけにする
6. **Docker イメージ選び**
    - `cimg/node:20.12` など CircleCI の最適化済みイメージを使う
    - カスタムイメージは自由度が高いが起動に時間がかかる場合あり
