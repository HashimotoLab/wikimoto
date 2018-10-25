# 並列処理ライブラリOpenMPを有効にしたGraphillionのインストール

**WARNING!**

これは不完全な記事です．この手順に沿ってセットアップしても動かないGraphillionがインストールされます．悲しいね．

## OpenMPとGraphillion

OpenMPは並列処理を可能にするライブラリでFORTRAN，C/C++で利用できる．GraphillionのメソッドはPythonで書かれているが，基盤となるZDDはOpenMPに対応したC++で記述されており並列処理の恩恵を受けることができる．  
執筆時点でのGraphillionの最新バージョンであるv1.2において，並列化の対象になっているメソッドは以下の通り．  

* `GraphSet.graphs(constraints)`
* `GraphSet.connected_components(vertices)`
* `GraphSet.cliques(k)`
* `GraphSet.trees(root, is_spanning)`
* `GraphSet.forests(roots, is_spanning)`
* `GraphSet.cycles(is_hamilton)`
* `GraphSet.paths(terminal1, terminal2, is_hamilton)`

## OpenMPとGraphillionのインストール

**前提としてCコンパイラはgcc使うこと．**  
MacのみXcode経由でインストールしたclangでもGraphillionをコンパイルできるが，Appleが頑なにOpenMPに対応させないおかげで並列化できない状態でインストールされてしまう．

### Mac

前節で述べている通りXcodeのclangでは並列化できないし，システムの/usr/bin/gccもOpenMPに対応していないので，homebrewからOpenMPに対応したgccをインストールする．  

```bash
$ brew install gcc
```

これで最新版ののgccがインストールされる．執筆時点ではv8.2である．

続いて，OpenMPIをインストールしOpenMPライブラリを使用できるようにする．

```bash
$ brew install openmpi --with-cxx-bindings
```

最後に並列処理で使用するCUPのコア数を指定する．

{% code-tabs %}
{% code-tabs-item title=".bash_profile" %}
```bash
export OMP_NUM_THREADS=4 # 現在使っているMacが4コアのCUPなので4を指定した．
```
{% endcode-tabs-item %}
{% endcode-tabs %}


これでOpenMPを使う準備は整った．OpenMPに対応したプログラムを実行してみて，ちゃんと並列で動作するか確認してみる．

{% code-tabs %}
{% code-tabs-item title="parallel_sample.c" %}
```c
#include <omp.h>
#include <stdio.h>

int main(void) {
  #pragma omp parallel
  printf("Hello from thread %d, nthreads %d\n", omp_get_thread_num(), omp_get_num_threads());
  return 0;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

あとは，長いオプションをつけたg++でコンパイルする．

```bash
$ g++ -fopenmp -I/usr/local/opt/open-mpi/include -L/usr/local/opt/open-mpi/lib -lmpi parallel_sample.c
```

これを実行してみると．．．

{% code-tabs %}
{% code-tabs-item title="実行結果" %}
```
Hello from thread 0, nthreads 4
Hello from thread 2, nthreads 4
Hello from thread 1, nthreads 4
Hello from thread 3, nthreads 4
```
{% endcode-tabs-item %}
{% endcode-tabs %}

4つのプロセスが立ち上がり4回printf関数が呼び出されている．また，実行するたびスレッドの終了順も変わるので並列化できているっぽい．


### Ubuntu

サーバのUbuntuにも入れたいのでそのうち書く．


### Windows

ビル・ゲイツ

## メモ

長々書いたのに結局動かなくて悲しいので怪しい箇所をメモしておく

* openmpiのインストール  
今回はhomebrew経由でインストールしたが，こうするとopenmpiをコンパイルするコンパイラがXcode経由のclangになってしまうらしい（あれだけ忌避していたのに！）  
しかし，Macの項でCのサンプルプログラムを実行する際に，オプションで使うライブラリを明示しているので大丈夫な気もする．確信はない．

* cmakeのコンパイルオプションが怪しい  
動的ライブラリとかいうのを上手く使えるようにするオプションが無い．

### ImportError!!!

Graphillionをインポートしようとしても以下の例外が発生してインポートに失敗する．

`ImportError: dlopen(/usr/local/lib/python3.7/site-packages/_graphillion.cpython-37m-darwin.so, 2): Symbol not found: \_\_ZNSt15basic_streambufIcSt11char_traitsIcEE4syncEv`

### 書くこと

* 環境変数CCのコンパイラを明示する

* cmakeのコンパイラを明示する（環境変数CXX）


## 参考にしたwebサイト
