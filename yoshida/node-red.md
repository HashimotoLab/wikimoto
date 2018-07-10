# Node-RED

## TODO

* [ ] 三冊の本を読破する
* [ ] 研究に使える要素を探す
* [ ] ラズパイ特有のハードと連携したノードがあるかどうか，あったら試す

## Node-REDとは

ハード・API・オンラインサービスなどを組み合わせて使う際に，それらを簡単につなぐことができるツール

分かりやすいGUIが特徴で，データフローをはじめとしたプログラミング処理や，Webアプリケーションを構築できる

IoTのためのビジュアルツールと呼ばれる

## インストール

### Windows

1. Node.jsのインストーラを[公式サイト](https://nodejs.org/ja/)からダウンロードし，インストールする
2. `node -v`，`npm -v`で入っていることを確認
3. `npm install -g --unsafe-perm node-red`でNode-REDをインストール
4. `node-red`で起動
5. ブラウザを起動し，[http://localhost:1880にアクセスすると画面が表示される](http://localhost:1880にアクセスすると画面が表示される)

### Mac

1. Node.jsのインストーラを[公式サイト](https://nodejs.org/ja/)からダウンロードし，インストールする
2. `node -v`，`npm -v`で入っていることを確認
3. `sudo npm install -g --unsafe-perm node-red`でNode-REDをインストール
4. `node-red`で起動
5. ブラウザを起動し，[http://localhost:1880にアクセスすると画面が表示される](http://localhost:1880にアクセスすると画面が表示される)

## ノードの追加

メニューボタン→ノードを追加→任意のノードを検索→ノードを追加 or `npm`コマンドでインストール

## できること

* Twitterのツイート取得・投稿
* HTML，CSS，Javasciptを用いたWebページの作成と，その情報のやりとり
* Microsoft Azureを使った画像認識（精度は低いが，画像は何かを示してくれる），日本語訳
* 任意の値を用いたダッシュボードの作成による可視化
* データベースの利用
* 指定した間隔，指定した日時において，特定の動作を行う（曜日指定も可）
* マッピングの可視化

## Tips

* フロー自体がJSON形式で保存されているため，そのJSONをエクスポートできたり，インポート欄にペーストで復活できる．
* [Node-REDライブラリ検索サイト](http://flows.nodered.org/)
* changeノードを制するものがNode-REDを制する
* JavaScriptを記述するノードは、値をreturnしないと後続ノードの処理を実行しないという動きをする

## 参考

* はじめてのNode-RED, 工学社
* Node-Redで極楽コンピュータ・プログラミング, CQ出版社  
* つないでつないでプログラミング Node-REDでつくる初めてのアプリ, リックテレコム  

{% embed data="{\"url\":\"https://qiita.com/zuhito/items/e9abfd6f1ba188f908ed\",\"type\":\"link\",\"title\":\"目からウロコ！Node-REDのデザインパターン10選 - Qiita\",\"description\":\"私が\[Node-RED\]\(http://nodered.jp\)を使い始めて4カ月が経ちました。開発をしていると、Node-REDには単にノードを横に繋いでゆくだけではなく、ノードの接続関係を活用したデザインパターンが沢山あることに気が...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.qiita.com/assets/favicons/public/apple-touch-icon-f9a6afad761ec2306e10db2736187c8b.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.qiita.com/assets/qiita-fb-2887e7b4aad86fd8c25cea84846f2236.png\",\"width\":200,\"height\":200,\"aspectRatio\":1}}" %}

