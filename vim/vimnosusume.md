# Vimのすすめ

## VIMとは

> VI Improvedの略でCUIのテキストエディタで神

## Vimの導入

python3、lua付きのvimをソースからコンパイルする

Ubuntu向け（多分他のLinuxもいける）

```bash
$ sudo apt install -y git gettext libtinfo-dev libacl1-dev libgpm-dev build-essential python3-dev lua5.2 liblua5.2-dev luajit libluajit-5.1 autoconf automake cproto
$ git clone https://github.com/vim/vim.git ~/vim
$ cd ~/vim
$ git pull
$ cd src
$ make distclean
$ ./configure --with-features=huge --enable-perlinterp --enable-python3interp --enable-rubyinterp --enable-luainterp --with-luajit --with-lua-prefix=/usr/local --enable-fail-if-missing
$ make
$ sudo make install
```

## 聖典

{% embed data="{\"url\":\"https://postd.cc/?s=vim-galore\",\"type\":\"link\",\"title\":\"Vim-galore \| POSTD\",\"icon\":{\"type\":\"icon\",\"url\":\"https://postd.cc/app-icon.png\",\"aspectRatio\":0}}" %}

素のVimで出来ることが体系立てて丁寧に説明されています

## チュートリアル

MacやLinuxの方へ

```text
$ vimtutor
```

## プラグイン

プラグイン検索サービス

{% embed data="{\"url\":\"https://vimawesome.com/\",\"type\":\"link\",\"title\":\"Vim Awesome\",\"description\":\"Awesome Vim plugins from across the universe\",\"icon\":{\"type\":\"icon\",\"url\":\"https://vimawesome.com/static/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://vimawesome.com/static/img/og.png\",\"width\":796,\"height\":416,\"aspectRatio\":0.5226130653266332}}" %}



### プラグインマネージャー

#### dein.vim

