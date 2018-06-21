# python\_seminar

### 問題1 小問集合（関数作成）

{% code-tabs %}
{% code-tabs-item title="problem1\_create\_functions.py" %}
```python
"""
# 関数作成

提示された形式の関数を作成せよ

課題1-1 fizzbuzz
start以上stop未満の範囲の数字を","(カンマ)で区切って1行に10個表示する関数を作成せよ
ただし、3の倍数のときはFizz、5の倍数のときはBazz、15の倍数のときはFizzBazzを
数字の代わりに出力すること

fizzbuzz(start, stop)
    # arguments:
      start(int)
      stop(int)
    # returns:
      なし


課題1-2 素数判定
引数として与えられた自然数nが素数か判定する関数を作成せよ

is_prime(n)
    # arguments:
      n(int)
    # returns:
      True or False(bool)


課題1-3 九九
1行目が九九の1の段、2行目が九九の2の段、n行目が九九のnの段...となっている2次元のリストを作成する関数を作成せよ

make_kuku_list(n):
    # arguments:
      n(int)
    # returns:
      a(list)


課題1-4 モンテカルロ法による円周率の近似計算
発生点pointsはデフォルト値100を持つオプション引数とせよ

calc_pi_by_montecarlo(points=100)
    # arguments:
      points(int, optional)
    # returns:
      pi(float)
"""

def fizzbuzz(start, stop):
    for i in range(start, stop):
        if i % 15 == 0:
            print("FizzBazz", end="")
        elif i % 3 == 0:
            print("Fizz", end="")
        elif i % 5 == 0:
            print("Bazz", end="")
        else:
            print(i, end="")

        if i % 10 == 9:
            print()
        else:
            print(end=",")

def is_prime(n):
    if n < 2:
        return False

    for i in range(2, n):
        if n%i == 0:
            return False
    return True

def make_kuku_list(n):
    kuku_list = []
    for i in range(n):
        row = []
        for j in range(1, 10):
            row.append((i + 1) * j) 
        kuku_list.append(row)
    return kuku_list

def calc_pi_by_montecarlo(points=100):
    import random

    inner_points = 0
    for i in range(points):
        x = random.random() 
        y = random.random() 
        if pow(x, 2) + pow(y, 2) < 1:
            inner_points += 1
    pi = 4 * inner_points / points
    return pi

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 問題2 JSONで記述されたルールに基づいたfizzbuzz

{% code-tabs %}
{% code-tabs-item title="problem2\_flexible\_fizzbuzz.py" %}
```python
"""
* jsonモジュールの使い方
* dictの使い方

# flexible fizzbuzz
1以上100以下の数字を","(カンマ)で区切って1行に10個表示するプログラムを作成せよ。
ただし、与えられたjsonファイルに記述された倍数のときには数字の代わりに対応するメッセージを表示せよ。
また、複数の数字の倍数の場合にはメッセージを連結して表示せよ。
jsonファイルは以下のフォーマットで与えられる。

{
    "message1":num1,
    "message2":num2
}

num1の倍数のときに表示する内容はmessage1であることを表している。



{
  "fizz":3,
  "buzz":5,
  "hello":7,
  "world":9
}

表示例
1,2,fizz,4,buzz,fizz,hello,8,fizzworld,buzz
11,fizz,13,hello,fizzbuzz,16,17,fizzworld,19,buzz
fizzhello,22,23,fizz,buzz,26,fizzworld,hello,29,fizzbuzz
31,32,fizz,34,buzzhello,fizzworld,37,38,fizz,buzz
41,fizzhello,43,44,fizzbuzzworld,46,47,fizz,hello,buzz
fizz,52,53,fizzworld,buzz,hello,fizz,58,59,fizzbuzz
61,62,fizzhelloworld,64,buzz,fizz,67,68,fizz,buzzhello
71,fizzworld,73,74,fizzbuzz,76,hello,fizz,79,buzz
fizzworld,82,83,fizzhello,buzz,86,fizz,88,89,fizzbuzzworld
hello,92,fizz,94,buzz,fizz,97,hello,fizzworld,buzz
"""

import json

f = open("fizzbuzz_rule.json")
fizzbuzz_rule = json.load(f)

matched_nums = []
for num in range(1, 101):
    for message, divisor in fizzbuzz_rule.items():
        if num % divisor == 0:
            print(message, end="")
            matched_nums.append(num)

    if num not in matched_nums:
        print(num, end="")

    if num % 10 == 0:
        print()
    else:
        print(end=",")

f.close()

```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="fizzbuzz\_rule.json" %}
```javascript
{
  "fizz":3,
  "buzz":5,
  "hello":7,
  "world":9
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 問題3 OSPFのRFC取ってきて単語カウントする

{% code-tabs %}
{% code-tabs-item title="problem3\_ospf\_count.py" %}
```python

"""
httpのGETメソッドを用いてOSPFのRFC(https://tools.ietf.org/html/rfc2328)を取得し，
本文中に出てくる"OSPF"という単語をカウントせよ．
その際，httpリクエストが失敗した時のエラー処理も含めること．
"""
import requests, bs4, sys

res = requests.get('https://tools.ietf.org/html/rfc2328')
try:
    res.raise_for_status()
except Exception as exc:
    print('error:{}'.format(exc))
    sys.exit()

ospf_soup = bs4.BeautifulSoup(res.text, "html.parser")
body = ospf_soup.find('body').text
print(body.split().count("OSPF"))

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 問題4  テキストファイルの読み書き

{% code-tabs %}
{% code-tabs-item title="problem4\_phone\_number\_match.py" %}
```python
"""
1万個のランダムな携帯電話番号が記述されたテキストファイル(phone_number.txt)には，
携帯電話番号として誤っているものが含まれている．
このファイルを読み込み，正規表現を用いて携帯電話番号として正しいか確認し，
True  :別のテキストファイル(correct_phone_number.txt)に書き込む
False :標準出力
という処理を行うプログラムを作成せよ．

ここで携帯電話番号は，
(070or080or090)-(4桁の数字)-(4桁の数字）とする

例
090-1111-1111 → OK
0123-4567-890 → NG
060-1111-1111 → NG
"""

import re

read_file = open('phone_number.txt', 'r')
write_file = open('correct_phone_number.txt', 'w')

# 正規表現パターンを正規表現オブジェクトにコンパイル
phone_regex = re.compile(r'^(070|080|090)-\d{4}-\d{4}$')

f = read_file.read()

for line in f.splitlines():
    if phone_regex.match(line):
        write_file.write(line + '\n')
    else:
        print(line)

read_file.close()
write_file.close()
```
{% endcode-tabs-item %}
{% endcode-tabs %}

phone\_number.txtはRockdiskのわしのゼミ関連フォルダにおります

