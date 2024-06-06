---
title: "mac で cdk の環境構築"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "cdk"]
published: true
---

# 概要

各所で cdk のセットアップ方法を共有する必要があったので、zenn の記事にしようと思いました。

主に以下のツールを使って `cdk synth` が通るまでの手順を記載しています。

| ツール   | 用途                                        |
| -------- | ------------------------------------------- |
| homebrew | 言わずと知れた macOS のパッケージ管理ツール |
| anyenv   | 各言語のバージョンを管理するためのツール    |
| nodenv   | Node のバージョンを管理するためのツール     |
| Node     | JavaScript 実行環境                         |
| aws cli  | aws を cli で操作するためのツール           |

自由に使える AWS 環境がすでにあることを前提としています。

CDK の記述は TypeScript で行います。

# 手順

## homebrew の設定

[brew.sh](https://brew.sh/ja/) の記述に従ってインストールしてください。

mac にソフトウェアをインストールしたい場合、可能な限り homebrew or AppStore でのインストールを推奨します。

ブラウザから dmg ファイルなどでインストールした場合、アップデートなどの対応を自身で定期的に行い続ける必要があります。

その点、homebrew ならコマンド一つで完了です。

## AWS CLI の設定

[AWS CLI の開始方法](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-prereqs.html) に従って AWS CLI をインストールしてください。

profile を設定した状態で `aws s3 ls --profile ${hoge}` できれば成功です。

## anyenv の設定

anyenv を用いることで、よく用いられる言語の環境構築をコマンド一つで管理できます。

各言語を homebrew などでインストールすることもできますが、バージョン切り替えが有識者以外は困難です。

anyenv の使用を強く推奨します。

anyenv は下記のコマンドで初期設定可能です。

```
brew install anyenv
echo 'eval "$(anyenv init -)"' >> ~/.zshrc
. ~/.zshrc
anyenv install --init
anyenv -v
```

CommandLineTooles をインストールしていない場合、anyenv の初期化に失敗するので、下記コマンドを実行してください。

このコマンドは時間がかかるので、余裕のある時にやりましょう。

```
xcode-select --install
```

anyenv は時々手動でアップデートしてあげる必要があります。

下記のようにプラグインを使ってアップデートすると楽です。

```
mkdir -p -p $(anyenv root)/plugins
git clone https://github.com/znz/anyenv-update.git ~/.anyenv/plugins/anyenv-update
anyenv update
```

## nodenv/node の設定

nodenv を用いることで、node のバージョンをコマンド一つで管理できます。

node を直接インストールした場合、node のバージョン切り替えが有識者以外は困難です。

nodenv を直接インストールも可能ですが、複数の言語を使いたい場合、pyenv や tfenv など複数の hogenv を併用することになるため、 hogenv 自体のバージョン管理が面倒になります。

anyenv 経由で nodenv をインストールすることを推奨します。

nodenv の anyenv でのインストールは下記コマンドで行います。

```
anyenv install nodenv
exec $SHELL -l
nodenv -v
```

node を nodenv 経由でインストールするには下記コマンドを使用してください。

個人的にはメジャーバージョンが最新一つ前の一番新しいものを使うことが多いです。

```
nodenv install -l # バージョン確認
nodenv install ${your_favorite_version}
```

node の最新バージョンが見つからない場合は、`anyenv update` を実行してください。

nodenv で使うバージョンを切り替えたい場合、下記のいずれかのコマンドを用いてください。

```
nodenv global ${version_number}
nodenv local ${version_number}
```

後述する local で指定されている場合を除き、端末上で使用する node のバージョンは global で設定されたものになります。

local を使用した場合、カレントディレクトリに `.node-version` ファイルが生成されます。

そのディレクトリで node を使用する場合、global ではなく `.node-version` で指定されているバージョンが優先されます。

ローカルに nodenv で管理されていない node を独自でインストールしている場合、このルールが適用されない場合があるので注意してください。

global/local の仕様は全ての hogenv 兄弟で大体同じです。

## CDK の動作確認

[最初の AWS CDK アプリ](https://docs.aws.amazon.com/ja_jp/cdk/v2/guide/hello_world.html) に従って、`npx cdk deploy` してみてください。

自分が想像できるうまくいかないケースは下記になります。

#### bootstrap を忘れている

初めてデプロイするアカウント/リージョンでは `npx cdk bootstrap` を行う必要があります。

自分はよく bootstrap を忘れて、なんだっけ？となります。

ref) https://docs.aws.amazon.com/ja_jp/cdk/v2/guide/bootstrapping.html

#### プロファイルの設定を忘れている

デプロイ時に AWS のプロファイルを使用する場合、 aws cli の時と同様、`--profile` オプションを指定する必要があります。

これは bootstrap の時も同様です。

自分はよく default のアカウント/リージョンに間違えて作ってしまっていたので、default profile は削除しました。

#### 何かの仮想環境をアクティベートしている

例えば python で `source env/bin/activate` していると、 cli のバージョンが変わってうまく動かなかったりします。

自分はこの問題に最初出会ってから解消するまでに数日費やしました。

#### npx を使っていない

あまり詳しくはないのですが、npx を使うと `package.json` で cdk のバージョンが管理できる様です。

npx を使っていないと、 homebrew などでインストールした cdk が使用され、バージョンが合わず動かなくなる様です。

homebrew でインストールした cdk はバージョンを管理しにくいので、npx 経由で使用しましょう。

# 終わりに

あなたもこれで CDK 使いになりました。

おめでとうございます。
