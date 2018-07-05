# Raspberry Pi

## RaspbianOSのダウンロード

[公式サイト](https://www.raspberrypi.org/downloads/raspbian/)からMacなりうぃんどおずなりでOSをダウンロードする

#### ダウンロードしたイメージを解凍する

```bash
$ tar xvf 2018-04-18-raspbian-stretch.zip
```

ファイル名は落としてきたものに準ずる

#### micro SDカードのフォーマット

Macだとdisk utilityアプリで初期化・マウント解除出来る

#### micro SDカードへの書き込み

 書き込みを行う前に，

`df -h`

でSDカードのパスをちゃんと見ておく

```bash
$ sudo dd bs=1m if=2018-04-18-raspbian-stretch.img of=/dev/rdisk3
```

#### SDカードを差し込み，ラズパイ起動

タイムゾーン，キーボードの設定をraspiconfigからやる

プロキシ設定は

```bash
$ sudo vi /etc/apt/apt.conf
```

でvimを起動し以下を記述

{% code-tabs %}
{% code-tabs-item title="/etc/apt/apt.conf" %}
```text
Acquire::http::proxy "http:wproxy.akita-u.ac.jp:8080";
Acquire::https::proxy "http:wproxy.akita-u.ac.jp:8080";
```
{% endcode-tabs-item %}
{% endcode-tabs %}

apt-getのアップデート，アップグレード，本体のファームウェアのアップデート

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrade
$ sudo apt-get rpi-update
```

ぼちぼち時間かかるかも

大体終わったら，再起動しといた方が良い

### プロキシを通してブラウジング

`chromium-browser --proxy-server="wproxy.akita-u.ac.jp:8080"`

### vncserverの起動

widthとheightの部分に好きなサイズを入れる

`vncserver -geometry widthxheight`


