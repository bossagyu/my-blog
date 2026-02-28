+++
title = 'Pyenvとvenvを用いたローカル環境のセットアップ方法'
description = 'MacでPyenvとvenvを使ったPython環境構築方法を解説。Pythonバージョン管理と仮想環境の作成、更新、削除までの手順を初心者向けに詳しく説明します。'
date = 2023-12-10T23:19:33+09:00
lastmod = 2023-12-10T23:19:33+09:00
draft = false
categories = ['Engineering']
tags = ['Python', 'mac']
aliases = ['/blog/004-paython-setup/']
+++

## 概要
macのローカル環境で開発を行う際のpythonの環境構築の方法について記載します。  
今回は以下の２つの仕組みを利用して、pythonのバージョン管理と仮想環境の管理を行います。

* pyenv
  * 複数のpythonのバージョンを扱うために利用します。
* venv
  * プロジェクトごとに環境を分けるために利用します。


それぞれの違いや必要性の解説は[こちら](https://jimaru.blog/programming/python/venv_pyenv_choice/)の記事が参考になります。

## Pythonのインストール
まずは、ローカル環境にPyenvをインストールし、任意のPythonバージョンを利用できるようにします。

pyenvをインストールします。
```shell
brew install pyenv
```

インストールしたpyenvのバージョンを確認します。
```shell
pyenv --version
pyenv 2.3.35
```

zshに設定を追加します。
```shell
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc    
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

`.zshrc`の内容を読み込みます。
```shell
source ~/.zshrc
```

インストール可能なPythonのバージョン一覧を表示します。
```shell
pyenv install --list
```

指定したバージョンをインストールします。
```shell
pyenv install 3.11.7
```

プロジェクトフォルダに指定したPythonのバージョンを利用します。
```shell
cd <作成したプロジェクトフォルダ>
pyenv local 3.11.7
pyenv versions
```
globalの場合は全体に反映されます。
```shell
pyenv global 3.11.7
```

実行されているpythonのバージョンを確認します。
```shell
python -V
```

## venvで仮想環境の作成

プロジェクトのディレクトリに仮想環境を作成します。
```shell
# python -m venv <仮想環境名>
python -m venv venv
```

仮想環境を有効化します。
```shell
source venv/bin/activate
```

ディアクティベートは以下のコマンドで実行できます。
```shell
deactivate
```

## venvのアップデート方法

依存パッケージを最新化する場合は、仮想環境を有効化した上で以下を実行します。`requirements.txt` を利用しているなら、ファイルを更新した後に再インストールするとよいでしょう。
```shell
source venv/bin/activate
pip install --upgrade pip
pip install -U -r requirements.txt
```

Pythonのマイナーバージョンを上げるときは、`pyenv` で新しいバージョンを入れ直したあと、仮想環境を作り直すとライブラリの整合性が保ちやすいです。
```shell
pyenv install 3.12.2
pyenv local 3.12.2
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -U -r requirements.txt
```

## venvの削除方法

不要になった仮想環境はディレクトリごと削除すればよいです。作業ディレクトリを確認した上で実行してください。
```shell
rm -rf venv
```

以上でローカル環境の構築が完了です。

## 関連記事

- [MacでUVを用いてPythonの開発環境を構築する](/blog/032-python-uv/)（モダンな代替ツールとして）
