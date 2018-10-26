# install graphillion with parallel enabled

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

**前提としてコンパイラはGCC使うこと．**  
MacのみXcode経由でインストールしたclangでもGraphillionをコンパイルできるが，Appleが頑なにOpenMPに対応させないおかげで並列化できない状態でインストールされてしまう．

プラットフォームごとのインストール手順をまとめていく．

### Mac

#### Python

使用したPythonは2.7.15と3.7で，いずれもhomebrew経由で/usr/local/bin/以下にインストールし，ビルドにはXcode経由のclangを使用した．  
本当はOpenMPのビルドで使用するGCCコンパイラでビルドしたかったが失敗したので，やむなくMacで推奨されているXcode経由のclangでビルドした．

#### OpenMPのインストール

前節で述べている通りXcodeのclangでは並列化できないし，システムの/usr/bin/gccもOpenMPに対応していないので，homebrewからOpenMPに対応したgccをインストールする．

```bash
$ brew install gcc
```

これで最新版ののgccがインストールされる．執筆時点ではv8.2である．

続いて，OpenMPIをインストールしOpenMPライブラリを使用できるようにする．

```bash
$ brew install openmpi --with-cxx-bindings
```

最後に並列処理で使用するCPUのコア数を指定する．

{% code-tabs %}
{% code-tabs-item title=".bash\_profile" %}
```bash
export OMP_NUM_THREADS=4 # 現在使っているMacが4コアのCPUなので4を指定した．
```
{% endcode-tabs-item %}
{% endcode-tabs %}

これでOpenMPを使う準備は整った．OpenMPに対応したプログラムを実行してみて，ちゃんと並列で動作するか確認してみる．

{% code-tabs %}
{% code-tabs-item title="parallel\_sample.c" %}
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
適宜エイリアスを設定して短くすると良さそう．

```bash
$ /usr/local/bin/g++-8 -fopenmp -I/usr/local/opt/open-mpi/include -L/usr/local/opt/open-mpi/lib -lmpi parallel_sample.c
```

これを実行してみると．．．

{% code-tabs %}
{% code-tabs-item title="実行結果" %}
```text
Hello from thread 0, nthreads 4
Hello from thread 2, nthreads 4
Hello from thread 1, nthreads 4
Hello from thread 3, nthreads 4
```
{% endcode-tabs-item %}
{% endcode-tabs %}

4つのプロセスが立ち上がり4回printf関数が呼び出されている．また，実行するたびスレッドの終了順も変わるので並列化できているっぽい．

#### Graphillionのインストール

**思考停止で`pip install`したいところだが，いくつか設定すべき事項がある．**

pipでモジュールをインストールする際には必ず`setup.py`というスクリプト起動し，インストールに必要な手続きを行う．  
Graphillionの`setup.py`は大きく分けて2つの処理を行っている．  
以下では，2つのチェックの概要を示しつつOpenMPを有効にしてGraphillionをビルドするために必要な手順を説明していく．

1つ目の処理はOpenMPを使用可能なコンパイラが存在するかチェックする処理である．環境変数`CC`にセットされているコンパイラでOpenMPに対応したCのプログラムをコンパイルし．その成否によってOpenMPを有効にしてGraphillionをビルドするかどうかを決定している．  
環境変数`CC`に適切なコンパイラを指定しないと`/usr/bin/gcc`を使用してチェックを行ってしまう．前節でインストールしたOpenMP対応のGCCでビルドしたいので`CC`にGCCのC++コンパイラへのパスを設定する．

{% code-tabs %}
{% code-tabs-item title=".bash_profile" %}
```bash
export CC="/usr/local/bin/g++-8"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

これで，`setup.py`はOpenMP対応のコンパイラが存在することを認識してくれる．

2つ目の処理はGraphillionのビルドである．ここが1番のハマりポイントであった．  
1つ目の処理をOpenMPを有効にして通過したのに何故かビルドでコケてしまう．これはGraphillionが使用しているビルドツールのCMakeがOpenMPに対応していないコンパイラを使ってビルドを行ってしまうことが原因である．  
CMakeで使用するC++コンパイラは環境変数`CXX`にコンパイラへのパスを記述することによって指定できる．

{% code-tabs %}
{% code-tabs-item title=".bash_profile" %}
```bash
export CXX="/usr/local/bin/g++-8"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

環境変数`CC`と`CXX`に適切なコンパイラへのパスを記述できたら準備完了である．いつもどおり`pip`からインストールする．

```bash
$ pip install graphillion
$ pip3 install graphillion
```

### ImportError!!!

長かった，本当に長かった．これでOpenMPによって並列処理が可能になったGraphilionが使えるようになります．**Python2.7.15のみで．**

2.7.15のPythonで並列処理に対応したGraphillionのメソッドを呼び出してアクティビティマネージャからプロセスを確認すると，間違いなく複数スレッド（今回の環境だと4スレッド）で稼働している．

ではPython3.7ではどうか．

**Graphillionをインポートしようとしても以下の例外が発生してインポートに失敗する．**

`ImportError: dlopen(/usr/local/lib/python3.7/site-packages/_graphillion.cpython-37m-darwin.so, 2): Symbol not found: \_\_ZNSt15basic_streambufIcSt11char_traitsIcEE4syncEv`

おしまい．


### Ubuntu

サーバのUbuntuにも入れたいのでそのうち書く．

### Windows

ビル・ゲイツ

## メモ

長々書いたのに結局動かなくて悲しいので怪しい箇所をメモしておく

* openmpiのインストール 今回はhomebrew経由でインストールしたが，こうするとopenmpiをコンパイルするコンパイラがXcode経由のclangになってしまうらしい（あれだけ忌避していたのに！） しかし，Macの項でCのサンプルプログラムを実行する際に，オプションで使うライブラリを明示しているので大丈夫な気もする．確信はない． 参考になりそうなサイトを挙げておく
  * [MacでMPIとopenmpをハイブリッドにつかえる環境を構築する](https://qiita.com/himo/items/c30d83d0f7642fb3af57)
  * [macOS Sierra で GNU gcc + MPI + OpenMP を使う.](https://qiita.com/syoyo/items/676d1e1c07aef26a49e2)
* cmakeのコンパイルオプションが怪しい 動的ライブラリとかいうのを上手く使えるようにするオプションが無い．


### 書くこと

* 環境変数CCのコンパイラを明示する
* cmakeのコンパイラを明示する（環境変数CXX）

## 参考にしたwebサイト

{% embed url="https://qiita.com/kaityo256/items/ae9329dae24ea8828ae0" caption="" %}

{% embed url="https://qiita.com/hinatades/items/79935d8a2a93ddabe077" caption="" %}

