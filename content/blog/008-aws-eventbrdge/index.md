+++
title = 'AWS EventBridgeを用いてLambdaを定期実行する方法'
date = 2023-12-21T23:03:13+09:00
draft = false
categories = ['Engineering']
tags = ['aws', 'lambda', 'eventbridge']
+++

## 概要
AWS EventBridgeを用いてLambdaを定期実行する方法を解説します。

## 前提
Lambda関数についてはすでに作成されていることを前提としています。  

## 手順
Lambda関数を選択し「トリガーを追加」を選択します。  
![img-008-001.png](img-008-001.png)

トリガーから「EventBridge」を選択します。  
![img-008-002.png](img-008-002.png)

ルールの作成画面が表示されるので、設定します。  
今回はcron形式で5分ごとに実行するように設定しています。  
cronのsyntaxについては [Schedule type on EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html#cron-based) のページを参考にしてください。  
![img-008-003.png](img-008-003.png)

設定が完了するとLambda関数のダイアグラムのトリガーにEventBridgeが追加されます。  
![img-008-004.png](img-008-004.png)

ちなみに私はLINEにメッセージを通知するFunctionを作って動かしてみました。  
こんな感じで5分に1回通知がくるようになりました。

![img-008-005.png](img-008-005.png)

## まとめ
AWS EventBridgeを用いてLambdaを定期実行する方法を解説しました。  
設定したEventBridgeについてはこのまま放置しておくと、課金が発生するので、不要になったら削除しておきましょう。