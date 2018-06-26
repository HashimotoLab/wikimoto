# Node-RED

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
5. ブラウザを起動し，http://localhost:1880にアクセスすると画面が表示される

### Mac

1. Node.jsのインストーラを[公式サイト](https://nodejs.org/ja/)からダウンロードし，インストールする
2. `node -v`，`npm -v`で入っていることを確認
3. `sudo npm install -g --unsafe-perm node-red`でNode-REDをインストール
4. `node-red`で起動
5. ブラウザを起動し，http://localhost:1880にアクセスすると画面が表示される

## ノードの追加

メニューボタン→ノードを追加→任意のノードを検索→ノードを追加
or 
`npm`コマンドでインストール

## Tips

* フロー自体がJSON形式で保存されているため，そのJSONをエクスポートできたり，インポート欄にペーストで復活できる．
* [Node-REDライブラリ検索サイト](http://flows.nodered.org/)


## 参考

* はじめてのNode-RED, 工学社
