+++
title = 'Pyenvとvenvを用いたローカル環境のセットアップ方法'
date = 2023-12-11T22:19:33+09:00
draft = false
+++

## 概要
macのローカル環境で開発を行う際のpythonの環境構築の方法について記載する。  
今回は以下の２つの仕組みを利用して、pythonのバージョン管理と仮想環境の管理を行う。

* pyenv
  * 複数のpythonのバージョンを扱うために利用する。
* venv
  * プロジェクトごとに環境を分けるために利用する。


それぞれの違いや必要性の解説は[こちら](https://jimaru.blog/programming/python/venv_pyenv_choice/)の記事が参考になります。

## Pythonのインストール
まずは、ローカル環境にPyenvをインストールし、任意のPythonバージョンを利用できるようにします。

pyenvをインストールします。
```
brew install pyenv
```

インストールしたpyenvのバージョンを確認します。
```
pyenv --version
pyenv 2.3.35
```

zshに設定を追加する。
```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc    
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

`.zshrc`の内容を読み込む。
```
source ~/.zshrc
```

インストール可能なPythonのバージョン一覧を表示する。
```
pyenv install --list
```

指定したバージョンをインストールする。
```
pyenv install 3.11.7
```

プロジェクトフォルダに指定したPythonのバージョンを利用する。
```
cd <作成したプロジェクトフォルダ>
pyenv local 3.11.7
pyenv versions
```
globalの場合は全体に反映される。
```
pyenv global 3.11.7
```

実行されているpythonのバージョンを確認する。
```
python -V
```

## venvで仮想環境の作成

プロジェクトのディレクトリに仮想環境を作成する。
```
# python -m venv <仮想環境名>
python -m venv venv
```

仮想環境を有効化する。
```
source venv/bin/activate
```

ディアクティベートは以下のコマンドで実行できる。
```
deactivate
```

以上でローカル環境の構築が完了です。