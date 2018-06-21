# 固い絆に想いを寄せてビーコン

乾杯今君は人生のfact!

## Eddystone

* Bluetoothの通信プロトコルだよ
* Bluetooth4.0が必要だよ
* **３万円ど底辺ゴミパソコンじゃなければ使えるよ**
* Windowsではやらないということは必ずしもmustではない

### Eddystone-URL

ラズパイ、うぶんつ、マックだと簡単に使えるよ

#### ラズパイ/Ubuntu設定

```bash
$ sudo hciconfig hci0 up                #Bluetoothを有効にする
```

```bash
$ sudo hciconfig hci0 leadv 3        #Bluetoothデバイスをadvertise and not-connectableにする
```

```bash
$ sudo pip install PyBeacon            #PyBeaconのインストール
```

```bash
$ PyBeacon -v                            #確認
```

うぶんつでこれでダメだったらブルーツ入れて　bluez

```bash
$ PyBeacon -u てきとーなURL                #実行
$ PyBeacon -t                            #停止
```

#### Mac設定

node.jsとnpmを使うからダウンロードして、どうぞ

{% embed data="{\"url\":\"http://nodejs.org/\",\"type\":\"link\",\"title\":\"Node.js\",\"icon\":{\"type\":\"icon\",\"url\":\"https://nodejs.org/static/favicon.png\",\"width\":32,\"height\":32,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://nodejs.org/static/images/logo-hexagon-card.png\",\"width\":224,\"height\":256,\"aspectRatio\":1.1428571428571428}}" %}



```bash
$ mkdir ~/てきとーにフォルダ名            #フォルダをてきとーに作る
```

```bash
$ cd ~/さっき作ったフォルダ名
$ npm install eddystone-beacon        #node-eddystone-beaconをインストール
```

多分これが一番速いと思います

{% code-tabs %}
{% code-tabs-item title="sample.js" %}
```javascript
EB = require('eddystone-beacon');
EB.advertiseUrl('https://goo.gl/83dwiu);
```
{% endcode-tabs-item %}
{% endcode-tabs %}

実行

```bash
$ node ~/eddystone/sample.js
```

止めるときはcontrol+Cだよ



## 沈黙の戸舘　研究studyApocalypse黙示録

まず研究を進める道筋だよ

### 手持ち

#### Raspberrypiセルフ動的ビーコン\(場所\)

{% code-tabs %}
{% code-tabs-item title="SelfDynamicBeacon.py" %}
```python
import sys
import time
import datetime
from sense_hat import SenseHat
from evdev import InputDevice, list_devices, ecodes
import subprocess
import time

print("Press Ctrl-C to quit")
time.sleep(1)
sense = SenseHat()
sense.clear()           # Blank the LED matrix
found = False
devices = [InputDevice(fn) for fn in list_devices()]

for dev in devices:
    if dev.name == 'Raspberry Pi Sense HAT Joystick':
        found = True;
        sense.show_message('Start!')
        break

if not(found):
    print('Raspberry Pi Sense HAT Joystick not found. Aborting ...')
    exit()

cmd = "PYBeacon -u "
url = ""

def handle_code(code,url):
    cmd = "PyBeacon -u "
    cmdStop = "PyBeacon -t"
    if code == ecodes.KEY_DOWN:
        url = "https://goo.gl/Aq18zF"
        sense.show_message(url)
    elif code == ecodes.KEY_UP:
        url = "https://goo.gl/epjq13"
        sense.show_message(url)
    elif code == ecodes.KEY_RIGHT:
        url = "http://urx.cloud/K59M"
        sense.show_message(url)
    elif code == ecodes.KEY_LEFT:
        check = subprocess.check_output(cmdStop, shell=True, universal_newlines=True)
        sense.show_message("STOP")
    elif code == ecodes.KEY_ENTER:
        print(cmd+url)
        check = subprocess.check_output(cmd + url, shell=True, universal_newlines=True)
        time.sleep(1.0)
        sense.show_message("ASSEMBLE!!")
    return url

try:
    for event in dev.read_loop():
        if event.type == ecodes.EV_KEY:
            url = handle_code(event.code,url)
    print(url)
except KeyboardInterrupt:
    sys.exit()

```
{% endcode-tabs-item %}
{% endcode-tabs %}

上右左のレバーがURL、左のレバーがURL発信ストップ

URL発信はLEDに2度URLが流れた後にレバーを押し、成功するとLEDにASSEMBLE!!が流れる

停止はレバーを左に倒すだけ

※現時点だとコンソールで実行していなければレバーが対応しない。独立させた状態でレバーを反応させるには.h264ファイルの解明が必要か

#### GPSで位置情報取得\(HTML\)

吉田が見つけました。金野が書きました。戸舘が貰いました。ぜんいんあほ

