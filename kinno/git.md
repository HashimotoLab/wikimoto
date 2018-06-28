# git

## TODO

* エイリアスの設定方法を書く
* 便利なエイリアスをまとめる
* コンフリクトしたときの操作を書く
* リモートのコミットを変更する方法を書く

## バージョン管理ツールを使う

gitはいわゆるバージョン管理ツールの一種です．

バージョン管理（変更箇所の管理）するときにやりがちなのが，対象のプロジェクトに変更が加えられるたびに日付を付与したフォルダやzipファイルを新たに作成する方法だと思います．2，3回程度の変更に対してなら運用できそうな方法ですが，これが月単位で続いたり一日に何度も変更が発生する場合には

* ファイルが多すぎてどこを見ればよいか分からない
* そもそも変更を管理したいのに変更差分を（効率よく）見る方法がない

といった問題が発生してツラくなってくるはずです．

これに対してgitのようなバージョン管理ツールでは，見かけ上管理対象のプロジェクトはただ1つだけ存在しています．なので日付ベースの管理方法のようにファイルの氾濫で混乱することはまずありません．さらに便利なのが

* 変更履歴を見られる．さらに条件で絞込もできる
* 履歴から選択したある時点での差分を表示できる
* ファイルの状態を過去のある時点のものに戻せる
* 削除してしまったファイルを復元できる
* GUIのクライアントを使えばバイナリファイルの差分も見られる

このような操作がコマンドラインあるいはGUIクライアントから容易に行えます．

色々書いてきましたが，正直良くわかっていないことが多いので，新たに得られた知見があればどんどん追記していきます．

## コンフィグファイル

### コンフィグファイルの場所と優先順位

pending

### パーミッションの変更を検知させない

デフォルトだとファイルのパーミッション変更も差分として検知してしまうが，これを無視させる

全体に適用させるなら以下のコマンドを実行する

```text
$ git config core.filemode false
```

特定のリポジトリだけに設定するなら，`.git/config`を編集する

```text
[core]
    repositoryformatversion = 0
    filemode = true # ←ここをfalseに変える
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
```

（OneDriveくんがファイルを同期したタイミングでファイルパーミッションを勝手に755から644に変えるのなんなんすかね）

## HEADと`~`，`^`

### HEAD

`HEAD`とは現在のブランチの最新のコミットを指すポインタである．  
例えば`show`コマンドの引数として`HEAD`を指定すれば最新のコミットのログを見ることができる．

```bash
$ git show HEAD # 最新のコミットを表示
```

また`HEAD`にはエイリアスとして`@`が割り当てられているので，上に挙げたコマンドは以下と等価である．

```bash
$ git show @
```

### HEADを起点としたコミットの遡り

`HEAD`に続けて`~`や`^`を付けると，HEADから指定した数だけ遡ったコミットを参照することができる．

#### `~`チルダ

`HEAD`に続けて遡りたい数だけ`~`を付けると，その数に該当するコミットを参照できる．  
以下のコマンドでは`HEAD`の1つ前のコミットから数えて4番目のコミットを参照する．

```bash
$ git show HEAD~~~~ # 4つ前のコミットを参照する
```

この記法では`~`が冗長になってしまうので略記法として`HEAD~4`とすることもできる．

まとめると，`~n`**はn世代前のコミットを参照する操作**

#### `^`キャレット

（これキャレットって読むんですね，ずっとハットだと思ってた．）

`^`も`~`と同様に遡りたい数だけ`HEAD`のあとに付与すると，その数だけ遡ったコミットを参照することができる．

```bash
$ git show HEAD^^^^ # こっちも4つ前のコミットを参照する
```

ただし`~`と同じ考えで`HEAD^n`とすると意味が違ってくるので注意が必要．  
`HEAD^n`は`HEAD`のn番目の親コミットを参照するという意味である．

例えば，以下の図のように`HEAD`の1つ前のコミットで2つのブランチをマージしていた場合，`HEAD`の親コミットは2つ存在することになる．  
このような場合，親コミットをどちらか片方のブランチから選んで指定する必要がある．

