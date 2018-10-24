# SensorTag+ThingSpeak

## SensorTagとは

センサ・データとクラウド・コネクティビティを組み合わせた次世代IoT開発キット

**センサの種類**

* 赤外線および周囲温度センサ
* 周辺光センサ
* 湿度センサ
* 大気圧センサ
* 9軸モーション・トラッキング・デバイス-加速度計，ジャイロスコープ，コンパス
* 磁気センサ

## ThingSpeakとは

ThingSpeakは，Internet of Things\(IoT\)プラットフォームであり，クラウドでセンサのデータを収集および保存したり，IoTアプリケーションを開発することが可能である．

MATLABでデータを解析し可視化できるアプリを提供しており，そのデータに基づいてアクションを実行することができる．

センサデータはArduino，RaspberryPi，BeagleBone Black，および他のハードウェアからThingSpeakに送信可能である．

```python
import bluepy
import time
import sys
import argparse
import requests
from datetime import datetime

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('-i',action='store',type=float, default=150.0, help='scan interval')
    parser.add_argument('-t',action='store',type=float, default=5.0, help='scan time out')

    arg = parser.parse_args(sys.argv[1:])

    scanner = bluepy.btle.Scanner(0)

    # ヘッダー定義
    headers = {'X-THINGSPEAKAPIKEY': 'APIのキー'}

    while True:
        print(datetime.now().strftime("%Y/%m/%d %H:%M:%S"))
        dev = None
        print('scanning tag...')
        devices = scanner.scan(arg.t) # BLEをスキャンする
        for d in devices:
            for (sdid, desc, val) in d.getScanData():
                if sdid == 9 and val == 'CC2650 SensorTag':
                    dev = d
                    print('found SensorTag, addr = %s' % dev.addr)
        sys.stdout.flush()

        if dev is not None:
            for _ in range(5): # 最大5回実行
                try:
                    # 見つけたデバイスに接続する
                    tag = bluepy.sensortag.SensorTag(dev.addr)
                except Exception as e:
                    # 必要であれば失敗時の処理
                    time.sleep(5.0)
                else:
                    break # 失敗しなかった時はループを抜ける
            else:
                pass # リトライが全部失敗した時の処理

            tag.IRtemperature.enable()
            tag.humidity.enable()
            tag.barometer.enable()
            tag.battery.enable()
            tag.lightmeter.enable()

            time.sleep(1.0)

            temperature = tag.IRtemperature.read()[0]
            humidity = tag.humidity.read()[1]
            barometer = tag.barometer.read()[1]
            lightmeter = tag.lightmeter.read()
            battery = tag.battery.read()

            # dataの定義
            payload = {'field1': temperature, 'field2': humidity, 'field3': barometer, 'field4': lightmeter, 'field5': battery}

            tag.IRtemperature.disable()
            tag.humidity.disable()
            tag.barometer.disable()
            tag.battery.disable()
            tag.lightmeter.disable()

            # ThingSpeakにデータを渡す
            r = requests.post("https://api.thingspeak.com/update/", data=payload, headers=headers)

            print(r.status_code)

            sys.stdout.flush()

            time.sleep(arg.i)

            tag.disconnect()
            del tag

if __name__ == "__main__":
    main()
```

