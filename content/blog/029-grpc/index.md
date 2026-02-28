+++
title = 'gRPCについての調査'
description = 'gRPCの基礎からPythonでの実装まで解説。Protocol Buffersによるスキーマ定義、.protoファイルの書き方、Quick Startチュートリアルの実践例を紹介します。'
date = 2024-09-01T17:53:57+09:00
lastmod = 2024-09-01T17:53:57+09:00
draft = false
categories = ['Engineering']
tags = ['gRPC']
+++

## gRPCとは

* PRCを実現するためにGoogleが開発したプロトコルの一つ
* Protocol Bufferを使ってデータをシリアライズし、高速な通信を実現できる点が特徴
* IDLを使ってあらかじめAPIの仕様を.protoファイルとして定義し、そこから、サーバ側&クライアント側に必要なソースコードを生成する。

REST と gRPCの違い
 
* RESTはリソース志向、RPCはメソッドの呼び出しが起点となり、データは副産物であるという考え方。

## 利点と欠点

### 利点

- HTTP/2による高パフォーマンス
- Protocol Buffersによるデータ転送
    - IDLを書くことになるので、スキーマファーストで開発することになる
- 柔軟なストリーミング方式

### 欠点

- HTTP/2非対応
- ブラウザの対応状況が不十分
- 言語によって機能の実装増強にばらつきがある
- バイナリにシリアライズされると人間が読めない
- RESTでも十分早い

## .protファイル

gRPCではシリアライズフォーマットとしてProtocol Buffersを利用する。  
`.proto`  を拡張子として持つファイル上にスキーマ定義を行い、 `protoc` コマンド絵各言語用のコードを生成する。  
Protocol Buffersでは全ての値が型を持つ。型はスカラー型とメッセージ型に分けることができる。

### スカラー型

* 数値、文字列、真偽値、バイト配列

### メッセージ型

- 複数のフィールドを持ったメッセージ型
- メッセージ型は一つの .proto ファイルに複数定義することができる

```shell
message Person {
    int32 id = 1;
    string name = 2;
    string email  =3;
}
```

## gRPCのQuick Startを実施する

今回はPythonの環境を用いて、gRPCのQuick Startを実施する。  
https://grpc.io/docs/languages/python/quickstart/


起動に必要なPythonの環境を整える。

```shell
python -m pip install grpcio
python -m pip install grpcio-tools
```

サンプルコードのダウンロード

```shell
git clone -b v1.64.0 --depth 1 --shallow-submodules https://github.com/grpc/grpc
cd grpc/examples/python/helloworld
```

サーバを起動する。

```shell
python greeter_server.py

# 出力
Server started, listening on 50051
```

別のターミナルを起動し、クライアントを起動する。

```shell
python greeter_client.py

## レスポンス
Will try to greet world ...
Greeter client received: Hello, you!
```

gRPCのクライアントとサーバを用いて通信を行うことができました。

## .proto ファイルを変更してみる

今回は、`helloworld.proto` ファイルを変更して、新しいメソッドを追加してみます。

`helloworld.prot` ファイルが格納されている

```shell
cd grpc/examples/protos
```

以下のように修正する

```python
syntax = "proto3";

option java_multiple_files = true;
option java_package = "io.grpc.examples.helloworld";
option java_outer_classname = "HelloWorldProto";
option objc_class_prefix = "HLW";

package helloworld;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}

  // 以下の1行を追加
  rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}

  rpc SayHelloStreamReply (HelloRequest) returns (stream HelloReply) {}

  rpc SayHelloBidiStream (stream HelloRequest) returns (stream HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

grpcのコードを生成する
```shell
cd examples/python/helloworld

python -m grpc_tools.protoc -I../../protos --python_out=. --pyi_out=. --grpc_python_out=. ../../protos/helloworld.proto
```

以下のファイルが再作成されている。
```shell
ls -l

-rw-r--r--@ 1 xx  xx  1823  9  1 18:12 helloworld_pb2.py
-rw-r--r--@ 1 xx  xx  578  9  1 18:12 helloworld_pb2.pyi
-rw-r--r--@ 1 xx  xx  7018  9  1 18:12 helloworld_pb2_grpc.py
```

更新される `_pd` ファイルとは、protocol Buuffersの定義クラスが自動で生成されており基本的にはさわらない。

greeter_server.py を更新する。

```python
from concurrent import futures
import logging

import grpc
import helloworld_pb2
import helloworld_pb2_grpc


class Greeter(helloworld_pb2_grpc.GreeterServicer):
    def SayHello(self, request, context):
        return helloworld_pb2.HelloReply(message="Hello, %s!" % request.name)
    # 以下の関数を追加
    def SayHelloAgain(self, request, context):
        return helloworld_pb2.HelloReply(message="Hello Again, %s!" % request.name)

def serve():
    port = "50051"
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    helloworld_pb2_grpc.add_GreeterServicer_to_server(Greeter(), server)
    server.add_insecure_port("[::]:" + port)
    server.start()
    print("Server started, listening on " + port)
    server.wait_for_termination()


if __name__ == "__main__":
    logging.basicConfig()
    serve()
```

greeter_client.py を更新する。

```python
def run():
    # NOTE(gRPC Python Team): .close() is possible on a channel and should be
    # used in circumstances in which the with statement does not fit the needs
    # of the code.
    print("Will try to greet world ...")
    with grpc.insecure_channel("localhost:50051") as channel:
        stub = helloworld_pb2_grpc.GreeterStub(channel)
        response = stub.SayHello(helloworld_pb2.HelloRequest(name="you"))
        print("Greeter client received: " + response.message)
        response = stub.SayHelloAgain(helloworld_pb2.HelloRequest(name="you"))
        print("Greeter client received: " + response.message)
```

serverを再起動し、clientを実行する。

```shell
python greeter_server.py
python greeter_client.py

# 出力
Greeter client received: Hello, you!
Greeter client received: Hello Again, you!
```

追加したメソッドが正常に動作していることが確認できました。

## まとめ

今回はgRPCについて調査の調査と公式ドキュメントのチュートリアルを行いました。  
gRPCはスキーマファーストで開発することができ、HTTP/2による高パフォーマンス通信が可能であることら、最近ではREST APIの代替手段として注目されている技術ですので、ぜひ抑えておきたいです。
