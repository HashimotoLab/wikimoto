# ESP

ESPのこと書きます

## ESPとは

中国企業Espressif Systemsによって製造されている完全なTCP/IPスタックとマイクロコントローラを備えた低コストの小型WiFiモジュール  

シンプルな温度測定プログラム
```
#include <Wire.h>

int I2CAdrs;

// 初期化
void setup() {
  I2CAdrs = 0x48;   // スレーブアドレス

  Serial.begin(19200);  // シリアル通信パラメータ
  Wire.begin();         // I2Cマスタ初期化
}

// メインループ
void loop() {
  uint16_t val;
  float tmp;
  int ival;

  Wire.requestFrom(I2CAdrs, 2);
  val = (uint16_t)Wire.read() << 8;
  val |= Wire.read();
  val >>= 3;
  ival = (int)val;

  if (val & (0x8000 >> 3)) {
    ival = ival - 8192;
  }

  tmp = (float)ival / 16.0;
  Serial.println(tmp, 2);

  delay(500);

}
```
