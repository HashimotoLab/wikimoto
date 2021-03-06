# latex

latexは学術界隈で非常によく使われている組版システムです。独特の記法を使用しなければならないため、初期の学習コストはやや高めですが、慣れてくるとメチャクチャかっこいい体裁の文章を手軽に作れるようになるのでぜひ覚えましょう。

## 執筆環境

latexを記述する環境は様々存在していますが、おすすめはVim\(Neovim\)です。理由としてはwikimotoのvimのページで述べられている通り、"""神"""だからです。

Vimはどの言語のコーディングにおいても快適な環境を構築することができますが、特にlatexに絞って言うと、latex側の様々な設定に対してVimが柔軟に対応することができる点が神です。

他のエディタ、例えばsublime textでlatexの執筆環境をセッティングする拡張機能では、latexのコンパイルのルールをシステムではなく拡張機能側で内包してしまうため柔軟な対応ができません。それどころか、拡張機能をアップデートするたびにユーザが作った設定が全部ぶっ飛びます（すごい！）。

### Tips by Yoshida

Windowsで編集されたShift\_JISのtex・styファイルなどをVimでMac用に変換する手順

```bash
:e ++enc=shift-jis # ファイルの文字コードをShift-JISに変換して開き直す
:set fenc=         # ファイルエンコーディングをリセット
:%s/¥/\\/g         # ¥マークを\に全置換
```



