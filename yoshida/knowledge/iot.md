---
description: IoTそのものの勉強
---

# IoT

## TODO

* [ ] IoTを知る
* [ ] トレンド・関連研究を知る

## IoT（Internet of Things）とは

モノのインターネットとは，様々なモノがインターネットに接続され，（単に繋がるだけでなく，モノがインターネットのように繋がる），情報交換することにより相互に制御する仕組みである．

### IoT市場の規模

国内IoT市場は2022年まで年間平均成長率14.9%で成長し，市場規模は12兆円に達する

![](../../.gitbook/assets/20180314_1%20%281%29.png)

{% embed url="https://www.idcjapan.co.jp/Press/Current/20180314Apr.html" %}

### 問題点

* クラウド一極集中による蓄積資源の枯渇

### エッジコンピューティング

処理能力をLAN内に持つ．

フォグコンピューティングと比べて，処理能力がデータソースより，つまり，各デバイスが処理を行うことが多い

### フォグコンピューティング

ネットワーク環境の中で，データがクラウドに行く前，端末に近い場所でのミドルウェアによる分散処理環境のこと．処理能力をIoTデバイスのあるLAN内に置く

エッジコンピューティングと比べて，スケーラブル（規模の拡大に対応できる）であり，集中的な処理装置を持ち，様々なポイントからデータが送られてくることから想定している

フォグ：霧

## データソース

### デバイスから得られるもの

* GPS
* 加速度センサ
* ジャイロセンサ
* 照度センサ
* 近接センサ
* 指紋センサ

### ウェアラブルデバイスから得られるもの

**Spire**

呼吸のサイクルを計測することで，集中度，緊張状態，運動などのアクティブな状態，座った状態，平穏な状態などをリアルタイムでモニタリングしながら，最適な深さやサイクルをアドバイスできるデバイス

**Fitbit**

### IoTシステムで扱うデータ

以下のような特徴が考えられる

1. 量：継続的かつ大量に生成される
2. データタイプ（規格・仕様）：デバイスやゲートウェイには様々な規格のものがあり，やり取りされるデータのフォーマットやプロトコルなど，データタイプもバラバラである
3. ノイズ：センサの設置場所や通信環境などからノイズが発生することがある．断続的にノイズがデータに混じったり，データ自体が欠損することがある
4. タイムラグ：通信状態によるタイムラグが発生したり，システム内の多数のデバイス間で時間の取り扱いに誤差が生じる可能性がある
5. 追加や変更：取得するデータの追加や変更が頻繁に行われる可能性がある

### データ活用プロセス

1. 収集
2. 蓄積
3. 整形
   * クレンジング大事
4. 集約
5. 分析 
6. 可視化

**セルフサービスBI（Business Intelligence）ツール**

現場の社員が自らデータを分析し，リポートを作成すること

タブロージャパンのTableau，日本マイクロソフトのPower BI，クリックテック・ジャパンのQlik Senseなど

1. 検証
2. 可視化した結果を検証し，追加収集すべきデータを検討して収集段階にフィードバックする

### IoTにおける情報セキュリティ

**機密性**

その情報にアクセスできる人のみがアクセスできること

**完全性**

適切な権限を付与された人以外から情報が改ざん・消去などされないこと

**可用性**

必要時に適切な人が確実に情報を利用できること

IoTでさらに必要となるセキュリティ要素

**安全性**

繋がっているモノ・コトに適切な挙動を確保できること

**プライバシー**

適切な人と目的のみにデータの参照が制限されていること

## 関連研究

中村優吾, "IoTデータ流の実時間処理を実現するIoTデバイス向け分散処理ミドルウェアの設計と評価", 奈良先端科学技術大学院大学 修士論文
