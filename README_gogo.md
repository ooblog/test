# 「test」テストリポジトリ〜午前中にGitHubを使えるようになったメンバーにはブランチの管理も覚えてもらうマニュアル。

「SSH」て単語が何の事かわからない人は「[午前の部](README.md)」にどうぞ。  
「git ls-files」ってなんだっけって人も「[午前の部](README.md)」にどうぞ。  
「.gitignore」って何の役に立つと思ってる人も「[午前の部](README.md)」にどうぞ。  

なんで「push」するとき「master」が必要なのと思った人は逆に午後の部予習してからの方が理解しやすいかも。  

# 動いてる本番環境(master)に手をかけるな。まずはブランチを切る。

ちょっと直すだけだからmasterブランチに直接コミットでいいやと思って泥沼にハマり、  
手動でバックアップコピー取ってからの「git log」でハッシュ確認し「git reset --hard」の使い方覚えた人挙手。  

## ブランチの作成とブランチの種類確認が同じ「branch」コマンドなので注意。

    # git branch
    * master

    # git branch switchcase

    # git branch
    * master
      switchcase

## ブランチの切り替えは「checkout」。

    # git checkout switchcase
    Switched to branch 'switchcase'

    # git branch
      master
    * switchcase

## ブランチを「push」する時は「master」ではない。

ブランチの使い分けをしなかった初学状態のpush。  

    # git push github:ooblog/yonmoji&#95;ge.git master

これからはブランチ名を確認＆明記。  

    # git push github:ooblog/yonmoji&#95;ge.git switchcase

## ブランチ引数を忘れた場合はどうなるか。

[引数なしのgit pushは危険なので気をつけましょう](http://dqn.sakusakutto.jp/2012/10/git_push.html)
>引数なしで"git push"すると、何がpushされるかご存知でしょうか？  
>デフォルトでは、ローカルブランチと同名のブランチがリモート上にあるならそれらを一気にpushしてしまいます。  
>カレントブランチが何であろうと関係ないのです。  
>公式アナウンスによれば、Git次期バーション(おそらく2.0)あたりから"simple"モードがデフォルトになるそうです。  
>simpleモードとは、「カレントブランチと同名のリモートブランチが存在する場合のみ、カレントブランチのpushが行われる」モードだそうです。  

参考までにバージョン確認方法。

    git --version

## まだ書きかけ。

備忘録。

## あとがき＆今後の予定。

執筆者もGitHub勉強中。  
ブランチ管理が必要になったのでとりあえず最小限メモ書き。  
