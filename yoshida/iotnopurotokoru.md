# プロトコル・無線通信規格・○aaS

## TODO

* [ ] IoT関連のプロトコルについて知識を蓄える
* [ ] HTTPのメソッドと，IoTでよく用いられるMQTTを知る
* [ ] プロトコルのレイヤも気にする
* [ ] 無線通信規格の特徴とメリットデメリットを知る
* [ ] SaaSとかIaaSとかの違いについて知る

## HTTP\(HyperText Transfer Protocol\)

HTMLなどのコンテンツの送受信に用いられる通信プロトコル

アプリケーション層に位置する

### 代表的なメソッド

| メソッド | 動作 |
| :---: | :---: |
| GET | 指定されたURIのリソースを取り出す． |
| POST | クライアントがサーバにデータを送信する |
| PUT | 指定したURLのリソースを保存する |
| HEAD | サーバに対して，ヘッダーのみを要求 |
| DELETE | 指定されたURIのドキュメントを削除する |
| TRACE | サーバまでのネットワーク経路をチェックする |

### ステータスコード

よく見るもの，見そうなもの

#### 1xx Informational 情報

| コード | 意味 |
| :---: | :---: |
| 100 Continue | 継続，クライアントはリクエストを継続できる |
| 102 Processing | 処理中 |

#### 2xx Success 成功

| コード | 意味 |
| :---: | :---: |
| 200 OK | リクエストが成功 |
| 201 Created | リクエストは完了し，新たに作成されたリソースのURLが返される |
| 202 Accepted | リクエストは受理されたが，処理は完了していない |
| 204 No Content | 内容なし． |

#### 3xx Redirection リダイレクション

| コード | 意味 |
| :---: | :---: |
| 305 Use Proxy | プロキシを使用せよ |

#### 4xx Client Error クライアントエラー

| コード | 意味 |
| :---: | :---: |
| 400 Bad Request | リクエストが不正である |
| 401 Inauthorized | 認証が必要である |
| 403 Forbidden | 禁止されている |
| 404 Not Found | 未検出 |

#### 5xx Server Error サーバーエラー

| コード | 意味 |
| :---: | :---: |
| 500 Internal Server Error | サーバー内部エラー |
| 502 Bad Gateway | 不正なゲートウェイ |

## MQTT\(Message Queueing Telemetry Transport\)

メッセージ指向ミドルウェアのアプリケーション層で使用される，TCP/IPによる**Pub/Sub型データ配信モデル**の軽量なメッセージキュープロトコルである．  
非力なデバイスやネットワークが不安定な場所でも動作しやすい様に，メッセージ通信電文が軽量に設計されていることが特徴

### 特徴

* 軽量なプロトコルであり，シンプル
* 柔軟性の高いメッセージ配布ができる
* QoSレベルが3段階で指定できる
* メッセージ再配布機能がある
* ブローカーが最後に配布したメッセージは必ず保存する

### 構造

|  | 役割 |
| :---: | :---: |
| Publisher | メッセージを送信する（出版者） |
| Subscriber | メッセージを受け取る（購読者） |
| Broker | メッセージを仲介する（仲介者） |

![](../.gitbook/assets/mqtt_model.png)

### Pub/Sub型データ配信モデル

出版-購読型モデルは，非同期メッセージングパラダイムの一種であり，メッセージの送信者（出版側）が特定の受信者（購読側）を想定せずにメッセージを送るようプログラムされたものである．

**利点**

* 出版側と購読側は疎結合されており，相手の存在を知る必要もない．トピックに関して通信さえできれば，両者はネットワーク構成も知る必要がない．また，相手の状態がどうであろうと個々のシステムは正常に稼働し続ける．
* クライアント-サーバモデルよりも良いスケーラビリティを実現できる．

**欠点**

* 購読側がブローカー（サーバ）に対してメッセージ送信を要求するのは帯域内で行われ，セキュリティ問題が発生する可能性がある．購読側の正当性の検証をしない．

## AMQP\(Advanced Message Queueing Protocol\)

メッセージ指向ミドルウェアのオープンスタンダードなアプリケーション層プロトコル

### メッセージングシステム

* Exchange
  * Exchangeは，生成されたMessageを受け取る役割を持つ．そのMessageはBindingに従って，適切なMessage Queueへ渡す．
* Binding
  * ExchangeとMessageQueueの対応づけを行う．
* Message Queue
  * Messageを蓄積し，Consumer Applicationに引き渡す役割を持つ．

Messageの大まかな流れ

1. Publisher Applicationによって生成されたMessageは，Exchangeに渡される．
2. ExchangeはBindingに基づいて，Messageを適切なMessage Queueに引き渡す．
3. Consumer Applicationによって，Message QueueからMessageが取り出される．

![](../.gitbook/assets/amqp.png)

### Exchangeについて

Exchangeは，設定された配送方法やbindingに従って，Message Queueにメッセージを配送する役割を持つ．  
Exchangeで指定可能な配送方法は以下の様なものがある．

* Direct
  * DirectなExchangeは，Messageに付与されているrouting keyと，bindingに設定されているbinding keyを見て，routing key = binding keyとなる様な，Message QueueにMessageを配送する．
* Fanout
  * FanoutなExchangeは，bindされているMessage Queue全てに受け取ったメッセージの配送を行います．
* Topic
  * Directと同じで，メッセージにrouting keyを指定して，そのkeyを元に配送するMessage Queueを選択する．

