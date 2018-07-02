# proposal\_packages

今後使う人がいるのか分かりませんが，自分への備忘録も兼ねて

## ライブラリの構成

proposal_packagesは研究で使用する種々の機能をPythonのモジュール群にまとめたパッケージです．

<img src="proposal_packages_stracture.png" alt="" title="proposal_packages_stracture" width="400" height="850" />

各ファイルの簡単な説明を以下に示す．

* **sample**  
モジュールの使用例を示したファイルが置いてある  
いまのところ，方向性ラッパーのものしかない

* **test**
モジュールのユニットテストを行うテストライブラリ  
いまのところ，双方向方向性ラッパーのものしかない

* **\_\_init\_\_.py**  
Pythonでディレクトリをモジュールとして認識させるために必要なファイル  
モジュールインポートする際，ここに書かれた処理が必ず実行されるが特に何も書いていない

* **brite2dat.py**  
BRITEが生成するbriteファイルを研究室でよく使うGLPKのdatファイルに変換したり，networkxでトポロジを描画する際に必要な位置座標を格納したcsvファイルを生成するコマンドラインツール  
内部でbrite\_utils.pyを呼び出している

* **brite\_utils.py**  
briteファイルをパースしてノード，リンク，位置座標をPythonのデータ形式に変換するクラスと，それらをdatファイル，位置座標のcsvファイルに書き出す関数が定義されている  

* **dat\_utils.py**
datファイルをパースして各パラメータをPythonのデータ形式に変換するクラスが定義されている

* **directed_graph.py**
Graphillionで有向グラフに対して方向性を考慮したパス列挙を行えるようにするラッパー

* **directed_link_two_virtual_nodes.py**  
Graphillionで双方向リンクを持つグラフに対して方向性を考慮したパス列挙を行えるようにするラッパー  
仮想ノードを2個追加する  

* **directed_link.py**  
Graphillionで双方向リンクを持つグラフに対して方向性を考慮したパス列挙を行えるようにするラッパー  
仮想ノードを1個追加する  

* **drawing.py**  
モデルデータのdatファイルと位置座標csvファイルを読み込んでモデルデータを描画するライブラリ  

* **gridgraph.py**  
格子グラフの生成と描画を行うライブラリ  

* **graphillion_utils.py**  
Graphillionにないがよく使う関数をまとめたライブラリ  
作っている途中


## dat\_utils.py

### 概要

datファイルに記述されたパラメータをPythonのデータ形式に変換するクラス`Dat`が定義されている．  
パーサは宣言文を元にパラメータの位置を特定するので宣言文が正しく記述されていないとパラメータを取り出すことができない．  
各パラメータは以下の宣言文で書かれているものとする．  

```text
* number of demand path
param dk:= val;

* number of traffic
param m:= val;

* number of node
param n:= val;

* HOP
param : EN :HOP:=

* cost metric:delay
param : EM :COST:= 

* link capacity:bandwidth
param : E :c:= 

* traffic matrix:start node
param : SK :s:= 

* traffic matrix:target node
param : TK :t:= 

* demand path
param : DK :d:= 
"""
```

### パラメータ

#### クラス変数

|variable|概要|
|:---:|:----:|
|`attr_regexp`|各パラメータの名前をキー，宣言文の正規表現を値とする辞書|

宣言文の微妙な表記揺れを吸収するために本当は使いたくないが正規表現を使用している．  
宣言文が統一されているなら`str.starswith`を使って文字列先頭のマッチングをしたほうがよい．

#### インスタンス変数

|variable|概要|
|:---:|:----:|
|`filename`|datファイルの名前|
|`all_lines`|datファイルを読み込んで得られた文字列|
|`__dk`|トラフィックのばらつきを正規化する値|
|`__m`|トラフィック数|
|`__n`|ノード数|
|`__hop`|各リンク間のホップ数|
|`__cost`|各リンクのコスト|
|`__capacity`|各リンクの容量|
|`__start`|スタートノード|
|`__target`|ターゲットノード|
|`__DK`|トラフィックのばらつき|

`filename`以外はprivateな変数で直接名前を指定して参照できないようになっている．

### メソッド

#### `__init__(self, datfile)`

コンストラクタ．初期化処理として，datファイルの読み込みとprivateなインスタンス変数に`attr_regexp`のキーの文字列を代入する．

