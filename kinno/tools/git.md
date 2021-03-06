# git

## TODO

* [ ] エイリアスの設定方法を書く
* [ ] 便利なエイリアスをまとめる
* [ ] コンフリクトしたときの操作を書く
* [ ] `rebase`について書く
* [ ] `reset`の項を修正する（指定したコミットの状態に戻す，などの文言を追加）

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

```bash
$ git config core.filemode false
```

特定のリポジトリだけに設定するなら，`.git/config`を編集する

```bash
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

どういう場面で親を指定するかというと，例えば，以下の図のように`HEAD`の1つ前のコミットで2つのブランチをマージしていた場合，`HEAD`の親コミットは2つ存在することになる．  
このとき，親コミットをどちらか片方のブランチから選んで指定しないとコミットを遡って参照することができない．

```bash
        ここがHEAD^1で指定される
                 ↓
master *-*-*-*-*-*-* ←ここがHEADだとするとHEADの親コミットはmasterとsubブランチの2つ存在するので
       \          /   明示的に親を指定する必要がある
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

```bash
$ git merge --no-ff <branchname>
```

#### ブランチの削除

* ローカルブランチの場合

```bash
$ git branch -d <branchname>
```

* リモートブランチの場合

```bash
$ git push origin --delete <branchname>
```

### 2. コミット操作

#### 直前のコミットのやり直し

```bash
$ git commit --amend
```

#### コミットの取り消し

**resetを使う方法**

コミット履歴の整理に使えるが，複数人で作業している場合，すでにリモートへ公開されているコミットの取り消しは危険な操作である．取り消されたコミットを存在させたままローカルで作業を続けてしまうことで，ブランチの状況がメンバーごとに変化してしまう恐れがある．  
wikimotoの場合，gitベースで記事を更新しているメンバーが複数人いるため`reset`によるコミット操作はするべきではない．

* ワーキングディレクトリの内容を保持したままコミットだけ取り消す

```bash
$ git reset --soft <commit>
```

* ワーキングディレクトリの内容を保持せずコミットも取り消す

```bash
$ git reset --hard <commit>
```

* `reset`による操作をリモートリポジトリにも反映させる

```bash
$ git push -f
```

**revertを使う方法**

`reset`がコミットをなかったものにしてしまうのに対して，`revert`はあるコミットによって行われた変更を元に戻すコミットを生成する．  
コミットが消滅しないのでメンバー間のコミットログの齟齬も起きない．wikimotoでリモートのコミットをなかったことにしたい場合は`revert`を使ったほうがいい．

```bash
git revert <commit> # commitによる変更を元に戻す新たなコミットを生成
git push # revertによる打ち消しコミットをリモートへ反映させる
```

#### 取り消しの取り消し

`git reset --hard`で誤ってワーキングディレクトリの状態も戻してしまったときの対処法

```bash
$ git reset --hard "HEAD{1}"
```

#### コミットログの絞り込み

`git log`や`git diff`にオプション`--diff-filter`を使用すると追加，削除など特定の操作をしたコミットや差分だけを表示することができる

書式は以下の通り

```bash
$ git log --diff-filter=
```

```bash
$ git diff --diff-filter=
```

`--diff-filter=`に続けて`A`（追加），`C`（コピー），`D`（削除），`M`（変更），`R`（リネーム）などを指定して絞り込む  
ほかにもいつくかオプションが存在するが，あまり使わなそうなので省略する  
さらに，指定するアルファベットを小文字にすると**除外**の意味になる．例えば`a`を指定すると，ファイルを追加したコミットを除外して表示することができる

### 3. ファイルの状態

#### 特定ファイルを特定コミットの状態に戻す

```bash
$ git checkout [hash] [filepath]
```

#### 編集中のファイルをHEADに戻す

```bash
$ git checkout [filepath]
```

#### 削除してしまったファイルを復元する

ファイルを削除したコミットを検索する

```bash
$ git log --diff-filter=D --summary
```

ファイルの状態を削除したコミットの直前のコミットに戻せばファイルを復元できる  
直前のコミットの指定には`^`を使用する

```bash
$ git checkout [hash]^ -- [filepath]
```

#### ファイルをgitの管理対象から除外する

* ファイルごと削除する場合

```bash
git rm [filepath]
```

* ファイルを残したまま管理から除外する場合

```bash
git rm --cached [filepath]
```

## バイナリ文章フォーマットの差分を取る

officeやpdfのようなバイナリ文章フォーマットの差分を`git diff`で表示する

macOSでの例を示す

### インストールするもの

* Java
* Appache Tika

```bash
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

```bash
$ chmod +x unopenxml
```

`~/.gitconfig`に以下のように記述する

```bash
[diff "openxml"]
  textconv = /usr/local/bin/unopenxml
```

あとは、バイナリ文章フォーマットの差分を取りたいリポジトリに`.gitattributes`を作成し、対象ファイルを記述する。以下の例ではpdfとMicrosoft Officeのファイルに対して`unopenxml`を用いた`git diff`を行えるように設定している

```bash
*.pdf diff=openxml
*.pptx diff=openxml
*.docx diff=openxml
*.xlsx diff=openxml
```

## 参考資料

### コミット操作

{% embed url="https://qiita.com/kansiho/items/2bacecdb95d752cb38b7" %}

{% embed url="http://d.hatena.ne.jp/mrgoofy33/20100910/1284069468" %}

{% embed url="https://devlights.hatenablog.com/entry/20121121/p1" %}

### ファイルの状態

{% embed url="http://konbu13.hatenablog.com/entry/2014/12/01/070255" %}

{% embed url="https://qiita.com/saekis/items/5af43cd55315fc3b6d04" %}

### バイナリ文章フォーマットの差分を取る

{% embed url="https://qiita.com/shuhei/items/6a18d968051378d7ac1a" %}