```text
        ここがHEAD^1で指定される
                 ↓
master *-*-*-*-*-*-* ←ここがHEADだとするとHEADの親コミットはmasterとsubブランチの2つ
       \          /
   sub  *-*-*-*-*
                ↑
        ここがHEAD^2で指定される
```

```bash
$ git show HEAD^1 # master側の親コミットを参照
```

```bash
$ git show HEAD^2 # sub側の親コミットを参照
```

`HEAD`に対して3，4も親が存在することはほとんどない（プロジェクトの運用方法を見直したほうがよい）と思うので，使っても`HEAD^2`くらいだと思う．wikimotoの場合は，ブランチを切ってmasterにマージする運用方法はとっていないので使う機会はなさそう．

まとめると，`^n`**はn番目の親を参照する操作**

## コマンド

### 1. ブランチ操作

#### ブランチの結合

```text
$ git merge --no-ff <branchname>
```

#### ブランチの削除

* ローカルブランチの場合

```text
$ git branch -d <branchname>
```

* リモートブランチの場合

```text
$ git push origin --delete <branchname>
```

### 2. コミット操作

#### 直前のコミットのやり直し

```text
$ git commit --amend
```

#### コミットの取り消し

* ワーキングディレクトリの内容を保持したままコミットだけ取り消す

```text
$ git reset --soft <commit>
```

使用例として、直前のコミットのみ取り消すコマンドを示す

```text
$ git reset --soft HEAD^ # HEAD^は直前のコミットを指す
```

* ワーキングディレクトリの内容を保持せずコミットも取り消す

```text
$ git reset --hard
```

#### 取り消しの取り消し

`git reset --hard`で誤ってワーキングディレクトリの状態も戻してしまったときの対処法

```text
$ git reset --hard "HEAD{1}"
```

#### コミットログの絞り込み

`git log`や`git diff`にオプション`--diff-filter`を使用すると追加，削除など特定の操作をしたコミットや差分だけを表示することができる

書式は以下の通り

```text
$ git log --diff-filter=
```

```text
$ git diff --diff-filter=
```

`--diff-filter=`に続けて`A`（追加），`C`（コピー），`D`（削除），`M`（変更），`R`（リネーム）などを指定して絞り込む  
ほかにもいつくかオプションが存在するが，あまり使わなそうなので省略する  
さらに，指定するアルファベットを小文字にすると**除外**の意味になる．例えば`a`を指定すると，ファイルを追加したコミットを除外して表示することができる

### 3. ファイルの状態

#### 特定ファイルを特定コミットの状態に戻す

```text
$ git checkout [hash] [filepath]
```

#### 編集中のファイルをHEADに戻す

```text
$ git checkout [filepath]
```

#### 削除してしまったファイルを復元する

ファイルを削除したコミットを検索する

```text
$ git log --diff-filter=D --summary
```

ファイルの状態を削除したコミットの直前のコミットに戻せばファイルを復元できる  
直前のコミットの指定には`^`を使用する

```text
$ git checkout [hash]^ -- [filepath]
```

## バイナリ文章フォーマットの差分を取る

officeやpdfのようなバイナリ文章フォーマットの差分を`git diff`で表示する

macOSでの例を示す

### インストールするもの

* Java
* Appache Tika

```text
$ brew install tika
```

### スクリプトの準備

パスの通ったディレクトリに以下のスクリプト`unopenxml`を配置する。なお、この例では`/usr/local/bin/`とした

{% code-tabs %}
{% code-tabs-item title="unopenxml" %}
```bash
#!/bin/sh
tika -t "$1"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

忘れずに実行権限を付与する

```text
$ chmod +x unopenxml
```

`~/.gitconfig`に以下のように記述する

```text
[diff "openxml"]
  textconv = /usr/local/bin/unopenxml