**arguments:**  

* datfile(string)  
datファイルのパス

#### `__get_attr1(self, param)`

パラメータ`dk`，`m`，`n`の値を取り出す．

**arguments**

* param(strinig)  
`self.__dk`，`self.__m`，`self.__n`のいずれかを指定

**returns**

パラメータ`dk`，`m`，`n`の値

#### `read_params(self, param, func)`

`dk`，`m`，`n`以外のパラメータを取り出す際に使用するジェネレータ．引数`param`に対応する`attr_regexp`の正規表現を利用してパラメータの位置を特定しファイルを1行ずつ読み込んでいく．パラメータの終端は`;`のみの行なので`;`が現れたら読み込みを終了する．各行はタブ文字もしくはスペース区切りで`val1 val2`または`val1 val2 val3`と表現されている．これを区切り文字で分割し各値を格納したリストを作る．このリストを引数`func`の引数として渡すことでパラメータを様々な形式で取り出せるようになっている．

**arguments**

* param(strinig)  
`dk`，`m`，`n`以外のパラメータ

* func(function)  
関数オブジェクト  
funcによってパラメータの各行が処理される

**yields**

funcを適用したパラメータの各行

#### `__get_attr2(self, param)`

パラメータ`capacity`，`cost`，`hop`を取り出す．  
これらのパラメータの各行は`node_i node_j param`の形式となっている．  
内部では`read_params`を呼び出していて各行に適用する関数として`lambda params: (int(params[0]), int(params[1]), float(params[2]))`を指定している．これによって，取り出されるパラメータの各行は`int int float`の形式になっている．

**arguments**

* param(strinig)  
パラメータ`capacity`，`cost`，`hop`のいずれかを指定

**returns**

パラメータ`capacity`，`cost`，`hop`の値

#### `__get_attr3(self, param)`

パラメータ`traffic`を取り出す．  
`__get_attr2`と同様に`read_params`を呼び出している．各行に適用する関数として`lambda params: (int(params[0]), int(params[1]))`を指定している．これによって，取り出されるパラメータの各行は`int int`の形式になっている．

**arguments**

* param(strinig)  
パラメータ`traffic`を指定

**returns**

パラメータ`traffic`の値

#### `__get_attr4(self, param)`

パラメータ`DK`を取り出す．  
`__get_attr2`と同様に`read_params`を呼び出している．各行に適用する関数として`lambda params: (int(params[0]))`を指定している．これによって，取り出されるパラメータの各行は`int`の形式になっている．

**arguments**

* param(strinig)  
パラメータ`DK`を指定

**returns**

パラメータ`DK`の値

#### ゲッターメソッド

各パラメータにアクセスするためのゲッターメソッドが定義されている．ゲッターメソッドは内部で対応する`__get_attrX`を呼び出してパラメータを取り出している．

* `dk(self)`  
`return self.__get_attr1(self.__dk)`
* `m(self)`  
`return self.__get_attr1(self.__m)`
* `n(self)`  
`return self.__get_attr1(self.__n)`
* `n(self)`  
`return self.__get_attr1(self.__n)`
* `cost(self)`  
`return self.__get_attr2(self.__cost)`
* `capacity(self)`  
`return self.__get_attr2(self.__capacity)`
* `hop(self)`  
`return self.__get_attr2(self.__hop)`
* `traffic(self)`  
`return self.__get_attr3(self.__start, self.__target)`
* `DK(self)`  
`return self.__get_attr4(self.__start, self.__DK)`

### 使用例

各パラメータはDatオブジェクトのインスタンス変数の参照によって取り出す．

```python
from proposal_packages.dat_utils import Dat

dat = Dat("to/datfile/sample.dat")

# 各パラメータを取り出す
print("file name", sample.filename)
print("dk", sample.dk)
print("m", sample.m)
print("n", sample.n)
print("capacity", sample.capacity)
print("cost", sample.cost)
print("hop", sample.hop)
print("traffic", sample.traffic)
print("DK", sample.DK)
```

取り出させるパラメータの形式を変更する．例えば`cost`は`int int float`の形式だが，これを`float float float`に変更する．

```python
converter = lambda params: (float(params[0]), float(params[1]), float(params[2]))
for p in sample.read_params("cost", converter):
    print(p)
```
