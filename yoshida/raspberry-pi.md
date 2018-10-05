---
description: Raspberry Piの初期設定・諸々のパッケージ導入について
---

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

```bash
$ df -h
```

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
$ sudo rpi-update
```

ぼちぼち時間かかるかも

大体終わったら，再起動しといた方が良い

## 各パッケージの導入

### tmuxの導入

1. ホームディレクトリorセットアップ用にディレクトリを作成し移動する（ここでは作業ディレクトリをtmux\_dirとする）

```bash
$ mkdir tmux_dir
$ cd tmux_dir
```

1. 依存ライブラリであるlibeventを導入する（githubのreleaseページでバージョンを確認した方が良い）

```bash
$ wget https://github.com/libevent/libevent/releases/download/release-2.1.8-stable/libevent-2.1.8-stable.tar.gz
$ tar xvzf libevent-2.1.8-stable.tar.gz
$ cd libevent-2.1.8-stable
$ ./configure
$ make
$ sudo make install
$ sudo ldconfig
```

1. ncuresesも導入する

```bash
$ sudo apt-get install libncurses5-dev
```

1. tmuxを導入する（githubのreleaseページでバージョンを確認した方が良い）

```bash
$ cd ~/tmux_dir
$ wget https://github.com/tmux/tmux/releases/download/2.7/tmux-2.7.tar.gz
$ tar xvzf tmux-2.7.tar.gz
$ cd tmux-2.7
$ ./configure
$ make
$ sudo make install
```

### OpenCVの導入

以下のサイトに従って導入できる

OpenCVのバージョンは好みのを入れる

{% embed data="{\"url\":\"https://sites.google.com/site/memorandumjavaandalgorithm/raspberry-pi-jiang-zuo-opencv3\",\"type\":\"link\",\"title\":\"Raspberry Pi講座 Python3 + OpenCV3\(環境構築編\) - Hobby robot laboratory\",\"icon\":{\"type\":\"icon\",\"url\":\"https://ssl.gstatic.com/sites/p/4b55b0/system/app/images/apple-touch-icon.png\",\"aspectRatio\":0}}" %}

## Tips

### プロキシを通してブラウジング

```bash
$ chromium-browser --proxy-server="wproxy.akita-u.ac.jp:8080"
```

### vncserverの起動

widthとheightの部分に好きなサイズを入れる

```bash
$ vncserver -geometry widthxheight
```

**参考**

{% embed data="{\"url\":\"https://qiita.com/tenmihi/items/adc7861e69c4418bb1e6\",\"type\":\"link\",\"title\":\"Raspbianにtmuxを導入する - Qiita\",\"description\":\"\#\# やりたいこと Raspbianでもtmux使ってハッピーになりたい  \#\# 導入を行った環境 \* Raspberry Pi 3 Model B+ \* Raspbian 8.0  \#\# 手順  \#\#\# 準備 セットアップ用にフォル...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.qiita.com/assets/favicons/public/apple-touch-icon-f9a6afad761ec2306e10db2736187c8b.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.qiita.com/assets/qiita-fb-2887e7b4aad86fd8c25cea84846f2236.png\",\"width\":200,\"height\":200,\"aspectRatio\":1}}" %}