```

あとは、バイナリ文章フォーマットの差分を取りたいリポジトリに`.gitattributes`を作成し、対象ファイルを記述する。以下の例ではpdfとMicrosoft Officeのファイルに対して`unopenxml`を用いた`git diff`を行えるように設定している

```text
*.pdf diff=openxml
*.pptx diff=openxml
*.docx diff=openxml
*.xlsx diff=openxml
```

## 参考資料

### コミット操作

{% embed data="{\"url\":\"https://qiita.com/kansiho/items/2bacecdb95d752cb38b7\",\"type\":\"link\",\"title\":\"さっきの取り消したい！って時のGitコマンドまとめ\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.qiita.com/assets/favicons/public/apple-touch-icon-f9a6afad761ec2306e10db2736187c8b.png\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"http://d.hatena.ne.jp/mrgoofy33/20100910/1284069468\",\"type\":\"link\",\"title\":\"git commitをやり直しする＆取り消しする\(「git commit --amend」と「git reset」\) - hogehoge foobar Blog Style5\",\"description\":\"git commitを実行あとでコミットをやり直したり、コミット自体を取り消す方法です。 直前にしたコミットを..\",\"icon\":{\"type\":\"icon\",\"url\":\"http://d.hatena.ne.jp/images/wh\_favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://devlights.hatenablog.com/entry/20121121/p1\",\"type\":\"link\",\"title\":\"git reset --hardした内容を取り消す \(git reset --hard, reflog, HEAD@{x}, 取り消してしまったコミットを元に戻す\) - いろいろ備忘録日記\",\"description\":\"相変わらずGit勉強中です。 以下自分用のメモです。 特定のコミット自体をなかったことにするには git reset --hard ... を利用すればいいのですが、このコマンドはhardとオプションが ついているように、コミット自体が無かったことになってしまいます。 なので、間違えて違うコミットの部分にresetしてしまうと ｱﾜﾜﾜな事になります。（というか、なりましたｗ） でも、さすがgitさん。当然元に戻す方法がありました。 reflogを使って、元に戻せます。 元に戻す場合に利用するコマンドも git reset --hard です。 git reset --hard \\\"HEAD@{…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.blog.st-hatena.com/images/common/meta-icon-global.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.blog.st-hatena.com/images/theme/og-image-1500.png\",\"width\":1500,\"height\":1051,\"aspectRatio\":0.7006666666666667}}" %}

### ファイルの状態

{% embed data="{\"url\":\"http://konbu13.hatenablog.com/entry/2014/12/01/070255\",\"type\":\"link\",\"title\":\"gitで特定のファイルだけ前の状態に戻したいときのコマンド - うめすこんぶ\",\"description\":\"gitで何かのファイルを編集していて、しまった、失敗したから戻したい、となった時に、 git resetを使うと戻さなくていいファイルまで戻してしまう。 そうならないように、特定ファイルだけ戻す方法です。 git log ファイルパス git checkout ad9772b1dbcf4e3d7f658305c7184d673fc32637 ファイルパス git logでファイルのパスを入力、コミットログのハッシュ値 \(ad9772b1dbcf4e3d7f658305c7184d673fc32637\) を調べ、git checkoutで戻せます。\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.image.st-hatena.com/image/square/5ddc0c4cb6d8858fa530f3c21e310b7c322c4234/backend=imagemagick;height=192;version=1;width=192/https%3A%2F%2Fcdn.user.blog.st-hatena.com%2Fcustom\_blog\_icon%2F49574567%2F1514234322386484\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.image.st-hatena.com/image/scale/2c934bbe7c4d9579f8aa179cfbfe5f7506d05be7/backend=imager;enlarge=0;height=1000;version=1;width=1200/https%3A%2F%2Fcdn.user.blog.st-hatena.com%2Fdefault\_entry\_og\_image%2F49574567%2F1514234582273105\",\"width\":64,\"height\":48,\"aspectRatio\":0.75}}" %}

{% embed data="{\"url\":\"https://qiita.com/saekis/items/5af43cd55315fc3b6d04\",\"type\":\"link\",\"title\":\"Gitで特定のファイルのみ前のバージョンに戻す\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.qiita.com/assets/favicons/public/apple-touch-icon-f9a6afad761ec2306e10db2736187c8b.png\",\"aspectRatio\":0}}" %}

### バイナリ文章フォーマットの差分を取る

{% embed data="{\"url\":\"https://qiita.com/shuhei/items/6a18d968051378d7ac1a\",\"type\":\"link\",\"title\":\"git diff で Office ファイルの差分を見る\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.qiita.com/assets/favicons/public/apple-touch-icon-f9a6afad761ec2306e10db2736187c8b.png\",\"aspectRatio\":0}}" %}

