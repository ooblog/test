# 「test」テストリポジトリ〜午前中にGitHubを使えるようになったメンバーにはブランチの管理も覚えてもらうマニュアル。

「SSH」て単語が何の事かわからない人は「[午前の部](README.md)」にどうぞ。  
「git ls-files」ってなんだっけって人も「[午前の部](README.md)」にどうぞ。  
「.gitignore」って何の役に立つと思ってる人も「[午前の部](README.md)」にどうぞ。  

なんで「push」するとき「master」が必要なのと思った人は逆に午後の部予習してからの方が理解しやすいかも。  

## 動いてる本番環境(master)に手をかけるな。まずはブランチを切る。

ちょっと直すだけだからmasterブランチに直接コミットでいいやと思って泥沼にハマり、  
手動でバックアップコピー取ってからの「git log」でハッシュ確認し「git reset --hard」の使い方覚えた人挙手。  

### ブランチの作成とブランチの確認が同じ「branch」コマンドなので注意。

    # git branch
    * master

    # git branch 20160328M231906

    # git branch
      20160328M231906
    * master

### ブランチの切り替えは「checkout」。

    # git checkout 20160328M231906
    Switched to branch '20160328M231906'

    # git branch
    * 20160328M231906
      master

### ブランチ名の変更は「branch -m」。

