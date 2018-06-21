# もしもの時のMacでApache2

## 学内のネットワークでサーバーうんちゃらかんちゃら

### 最近のMacはApache2が標準搭載だよという話

確認

```bash
$ httpd -v
```

例としてはこんな感じで出るよ

```bash
Server version: Apache/2.4.25
Server built:   Feb  6 2017 20:02:10
```

起動！

```bash
$ sudo apachectl start
```



safariのURLのとこにlocalhostって入れてみなさい



閉廷！

```text
$ sudo apachectl stop
```

#### プロキシ設定\(必要だ！と言い切る自信がないけどたぶん必要\)

```bash
$ cd /etc/apache2/other            #otherまで移動しな
```

```bash
$ sudo vim proxy.conf                #コンフィグファイルを作るよ
```

ファイルの中身はこんな感じ

{% code-tabs %}
{% code-tabs-item title="proxy.conf" %}
```text
<IfModule mod_proxy.c>
ProxyRequests On
ProxyVia On
Listen 8080

<Proxy *>
Order deny,allow
Deny from all
Allow from all
</Proxy>
</IfModule>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```bash
$ cd /etc/apache2                #apache2まで移動しな
```

```bash
$ sudo vim httpd.conf            #httpdのコンフィグファイルをちょっとだけ変更
```

127行目

```bash
#LoadModule proxy_module libexec/apache2/mod_proxy.so
```

を

```text
LoadModule proxy_module libexec/apache2/mod_proxy.so
```

これでいける　無理ならもう知らない

safariで自分のグローバルIPに行ってみな

