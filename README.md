# dagger-learning

## Useful links

* [Home](https://dagger.io/)
* [Documentation](https://docs.dagger.io/)
* [Git repository](https://github.com/dagger/dagger)

## Getting started

ローカル環境（MacBook）にDaggerをインストールして試す。
Command Line Toolsを最新化していないと上手くいかない点に注意すること。
ローカルを汚したくないならCloud9などを使う。

* [CI/CD in your local dev](https://docs.dagger.io/1200/local-dev)

### Environments

* masOS: 12.3.1
* Homebrew: 3.4.10
* Docker: 20.10.13
* Dagger: 0.2.9

### Sample project

サンプルプロジェクトをビルドしてみる。
`dagger do build`実行時にエラーが発生した。
`dagger project update`を実行して回避できた（原因はわからなかった）。

```
git clone https://github.com/dagger/dagger
cd dagger
cd pkg/universe.dagger.io/examples/todoapp
dagger project update
dagger do build
open _build/index.html
```

　`dagger do build`を実行すると`todoapp.cue`に定義されたビルドアクションが実行される。

ビルド1回目は時間が掛かるが2回目以降はすぐに終わる。
ほとんどの時間はdepsフェーズでコンテナイメージをビルドするための時間。

```
% dagger do build      
[✔] actions.build.run.script                                                0.1s
[✔] actions.deps                                                           71.7s
[✔] actions.test.script                                                     0.1s
[✔] client.filesystem."./".read                                             0.2s
[✔] actions.test                                                            2.1s
[✔] actions.build.run                                                      16.8s
[✔] actions.build.contents                                                  0.0s
[✔] client.filesystem."./_build".write                                      0.1s
```

```
% dagger do build
[✔] actions.build.run.script                                                0.0s
[✔] actions.deps                                                            5.0s
[✔] client.filesystem."./".read                                             0.1s
[✔] actions.test.script                                                     0.0s
[✔] actions.test                                                            2.1s
[✔] actions.build.run                                                      10.1s
[✔] actions.build.contents                                                  0.0s
[✔] client.filesystem."./_build".write                                      0.1s
```

actionsの定義を上から順に実行している？

### CUE file definition

`todoapp.cue`には`build`を含めて4つの`actions`が定義されている。

* deps
* test
* build
* deploy

それぞれ`dagger do [action name]`コマンドで実行できる。

```
% dagger do deps
[✔] actions.deps                                                            0.5s
[✔] client.filesystem."./".read                                             0.0s
```

```
% dagger do test
[✔] client.filesystem."./".read                                             0.0s
[✔] actions.test.script                                                     0.0s
[✔] actions.deps                                                            0.6s
[✔] actions.test                                                            0.0s
```

```
% dagger do deploy
[✗] actions.deploy.container.script                                         0.0s
[✗] actions.deps                                                            0.0s
[✗] client.env                                                              0.0s
[✗] actions.test.script                                                     0.0s
[✗] actions.build.run.script                                                0.0s
[✗] client.filesystem."./".read                                             0.0s
[✗] actions.deploy                                                          0.0s
12:08PM FTL failed to execute plan: task failed: client.env: environment variable "APP_NAME" not set
```

deployは環境変数がないのでエラーした。
必要性は今のところ感じないが独自定義もできそう。

### My sample project

公式サンプルはフロントエンド用だったので、
[react-learning](https://github.com/kn25ha01/react-learning)でもそのまま動きそう？
プロジェクトルートにコピペしてもそのまま動かなかった。

`dagger project init`を実行すると`cue.mod`ディレクトリが生成された。
`dagger project update`も実行する。
パスが合ってなくてすぐに動かない。