{% embed data="{\"url\":\"https://github.com/Shougo/dein.vim\",\"type\":\"link\",\"title\":\"Shougo/dein.vim\",\"description\":\"dein.vim - :zap: Dark powered Vim/Neovim plugin manager\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars3.githubusercontent.com/u/41495?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

### ファイラ

#### NERDTree

{% embed data="{\"url\":\"https://github.com/scrooloose/nerdtree\",\"type\":\"link\",\"title\":\"scrooloose/nerdtree\",\"description\":\"nerdtree - A tree explorer plugin for vim.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/1671?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

#### vimfiler

{% embed data="{\"url\":\"https://github.com/Shougo/vimfiler.vim\",\"type\":\"link\",\"title\":\"Shougo/vimfiler.vim\",\"description\":\"vimfiler.vim - :file\_folder: Powerful file explorer implemented by Vim script\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars3.githubusercontent.com/u/41495?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

## Tips

ほぼ毎日触っているのに無限に知らない操作が出てくる

### ファイルを再オープン

vimで開いているファイルが別のアプリケーションなどによって変更された際に、その変更を反映することができる。

```text
:e!
```

### エンコードを指定してファイルを再オープン

いちいち変換したりファイルを閉じる必要がないのは便利。

```text
:e ++enc=[エンコード]
# e.g.
:e ++enc=shift_jis
:e ++enc=utf-8
```

### コマンド履歴を表示

コマンドラインモードで使用したコマンドの履歴を表示する。コマンドは`ce`などで単語削除したり、ビジュアル、リプレイスモードで編集もできる。

```text
# ノーマルモードで実行
q: or q/ or q?
```

### 文字列置換

基本的な書式は以下のとおり。これはカーソルのある行で最初にマッチした文字列`after`を`before`に置換する。

```text
:s/before/after/
```

#### オプション1 `%`

書式の`s`の前に`%`を付けるとファイル全体が検索対象となる。ちなみに、コマンドラインモードにおいて`%`は**現在操作対象になっているファイル**という意味である。

#### オプション2 書式の末尾に付与

書式の末尾に付けるオプションを以下の表にまとめる。

| オプション | 動作 |
| --- | --- | --- | --- | --- | --- |
| `g` | 行中のマッチングした文字列すべてを置換 |
| `c` | マッチングのたびに置換するかどうか確認 |
| `i` | アルファベットの大文字小文字を無視 |
| `I` | アルファベットの大文字小文字を考慮 |
| `n` | マッチング箇所の数を表示。置換は行わない |

#### 使用例

* ファイル中のすべてのマッチした文字列を置換

```text
:%s/before/after/g
```

* 範囲を指定して置換

```text
:10,20s/before/after/g
```

* ビジュアルラインモードで範囲を指定して、マッチングした文字列をすべて置換。範囲選択後に`:`を押下すると`'<,'>`が自動挿入されるので実際に入力するのは最初の`/`以降である。

```text
:'<,'>s/before/after/g
```

### ファイルの差分を表示

バッファに存在していて現在カーソルがあるファイルとの差分を水平分割して表示する。

```text
:diffsplit filename
```

垂直分割して表示する。

```text
:vartical diffsplit filename
```

### タブで開く

`:tab`に続けて新しくウィンドウを開くコマンドを指定することで、新しくタブを開いてコマンドを実行する。

ただし、`:diffpatch`、`:execute`、`:normal`、`diffsplit`に対しては機能しない。

以下の例ではtabpageのヘルプをタブを開いて表示する。

```text
:tab h tabpage
```

### 複数行をコメントアウト or アンコメント

**正確には行先頭に任意の文字列を挿入する or 行先頭の文字を削除する操作である。**

#### コメントアウト

1. ブロックビジュアルモード`ctr + v`に移行しコメントアウトしたい範囲の行先頭を選択
2. `shift + i`でインサートモードに移行し、`#`や`//`を入力
3. `esc`や`ctr + [`でノーマルモードに移行

#### アンコメント

コメントアウトの対義語らしいです。

ブロックビジュアルモードに移行して行先頭を選択する。あとは`c`、`d`などで選択文字を削除する。

### root権限でファイルを保存

`sudo vim filename`しないでルート直下のディレクトリにあるコンフィグファイルを編集して保存できずに絶望した

```text
:w !sudo tee > /dev/null %
```

#### 結局何をやっているコマンドなのか

`sudo`つけ忘れるたびにググって意味も分からず打ち込んでいるのは良くないので調べた。

各コマンドを分解して意味を整理していく。

```text
:w
```

知ってのとおり、ファイルを保存するコマンドである。root権限無しで実行すると当然ファイルは保存されないが、**変更後の内容は続けて入力したshellコマンドに渡すことができる**。

```text
!sudo tee
```

Vimのコマンドラインモードで!に続けてshellコマンドを打ってCRを押下すると、そのコマンドを実行することができる。つまり上記のコマンドは`sudo`権限で`tee`を実行していると解釈できる。

`tee`は標準入力から受け取った内容を標準出力とファイルに同時に書き込むコマンドである。リダイレクト操作`>`に非常に似ているが、`>`は常にリダイレクト内容を上書きするのに対し、`tee`は`-a`オプションを付けることでファイルに追加書き込みをすることができるという違いがある（`>>`と等価な操作である）。

ここまでをまとめると、`sudo`なし`:w`の結果が`!sudo tee`に渡されて変更後の内容がどこかしらの出力デバイスとファイルに書き出されているといえる。

```text
> /dev/null %
```

どこかしらの出力デバイスとファイルを指定しているのが上記コマンドである。

`tee`は特に指定がないと出力デバイスは標準出力が指定されるが、ここでは&gt;を使用して明示的に出力デバイスを`/dev/null`にしている。`/dev/null`はUNIX、Linuxで使用される疑似デバイスの一種で、ここに渡された内容はすべて捨てられ、いかなる操作を行っても何も返さない。つまり、保存の際に必要のない標準出力への表示を読み捨てるための操作を行っている。

`%`は文字列置換の項目でも述べているとおり、現在操作対象になっているファイルを表している。`tee`が渡してくる変更後の内容を変更前の内容を持ったカレントファイルに上書きするために`%`を指定している。

以上をまとめると、変更後の内容をsudo権限を付けた`tee`コマンドでカレントファイルに上書きしているだけである。やや長いコマンドだが分解して追っていくと、そんなに激しい操作をしているわけではないことが分かる。



