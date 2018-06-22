# Google Apps Script

## Google Apps Scriptとは

Many Google apps, one platform in the cloud

カレンダー，ドキュメント，ドライブ，Gmail，スプレッドシートなどのあらゆるサービスを統合処理するスクリプト環境であり，クラウド上のJavaScriptを用いたプラットフォーム

Googleアカウントを持ち，Googleドキュメントやその他のサービスを利用している者なら誰でも利用できる．

### 事前知識

* Standalone Script（ドライブ上に単独で存在するもの）
* Container Bound Script（スプレッドシートに紐づくもの）

この2つが存在し，Googleドライブ内からGoogle Apps Scriptを新規作成するとStandalone Scriptが起動

スプレッドシート内の「ツール→スクリプトエディタ」からスクリプトエディタを表示するとContainer Bound Scriptが起動

## ショートカットキー

| コマンド | ショートカットキー |
| --- | --- |
| 開く | cmd + o |
| 保存 | cmd + s |
| 元に戻す | cmd + z |
| やり直し | cmd + y |
| 検索と置換 | cmd + f |
| ログの表示 | cmd + Enter |
| 「関数を選択」で選択している関数を実行 | cmd + r |

## サンプルコード

### Hello World（Container Bound Scriptの例）

```javascript
function myFunction() {
  Logger.log("Hello World!!");
  Browser.msgBox(Logger.getLog());
}
```

### スプレッドシート内の特定のセルの値を取得し表示する

{% code-tabs %}
{% code-tabs-item title="cellValue.gs" %}
```javascript
function myFunction() {

  // シートの取得
  var sheet = SpreadsheetApp.getActiveSheet()
  // セルの指定
  var range = sheet.getRange('セルの番号')
  // ログの表示
  Logger.log(range.getValue());
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}


## test