[git: ローカルのブランチ名を変更したい](http://qiita.com/suin/items/96c110b218d919168d64)
>今開いているブランチをリネームする場合は、単純に新しいブランチ名を指定するだけです。
>git branch -m <新しいブランチ名>

### ブランチの削除は「branch -d」(もしくは「branch -D」)。

[Git でローカル・リモートのブランチを削除する方法 (git branch -d)](http://www.yunabe.jp/docs/git_delete_branch.html)
>git branch -d <branch&#95;name>  
>現在のブランチは削除できません  
>削除しようとしているブランチがマージされていないとerror: The branch 'mydev' is not fully merged.のようなエラーが表示されます。  
>強制的にブランチを削除したい場合は git branch -D <branch&#95;name>  

マージについては後述。  

## ブランチを「push」(バックアップ)する場所は「master」ではない。

ブランチの使い分けをしなかった初学状態のpush。  

    # git push github:ooblog/LTsv9kantray.git master

これからはブランチ名を確認＆明記。  

    # git push github:ooblog/LTsv9kantray.git 20160328M231906

### ブランチ引数を忘れた場合はどうなるか。

[引数なしのgit pushは危険なので気をつけましょう](http://dqn.sakusakutto.jp/2012/10/git_push.html)
>引数なしで"git push"すると、何がpushされるかご存知でしょうか？  
>デフォルトでは、ローカルブランチと同名のブランチがリモート上にあるならそれらを一気にpushしてしまいます。  
>カレントブランチが何であろうと関係ないのです。  
>公式アナウンスによれば、Git次期バーション(おそらく2.0)あたりから"simple"モードがデフォルトになるそうです。  
>simpleモードとは、「カレントブランチと同名のリモートブランチが存在する場合のみ、カレントブランチのpushが行われる」モードだそうです。  

参考までにバージョン確認方法。バージョンが低いと使えない機能とかある。

    git --version

## 動いてる本番環境(master)に手をかける。ブランチをマージする。

masterにブランチをマージする場合、masterに移動してマージするブランチ名を指定。

    # git checkout master
    # git merge 20160328M231906
    Updating 553f7ae..a795676
    Fast-forward
     .gitignore                            |    3 ++-
     LTsv.txt                              |   32 +++++++++++++++++---------------
     LTsv_calc.py => LTsv/LTsv_calc.py     |    2 +-
     LTsv_file.py => LTsv/LTsv_file.py     |    2 +-
     LTsv_gui.py => LTsv/LTsv_gui.py       |    2 +-
     LTsv_joy.py => LTsv/LTsv_joy.py       |    8 ++++----
     LTsv_joy.tsv => LTsv/LTsv_joy.tsv     |    9 ++++++---
     LTsv_kbd.py => LTsv/LTsv_kbd.py       |   10 +++++-----
     LTsv_kbd.tsv => LTsv/LTsv_kbd.tsv     |    8 +++++---
     LTsv_printf.py => LTsv/LTsv_printf.py |    2 +-
     LTsv_time.py => LTsv/LTsv_time.py     |    2 +-
     README.md                             |    4 ++--
     kanfont.py                            |    8 ++++++--
     kanfont.tsv                           |    4 ++--
     kantray.py                            |    3 ++-
     kantray.tsv                           |    4 ++--
     kantray.txt                           |    2 +-
     kanzip.py                             |    9 +++++----
     18 files changed, 64 insertions(+), 50 deletions(-)
     rename LTsv_calc.py => LTsv/LTsv_calc.py (99%)
     rename LTsv_file.py => LTsv/LTsv_file.py (99%)
     rename LTsv_gui.py => LTsv/LTsv_gui.py (99%)
     rename LTsv_joy.py => LTsv/LTsv_joy.py (98%)
     rename LTsv_joy.tsv => LTsv/LTsv_joy.tsv (75%)
     rename LTsv_kbd.py => LTsv/LTsv_kbd.py (99%)
     rename LTsv_kbd.tsv => LTsv/LTsv_kbd.tsv (98%)
     rename LTsv_printf.py => LTsv/LTsv_printf.py (99%)
     rename LTsv_time.py => LTsv/LTsv_time.py (99%)
    # git status
    # On branch master
    # Your branch is ahead of 'origin/master' by 17 commits.
    #
    nothing to commit (working directory clean)

[ブランチをマージする【チュートリアル1 ブランチを使ってみよう](https://git-scm.com/book/ja/v1/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A8%E3%83%9E%E3%83%BC%E3%82%B8%E3%81%AE%E5%9F%BA%E6%9C%AC)
>$ git checkout master
>$ git merge hotfix
>あるコミットに対してコミット履歴上で直接到達できる別のコミットをマージしようとした場合、
>Git は単にポインタを前に進めるだけで済ませます。マージ対象が分岐しているわけではないからです。
>この処理のことを "fast forward" と言います。

開発が複雑な場合ブランチにマージするケースも。

[ブランチ切って更新してマージするまでの流れ](http://qiita.com/shuntaro_tamura/items/6c8bf792087fe5dc5103)
>今回は、developブランチに機能を追加するため、feature-create-apiというブランチを作成し、  
>後でdevelopブランチにマージすることを考える。  
>マージ先のブランチに移動  
>git checkout develop  
>マージ  
>git merge --no-ff feature-create-api  
>--no-ffオプション：fast-forwardの関係であっても、必ずマージコミットを作る  

## masterにブランチをマージしても問題が無かったら、pushして、ブランチの削除。

pushは今まで通り。  

    # git push github:ooblog/LTsv9kantray.git master  
    Total 0 (delta 0), reused 0 (delta 0)
    To github:ooblog/LTsv9kantray.git
       553f7ae..a795676  master -> master

マージしたブランチは「-d」(小文字オプション)で削除できる。

    # git branch -d 20160328M231906
    Deleted branch 20160328M231906 (was a795676).

## リモート上(GitHubサーバー)のブランチ削除もコマンドから。

    git push github:ooblog/LTsv9kantray.git :20160328M231906
    To github:ooblog/LTsv9kantray.git
     - [deleted]         20160328M231906

[Githubのブランチ削除](http://qiita.com/kwgch/items/98bbaae8e9f40a9548e0)
>やりたいこと  
>Githubのブランチ削除したい  
>やりかた  
>$ git push origin :branch  
>前提：originはgithubリポジトリ  
>$ git push origin branch  
>は  
>$ git push origin branch(local):branch(remote)  
>の意味なので  
>$ git push origin :branch  
>とすれば空がpushされて削除扱いになる  

[Git超入門："git push origin master"の"push"と"origin"と"master"の意味がわからないあなたへ](http://dqn.sakusakutto.jp/2011/10/git_push_origin_master.html)
> origin ...　の意味は？ずばり、URLに"origin"という短縮名(ニックネーム)を付ける  
>ここが重要なのですが　別にニックネームをつけなくてもよい。リモートサーバのURLを直接書いてもかまいません。  
>どうですか？ちょっと楽になったでしょ？  
>そして、"master"は正式に書くと"master:master"です。  

## まだ書きかけ。

備忘録。  

## あとがき＆今後の予定。

執筆者もGitHub勉強中。  
ブランチ管理が必要になったのでとりあえず最小限メモ書き。  
