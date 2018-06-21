# git

## コマンド

### 1. ブランチ操作

**ブランチの結合**

```text
git merge --no-ff <branchname>
```

**ブランチの削除**

* ローカルブランチの場合

```text
git branch -d <branchname>
```

* リモートブランチの場合

```text
git push origin --delete <branchname>
```

### 2. コミット操作

**直前のコミットのやり直し**

```text
git commit --amend
```

**コミットの取り消し**

* ワーキングディレクトリの内容を保持したままコミットだけ取り消す

```text
git reset --soft <commit>
```

使用例として、直前のコミットのみ取り消すコマンドを示す

```text
git reset --soft HEAD^ # HEAD^は直前のコミットを指す
```

* ワーキングディレクトリの内容を保持せずコミットも取り消す

```text
git reset --hard
```

**取り消しの取り消し**

`git reset --hard`で誤ってワーキングディレクトリの状態も戻してしまったときの対処法

```text
git reset --hard "HEAD{1}"
```

### 3. ファイルの状態

* 特定ファイルを特定コミットの状態に戻す

```text
git checkout [hash] [filepath]
```

* 編集中のファイルをHEADに戻す

```text
git checkout [filepath]
```

## バイナリ文章フォーマットの差分を取る

officeやpdfのようなバイナリ文章フォーマットの差分を`git diff`で表示する

macOSでの例を示す

### インストールするもの

* Java
* Appache Tika

```text
brew install tika
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
chmod +x unopenxml
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