{% code-tabs %}
{% code-tabs-item title="Coordinate.html" %}
```markup
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title></title>
  <script>
    navigator.geolocation.getCurrentPosition(show_position);
    function show_position(position) {
      var p_text = "latitude: " + position.coords.latitude + "<br>";
      var p_text2 = "longitude: " + position.coords.longitude + "<br>";
      document.getElementById("result").innerHTML = p_text;
      document.getElementById("result2").innerHTML = p_text2;
    }
  </script>
</head>
<body>
  <span id="result"></span>
  <span id="result2"></span>
</body>
</html>

```
{% endcode-tabs-item %}
{% endcode-tabs %}

更に発展

{% embed data="{\"url\":\"https://syncer.jp/how-to-use-geolocation-api\",\"type\":\"link\",\"title\":\"JavaScriptで位置情報を取得する方法\(Geolocation API\)\",\"description\":\"スマートフォンなどでユーザーの現在地を取得する方法を、JavaScriptをサンプルに解説。\",\"icon\":{\"type\":\"icon\",\"url\":\"https://syncer.jp/android-chrome-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://syncer.jp/storage/javascript/how-to-use-geolocation-api/static/dst/eyecatch\_og.png\",\"width\":600,\"height\":315,\"aspectRatio\":0.525}}" %}

#### ※超注意

研究室のHTML5の本は2011年のだから書いてないんだけど今だとGeolocation APIを外部から使うことはできないよ。使うためにはsecureなWebページにするしかない。http:からhttps:にするにはかなり面倒くさい申請が必要だけどやりたいって人はがんばって。

{% embed data="{\"url\":\"https://developers.google.com/web/updates/2016/04/geolocation-on-secure-contexts-only\",\"type\":\"link\",\"title\":\"Geolocation API Removed from Unsecured Origins in Chrome 50  \|  Web        \|  Google Developers\",\"description\":\"Starting with version 50, Chrome no longer supports the HTML5 Geolocation API over non-secure connections.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://developers.google.com/\_static/ef5182aa39/images/touch-icon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://developers.google.com/web/images/social-webfu-16x9.png\",\"width\":1200,\"height\":675,\"aspectRatio\":0.5625}}" %}

{% embed data="{\"url\":\"https://wpstorelocator.co/document/html-5-geolocation-not-working/\",\"type\":\"link\",\"title\":\"HTML 5 Geolocation Not Working – WP Store Locator\"}" %}

#### 動的ビーコン\(ストップウォッチ/タイマー的な時間\)

{% code-tabs %}
{% code-tabs-item title="DynamicBeacon.py" %}
```python
import subprocess
from time import sleep
#import datatime
#today = datatime.data.today()
#todaydetail = datetime.datetime.today()
argsgoogleStart = ['PyBeacon', '-u', 'https://goo.gl/83dwiu']
argsyahooStart = ['PyBeacon', '-u', 'https://goo.gl/EDPB1M']
argsStop = ['PyBeacon', '-t']
try:
    n=0
    while(n<3):
        res = subprocess.check_call(argsgoogleStart)
        sleep(15)
        res = subprocess.check_call(argsStop)
        res = subprocess.check_call(argsyahooStart)
        sleep(15)
        res = subprocess.check_call(argsStop)
        n+=1
except:
    print (Error)

```
{% endcode-tabs-item %}
{% endcode-tabs %}

15秒ごとに異なるURLを発信し3周したところで終了

#### 動的ビーコン\(時計的な時間\)

{% code-tabs %}
{% code-tabs-item title="DynamicBeacon2.py" %}
```python
import subprocess
from time import sleep
import datetime
#today = datetime.data.today()
#todaydetail = datetime.datetime.today()
argsgoogleStart = ['PyBeacon', '-u', 'https://goo.gl/83dwiu']
argsyahooStart = ['PyBeacon', '-u', 'https://goo.gl/EDPB1M']
argsakitauStart= ['PyBeacon', '-u', 'https://goo.gl/sCe499']
argsStop = ['PyBeacon', '-t']
try:
    n=0
    while(n<3):
        # _today = datetime.date.today()
        todaydetail = datetime.datetime.today()
        if todaydetail.second in range(0,19):
            res = subprocess.check_call(argsgoogleStart)
            sleep(20-todaydetail.second)
        elif todaydetail.second in range(20,39):
            res = subprocess.check_call(argsyahooStart)
            sleep(40-todaydetail.second)
        else:
            res = subprocess.check_call(argsakitauStart)
            sleep(60-todaydetail.second)
        res = subprocess.check_call(argsStop)
        n+=1
except:
    print("Error")

```
{% endcode-tabs-item %}
{% endcode-tabs %}

現在時刻によって発信するURLが3つに変化

こんなんどうでもいい

### 作品案

Physical Webだとスマートデバイスに強制的にURLのメタデータをプッシュ通知できたけど今はできない。受信側が能動的に見るサービスにしなければならない。

案：広告をビーコンで淋々と泣きながらはじけてとんだけど

バス、電車、広告トラックとかから発信される。広告なら通知が来れば迷惑だけどみたいと思う人は能動的な動き\(Webページに飛ぶ\)をしてくれる。

スマホはURLを打つには向いてない。QRコードも読取るまでの動作が多いし公共の場でカメラを取り出すのはなにかと人目が気になる。

プッシュ通知しないことが逆にメリットになるので？こいつはことだ！考察せにゃ



